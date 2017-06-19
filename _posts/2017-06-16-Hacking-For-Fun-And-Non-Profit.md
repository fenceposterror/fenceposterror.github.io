---
layout: post
title: "Hacking Open Source Software for Fun and Non-Profit"
date: 2017-06-16
---
 
«Hacking Open Source Software for Fun and Non-Profit» is the title of [blog post](http://haxx.ml/post/137946990286/hacking-open-source-software-for-fun-and) by fellow security researcher [@addelindh](https://twitter.com/addelindh). Testing Open Source Software (OSS) is something that has been on my to-do list for a while, because I am convinced that a certain amount of time should be dedicated to it. The reason for this is simple: I firmly believe in giving back to a community that I rely on a daily basis. This blog post is about an ongoing audit of the [Ampache](http://ampache.org) media streaming server, the vulnerabilities found and lessons learned. 

![archive]({{ site.url }}/assets/sqli01.png)

From the Ampache website:
 
> A web based audio/video streaming application and file manager allowing you to access your music & videos from anywhere, using almost any internet enabled device.
 
## Goals
 
The three main goals were:
 
1. Give something back to the community
2. Learn more about code auditing
3. Try out RIPS
 
I had been curious about [RIPS](https://www.ripstech.com), for a while, partially because of their [Advent of PHP Application Vulnerabilities](https://www.ripstech.com/blog/2016/apav-advent-of-php-application-vulnerabilities/). When I wanted to purchase an on-demand license, they were so generous and offered me a free trial, once they heard what I wanted to use it for. Thanks, guys! 
 
## Lessons
 
### Unquoted SQL Injections
 
Most of my previously conducted audits for my employers I had to conduct without code – searching manually for vulnerabilities in a deployed instance. Working directly with code made me a bit overeager and I reported a vulnerability that I thought was especially cool: unquoted SQL injections. I did so in a hurry and without verification and reported a false positive. Unquoted SQL injections are seldom covered.  If you haven’t read about unquoted SQL injections, I highly recommend reading the article [The Unexpected SQL Injection](http://www.webappsec.org/projects/articles/091007.txt). An article discussing the exploitation of an unquoted SQL injection can be found on the RIPS blog, titled [Guest Post: Vtiger 6.5.0 - SQL Injection](https://www.ripstech.com/blog/2016/vtiger-sql-injection/). 
 
### Bypassing quote()
 
I went back to check what went wrong and learned something else interesting. There was a method named `escape` in [dba.class.php](https://github.com/ampache/ampache-debian/blob/master/lib/class/dba.class.php#L132), that I had overlooked
 
``` php
132        public static function escape($var)
133        {
134            $dbh = self::dbh();
135            if (!$dbh) {
136                debug_event('Dba', 'Wrong dbh.', 1);
137                exit;
138            }
139            $var = $dbh->quote($var);
140            // This is slightly less ugly than it was, but still ugly
141            return substr($var, 1, -1);
142        }
```
 
If I had looked once, I had noticed that things got quoted and therefore should have been fine, skipping the nuisance for the developers. The interesting part is on the second look in line 141. `substr` can break a properly quoted string. While in this case it doesn’t, it is quite nice that RIPS knows - it is worth not just assuming that the method is safe. RIPS also offers configuration options where one can set not automatically discovered validators and sanitizers. 
 
### Find Vulnerabilities in Non-Active Code
 
One of the pitfalls in manual-only testing is that you won't see functionality that is not configured. Ampache has a function to log successful logins of users. In that function it also logs the user agent. 
 
``` php
911    $agent = Dba::escape($_SERVER['HTTP_USER_AGENT']);
```
 
This code is never reached, if the default setting of `track_user_ip` is not changed to `true`. While this instance is safe, there is a lot more functionality, like connecting to other services among other features, which I haven’t yet investigated. They are turned off by default. It seems there is more. 
 
### Logic Errors Are Difficult to Find with Static Code Analysis
 
During the quick manual checks in the beginning, I noticed the app is missing a CSRF token for changing passwords and it also doesn’t ask for the old password. Since I hadn’t worked much with static code analyzers before, their shortcomings were not obvious to me. However, finding them is impossible with this sort of approach due to the fact that they’re logical application errors. Just as side note, when I wanted to report the change password issues, I noticed, that it had already been reported as [issue 539](https://github.com/ampache/ampache/issues/539), but this hasn’t been addressed yet.
 
### Same PHP Object Instantiation but Different
 
My favorite issue, maybe because I haven’t cracked it yet, is a PHP Object Instantiation, something that could be considering a sub-class of PHP Object Injections (POI). Usually when there is talk of a POI vulnerability, one thinks of unserialize and magic functions. This one is different.
 
Quick excursion: Ampache has multi-user support for playlists. While the playlist is playing, other users can vote for songs or suggest songs. Depending on those votes, the next song will be chosen. This feature is called when choosing democratic as type of playback in the streaming. The following vulnerability is located in the voting feature.
 
First, the GET parameter `type` is received in line 43 of democratic.ajax.php and passed to the `add_vote` method.
 
```php
43      $democratic->add_vote(array(array('object_type' => $_REQUEST['type'], 'object_id' => $_REQUEST['object_id'])));
```
 
The method is defined in [democratic.class.php](https://github.com/ampache/ampache/blob/develop/lib/class/democratic.class.php#L348).
 
```php
348        public function add_vote($items)
349        {
350            /* Iterate through the objects if no vote, add to playlist and vote */
351            foreach ($items as $element) {
352                $type      = array_shift($element);
353                $object_id = array_shift($element);
354                if (!$this->has_vote($object_id, $type)) {
355                    $this->_add_vote($object_id, $type);
356                }
357            } // end foreach
358        } // vote
```
 
In line 355 `type` parameter is passed to `_add_vote`.
 
```php
396        private function _add_vote($object_id, $object_type = 'song')
397        {
398            if (!$this->tmp_playlist) {
399                return false;
400            }
401    
402            $media = new $object_type($object_id);
403                  ...
```
 
Finally in line 402 the object is instantiated.
 
When looking for this kind of POI, there is little information on it. I had found this [security exchange post](https://t.co/tWctpDI45H) and [@floyd_ch](https://www.twitter.com/floyd_ch) was so kind to point me to probably the best article on this subject: [PHP Object Instantiation](https://blog.leakfree.nl/2015/03/12/php-object-instantiation-cve-2015-1033/). If someone takes a shot at it, I’d be very curious to know if they find a chain that makes this exploitable. 
 
RIPS also found various authenticated reflected XSS (issues [1533](https://github.com/ampache/ampache/issues/1533), [1532](https://github.com/ampache/ampache/issues/1532) and [1541](https://github.com/ampache/ampache/issues/1541)) and also authenticated SQLi (issue [1536](https://github.com/ampache/ampache/issues/1536)). The best documentation of those is always right in the tickets themselves. I'll leave possible exploit chains up to the reader's imagination.
 
### Organizational Issues
 
Regarding the organizational aspects of auditing OSS, how do you report a vulnerability to an open source project, that doesn’t have a dedicated e-mail address set up? In this case I asked in the mailing list beforehand and they said to just file an issue. It would be really nice to be able to report an issue as “private” so only the reporter and the admins can see the issue. This would be a great feature for Github to implement. 
 
## Conclusion
 
First of all, I’m not done yet, and if something else pops up worth discussing, I will write another blog post. 
 
Mapping a detected vulnerability in the browser to the code, if you are not very familiar with the code base is an interesting activity. Also doing the inverse was fun. Finding something manually and then trying to find the matching finding of RIPS.
 
It is worth pointing out, that OSS is hard. This project is done completely by volunteers without doing this on their job time. Some of the vulnerabilities were closed quickly. Others are still open. I also have not reported everything yet, giving the developers a chance to close one by one without sending them too many reports at once. It seems like they’ll soon release a last version including the patches and then try to port the project to the Laravel framework. 
 
Thanks to RIPStech who let me play with their code analysis solution to contribute to open source! It is easy to use and has a comfortable GUI, that lets you explore the code very comfortably. It is noticeable, that the solution was developed by people who use it themselves - providing features tailored to bug hunters. Last but not least an appeal to security researchers, pentesters, white hats or whatever you call yourselves: If you have some spare time, consider hacking open source software. Let’s do our part and contribute.

## Addendum

Since I was told, that some of my points could create confusion to whether RIPS didn't perform properly -  let me clarify: RIPS did an outstanding job finding a total of 165 vulnerabilities and other security issues in 3m26s in 109039 lines of code spread over 531 files. That is after configuring it, so without false positives. Some vulnerabilities have up to seven levels from source to sink. I'm impressed and I thought it was clear, that I'd recommend it to hunt for bugs in PHP.
