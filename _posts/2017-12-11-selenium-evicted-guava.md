---
layout: post
title: Evicted dependencies with Selenium
---

Had a weird exception when I added a Selenium test to a project:

``` diff
ThrowableException: Class org.openqa.selenium.support.ui.ExpectedConditions$6 does not implement the requested interface java.util.function.Function (FluentWait.java:209)
```

The error should be related to an outdated Guava version, but I didn't have any Guava dependencies in my `sbt` project, or at least I thought I didn't.

I added the [sbt dependency graph](https://github.com/jrudolph/sbt-dependency-graph) plugin to check my dependency tree and there it was:

``` diff
[info]  | +-org.seleniumhq.selenium:selenium-remote-driver:3.5.3
[info]  |   +-cglib:cglib-nodep:3.2.4
[info]  |   +-com.google.code.gson:gson:2.8.0
[info]  |   +-com.google.guava:guava:23.0 (evicted by: 23.1-android)
[info]  |   +-com.google.guava:guava:23.1-android
```

Guava 23.0 was being evicted by the `23.1-android` version which I managed to trace back to the Bugsnag dependency:

``` diff
[info]   +-com.bugsnag:bugsnag:3.1.3
[info]   | +-com.fasterxml.jackson.core:jackson-databind:2.9.1
[info]   | | +-com.fasterxml.jackson.core:jackson-annotations:2.9.0
[info]   | | +-com.fasterxml.jackson.core:jackson-core:2.9.1
[info]   | |
[info]   | +-com.google.guava:guava:23.1-android
[info]   | | +-com.google.code.findbugs:jsr305:1.3.9 (evicted by: 2.0.1)
[info]   | | +-com.google.code.findbugs:jsr305:2.0.1
[info]   | | +-com.google.errorprone:error_prone_annotations:2.0.18
[info]   | | +-com.google.j2objc:j2objc-annotations:1.1
[info]   | | +-org.codehaus.mojo:animal-sniffer-annotations:1.14
```

It was an easy fix now, I simply excluded Guava from Bugsnag in `build.sbt` and let it use the 23.0 version from selenium:

``` scala
"org.seleniumhq.selenium" % "selenium-java" % "3.5.3" % "test",
"com.bugsnag" % "bugsnag" % "3.1.3" exclude("com.google.guava", "guava"),
```

