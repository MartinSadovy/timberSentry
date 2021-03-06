# Timber Sentry [![](https://jitpack.io/v/com.ivoberger/timberSentry.svg)](https://jitpack.io/#com.ivoberger/timberSentry)

This is a small library combining the [Sentry][1] error reporting tool and [Timber][2] logging library.
Currently only Android is supported.

## Usage

The library provides a `SentryTree` which implements `Timber.Tree` and initializes Sentry for you. The simplest version is:
```Kotlin
Timber.plant(SentryTree("sentry DSN"))
// without any arguments works too if you define your DSN using a sentry.properties file
Timber.plant(SentryTree())
// now simply use Timber as usual
Timber.d("Something happened")
```
You might want to initialize Sentry with an `AndroidSentryClientFactory`. To do so simply supply your applications context like so:
```Kotlin
Timber.plant(SentryTree("sentry DSN", this))
// or if you use a properties file:
Timber.plant(SentryTree(context = this))
```
If you only want to report to Sentry in release builds you can use this together with Timber's `DebugTree`:
```Kotlin
if (BuildConfig.DEBUG) Timber.plant(DebugTree())
else Timber.plant(SentryTree())
```

You can further control event from which priorities will be captured and which will be recorded as [breadcrumbs][3]:
```Kotlin
val sentryTree = SentryTree(
    sentryDsn = "sentry DSN",
    context = this,
    // the following are the defaults
    minCapturePriority = Log.ERROR,
    minBreadcrumbPriority = Log.INFO,
    extrasDelimiter = '|'
)
Timber.plant(sentryTree)
```
Sentry supports adding extra data to your reports. Additionally to the ways the Sentry SDK provides you can now do this by appending the extras data to your message using a delimiter:
```Kotlin
Timber.e("Something went wrong | extras label | extra data")
// if your data is in an Iterable<Pair<String, Any>> or a Map<String,Any>
// with a label to data structure you can use the helper functions
Timber.e("Something went wrong %s", extrasListOrMap.toSentryExtras())
```
By default extras for an event that won't be captured will be added to the default Sentry context. Otherwise they'll only be added to the captured event.
Using the `addAllExtrasToContext` constructor variable this can be changed so extras will always be added to the context while `clearContext` controls if the context is cleared after an event was captured.

## Download
Currently only available via [jitpack.io](https://jitpack.io):
Add to your top-level `build.gradle`
```groovy
allprojects {
    repositories {
        maven { url 'https://jitpack.io' }
    }
}
```
Add to your module `build.gradle`:
```groovy
dependencies {
    implementation 'com.ivoberger:timberSentry:0.3.0'
}
```


## License

    Copyright 2018 Ivo Berger

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.

[1]: https://sentry.io/
[2]: https://github.com/JakeWharton/timber
[3]: https://docs.sentry.io/clients/java/context/
