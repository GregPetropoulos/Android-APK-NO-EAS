
# Expo 53 Android only local build with abb and apk with no EAS servers and no CNG. 

## Get APK (app)
- The apk can be downloaded on to your android device by clicking the link.
- On your device go to `system > settings > developer options >` and turn on USB Debugging
- https://drive.google.com/file/d/1LD4_uXwWZ2raT7AJiOA0vHhgaZqA4LrS/view?usp=drive_link

## Follow the below instructions to create your own apk 

### Step 1 my-app

`npx create-expo-app@latest my-app --template default`

`npx expo prebuild`

Go to the RN docs and pull this up

https://reactnative.dev/docs/signed-apk-android


### Step 2 my-app

#### Get a random password
```bash
openssl rand -hex 32
a77c28c8****...
```
#### Upload Key
jdk is here, cd into to  run the next command (On Windows cd one more folder into the bin)
```bash
/Library/Java/JavaVirtualMachines/zulu-17.jdk/Contents/Home
```
After your cd into the above folder, run the next command:
```bash
sudo keytool -genkey -v -keystore my-upload-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000
```

#### Add the keystore
From your jdk directory add the newly generated keystore file `my-upload-key.keystore`
to your android/app folder.


#### Update Gradle Properties
 - In android/gradle.properties
```properties
MYAPP_UPLOAD_STORE_FILE = my-upload-key.keystore
MYAPP_UPLOAD_KEY_ALIAS = my-key-alias
MYAPP_UPLOAD_STORE_PASSWORD = a77c28c8****... <---openssll pw goes here
MYAPP_UPLOAD_KEY_PASSWORD = a77c28c8****... <-----openssll pw goes here
```



#### Update build.gradle signingConfig with below
- In android/app/build.gradle
```gradle
android {
    ...
    defaultConfig { ... }
    signingConfigs {
        release {
            if (project.hasProperty('MYAPP_UPLOAD_STORE_FILE')) {
                storeFile file(MYAPP_UPLOAD_STORE_FILE)<——../my-upload-key.keystore
                storePassword MYAPP_UPLOAD_STORE_PASSWORD
                keyAlias MYAPP_UPLOAD_KEY_ALIAS
                keyPassword MYAPP_UPLOAD_KEY_PASSWORD
            }
        }
    }
    buildTypes {
        release {
            ...
            signingConfig signingConfigs.release
        }
    }
}
```


#### Add to .gitignore
- ```android/gradle.properties```
- ```android/app/my-upload-key.keystore```

## FOR ABB
- Then run this command —> ```npx react-native build-android --mode=release```
- Should take about 5-10 min to build
- Find the release in `android/app/build/outputs/build/release`

If you encounter a message as below, just add it to your `package.json` and run the build command again ```npx react-native build-android --mode=release```

  ```json
  "devDependencies": {
    "@react-native-community/cli": "latest",
  }
  ```

## FOR APK
- Plug android device into windows/mac and run `npx expo run:android --variant release`
- Look for the `apk` in `android\app\build\outputs\apk\release\` folder,
- Typically at this point the apk is installed on the connected device after the build is successful.
