---
layout: post
title: "Review Angular2 Apps"
date: 2018-02-06
---

[Angular2](https://angular.io/) is one of those frameworks you'll come across if you review recently developed applications. Various potential security issues found in the first version of Angular are being addressed by the new framework. So if you don't completely abuse it, the baseline is decent. There are a few minor issues that can be quite easily spotted when reviewing an application, and I decided to note them here.

## removeComments
The configuration options allow for removal of comments automatically during the build process. This is a good idea to prevent accidental information leakage. Simply check if the line 

    "removeComments": true

is in `tsconfig.json`.

## Script Gadgets
During the presentation of [Breaking XSS mitigations with Script Gadgets](https://2017.appsec.eu/presos/Hacker/Don%E2%80%99t%20trust%20the%20DOM%20Bypassing%20XSS%20mitigations%20via%20Script%20gadgets%20-%20Sebastian%20Lekies,%20Krzystof%20Kotowicz%20and%20Eduardo%20Vela%20Nava%20-%20OWASP_AppSec-Eu_2017.pdf) most will have noticed, that Angular2 was not in the list of investigated frameworks.

One of the reasons is, that the template compiler is by default not delivered to the client, since Angular2 uses [Ahead-of-Time](https://angular.io/guide/aot-compiler) (AOT) compilation. AOT is used whenever the flag `--aot` is passed. When the application is built with `--prod` - `--aot` is set as default. 

If you see a `vendor.bundle.js` file, aot wasn't used for the version you are looking at. It contains the compiler. Happy hunting for script gadgets.

While this shouldn't happen, it might and that is why we keep an eye open for one more setting. The Angular compiler can be set as a dependency in both `dependencies` or `devDependencies` in `package.json`. `devDependencies` will not be delivered when the app was compiled with `--prod`. 

What happens if the `dependencies` list the Angular compiler? 

Lucky for the application developers - in most cases it will be removed during [tree-shaking](https://webpack.js.org/guides/tree-shaking/). This might fail if the application directly references the compiler. To ensure that the Angular compiler really is not part of the app, the Angular compiler should only be listed in the `devDependencies`.

## bypassSecurityTrust* functions
That is what I call good naming. Simply grep for the [bypassSecurityTrust*](https://angular.io/api/platform-browser/DomSanitizer) functions, as an easy way to find where untrusted input is used:

    bypassSecurityTrustHtml
    bypassSecurityTrustStyle
    bypassSecurityTrustScript
    bypassSecurityTrustUrl
    bypassSecurityTrustResourceUrl

That's it. Hope you gained some new insights.
