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
$ ./assimp info Assimp\:\:ColladaLoader\:\:CreateMesh_heap-buffer-overflow 

Launching asset import ...           OK
Validating postprocessing flags ...  OK
Segmentation fault (core dumped)
```

Information obtained by using ASAN:

```shell
$ ./Asanassimp info Assimp\:\:ColladaLoader\:\:CreateMesh_heap-buffer-overflow 

Launching asset import ...           OK
Validating postprocessing flags ...  OK
=================================================================
==26710==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x6060000025e0 at pc 0x7ffff5e58b96 bp 0x7fffffffa060 sp 0x7fffffffa050
READ of size 8 at 0x6060000025e0 thread T0
    #0 0x7ffff5e58b95 in Assimp::ColladaLoader::CreateMesh(Assimp::ColladaParser const&, Assimp::Collada::Mesh const*, Assimp::Collada::SubMesh const&, Assimp::Collada::Controller const*, unsigned long, unsigned long) (/root/asan_assimp/bin/libassimp.so.5+0x8f3b95)
    #1 0x7ffff5e54a80 in Assimp::ColladaLoader::BuildMeshesForNode(Assimp::ColladaParser const&, Assimp::Collada::Node const*, aiNode*) (/root/asan_assimp/bin/libassimp.so.5+0x8efa80)
    #2 0x7ffff5e50ef1 in Assimp::ColladaLoader::BuildHierarchy(Assimp::ColladaParser const&, Assimp::Collada::Node const*) (/root/asan_assimp/bin/libassimp.so.5+0x8ebef1)
    #3 0x7ffff5e50c36 in Assimp::ColladaLoader::BuildHierarchy(Assimp::ColladaParser const&, Assimp::Collada::Node const*) (/root/asan_assimp/bin/libassimp.so.5+0x8ebc36)
    #4 0x7ffff5e4f9b8 in Assimp::ColladaLoader::InternReadFile(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, aiScene*, Assimp::IOSystem*) (/root/asan_assimp/bin/libassimp.so.5+0x8ea9b8)
    #5 0x7ffff5aeeb1f in Assimp::BaseImporter::ReadFile(Assimp::Importer*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, Assimp::IOSystem*) (/root/asan_assimp/bin/libassimp.so.5+0x589b1f)
    #6 0x7ffff5b280f8 in Assimp::Importer::ReadFile(char const*, unsigned int) (/root/asan_assimp/bin/libassimp.so.5+0x5c30f8)
    #7 0x555555596937 in Assimp::Importer::ReadFile(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, unsigned int) (/root/assimp-master/bin/Asanassimp+0x42937)
    #8 0x55555559409e in ImportModel(ImportData const&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) (/root/assimp-master/bin/Asanassimp+0x4009e)
    #9 0x55555559f0a7 in Assimp_Info(char const* const*, unsigned int) (/root/assimp-master/bin/Asanassimp+0x4b0a7)
    #10 0x555555593d30 in main (/root/assimp-master/bin/Asanassimp+0x3fd30)
    #11 0x7ffff50140b2 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x270b2)
    #12 0x55555556c35d in _start (/root/assimp-master/bin/Asanassimp+0x1835d)

0x6060000025e0 is located 0 bytes to the right of 64-byte region [0x6060000025a0,0x6060000025e0)
allocated by thread T0 here:
    #0 0x7ffff769d5a7 in operator new(unsigned long) ../../../../src/libsanitizer/asan/asan_new_delete.cpp:99
    #1 0x7ffff5ea140e in __gnu_cxx::new_allocator<__gnu_cxx::__normal_iterator<std::pair<unsigned long, unsigned long> const*, std::vector<std::pair<unsigned long, unsigned long>, std::allocator<std::pair<unsigned long, unsigned long> > > > >::allocate(unsigned long, void const*) (/root/asan_assimp/bin/libassimp.so.5+0x93c40e)
    #2 0x7ffff5e9bcb9 in std::allocator_traits<std::allocator<__gnu_cxx::__normal_iterator<std::pair<unsigned long, unsigned long> const*, std::vector<std::pair<unsigned long, unsigned long>, std::allocator<std::pair<unsigned long, unsigned long> > > > > >::allocate(std::allocator<__gnu_cxx::__normal_iterator<std::pair<unsigned long, unsigned long> const*, std::vector<std::pair<unsigned long, unsigned long>, std::allocator<std::pair<unsigned long, unsigned long> > > > >&, unsigned long) (/root/asan_assimp/bin/libassimp.so.5+0x936cb9)
    #3 0x7ffff5e9280d in std::_Vector_base<__gnu_cxx::__normal_iterator<std::pair<unsigned long, unsigned long> const*, std::vector<std::pair<unsigned long, unsigned long>, std::allocator<std::pair<unsigned long, unsigned long> > > >, std::allocator<__gnu_cxx::__normal_iterator<std::pair<unsigned long, unsigned long> const*, std::vector<std::pair<unsigned long, unsigned long>, std::allocator<std::pair<unsigned long, unsigned long> > > > > >::_M_allocate(unsigned long) (/root/asan_assimp/bin/libassimp.so.5+0x92d80d)
    #4 0x7ffff5e80af1 in std::vector<__gnu_cxx::__normal_iterator<std::pair<unsigned long, unsigned long> const*, std::vector<std::pair<unsigned long, unsigned long>, std::allocator<std::pair<unsigned long, unsigned long> > > >, std::allocator<__gnu_cxx::__normal_iterator<std::pair<unsigned long, unsigned long> const*, std::vector<std::pair<unsigned long, unsigned long>, std::allocator<std::pair<unsigned long, unsigned long> > > > > >::_M_fill_insert(__gnu_cxx::__normal_iterator<__gnu_cxx::__normal_iterator<std::pair<unsigned long, unsigned long> const*, std::vector<std::pair<unsigned long, unsigned long>, std::allocator<std::pair<unsigned long, unsigned long> > > >*, std::vector<__gnu_cxx::__normal_iterator<std::pair<unsigned long, unsigned long> const*, std::vector<std::pair<unsigned long, unsigned long>, std::allocator<std::pair<unsigned long, unsigned long> > > >, std::allocator<__gnu_cxx::__normal_iterator<std::pair<unsigned long, unsigned long> const*, std::vector<std::pair<unsigned long, unsigned long>, std::allocator<std::pair<unsigned long, unsigned long> > > > > > >, unsigned long, __gnu_cxx::__normal_iterator<std::pair<unsigned long, unsigned long> const*, std::vector<std::pair<unsigned long, unsigned long>, std::allocator<std::pair<unsigned long, unsigned long> > > > const&) (/root/asan_assimp/bin/libassimp.so.5+0x91baf1)
    #5 0x7ffff5e6f75b in std::vector<__gnu_cxx::__normal_iterator<std::pair<unsigned long, unsigned long> const*, std::vector<std::pair<unsigned long, unsigned long>, std::allocator<std::pair<unsigned long, unsigned long> > > >, std::allocator<__gnu_cxx::__normal_iterator<std::pair<unsigned long, unsigned long> const*, std::vector<std::pair<unsigned long, unsigned long>, std::allocator<std::pair<unsigned long, unsigned long> > > > > >::resize(unsigned long, __gnu_cxx::__normal_iterator<std::pair<unsigned long, unsigned long> const*, std::vector<std::pair<unsigned long, unsigned long>, std::allocator<std::pair<unsigned long, unsigned long> > > > const&) (/root/asan_assimp/bin/libassimp.so.5+0x90a75b)
    #6 0x7ffff5e58947 in Assimp::ColladaLoader::CreateMesh(Assimp::ColladaParser const&, Assimp::Collada::Mesh const*, Assimp::Collada::SubMesh const&, Assimp::Collada::Controller const*, unsigned long, unsigned long) (/root/asan_assimp/bin/libassimp.so.5+0x8f3947)
    #7 0x7ffff5e54a80 in Assimp::ColladaLoader::BuildMeshesForNode(Assimp::ColladaParser const&, Assimp::Collada::Node const*, aiNode*) (/root/asan_assimp/bin/libassimp.so.5+0x8efa80)
    #8 0x7ffff5e50ef1 in Assimp::ColladaLoader::BuildHierarchy(Assimp::ColladaParser const&, Assimp::Collada::Node const*) (/root/asan_assimp/bin/libassimp.so.5+0x8ebef1)
    #9 0x7ffff5e50c36 in Assimp::ColladaLoader::BuildHierarchy(Assimp::ColladaParser const&, Assimp::Collada::Node const*) (/root/asan_assimp/bin/libassimp.so.5+0x8ebc36)
    #10 0x7ffff5e4f9b8 in Assimp::ColladaLoader::InternReadFile(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, aiScene*, Assimp::IOSystem*) (/root/asan_assimp/bin/libassimp.so.5+0x8ea9b8)
    #11 0x7ffff5aeeb1f in Assimp::BaseImporter::ReadFile(Assimp::Importer*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, Assimp::IOSystem*) (/root/asan_assimp/bin/libassimp.so.5+0x589b1f)
    #12 0x7ffff5b280f8 in Assimp::Importer::ReadFile(char const*, unsigned int) (/root/asan_assimp/bin/libassimp.so.5+0x5c30f8)
    #13 0x555555596937 in Assimp::Importer::ReadFile(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, unsigned int) (/root/assimp-master/bin/Asanassimp+0x42937)
    #14 0x55555559409e in ImportModel(ImportData const&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) (/root/assimp-master/bin/Asanassimp+0x4009e)
    #15 0x55555559f0a7 in Assimp_Info(char const* const*, unsigned int) (/root/assimp-master/bin/Asanassimp+0x4b0a7)
    #16 0x555555593d30 in main (/root/assimp-master/bin/Asanassimp+0x3fd30)
    #17 0x7ffff50140b2 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x270b2)

SUMMARY: AddressSanitizer: heap-buffer-overflow (/root/asan_assimp/bin/libassimp.so.5+0x8f3b95) in Assimp::ColladaLoader::CreateMesh(Assimp::ColladaParser const&, Assimp::Collada::Mesh const*, Assimp::Collada::SubMesh const&, Assimp::Collada::Controller const*, unsigned long, unsigned long)
Shadow bytes around the buggy address:
  0x0c0c7fff8460: fd fd fd fd fd fd fd fd fa fa fa fa 00 00 00 00
  0x0c0c7fff8470: 00 00 00 00 fa fa fa fa fd fd fd fd fd fd fd fd
  0x0c0c7fff8480: fa fa fa fa fd fd fd fd fd fd fd fd fa fa fa fa
  0x0c0c7fff8490: 00 00 00 00 00 00 00 00 fa fa fa fa fd fd fd fd
  0x0c0c7fff84a0: fd fd fd fd fa fa fa fa fd fd fd fd fd fd fd fd
=>0x0c0c7fff84b0: fa fa fa fa 00 00 00 00 00 00 00 00[fa]fa fa fa
  0x0c0c7fff84c0: 00 00 00 00 00 00 00 00 fa fa fa fa fa fa fa fa
  0x0c0c7fff84d0: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c0c7fff84e0: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c0c7fff84f0: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c0c7fff8500: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
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
==26710==ABORTING
```

# Description

```
A heap-buffer-overflow was discovered in assimp. The issue is being triggered in function Assimp::ColladaLoader::CreateMesh(Assimp::ColladaParser const&, Assimp::Collada::Mesh const*, Assimp::Collada::SubMesh const&, Assimp::Collada::Controller const*, unsigned long, unsigned long).
```

# Poc

Poc file is [this](https://github.com/NISL-SecurityGroup/NISL-BugDetection/blob/main/project/packJPG/memory leaks/memory_leaks).
