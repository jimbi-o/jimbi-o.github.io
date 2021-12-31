---
title: "Agility SDK"
date: 2021-12-29T12:45:12+09:00
categories: graphics
tags: [Agility SDK, d3d12]
---

[作業ディレクトリ](https://github.com/jimbi-o/illuminate)

Enhanced Barriersが使いたいので導入。

## 最新版dxcの入手

[Releases · microsoft/DirectXShaderCompiler](https://github.com/microsoft/DirectXShaderCompiler/releases)から最新版のURLを取得。CMakeからプロジェクトに取り込み。

```cmake
CPMAddPackage(
  NAME dxc
  URL https://github.com/microsoft/DirectXShaderCompiler/releases/download/v1.6.2112/dxc_2021_12_08.zip
  VERSION v1.6.2112
  DOWNLOAD_ONLY yes
)
#...
target_include_directories(${CMAKE_PROJECT_NAME}
  PRIVATE
  "${dxc_SOURCE_DIR}/inc"
)
```

## Agility SDKの組み込み

こちらもcmakeで組み込み。[NuGet Gallery | Microsoft.Direct3D.D3D12 1.700.10-preview](https://www.nuget.org/packages/Microsoft.Direct3D.D3D12/1.700.10-preview)からDownload packageのリンクを取得。バージョン上がったら必要に応じて追随。

```cmake
CPMAddPackage(
  NAME d3d12
  URL https://www.nuget.org/api/v2/package/Microsoft.Direct3D.D3D12/1.700.10-preview
  VERSION v1.700.10-preview
  DOWNLOAD_ONLY yes
)
#...
target_include_directories(${CMAKE_PROJECT_NAME}
  PRIVATE
  "${d3d12_SOURCE_DIR}/build/native/include"
add_custom_command(TARGET ${CMAKE_PROJECT_NAME} POST_BUILD
  COMMAND ${CMAKE_COMMAND} -E make_directory "$<TARGET_FILE_DIR:${CMAKE_PROJECT_NAME}>/d3d12")
add_custom_command(TARGET ${CMAKE_PROJECT_NAME} POST_BUILD
  COMMAND ${CMAKE_COMMAND} -E copy_if_different
  "${d3d12_SOURCE_DIR}/build/native/bin/x64/D3D12Core.dll"
  "$<$<CONFIG:Debug>:${d3d12_SOURCE_DIR}/build/native/bin/x64/D3D12Core.pdb>"
  "$<$<CONFIG:Debug>:${d3d12_SOURCE_DIR}/build/native/bin/x64/d3d12SDKLayers.dll>"
  "$<$<CONFIG:Debug>:${d3d12_SOURCE_DIR}/build/native/bin/x64/d3d12SDKLayers.pdb>"
  "$<TARGET_FILE_DIR:${CMAKE_PROJECT_NAME}>/d3d12")
```

``D3D12SDKVersion``は[DirectX 12 Agility SDK Downloads - DirectX Developer Blog](https://devblogs.microsoft.com/directx/directx12agility/)から確認。

## 参考

* [Getting Started with the Agility SDK - DirectX Developer Blog](https://devblogs.microsoft.com/directx/gettingstarted-dx12agility/)
* [D3D12 Enhanced Barriers Preview - DirectX Developer Blog](https://devblogs.microsoft.com/directx/d3d12-enhanced-barriers-preview/)

## misc.

[``VS_PACKAGE_REFERENCES``はC#でしか使えない](https://stackoverflow.com/questions/18132987/cmake-and-msvs-nuget)ので使えず。

### nuget経由で頑張ってAgility SDKを入れようとした痕跡

#### nuget

##### wsl/ubuntu

``` console
sudo apt install nuget
sudo nuget update -self
```

パッケージが見つからない～でエラーになったので諦めた。

##### Windows

[NuGet Gallery | Downloads](https://www.nuget.org/downloads)から最新のnuget.exeを落とし、親ディレクトリをPATHに含める。※x86しか無い様子

#### Agility SDKの取得

```console
cmd.exe /C nuget.exe install Microsoft.Direct3D.D3D12 -Source https://api.nuget.org/v3/index.json -OutputDirectory agilityd3d12
```

* WSLから``cmd.exe``が動かない問題が発生。[``wsl --shutdown``で解決](https://github.com/microsoft/WSL/issues/4567)。
* WSLのホームディレクトリ以下から実行するとUNCパスはサポートされていませんエラーが発生、Windowsディレクトリ直下にインストールしようとして書き込み権限が無いのでエラーになる。/mnt以下のディレクトリで作業するのが無難。
