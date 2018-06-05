# Dependency management

If you are already using a library that is also used by wingu sdk, yo can override a version number of any transitive dependency of wingu, to resolve any potential conflicts. For example, if you are using `com.google.android.gms:play-services-gcm:11.6.0`, but wingu sdk uses `com.google.android.gms:play-services-maps:11.4.0`, your project may not compile.

This is especially useful and __recommended__ if you are using __Kotlin__ standard library, __Support Libraries__ or __Google Play Services__, to make sure that both your app and wingu sdk use the same versions.

See [full list of wingu dependencies](../releases/com/github/Speicher210/wingu-android-sdk/bom/3.0.2/bom-3.0.2.pom) which you can override.

### Overriding a library version

In your app's `build.gradle` file, add the following plugin:

```
plugins {
    id "io.spring.dependency-management" version "1.0.3.RELEASE"
}
```

Optionally, you can define library versions separately to reuse them later in your `dependencies` block:

```
ext {
    // adjust version numbers as needed by your project
    kotlinVersion = '1.2.31'
    playServicesVersion = '15.0.0'
    supportLibsVersion = '27.1.1'
    winguSdkVersion = '3.0.2'
}
```

Then apply the override:

```
buildscript {
    repositories {
        maven { url "https://github.com/Speicher210/wingu-android-sdk/raw/master/releases" }
    }
}

dependencyManagement {
    imports {
        mavenBom("com.github.Speicher210.wingu-android-sdk:bom:${winguSdkVersion}") {
            bomProperties([
                    'com.google.android.gms.version': playServicesVersion,
                    'com.android.support.version': supportLibsVersion,
                    'org.jetbrains.kotlin.version': kotlinVersion
            ])
        }
    }
}
```

As a result, any transitive dependencies of wingu sdk will resolve to the version numbers that you specified.
