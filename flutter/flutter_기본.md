# flutter 개발환경 구성

- [flutter doc install](https://docs.flutter.dev/get-started/install)
  - intel zip 파일 download
```shell
$ export PATH="$PATH:`pwd`/flutter/bin"
```

- flutter 환경 체크

```shell
$ flutter doctor
```

- Android Studio 설치
  - flutter plugin 설치
- XCode 설치

```shell
$ brew install rbenv

$ which gem

$ rbenv install -l
$ rbenv install 3.2.2
$ vim ~/.zshrc

eval "$(rbenv init - zsh)"
$ which gem
/Users/beaniejoy/.rbenv/shims/gem

$ sudo gem install cocoapods
```

- Android Studio에서 SDK 설치
  - Settings > Language & Frameworks > Android SDK
  - 위 메뉴에서 Android SDK Command-line Tools 다운
- android-licenses 동의 필요

```shell
$ flutter doctor --android-licenses
```

<br>

## FVM

flutter version manager  
[fvm install doc](https://fvm.app/documentation/getting-started/installation)

```shell
$ brew tap leoafarias/fvm
$ brew install fvm

$ fvm releases
$ fvm install 3.16.3 (flutter stable 최신 버전으로 다운)

$ fvm global 3.16.3 # global version 설정
$ cd [Android_studio_project_path]
$ fvm use 3.16.3 # 특정 flutter 프로젝트에서 사용할 버전 설정 가능(local)
```
fvm으로 flutter 명령어 실행하기

```shell
$ fvm flutter --version
$ fvm flutter create [app-name]
```

<br>

## Flutter 패키지 추가

```shell
$ fvm flutter pub add [package name]
```
터미널에서 위에 내용대로 실행하는 방법도 있고 `pubspec.yaml`에 명시적으로 추가하는 방법이 있음

<br>

## Flutter build runner

```shell
flutter pub run build_runner build
```

<br>

# flutter doctor 오류 항목

```
[!] Android toolchain - develop for Android devices (Android SDK version 34.0.0)
  • Android SDK at /Users/beanie.joy/Library/Android/sdk
  ✗ cmdline-tools component is missing
    Run `path/to/sdkmanager --install "cmdline-tools;latest"`
    See https://developer.android.com/studio/command-line for more details.
```
Android Studio 들어가서 `Android SDK` > `SDK Tools` > `Android SDK Command-line Tools` 설치되어 있는지 체크