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
[fvm install doc](https://fvm.app/docs/getting_started/installation)

```shell
$ brew tap leoafarias/fvm
$ brew install fvm

$ fvm releases
$ fvm install 3.16.3 (flutter stable 최신 버전으로 다운)

$ fvm global 3.16.3 # global version 설정
$ cd [Android_studio_project_path]
$ fvm use 3.16.3 # 특정 flutter 프로젝트에서 사용할 버전 설정 가능(local)
```