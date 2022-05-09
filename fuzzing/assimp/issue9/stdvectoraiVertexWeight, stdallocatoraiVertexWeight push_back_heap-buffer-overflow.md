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
$ ./assimp info std\:\:vector\<aiVertexWeight\,\ std\:\:allocator\<aiVertexWeight\>\ \>\:\:push_back_heap-buffer-overflow 

Launching asset import ...           OK
Validating postprocessing flags ...  OK
Segmentation fault (core dumped)

```

Information obtained by using ASAN:

```shell
$ ./Asanassimp info std\:\:vector\<aiVertexWeight\,\ std\:\:allocator\<aiVertexWeight\>\ \>\:\:push_back_heap-buffer-overflow 

Launching asset import ...           OK
Validating postprocessing flags ...  OK
=================================================================
==1128==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x604000000448 at pc 0x7ffff5c2dacd bp 0x7fffffffa020 sp 0x7fffffffa010
READ of size 8 at 0x604000000448 thread T0
    #0 0x7ffff5c2dacc in std::vector<aiVertexWeight, std::allocator<aiVertexWeight> >::push_back(aiVertexWeight const&) (/root/asan_assimp/bin/libassimp.so.5+0x6c8acc)
    #1 0x7ffff5e58ddb in Assimp::ColladaLoader::CreateMesh(Assimp::ColladaParser const&, Assimp::Collada::Mesh const*, Assimp::Collada::SubMesh const&, Assimp::Collada::Controller const*, unsigned long, unsigned long) (/root/asan_assimp/bin/libassimp.so.5+0x8f3ddb)
    #2 0x7ffff5e54a80 in Assimp::ColladaLoader::BuildMeshesForNode(Assimp::ColladaParser const&, Assimp::Collada::Node const*, aiNode*) (/root/asan_assimp/bin/libassimp.so.5+0x8efa80)
    #3 0x7ffff5e50ef1 in Assimp::ColladaLoader::BuildHierarchy(Assimp::ColladaParser const&, Assimp::Collada::Node const*) (/root/asan_assimp/bin/libassimp.so.5+0x8ebef1)
    #4 0x7ffff5e50c36 in Assimp::ColladaLoader::BuildHierarchy(Assimp::ColladaParser const&, Assimp::Collada::Node const*) (/root/asan_assimp/bin/libassimp.so.5+0x8ebc36)
    #5 0x7ffff5e4f9b8 in Assimp::ColladaLoader::InternReadFile(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, aiScene*, Assimp::IOSystem*) (/root/asan_assimp/bin/libassimp.so.5+0x8ea9b8)
    #6 0x7ffff5aeeb1f in Assimp::BaseImporter::ReadFile(Assimp::Importer*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, Assimp::IOSystem*) (/root/asan_assimp/bin/libassimp.so.5+0x589b1f)
    #7 0x7ffff5b280f8 in Assimp::Importer::ReadFile(char const*, unsigned int) (/root/asan_assimp/bin/libassimp.so.5+0x5c30f8)
    #8 0x555555596937 in Assimp::Importer::ReadFile(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, unsigned int) (/root/assimp-master/bin/Asanassimp+0x42937)
    #9 0x55555559409e in ImportModel(ImportData const&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) (/root/assimp-master/bin/Asanassimp+0x4009e)
    #10 0x55555559f0a7 in Assimp_Info(char const* const*, unsigned int) (/root/assimp-master/bin/Asanassimp+0x4b0a7)
    #11 0x555555593d30 in main (/root/assimp-master/bin/Asanassimp+0x3fd30)
    #12 0x7ffff50140b2 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x270b2)
    #13 0x55555556c35d in _start (/root/assimp-master/bin/Asanassimp+0x1835d)

0x604000000448 is located 8 bytes to the right of 48-byte region [0x604000000410,0x604000000440)
allocated by thread T0 here:
    #0 0x7ffff769d5a7 in operator new(unsigned long) ../../../../src/libsanitizer/asan/asan_new_delete.cpp:99
    #1 0x7ffff5c4e309 in __gnu_cxx::new_allocator<std::vector<aiVertexWeight, std::allocator<aiVertexWeight> > >::allocate(unsigned long, void const*) (/root/asan_assimp/bin/libassimp.so.5+0x6e9309)
    #2 0x7ffff5c4e1d3 in std::allocator_traits<std::allocator<std::vector<aiVertexWeight, std::allocator<aiVertexWeight> > > >::allocate(std::allocator<std::vector<aiVertexWeight, std::allocator<aiVertexWeight> > >&, unsigned long) (/root/asan_assimp/bin/libassimp.so.5+0x6e91d3)
    #3 0x7ffff5c4e091 in std::_Vector_base<std::vector<aiVertexWeight, std::allocator<aiVertexWeight> >, std::allocator<std::vector<aiVertexWeight, std::allocator<aiVertexWeight> > > >::_M_allocate(unsigned long) (/root/asan_assimp/bin/libassimp.so.5+0x6e9091)
    #4 0x7ffff5c4d714 in std::_Vector_base<std::vector<aiVertexWeight, std::allocator<aiVertexWeight> >, std::allocator<std::vector<aiVertexWeight, std::allocator<aiVertexWeight> > > >::_M_create_storage(unsigned long) (/root/asan_assimp/bin/libassimp.so.5+0x6e8714)
    #5 0x7ffff5c4cd80 in std::_Vector_base<std::vector<aiVertexWeight, std::allocator<aiVertexWeight> >, std::allocator<std::vector<aiVertexWeight, std::allocator<aiVertexWeight> > > >::_Vector_base(unsigned long, std::allocator<std::vector<aiVertexWeight, std::allocator<aiVertexWeight> > > const&) (/root/asan_assimp/bin/libassimp.so.5+0x6e7d80)
    #6 0x7ffff5c4c22c in std::vector<std::vector<aiVertexWeight, std::allocator<aiVertexWeight> >, std::allocator<std::vector<aiVertexWeight, std::allocator<aiVertexWeight> > > >::vector(unsigned long, std::allocator<std::vector<aiVertexWeight, std::allocator<aiVertexWeight> > > const&) (/root/asan_assimp/bin/libassimp.so.5+0x6e722c)
    #7 0x7ffff5e58875 in Assimp::ColladaLoader::CreateMesh(Assimp::ColladaParser const&, Assimp::Collada::Mesh const*, Assimp::Collada::SubMesh const&, Assimp::Collada::Controller const*, unsigned long, unsigned long) (/root/asan_assimp/bin/libassimp.so.5+0x8f3875)
    #8 0x7ffff5e54a80 in Assimp::ColladaLoader::BuildMeshesForNode(Assimp::ColladaParser const&, Assimp::Collada::Node const*, aiNode*) (/root/asan_assimp/bin/libassimp.so.5+0x8efa80)
    #9 0x7ffff5e50ef1 in Assimp::ColladaLoader::BuildHierarchy(Assimp::ColladaParser const&, Assimp::Collada::Node const*) (/root/asan_assimp/bin/libassimp.so.5+0x8ebef1)
    #10 0x7ffff5e50c36 in Assimp::ColladaLoader::BuildHierarchy(Assimp::ColladaParser const&, Assimp::Collada::Node const*) (/root/asan_assimp/bin/libassimp.so.5+0x8ebc36)
    #11 0x7ffff5e4f9b8 in Assimp::ColladaLoader::InternReadFile(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, aiScene*, Assimp::IOSystem*) (/root/asan_assimp/bin/libassimp.so.5+0x8ea9b8)
    #12 0x7ffff5aeeb1f in Assimp::BaseImporter::ReadFile(Assimp::Importer*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, Assimp::IOSystem*) (/root/asan_assimp/bin/libassimp.so.5+0x589b1f)
    #13 0x7ffff5b280f8 in Assimp::Importer::ReadFile(char const*, unsigned int) (/root/asan_assimp/bin/libassimp.so.5+0x5c30f8)
    #14 0x555555596937 in Assimp::Importer::ReadFile(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, unsigned int) (/root/assimp-master/bin/Asanassimp+0x42937)
    #15 0x55555559409e in ImportModel(ImportData const&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) (/root/assimp-master/bin/Asanassimp+0x4009e)
    #16 0x55555559f0a7 in Assimp_Info(char const* const*, unsigned int) (/root/assimp-master/bin/Asanassimp+0x4b0a7)
    #17 0x555555593d30 in main (/root/assimp-master/bin/Asanassimp+0x3fd30)
    #18 0x7ffff50140b2 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x270b2)

SUMMARY: AddressSanitizer: heap-buffer-overflow (/root/asan_assimp/bin/libassimp.so.5+0x6c8acc) in std::vector<aiVertexWeight, std::allocator<aiVertexWeight> >::push_back(aiVertexWeight const&)
Shadow bytes around the buggy address:
  0x0c087fff8030: fa fa 00 00 00 00 00 fa fa fa fd fd fd fd fd fa
  0x0c087fff8040: fa fa fd fd fd fd fd fa fa fa fd fd fd fd fd fd
  0x0c087fff8050: fa fa fd fd fd fd fd fd fa fa 00 00 00 00 00 fa
  0x0c087fff8060: fa fa fd fd fd fd fd fa fa fa fd fd fd fd fd fa
  0x0c087fff8070: fa fa fd fd fd fd fd fa fa fa 00 00 00 00 00 fa
=>0x0c087fff8080: fa fa 00 00 00 00 00 00 fa[fa]fa fa fa fa fa fa
  0x0c087fff8090: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c087fff80a0: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c087fff80b0: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c087fff80c0: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c087fff80d0: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
Shadow byte legend (one shadow byte represents 8 application bytes):
  Addressable:           00
  Partially addressable: 01 02 03 04 05 06 07 
  Heap left redzone:       fa
  Freed heap region:       fd
  Stack left redzone:      f1
  Stack mid redzone:       f2
  Stack right redzone:     f3
  Stack after return:      f5
  Stack use after scope:   f8
  Global redzone:          f9
  Global init order:       f6
  Poisoned by user:        f7
  Container overflow:      fc
  Array cookie:            ac
  Intra object redzone:    bb
  ASan internal:           fe
  Left alloca redzone:     ca
  Right alloca redzone:    cb
  Shadow gap:              cc
==1128==ABORTING
```

# Description

```
A heap-buffer-overflow was discovered in assimp. The issue is being triggered in function std::vector<aiVertexWeight, std::allocator<aiVertexWeight> >::push_back(aiVertexWeight const&).
```

# Poc

Poc file is [this](https://github.com/NISL-SecurityGroup/NISL-BugDetection/blob/main/project/assimp/heap-buffer-overflow-1/Assimp__ColladaLoader__CreateMesh_heap-buffer-overflow).
