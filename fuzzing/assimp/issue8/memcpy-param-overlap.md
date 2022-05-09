# Environment

Ubuntu 18.04，64 bit

# Command

**Compile test program:**

```shell
$ cmake CMakeLists.txt
$ cmake --build .
```

**Compile test program with address sanitizer:**

- Update Makefile:

```cmake
SET (CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fsanitize=address -fno-omit-frame-pointer")
SET (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fsanitize=address -fno-omit-frame-pointer ")
SET (CMAKE_LINKER_FLAGS "${CMAKE_LINKER_FLAGS} -fsanitize=address  -lasan -lstdc++ ")
SET (CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -fsanitize=address  -lasan -lstdc++ ")
```

- Compile program:

```shell
$ mkdir asan_assimp
$ cd asan_assimp
$ export CC=/usr/bin/gcc
$ export CXX=/usr/bin/g++
$ cmake CMakeLists.txt
$ cmake --build .
```

# Result

The result of running without ASAN:

```shell
$ ./assimp info memcpy-param-overlap 

Launching asset import ...           OK
Validating postprocessing flags ...  OK
ai_assert failure in /root/cmake_assimp/code/Common/ZipArchiveIOSystem.cpp(244): unzCloseCurrentFile(zip_handle) == UNZ_OK
Aborted (core dumped)
```

Information obtained by using ASAN:

```shell
$ ./Asanassimp info memcpy-param-overlap

Launching asset import ...           OK
Validating postprocessing flags ...  OK
=================================================================
==21537==ERROR: AddressSanitizer: memcpy-param-overlap: memory ranges [0x631000014800,0x63100002474b) and [0x6310000148b4, 0x6310000247ff) overlap
    #0 0x7ffff76211fd in __interceptor_memcpy ../../../../src/libsanitizer/sanitizer_common/sanitizer_common_interceptors.inc:827
    #1 0x7ffff6d16951 in unzReadCurrentFile (/root/asan_assimp/bin/libassimp.so.5+0x17b1951)
    #2 0x7ffff5b1d213 in Assimp::ZipFileInfo::Extract(void*) const (/root/asan_assimp/bin/libassimp.so.5+0x5b8213)
    #3 0x7ffff5b1ef60 in Assimp::ZipArchiveIOSystem::Implement::OpenFile(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&) (/root/asan_assimp/bin/libassimp.so.5+0x5b9f60)
    #4 0x7ffff5b1fa91 in Assimp::ZipArchiveIOSystem::Open(char const*, char const*) (/root/asan_assimp/bin/libassimp.so.5+0x5baa91)
    #5 0x7ffff6aef2bb in Assimp::D3MF::D3MFOpcPackage::D3MFOpcPackage(Assimp::IOSystem*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) (/root/asan_assimp/bin/libassimp.so.5+0x158a2bb)
    #6 0x7ffff6aee416 in Assimp::D3MFImporter::CanRead(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, Assimp::IOSystem*, bool) const (/root/asan_assimp/bin/libassimp.so.5+0x1589416)
    #7 0x7ffff5b2758b in Assimp::Importer::ReadFile(char const*, unsigned int) (/root/asan_assimp/bin/libassimp.so.5+0x5c258b)
    #8 0x555555596937 in Assimp::Importer::ReadFile(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, unsigned int) (/root/assimp-master/bin/Asanassimp+0x42937)
    #9 0x55555559409e in ImportModel(ImportData const&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) (/root/assimp-master/bin/Asanassimp+0x4009e)
    #10 0x55555559f0a7 in Assimp_Info(char const* const*, unsigned int) (/root/assimp-master/bin/Asanassimp+0x4b0a7)
    #11 0x555555593d30 in main (/root/assimp-master/bin/Asanassimp+0x3fd30)
    #12 0x7ffff50140b2 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x270b2)
    #13 0x55555556c35d in _start (/root/assimp-master/bin/Asanassimp+0x1835d)

0x631000014800 is located 0 bytes inside of 65535-byte region [0x631000014800,0x6310000247ff)
allocated by thread T0 here:
    #0 0x7ffff769bc47 in __interceptor_malloc ../../../../src/libsanitizer/asan/asan_malloc_linux.cpp:145
    #1 0x7ffff6d14fb0 in unzOpenCurrentFile3 (/root/asan_assimp/bin/libassimp.so.5+0x17affb0)
    #2 0x7ffff6d162e2 in unzOpenCurrentFile (/root/asan_assimp/bin/libassimp.so.5+0x17b12e2)
    #3 0x7ffff5b1d093 in Assimp::ZipFileInfo::Extract(void*) const (/root/asan_assimp/bin/libassimp.so.5+0x5b8093)
    #4 0x7ffff5b1ef60 in Assimp::ZipArchiveIOSystem::Implement::OpenFile(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&) (/root/asan_assimp/bin/libassimp.so.5+0x5b9f60)
    #5 0x7ffff5b1fa91 in Assimp::ZipArchiveIOSystem::Open(char const*, char const*) (/root/asan_assimp/bin/libassimp.so.5+0x5baa91)
    #6 0x7ffff6aef2bb in Assimp::D3MF::D3MFOpcPackage::D3MFOpcPackage(Assimp::IOSystem*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) (/root/asan_assimp/bin/libassimp.so.5+0x158a2bb)
    #7 0x7ffff6aee416 in Assimp::D3MFImporter::CanRead(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, Assimp::IOSystem*, bool) const (/root/asan_assimp/bin/libassimp.so.5+0x1589416)
    #8 0x7ffff5b2758b in Assimp::Importer::ReadFile(char const*, unsigned int) (/root/asan_assimp/bin/libassimp.so.5+0x5c258b)
    #9 0x555555596937 in Assimp::Importer::ReadFile(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, unsigned int) (/root/assimp-master/bin/Asanassimp+0x42937)
    #10 0x55555559409e in ImportModel(ImportData const&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) (/root/assimp-master/bin/Asanassimp+0x4009e)
    #11 0x55555559f0a7 in Assimp_Info(char const* const*, unsigned int) (/root/assimp-master/bin/Asanassimp+0x4b0a7)
    #12 0x555555593d30 in main (/root/assimp-master/bin/Asanassimp+0x3fd30)
    #13 0x7ffff50140b2 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x270b2)

0x6310000148b4 is located 180 bytes inside of 65535-byte region [0x631000014800,0x6310000247ff)
allocated by thread T0 here:
    #0 0x7ffff769bc47 in __interceptor_malloc ../../../../src/libsanitizer/asan/asan_malloc_linux.cpp:145
    #1 0x7ffff6d14fb0 in unzOpenCurrentFile3 (/root/asan_assimp/bin/libassimp.so.5+0x17affb0)
    #2 0x7ffff6d162e2 in unzOpenCurrentFile (/root/asan_assimp/bin/libassimp.so.5+0x17b12e2)
    #3 0x7ffff5b1d093 in Assimp::ZipFileInfo::Extract(void*) const (/root/asan_assimp/bin/libassimp.so.5+0x5b8093)
    #4 0x7ffff5b1ef60 in Assimp::ZipArchiveIOSystem::Implement::OpenFile(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&) (/root/asan_assimp/bin/libassimp.so.5+0x5b9f60)
    #5 0x7ffff5b1fa91 in Assimp::ZipArchiveIOSystem::Open(char const*, char const*) (/root/asan_assimp/bin/libassimp.so.5+0x5baa91)
    #6 0x7ffff6aef2bb in Assimp::D3MF::D3MFOpcPackage::D3MFOpcPackage(Assimp::IOSystem*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) (/root/asan_assimp/bin/libassimp.so.5+0x158a2bb)
    #7 0x7ffff6aee416 in Assimp::D3MFImporter::CanRead(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, Assimp::IOSystem*, bool) const (/root/asan_assimp/bin/libassimp.so.5+0x1589416)
    #8 0x7ffff5b2758b in Assimp::Importer::ReadFile(char const*, unsigned int) (/root/asan_assimp/bin/libassimp.so.5+0x5c258b)
    #9 0x555555596937 in Assimp::Importer::ReadFile(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, unsigned int) (/root/assimp-master/bin/Asanassimp+0x42937)
    #10 0x55555559409e in ImportModel(ImportData const&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) (/root/assimp-master/bin/Asanassimp+0x4009e)
    #11 0x55555559f0a7 in Assimp_Info(char const* const*, unsigned int) (/root/assimp-master/bin/Asanassimp+0x4b0a7)
    #12 0x555555593d30 in main (/root/assimp-master/bin/Asanassimp+0x3fd30)
    #13 0x7ffff50140b2 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x270b2)

SUMMARY: AddressSanitizer: memcpy-param-overlap ../../../../src/libsanitizer/sanitizer_common/sanitizer_common_interceptors.inc:827 in __interceptor_memcpy
==21537==ABORTING
```

# Description

```
A memcpy-param-overlap was discovered in assimp. at ../../../../src/libsanitizer/sanitizer_common/sanitizer_common_interceptors.inc:827 in __interceptor_memcpy.
```

# Poc

Poc file is [this](https://github.com/NISL-SecurityGroup/NISL-BugDetection/blob/main/project/packJPG/memory leaks/memory_leaks).
