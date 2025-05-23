# 2024-07-21

`libc++_shared.so` build.

https://developer.android.com/ndk/guides/cpp-support?hl=zh-cn&authuser=1

# 2025-05-21 

## 支持 16 KB 的页面大小 (Support 16 KB page sizes)

https://developer.android.com/16kb-page-size
https://developer.android.com/guide/practices/page-sizes?hl=zh-cn

## 构建支持 16 KB 设备的应用

https://developer.android.com/guide/practices/page-sizes?hl=zh-cn#build

### Android NDK r28 及更高版本

https://developer.android.com/guide/practices/page-sizes?hl=zh-cn#compile-r28

```
NDK 版本 r28 及更高版本默认编译为 16 KB 对齐。
```

### Android NDK r27

https://developer.android.com/guide/practices/page-sizes?hl=zh-cn#compile-r27

```groovy
android {
  ...
  defaultConfig {
    ...
    // This block is different from the one you use to link Gradle
    // to your CMake or ndk-build script.
    externalNativeBuild {
      // For ndk-build, instead use the ndkBuild block.
      cmake {
        // Passes optional arguments to CMake.
        arguments "-DANDROID_SUPPORT_FLEXIBLE_PAGE_SIZES=ON"
      }
    }
  }
}
```

### Android NDK r26 及更低版本

更新 `CMakeLists.txt` 以启用 16 KB ELF 对齐：

```
target_link_options(${CMAKE_PROJECT_NAME} PRIVATE "-Wl,-z,max-page-size=16384")
```

https://developer.android.com/guide/practices/page-sizes?hl=zh-cn#compile-r26-lower

## 检查共享库的 ELF 段对齐情况

https://developer.android.com/guide/practices/page-sizes?hl=zh-cn#elf-alignment

### 使用 `check_elf_alignment.sh` 脚本（Linux 或 macOS）

```
check_elf_alignment.sh `APK_NAME`.apk
```

该脚本会针对所有 `arm64-v8a` 共享库输出 `ALIGNED` 或 `UNALIGNED`。

### 直接使用命令行工具

```
`SDK_ROOT_LOCATION`/Android/sdk/ndk/`NDK_VERSION`/toolchains/llvm/prebuilt/darwin-x86_64/bin/llvm-objdump -p `SHARED_OBJECT_FILE`.so | grep LOAD
```

检查输出行，确保加载分段的值不小于 `2**14`.

# CMake Release Notes

https://cmake.org/cmake/help/latest/release/index.html