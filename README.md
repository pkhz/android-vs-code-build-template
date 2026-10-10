# Android Kotlin App Development Template with VS Code

This project is a minimal Android/Kotlin Compose app template set up for development in VS Code without Android Studio. The folder and file structure already complete for a minimal build of an Android app. What is needed is local environment setup for VS Code, JDK, Kotlin and SDK for development as per guide below.

## Project structure

```text
mobile/kotlin-test/
├── .gitignore              # git
├── .gitattributes          # git
├── build.gradle.kts        # gradle
├── gradle.properties       # gradle
├── gradlew                 # gradle
├── gradlew.bat             # gradle
├── settings.gradle.kts     # gradle
├── local.properties        # local-only, do not commit              
├── hello.kt                # kotlin sample for jar test - can be excluded for real android build
├── app/                    # android
│   ├── build.gradle.kts    # gradle
│   └── src/                # android
│       └── main/           
│           ├── AndroidManifest.xml
│           ├── java/com/example/kotlintest/ # follow package name as per android studio com.company.app
│           └── res/
├── gradle/                 # gradle
│   └── wrapper/
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
└── README.md
```
> **NOTE:** Check compatibility versions of each to build. Can check with AI/documentation for compatibility and versions to download/install.

## JDK and Kotlin

### 1) JDK
Download JDK ZIP (Temurin/Eclipse Adoptium is a good choice). As per sample is JDK 17.

https://adoptium.net/temurin/releases?version=17&os=any&arch=any

Extract and rename to simple jdk-17, example:

```text
C:\path\jdk-17
```

Check in bash/cmd/any inside folder jdk-17:

```bash
"bin/java.exe" -version
"bin/javac.exe" -version
```



### 2) Kotlin compiler
Download the Kotlin command-line compiler ZIP and extract it.

https://github.com/JetBrains/kotlin/releases

Find ```kotlin-compiler-x.x.xx.zip```

Extract, example:

```text
C:\path\kotlinc
```

To run a basic Kotlin jar test outside Android:

```bash
kotlinc hello.kt -include-runtime -d hello.jar
java -jar hello.jar
```

## VS Code setup

Install these VS Code extensions:

- Kotlin
- Gradle for Java

Add local environment variables in your VS Code settings or in your shell.

settings.json:

```json
{
  "java.jdt.ls.java.home": "C:\\path\\jdk-17",
  "terminal.integrated.env.windows": {
    "JAVA_HOME": "C:\\path\\jdk-17",
    "PATH": "C:\\path\\kotlinc\\bin;C:\\path\\jdk-17\\bin;${env:PATH}"
  }
}
```

## Android SDK and emulator setup


> **NOTE:** All command line below should run from VS Code terminal.

Download the Android command-line tools.

https://developer.android.com/studio (scroll to bottom section 'Command line tools only')

Extract,  create new folder ```latest``` and put contents originally in ```cmdline-tools\``` to newly created ```latest``` ([Android's documentation specifically describes placing the command-line tools contents under cmdline-tools/latest](https://developer.android.com/tools/sdkmanager)) :

```text
C:\path\sdk\        # rename to sdk for simplicity
└── cmdline-tools\ 
    └── latest\     # create new folder
        ├── bin\
        ├── lib\
        ├── NOTICE.txt
        └── source.properties
```

Set Windows system environment variables:

```text
Under User variables
Add new variable
Name: ANDROID_HOME 
Value: C:\path\sdk

Add new to existing PATH variable:
- C:\path\sdk\platform-tools
- C:\path\sdk\cmdline-tools\latest\bin
- C:\path\sdk\emulator
or
- %ANDROID_HOME%\platform-tools
- %ANDROID_HOME%\latest\bin
- %ANDROID_HOME%\sdk\emulator
```

Check inside VS Code terminal:

```
echo $ANDROID_HOME
where sdkmanager
# above should show path created in system environment variables

sdkmanager --version
# or if above not show path
sdkmanager.bat --version
# or if above not show path
"C:\path\sdk\cmdline-tools\latest\bin\sdkmanager.bat" --version

```

### Install required Android packages


```bash
sdkmanager.bat "platform-tools" "emulator"
```

Above will create new folder in ```sdk``` folder, check installed:

```bash
adb version
emulator -version
```

List available packages, check created ```list.txt```:

```bash
sdkmanager.bat --list > list.txt
```

> **NOTE:** Can replace all below package versions and emulator devices with any version wanted.


Install the current stable Android platform and build tools:

```bash
sdkmanager.bat "platforms;android-34" "build-tools;34.0.0"
```

Accept licences:

```bash
sdkmanager.bat --licenses
```

If prompted, accept with `y`.

### Install emulator system image

```bash
sdkmanager.bat "system-images;android-34;google_apis;x86_64"
```

### Create and list virtual devices

List available devices and emulator profiles:

```bash
avdmanager.bat list device
```

Create an emulator:
> **NOTE:** Can also replace name device to its ID as above list.
```bash
avdmanager.bat create avd -n pixel_9a -k "system-images;android-34;google_apis;x86_64"
# or using device id from list device
avdmanager.bat create avd -n 56 -k "system-images;android-34;google_apis;x86_64"
```

### Launch emulator

```bash
emulator -avd pixel_9a
```

Reset emulator to clean state if needed:

>Example use case: One UI crashed

```bash
emulator -avd pixel_9a --wipe-data --factory-reset
```

Stop emulator:

```bash
adb emu kill
```

or using power button and off or press [X] button on emulator window.

### Check installed packages

Check created ```installed.txt```
```bash
sdkmanager.bat --list_installed > installed.txt
```

### Remove packages if needed

```bash
sdkmanager.bat --uninstall "build-tools;36.0.0"
```

## Local-only config

Create a local.properties file in the project root. This file is machine-specific and should NOT be committed to Git.

Example, can be used for minimal app build:

```properties
sdk.dir=C:\path\sdk
org.gradle.java.home=C:\path\jdk-17
```

## Gradle / project setup

This project already contains the Android Gradle plugin and Kotlin Android plugin setup in the Gradle files.

The build files are:

- [build.gradle.kts](build.gradle.kts)
- [settings.gradle.kts](settings.gradle.kts)
- [app/build.gradle.kts](app/build.gradle.kts)


## Build commands

From the project root:

```bash
./gradlew.bat clean
./gradlew.bat assembleDebug
```

If you have an emulator running (also if app already launch, no need exit app, it will show updating in app):

```bash
./gradlew.bat installDebug
```

## Good Git habits for this repo

Keep these files local only:

- local.properties
- .idea/
- *.iml
- *.jks
- *.keystore
- build outputs
- .gradle/

This repo already contains an ignore file for the common generated files.

## Notes

- This project is designed for manual build and testing in VS Code.
- Android Studio is not required.
- The Java/Kotlin/Android SDK paths are local machine settings and should be kept out of Git.
- If the Gradle version or Android SDK version is mismatched, the first build will usually fail with a Gradle compatibility error; fix the installed SDK packages and/or wrapper version accordingly.
- Can ask AI to auto rename all related folders/files according to package name wanted.

## Quick start summary

1. Install JDK
2. Install Kotlin compiler
3. Install Android command-line tools
4. Install Android SDK packages
5. Add sdk.dir and JAVA_HOME to local.properties and environment variables
6. Open project in VS Code
7. Run:

```bash
./gradlew.bat assembleDebug
```

8. Install to emulator with:

```bash
./gradlew.bat installDebug
```
