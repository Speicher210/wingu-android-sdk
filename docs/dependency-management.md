# Dependency management

If you are already using a library that is also used by wingu sdk, yo can override a version number of any transitive dependency of wingu, to resolve any potential conflicts. For example, if you are using `com.google.android.gms:play-services-gcm:11.6.0`, but wingu sdk uses `com.google.android.gms:play-services-maps:11.4.0`, your project may not compile.

This is especially useful and __recommended__ if you are using Support Libraries or Google Play Services, to make sure that both your app and wingu sdk use the same versions.

See [full list of wingu dependencies](../releases/com/github/Speicher210/wingu-android-sdk/bom/1.0.0/bom-1.0.0.pom) which you can override.

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
	playServicesVersion = '11.6.0'
    supportLibsVersion = '26.1.0'
    winguSdkVersion = '1.0.0'
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
                    'com.android.support.version': supportLibsVersion
            ])
        }
    }
}
```

As a result, any transitive dependencies of wingu sdk will resolve to the version numbers that you specified.
