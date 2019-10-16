# Hotfix for Alfresco issue [ALF-21963](https://issues.alfresco.com/jira/browse/ALF-21963)

This project is a hotfix for the [ALF-21963](https://issues.alfresco.com/jira/browse/ALF-21963)
issue that still affects Alfresco Community 5.1.e.

## Problem

At the heart of the issue lies the incorrect encoding of a url parameter: Alfresco encodes a NodeRef
as a JavaScript string, which involves escaping the slashes in the NodeRef, i.e. `workspace:\/\/
SpacesStore\/...`. Backslashes aren't valid in a url, so this causes the generated link to lead to a
blank page, while the following appears in the Alfresco logs:

```
INFO: Error parsing HTTP request header
 Note: further occurrences of HTTP header parsing errors will be logged at DEBUG level.
java.lang.IllegalArgumentException: Invalid character found in the request target. The valid characters are defined in RFC 7230 and RFC 3986
        at org.apache.coyote.http11.InternalInputBuffer.parseRequestLine(InternalInputBuffer.java:194)
        at org.apache.coyote.http11.AbstractHttp11Processor.process(AbstractHttp11Processor.java:1050)
        at org.apache.coyote.AbstractProtocol$AbstractConnectionHandler.process(AbstractProtocol.java:637)
        at org.apache.tomcat.util.net.JIoEndpoint$SocketProcessor.run(JIoEndpoint.java:317)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
        at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61)
        at java.lang.Thread.run(Thread.java:748)
```

## Solution

This AMP overrides the offending Freemarker template with an identical one (source from Alfresco
5.1.e) _except_ it lacks the `?js_string` builtin that incorrectly encodes the NodeRef.

## Building and Installing

This project uses the [alfresco-gradle-sdk](https://github.com/xenit-eu/alfresco-gradle-sdk). Clone
this project and use `./gradlew amp` to build. Find the resulting AMP at `./build/dist/` and install
it into Share.


