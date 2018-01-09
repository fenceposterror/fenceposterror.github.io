# Review JSF Apps

Who doesn’t like XSS delivered for free by the underlying framework? How to spot this with a code review and also how to avoid the Java Server Faces ViewState blocking your testing is part of the article. While this is probably not new and definitely isn’t complete, I decided nonetheless to put some of this information together in one place anyway.

## ViewState - Server vs. Client

Detecting the underlying framework is always helpful. With JSF, it can easily be detected by the parameter `javax.faces.ViewState`. It is kept to keep track of the _view_ from the MVC. It can be client-side or server-side. A client-side `ViewState` can be easily detected by it being quite large. A server-side `ViewState` is usually shorter and looks more like 

    7614830582136862610:2983626105194287424

There are various attacks on the client-side `ViewState`, especially interesting Java Deserialization attacks, if it is implemented in the wrong way. Worth looking in this context is [JSF ViewState upside-down](https://www.synacktiv.com/ressources/JSF_ViewState_InYourFace.pdf) and [Dissecting Java Server Faces for Penetration Testing](http://www.secniche.org/jsf/dissecting_jsf_pt_aks_kr.pdf). There are more papers and blog posts. Most of them date a bit, but point to the most relevant issues still.

## Burp and ViewState

The `ViewState` can be a pain in the ass, when you want to use Burp's Repeater, Intruder or even the Scanner function. To avoid having to manually copy the `ViewState` from the last response, one can use Burp's Macro feature.

First, create a new `Macro` under `Project options` -> `Sessions` -> `Macros` by selecting a request that returns the `ViewState` of the page you are manipulating. `Configure item`, you can add a custom parameter location. Add `javax.faces.ViewState`.

![Extract ViewState](./assets/macro_extract_viewstate.png)

Second, add this Macro to the `Session Handling Rules` below `Use cookies from Burp's cookie jar`. Give it a meaningful name, chose `run macro` as action and select your previously created macro. It is worth making sure, that everything you want to be in scope is in scope, and not much else is messed with. 

![Configuring Scope](./assets/session_handling_scope.png)

Basically that is it. In case you need to do some trouble shooting, or want to validate it worked, use the `session tracer`. It should look roughly like this:

![Seeing that it got applied](./assets/session_handling_after_application.png)

## Easy XSS

Just grep in the code for `escape="false"`. If the default escape got turned off, without adding proper validators, providing input to those parameters, will result in XSS, because of reflecting the value that can't be converted back to the error page.

```
javax.faces.component.UIInput.CONVERSION -- {0}: Conversion error occurred
javax.faces.converter.BigDecimalConverter.DECIMAL={2}: ''{0}'' must be a signed decimal number.
javax.faces.converter.BigDecimalConverter.DECIMAL_detail={2}: ''{0}'' must be a signed decimal number consisting of zero or more digits, that may be followed by a decimal point and fraction. Example: {1}
javax.faces.converter.BigIntegerConverter.BIGINTEGER={2}: ''{0}'' must be a number consisting of one or more digits.
javax.faces.converter.BigIntegerConverter.BIGINTEGER_detail={2}: ''{0}'' must be a number consisting of one or more digits. Example: {1}
javax.faces.converter.BooleanConverter.BOOLEAN={1}: ''{0}'' must be 'true' or 'false'.
javax.faces.converter.BooleanConverter.BOOLEAN_detail={1}: ''{0}'' must be 'true' or 'false'. Any value other than 'true' will evaluate to 'false'.
javax.faces.converter.ByteConverter.BYTE={2}: ''{0}'' must be a number between 0 and 255.
javax.faces.converter.ByteConverter.BYTE_detail={2}: ''{0}'' must be a number between 0 and 255. Example: {1}
javax.faces.converter.CharacterConverter.CHARACTER={1}: ''{0}'' must be a valid character.
javax.faces.converter.CharacterConverter.CHARACTER_detail={1}: ''{0}'' must be a valid ASCII character.
javax.faces.converter.DateTimeConverter.DATE={2}: ''{0}'' could not be understood as a date.
javax.faces.converter.DateTimeConverter.DATE_detail={2}: ''{0}'' could not be understood as a date. Example: {1} Chapter 2 Request Processing Lifecycle 2-19
javax.faces.converter.DateTimeConverter.TIME={2}: ''{0}'' could not be understood as a time.
javax.faces.converter.DateTimeConverter.TIME_detail={2}: ''{0}'' could not be understood as a time. Example: {1}
javax.faces.converter.DateTimeConverter.DATETIME={2}: ''{0}'' could not be understood as a date and time.
javax.faces.converter.DateTimeConverter.DATETIME_detail={2}: ''{0}'' could not be understood as a date and time. Example: {1}
javax.faces.converter.DateTimeConverter.PATTERN_TYPE={1}: A 'pattern' or 'type' attribute must be specified to convert the value ''{0}''.
javax.faces.converter.DoubleConverter.DOUBLE={2}: ''{0}'' must be a number consisting of one or more digits.
javax.faces.converter.DoubleConverter.DOUBLE_detail={2}: ''{0}'' must be a number between 4.9E-324 and 1.7976931348623157E308 Example: {1}
javax.faces.converter.EnumConverter.ENUM={2}: ''{0}'' must be convertible to an enum.
javax.faces.converter.EnumConverter.ENUM_detail={2}: ''{0}'' must be convertible to an enum from the enum that contains the constant ''{1}''.
javax.faces.converter.EnumConverter.ENUM_NO_CLASS={1}: ''{0}'' must be convertible to an enum from the enum, but no enum class provided.
javax.faces.converter.EnumConverter.ENUM_NO_CLASS_detail={1}: ''{0}'' must be convertible to an enum from the enum, but no enum class provided.
javax.faces.converter.FloatConverter.FLOAT={2}: ''{0}'' must be a number consisting of one or more digits.
javax.faces.converter.FloatConverter.FLOAT_detail={2}: ''{0}'' must be a number between 1.4E-45 and 3.4028235E38 Example: {1}
javax.faces.converter.IntegerConverter.INTEGER={2}: ''{0}'' must be a number consisting of one or more digits.
javax.faces.converter.IntegerConverter.INTEGER_detail={2}: ''{0}'' must be a number between -2147483648 and 2147483647 Example: {1}
javax.faces.converter.LongConverter.LONG={2}: ''{0}'' must be a number consisting of one or more digits.
javax.faces.converter.LongConverter.LONG_detail={2}: ''{0}'' must be a number between -9223372036854775808 to 9223372036854775807 Example: {1}
javax.faces.converter.NumberConverter.CURRENCY={2}: ''{0}'' could not be understood as a currency value.
javax.faces.converter.NumberConverter.CURRENCY_detail={2}: ''{0}'' could not be understood as a currency value. Example: {1}
javax.faces.converter.NumberConverter.PERCENT={2}: ''{0}'' could not be understood as a percentage.
javax.faces.converter.NumberConverter.PERCENT_detail={2}: ''{0}'' could not be understood as a percentage. Example: {1}
javax.faces.converter.NumberConverter.NUMBER={2}: ''{0}'' is not a number.
javax.faces.converter.NumberConverter.NUMBER_detail={2}: ''{0}'' is not a number. Example: {1}
javax.faces.converter.NumberConverter.PATTERN={2}: ''{0}'' is not a number pattern.
javax.faces.converter.NumberConverter.PATTERN_detail={2}: ''{0}'' is not a number pattern. Example: {1}
javax.faces.converter.ShortConverter.SHORT={2}: ''{0}'' must be a number consisting of one or more digits.
javax.faces.converter.ShortConverter.SHORT_detail={2}: ''{0}'' must be a number between -32768 and 32767 Example: {1}
javax.faces.converter.STRING={1}: Could not convert ''{0}'' to a string.
```
