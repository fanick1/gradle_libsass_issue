Minimal example demonstrating an issue (https://github.com/gradle/gradle/issues/14484) when using library io.bit3:jsass:5.7.1 (or newer) as part of a Gradle build with Gradle 6.6 (or newer - e.g. 6.6.1) under Linux, JDK 11 64bit.
The jsass library extracts the libjsass.so under the hood and calls System.load(...path to libsass.so...). If this code is executed for example in
a gradle plugin, it crashes the gradle daemon

```
FAILURE: Build failed with an exception.

* What went wrong:
Gradle build daemon disappeared unexpectedly (it may have been killed or may have crashed)

```

for the sake of simplicity of the demo this repo contains libsass.so binaries as they should be present in the jsass lib

jsass 5.7.0 uses libsass 3.5.2 which should be this one:
https://github.com/bit3/jsass/blob/5.7.0/src/main/resources/linux-x64/libjsass.so

jsass 5.7.1 starts to use libsass 3.5.3 which should be this one:
https://github.com/bit3/jsass/blob/5.7.1/src/main/resources/linux-x64/libjsass.so

Repro steps:
to observe the issue (load the libsass 3.5.3):
execute `./gradlew explodes`

similarly to observe the non-issue (load the libsass 3.5.2)
execute `./gradlew good`

now if you switch to gradle 6.5 (modify the version in the wrapper{} block and run ./gradlew wrapper)
both of the tasks will succeed.

This seems to be in essence the same issue as https://gitlab.com/jsass/jsass/-/issues/75 but only manifesting itself with the gradle 6.6.

I have also managed to dig out some stack trace dump from system logs, see `stacktrace.txt`.
