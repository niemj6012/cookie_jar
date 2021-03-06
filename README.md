# CookieJar

[![build statud](https://img.shields.io/travis/flutterchina/cookie_jar/master.svg?style=flat-square)](https://travis-ci.org/flutterchina/cookie_jar)
[![Pub](https://img.shields.io/pub/v/cookie_jar.svg?style=flat-square)](https://pub.dartlang.org/packages/cookie_jar)
[![coverage](https://img.shields.io/codecov/c/github/flutterchina/cookie_jar/master.svg?style=flat-square)](https://codecov.io/github/flutterchina/cookie_jar?branch=master)
[![support](https://img.shields.io/badge/platform-flutter%7Cdart%20vm-ff69b4.svg?style=flat-square)](https://github.com/flutterchina/cookie_jar)

A cookie manager for http requests in Dart, by which you can deal with the complex cookie policy and persist cookies easily.

### Add dependency

```yaml
dependencies:
  dio: ^0.0.2
```

## Usage

A simple usage example:

```dart
import 'package:cookie_jar/cookie_jar.dart';
void main() async {
  List<Cookie> cookies = [new Cookie("name", "wendux"),new Cookie("location", "china")];
  var cj = new CookieJar();
  //Save cookies   
  cj.saveFromResponse(Uri.parse("https://www.baidu.com/"), cookies);
  //Get cookies  
  List<Cookie> results = cj.loadForRequest(Uri.parse("https://www.baidu.com/xx"));
  print(results);  
}    
       
```

## Classes

### `SerializableCookie`

This class is a wrapper for `Cookie` class. Because the `Cookie` class doesn't  support Json serialization, for the sake of persistence, we use this class instead of it.

### `CookieJar`

`CookieJar` is a default cookie manager which implements the standard cookie policy declared in RFC. CookieJar saves the cookies in **RAM**, so if the application exit, all cookies will be cleared. A example as follow:

```dart
var cj= new CookieJar();
```

### `PersistCookieJar`

`PersistCookieJar` is a cookie manager which implements the standard cookie policy declared in RFC. `PersistCookieJar`  persists the cookies in files, so if the application exit, the cookies always exist unless call `delete` explicitly. A example as follows:

```dart
// Cookie files will be saved in "./cookies"
var cj=new PersistCookieJar("./cookies");
```

> **Note**: In Flutter, File system is different from PC,  you can use [path_provider](https://pub.dartlang.org/packages/path_provider) package to get the path :
>
> ```dart
> // API `getTemporaryDirectory` is from "path_provider" package.
> Directory tempDir = await getTemporaryDirectory();
> String tempPath = tempDir.path;
> CookieJar cj=new PersistCookieJar(tempPath);
> ```



## APIs

**void saveFromResponse(Uri uri, List<Cookie> cookies);**

Save the cookies for specified uri.

**List<Cookie> loadForRequest(Uri uri);**

Load the cookies for specified uri.

**delete(Uri uri,[bool withDomainSharedCookie = false] )**

Delete cookies for specified `uri`. This API will delete all cookies for the `uri.host`, it will ignored the `uri.path`.

If `withDomainSharedCookie` is `true `  ,  will delete the domain-shared cookies.

*Note: This API is only available in `PersistCookieJar` class.*

## Working with `HttpClient`

Using  `CookieJar` or `PersistCookieJar` manages  `HttpClient ` 's  request/response cookies is very easy:

```dart
var cj=new CookieJar();
...
request= await httpClient.openUrl(options.method, uri);
request.cookies.addAll(cj.loadForRequest(uri));
response= await request.close();
cj.saveFromResponse(uri, response.cookies);
```

## Working with dio

[dio](https://github.com/flutterchina/dio) is a powerful Http client for Dart, which supports Interceptors, Global configuration, FormData, File downloading, Timeout etc.  And [dio](https://github.com/flutterchina/dio) supports to manage cookies with cookie_jar, the simple example is:

```dart
var dio = new Dio();
dio.cookieJar=new PersistCookieJar("./cookies");
Response<String>  response = await dio.get("https://www.baidu.com");
```

More details about [dio](https://github.com/flutterchina/dio)  see : https://github.com/flutterchina/dio .

## Copyright & License

This open source project authorized by https://flutterchina.club , and the license is MIT.

## Features and bugs

Please file feature requests and bugs at the [issue tracker][tracker].

[tracker]: https://github.com/flutterchina/cookie_jar

