# ReactMap for Android

Use ReactMap as an Android app with battery improvements, including:

* Use Google location services to follow location.
* Reduce animations.

Other features:

* See map even behind your status bar and navigation bar!
* More conveniently refresh the webpage when fullscreen. (During network congestion, Chrome HTTP/3 scheduler retries requests at its own pace. Doing a manual refresh overrides this.)
* Login button is clicked automatically (Discord login only).
* You could make alerts follow location in background.
* Handle links to the map without opening a new tab (requires custom build for custom domains).

## Guide for custom build

### Setup instructions

1. If you have not already, generate a signing key with [`keytool`](https://developer.android.com/build/building-cmdline#sign_cmdline).
   Using [RSA with a key length of 4096, 8192, or 16384 bits](https://github.com/google/bundletool/blob/0b9149c283e2df73850da670f2130a732639283d/src/main/java/com/android/tools/build/bundletool/commands/AddTransparencyCommand.java#L97) is recommended.
2. Create the following file at `https://mymap.com/.well-known/assetlinks.json` by creating the file in `/path/to/reactmap/public/.well-known/assetlinks.json`:
   ```json
   [
     {
       "relation": [
         "delegate_permission/common.handle_all_urls"
       ],
       "target": {
         "namespace": "android_app",
         "package_name": "be.mygod.reactmap.com.mymap",
         "sha256_cert_fingerprints": [
           "insert your sha256 cert fingerprint"
         ]
       }
     }
   ]
   ```
   where the fingerprint can be obtained via `keytool -list -v -keystore /path/to/keystore`.
   (If you prefer to using Android Studio to create this file with a wizard, see [here]( https://developer.android.com/studio/write/app-link-indexing#associatesite) for instructions.)
3. (Optional) Make modifications to the source code if you wish.

### Build instructions

Build with [injected properties](https://stackoverflow.com/a/47356720/2245107) (modifying these values as you wish):
```
./gradlew assembleRelease \
 -Pandroid.injected.signing.store.file=$KEYFILE \
 -Pandroid.injected.signing.store.password=$STORE_PASSWORD \
 -Pandroid.injected.signing.key.alias=$KEY_ALIAS \
 -Pandroid.injected.signing.key.password=$KEY_PASSWORD \
 -Preactmap.defaultHost=mymap.com \
 -Preactmap.packageName=be.mygod.reactmap.com.mymap
```

Optionally you can also inject `reactmap.appName`.
An alternative to using `-P` switches is to adding your properties to the `gradle.properties` file in the root directory.

Success! Find your apk in `app/build/outputs/apk/release`.
