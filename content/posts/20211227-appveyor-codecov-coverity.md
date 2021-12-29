---
title: "appveyor + codecov + coverity scan"
date: 2021-12-27T23:30:18+09:00
categories: misc
tags: [appveyor, codecov, coverity, github, blog]
---

[作業レポジトリ](https://github.com/jimbi-o/minimal-cpp-pj)

d3d12がVisual Studioのみなので、travis捨ててappveyorだけあればいいやという動機で着手。

## appveyor + codecov

```
version: 1.0.{build}

image:
- Visual Studio 2019

configuration:
- Debug
- Release

install:
- choco install opencppcoverage codecov
- set PATH=C:\Program Files\OpenCppCoverage;%PATH%

before_build:
- cmd:
    cmake -S . -B build -DBUILD_WITH_TEST=ON

build_script:
- cmake --build build --config %configuration%

for:
-
  matrix:
    only:
    - configuration: Debug
  after_build:
  - cd build
  - OpenCppCoverage.exe --sources src --sources tests --export_type cobertura:coverage.xml --modules "*.exe" --cover_children -- %configuration%/illuminate.exe -C %configuration% --output-on-failure
  - codecov -f coverage.xml --root %APPVEYOR_BUILD_FOLDER%
```

``OpenCppCoverage.exe``の``.exe``が無いと`-C`なんて無いよエラーが発生。

[参考](https://github.com/codecov/example-cpp11-cmake/blob/master/.appveyor.yml)

## appveyor + coverity scan

上のcodecovに加えて、Coverity Scanを実行。travisに合わせて``coverity_scan``ブランチへのpush時のみ、Coverity Scanを実行。

```
version: 1.0.{build}

environment:
  coverity_token:
    secure: b678Xt/k3QTlZPnJUI9IIuiWlG/Xbj/4RXJIHKf5Crg=
  coverity_email:
    secure: WrY/mxjEAqdbR41Nekols352OwgNL4ZhbsDOkuJdcTQ=

image:
- Visual Studio 2019

configuration:
- Debug
- Release

before_build:
- cmake -S . -B build -DBUILD_WITH_TEST=ON

build_script:
- cmake --build build --config %configuration%

for:
-
  branches:
    only:
      - coverity_scan
  configuration: Debug
  build_script:
  - cov-build --dir cov-int cmake --build build --config %configuration%
  - 7z a cov-int.zip cov-int
  - curl --form token=%coverity_token% --form email=%coverity_email% --form file=@cov-int.zip --form version="%APPVEYOR_BUILD_VERSION%" --form description="%APPVEYOR_BUILD_VERSION%" https://scan.coverity.com/builds?project=%APPVEYOR_REPO_NAME%
-
  matrix:
    only:
    - configuration: Debug
  install:
  - choco install opencppcoverage codecov
  - set PATH=C:\Program Files\OpenCppCoverage;%PATH%
  after_build:
  - cd build
  - OpenCppCoverage.exe --sources src --sources tests --export_type cobertura:coverage.xml --modules "*.exe" --cover_children -- %configuration%/illuminate.exe -C %configuration% --output-on-failure
  - codecov -f coverage.xml --root %APPVEYOR_BUILD_FOLDER%
```

``IF``をネストすると``()``が構文エラーを起こすらしいので、``for``で回避。

``85% capture success``に達してないので解析してもらえないけど、とりあえずCoverity Scanのサーバに投げるとこまで動いた。

[参考](https://thehermeticvault.com/software-development/using-coverity-scan-with-appveyor)
