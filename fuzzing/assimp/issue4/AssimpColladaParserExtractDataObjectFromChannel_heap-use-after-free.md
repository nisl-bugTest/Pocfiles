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
$ ./assimp info Assimp\:\:ColladaParser\:\:ExtractDataObjectFromChannel_heap-use-after-free  

Launching asset import ...           OK
Validating postprocessing flags ...  OK
Segmentation fault (core dumped)
```

Information obtained by using ASAN:

```shell
$ ./Asanassimp info Assimp\:\:ColladaParser\:\:ExtractDataObjectFromChannel_heap-use-after-free  

Launching asset import ...           OK
Validating postprocessing flags ...  OK
=================================================================
==19874==ERROR: AddressSanitizer: heap-use-after-free on address 0x602000000230 at pc 0x7ffff5ec697a bp 0x7fffffffaa40 sp 0x7fffffffaa30
READ of size 4 at 0x602000000230 thread T0
    #0 0x7ffff5ec6979 in Assimp::ColladaParser::ExtractDataObjectFromChannel(Assimp::Collada::InputChannel const&, unsigned long, Assimp::Collada::Mesh&) (/root/asan_assimp/bin/libassimp.so.5+0x961979)
    #1 0x7ffff5ec611b in Assimp::ColladaParser::CopyVertex(unsigned long, unsigned long, unsigned long, unsigned long, Assimp::Collada::Mesh&, std::vector<Assimp::Collada::InputChannel, std::allocator<Assimp::Collada::InputChannel> >&, unsigned long, std::vector<unsigned long, std::allocator<unsigned long> > const&) (/root/asan_assimp/bin/libassimp.so.5+0x96111b)
    #2 0x7ffff5ec5a35 in Assimp::ColladaParser::ReadPrimitives(pugi::xml_node&, Assimp::Collada::Mesh&, std::vector<Assimp::Collada::InputChannel, std::allocator<Assimp::Collada::InputChannel> >&, unsigned long, std::vector<unsigned long, std::allocator<unsigned long> > const&, Assimp::Collada::PrimitiveType) (/root/asan_assimp/bin/libassimp.so.5+0x960a35)
    #3 0x7ffff5ec3949 in Assimp::ColladaParser::ReadIndexData(pugi::xml_node&, Assimp::Collada::Mesh&) (/root/asan_assimp/bin/libassimp.so.5+0x95e949)
    #4 0x7ffff5ec0831 in Assimp::ColladaParser::ReadMesh(pugi::xml_node&, Assimp::Collada::Mesh&) (/root/asan_assimp/bin/libassimp.so.5+0x95b831)
    #5 0x7ffff5ec03d3 in Assimp::ColladaParser::ReadGeometry(pugi::xml_node&, Assimp::Collada::Mesh&) (/root/asan_assimp/bin/libassimp.so.5+0x95b3d3)
    #6 0x7ffff5ebfd2e in Assimp::ColladaParser::ReadGeometryLibrary(pugi::xml_node&) (/root/asan_assimp/bin/libassimp.so.5+0x95ad2e)
    #7 0x7ffff5eb00a0 in Assimp::ColladaParser::ReadStructure(pugi::xml_node&) (/root/asan_assimp/bin/libassimp.so.5+0x94b0a0)
    #8 0x7ffff5eafae1 in Assimp::ColladaParser::ReadContents(pugi::xml_node&) (/root/asan_assimp/bin/libassimp.so.5+0x94aae1)
    #9 0x7ffff5eadb28 in Assimp::ColladaParser::ColladaParser(Assimp::IOSystem*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) (/root/asan_assimp/bin/libassimp.so.5+0x948b28)
    #10 0x7ffff5e4f81d in Assimp::ColladaLoader::InternReadFile(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, aiScene*, Assimp::IOSystem*) (/root/asan_assimp/bin/libassimp.so.5+0x8ea81d)
    #11 0x7ffff5aeeb1f in Assimp::BaseImporter::ReadFile(Assimp::Importer*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, Assimp::IOSystem*) (/root/asan_assimp/bin/libassimp.so.5+0x589b1f)
    #12 0x7ffff5b280f8 in Assimp::Importer::ReadFile(char const*, unsigned int) (/root/asan_assimp/bin/libassimp.so.5+0x5c30f8)
    #13 0x555555596937 in Assimp::Importer::ReadFile(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, unsigned int) (/root/assimp-master/bin/Asanassimp+0x42937)
    #14 0x55555559409e in ImportModel(ImportData const&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) (/root/assimp-master/bin/Asanassimp+0x4009e)
    #15 0x55555559f0a7 in Assimp_Info(char const* const*, unsigned int) (/root/assimp-master/bin/Asanassimp+0x4b0a7)
    #16 0x555555593d30 in main (/root/assimp-master/bin/Asanassimp+0x3fd30)
    #17 0x7ffff50140b2 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x270b2)
    #18 0x55555556c35d in _start (/root/assimp-master/bin/Asanassimp+0x1835d)

0x602000000230 is located 0 bytes inside of 8-byte region [0x602000000230,0x602000000238)
freed by thread T0 here:
    #0 0x7ffff769e0c7 in operator delete(void*) ../../../../src/libsanitizer/asan/asan_new_delete.cpp:160
    #1 0x7ffff5f12e93 in __gnu_cxx::new_allocator<pugi::xml_node>::deallocate(pugi::xml_node*, unsigned long) (/root/asan_assimp/bin/libassimp.so.5+0x9ade93)
    #2 0x7ffff5ef8f21 in std::allocator_traits<std::allocator<pugi::xml_node> >::deallocate(std::allocator<pugi::xml_node>&, pugi::xml_node*, unsigned long) (/root/asan_assimp/bin/libassimp.so.5+0x993f21)
    #3 0x7ffff5ee4c6f in std::_Vector_base<pugi::xml_node, std::allocator<pugi::xml_node> >::_M_deallocate(pugi::xml_node*, unsigned long) (/root/asan_assimp/bin/libassimp.so.5+0x97fc6f)
    #4 0x7ffff5ee5369 in void std::vector<pugi::xml_node, std::allocator<pugi::xml_node> >::_M_realloc_insert<pugi::xml_node const&>(__gnu_cxx::__normal_iterator<pugi::xml_node*, std::vector<pugi::xml_node, std::allocator<pugi::xml_node> > >, pugi::xml_node const&) (/root/asan_assimp/bin/libassimp.so.5+0x980369)
    #5 0x7ffff5ed6e2e in std::vector<pugi::xml_node, std::allocator<pugi::xml_node> >::push_back(pugi::xml_node const&) (/root/asan_assimp/bin/libassimp.so.5+0x971e2e)
    #6 0x7ffff5ed0ec0 in Assimp::XmlNodeIterator::collectChildrenPreOrder(pugi::xml_node&) (/root/asan_assimp/bin/libassimp.so.5+0x96bec0)
    #7 0x7ffff5ed1054 in Assimp::XmlNodeIterator::collectChildrenPreOrder(pugi::xml_node&) (/root/asan_assimp/bin/libassimp.so.5+0x96c054)
    #8 0x7ffff5ed0d12 in Assimp::XmlNodeIterator::XmlNodeIterator(pugi::xml_node&, Assimp::XmlNodeIterator::IterationMode) (/root/asan_assimp/bin/libassimp.so.5+0x96bd12)
    #9 0x7ffff5ec0af6 in Assimp::ColladaParser::ReadSource(pugi::xml_node&) (/root/asan_assimp/bin/libassimp.so.5+0x95baf6)
    #10 0x7ffff5ec06ff in Assimp::ColladaParser::ReadMesh(pugi::xml_node&, Assimp::Collada::Mesh&) (/root/asan_assimp/bin/libassimp.so.5+0x95b6ff)
    #11 0x7ffff5ec03d3 in Assimp::ColladaParser::ReadGeometry(pugi::xml_node&, Assimp::Collada::Mesh&) (/root/asan_assimp/bin/libassimp.so.5+0x95b3d3)
    #12 0x7ffff5ebfd2e in Assimp::ColladaParser::ReadGeometryLibrary(pugi::xml_node&) (/root/asan_assimp/bin/libassimp.so.5+0x95ad2e)
    #13 0x7ffff5eb00a0 in Assimp::ColladaParser::ReadStructure(pugi::xml_node&) (/root/asan_assimp/bin/libassimp.so.5+0x94b0a0)
    #14 0x7ffff5eafae1 in Assimp::ColladaParser::ReadContents(pugi::xml_node&) (/root/asan_assimp/bin/libassimp.so.5+0x94aae1)
    #15 0x7ffff5eadb28 in Assimp::ColladaParser::ColladaParser(Assimp::IOSystem*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) (/root/asan_assimp/bin/libassimp.so.5+0x948b28)
    #16 0x7ffff5e4f81d in Assimp::ColladaLoader::InternReadFile(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, aiScene*, Assimp::IOSystem*) (/root/asan_assimp/bin/libassimp.so.5+0x8ea81d)
    #17 0x7ffff5aeeb1f in Assimp::BaseImporter::ReadFile(Assimp::Importer*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, Assimp::IOSystem*) (/root/asan_assimp/bin/libassimp.so.5+0x589b1f)
    #18 0x7ffff5b280f8 in Assimp::Importer::ReadFile(char const*, unsigned int) (/root/asan_assimp/bin/libassimp.so.5+0x5c30f8)
    #19 0x555555596937 in Assimp::Importer::ReadFile(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, unsigned int) (/root/assimp-master/bin/Asanassimp+0x42937)
    #20 0x55555559409e in ImportModel(ImportData const&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) (/root/assimp-master/bin/Asanassimp+0x4009e)
    #21 0x55555559f0a7 in Assimp_Info(char const* const*, unsigned int) (/root/assimp-master/bin/Asanassimp+0x4b0a7)
    #22 0x555555593d30 in main (/root/assimp-master/bin/Asanassimp+0x3fd30)
    #23 0x7ffff50140b2 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x270b2)

previously allocated by thread T0 here:
    #0 0x7ffff769d5a7 in operator new(unsigned long) ../../../../src/libsanitizer/asan/asan_new_delete.cpp:99
    #1 0x7ffff5f24b56 in __gnu_cxx::new_allocator<pugi::xml_node>::allocate(unsigned long, void const*) (/root/asan_assimp/bin/libassimp.so.5+0x9bfb56)
    #2 0x7ffff5f12efb in std::allocator_traits<std::allocator<pugi::xml_node> >::allocate(std::allocator<pugi::xml_node>&, unsigned long) (/root/asan_assimp/bin/libassimp.so.5+0x9adefb)
    #3 0x7ffff5ef9487 in std::_Vector_base<pugi::xml_node, std::allocator<pugi::xml_node> >::_M_allocate(unsigned long) (/root/asan_assimp/bin/libassimp.so.5+0x994487)
    #4 0x7ffff5ee50d4 in void std::vector<pugi::xml_node, std::allocator<pugi::xml_node> >::_M_realloc_insert<pugi::xml_node const&>(__gnu_cxx::__normal_iterator<pugi::xml_node*, std::vector<pugi::xml_node, std::allocator<pugi::xml_node> > >, pugi::xml_node const&) (/root/asan_assimp/bin/libassimp.so.5+0x9800d4)
    #5 0x7ffff5ed6e2e in std::vector<pugi::xml_node, std::allocator<pugi::xml_node> >::push_back(pugi::xml_node const&) (/root/asan_assimp/bin/libassimp.so.5+0x971e2e)
    #6 0x7ffff5ed0ec0 in Assimp::XmlNodeIterator::collectChildrenPreOrder(pugi::xml_node&) (/root/asan_assimp/bin/libassimp.so.5+0x96bec0)
    #7 0x7ffff5ed1054 in Assimp::XmlNodeIterator::collectChildrenPreOrder(pugi::xml_node&) (/root/asan_assimp/bin/libassimp.so.5+0x96c054)
    #8 0x7ffff5ed0d12 in Assimp::XmlNodeIterator::XmlNodeIterator(pugi::xml_node&, Assimp::XmlNodeIterator::IterationMode) (/root/asan_assimp/bin/libassimp.so.5+0x96bd12)
    #9 0x7ffff5ec0af6 in Assimp::ColladaParser::ReadSource(pugi::xml_node&) (/root/asan_assimp/bin/libassimp.so.5+0x95baf6)
    #10 0x7ffff5ec06ff in Assimp::ColladaParser::ReadMesh(pugi::xml_node&, Assimp::Collada::Mesh&) (/root/asan_assimp/bin/libassimp.so.5+0x95b6ff)
    #11 0x7ffff5ec03d3 in Assimp::ColladaParser::ReadGeometry(pugi::xml_node&, Assimp::Collada::Mesh&) (/root/asan_assimp/bin/libassimp.so.5+0x95b3d3)
    #12 0x7ffff5ebfd2e in Assimp::ColladaParser::ReadGeometryLibrary(pugi::xml_node&) (/root/asan_assimp/bin/libassimp.so.5+0x95ad2e)
    #13 0x7ffff5eb00a0 in Assimp::ColladaParser::ReadStructure(pugi::xml_node&) (/root/asan_assimp/bin/libassimp.so.5+0x94b0a0)
    #14 0x7ffff5eafae1 in Assimp::ColladaParser::ReadContents(pugi::xml_node&) (/root/asan_assimp/bin/libassimp.so.5+0x94aae1)
    #15 0x7ffff5eadb28 in Assimp::ColladaParser::ColladaParser(Assimp::IOSystem*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) (/root/asan_assimp/bin/libassimp.so.5+0x948b28)
    #16 0x7ffff5e4f81d in Assimp::ColladaLoader::InternReadFile(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, aiScene*, Assimp::IOSystem*) (/root/asan_assimp/bin/libassimp.so.5+0x8ea81d)
    #17 0x7ffff5aeeb1f in Assimp::BaseImporter::ReadFile(Assimp::Importer*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, Assimp::IOSystem*) (/root/asan_assimp/bin/libassimp.so.5+0x589b1f)
    #18 0x7ffff5b280f8 in Assimp::Importer::ReadFile(char const*, unsigned int) (/root/asan_assimp/bin/libassimp.so.5+0x5c30f8)
    #19 0x555555596937 in Assimp::Importer::ReadFile(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, unsigned int) (/root/assimp-master/bin/Asanassimp+0x42937)
    #20 0x55555559409e in ImportModel(ImportData const&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) (/root/assimp-master/bin/Asanassimp+0x4009e)
    #21 0x55555559f0a7 in Assimp_Info(char const* const*, unsigned int) (/root/assimp-master/bin/Asanassimp+0x4b0a7)
    #22 0x555555593d30 in main (/root/assimp-master/bin/Asanassimp+0x3fd30)
    #23 0x7ffff50140b2 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x270b2)

SUMMARY: AddressSanitizer: heap-use-after-free (/root/asan_assimp/bin/libassimp.so.5+0x961979) in Assimp::ColladaParser::ExtractDataObjectFromChannel(Assimp::Collada::InputChannel const&, unsigned long, Assimp::Collada::Mesh&)
Shadow bytes around the buggy address:
  0x0c047fff7ff0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c047fff8000: fa fa 00 fa fa fa 00 fa fa fa fd fa fa fa 00 00
  0x0c047fff8010: fa fa fd fa fa fa fd fd fa fa fd fa fa fa fd fd
  0x0c047fff8020: fa fa fd fa fa fa fd fd fa fa fd fa fa fa fd fd
  0x0c047fff8030: fa fa fd fa fa fa fd fd fa fa 00 04 fa fa fd fa
=>0x0c047fff8040: fa fa fd fd fa fa[fd]fa fa fa fd fd fa fa fd fa
  0x0c047fff8050: fa fa fd fd fa fa fd fa fa fa fd fd fa fa fa fa
  0x0c047fff8060: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8070: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8080: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8090: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
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
==19874==ABORTING
```

# Description

```
A heap-use-after-free was discovered in assimp. The issue is being triggered in function Assimp::ColladaParser::ExtractDataObjectFromChannel(Assimp::Collada::InputChannel const&, unsigned long, Assimp::Collada::Mesh&).
```

# Poc

Poc file is [this](https://github.com/NISL-SecurityGroup/NISL-BugDetection/blob/main/project/assimp/heap-use-after-free/Assimp__ColladaParser__ExtractDataObjectFromChannel_heap-use-after-free).
