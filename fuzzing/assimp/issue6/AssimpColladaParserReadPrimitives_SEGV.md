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
$ ./assimp info Assimp\:\:ColladaParser\:\:ReadPrimitives_SEGV 

Launching asset import ...           OK
Validating postprocessing flags ...  OK
Segmentation fault (core dumped)
```

Information obtained by using ASAN:

```shell
$ ./Asanassimp info Assimp\:\:ColladaParser\:\:ReadPrimitives_SEGV 

Launching asset import ...           OK
Validating postprocessing flags ...  OK
AddressSanitizer:DEADLYSIGNAL
=================================================================
==10609==ERROR: AddressSanitizer: SEGV on unknown address 0x000000000000 (pc 0x7ffff5ec594c bp 0x7fffffffb320 sp 0x7fffffffaf90 T0)
==10609==The signal is caused by a READ memory access.
==10609==Hint: address points to the zero page.
    #0 0x7ffff5ec594c in Assimp::ColladaParser::ReadPrimitives(pugi::xml_node&, Assimp::Collada::Mesh&, std::vector<Assimp::Collada::InputChannel, std::allocator<Assimp::Collada::InputChannel> >&, unsigned long, std::vector<unsigned long, std::allocator<unsigned long> > const&, Assimp::Collada::PrimitiveType) (/root/asan_assimp/bin/libassimp.so.5+0x96094c)
    #1 0x7ffff5ec3949 in Assimp::ColladaParser::ReadIndexData(pugi::xml_node&, Assimp::Collada::Mesh&) (/root/asan_assimp/bin/libassimp.so.5+0x95e949)
    #2 0x7ffff5ec0831 in Assimp::ColladaParser::ReadMesh(pugi::xml_node&, Assimp::Collada::Mesh&) (/root/asan_assimp/bin/libassimp.so.5+0x95b831)
    #3 0x7ffff5ec03d3 in Assimp::ColladaParser::ReadGeometry(pugi::xml_node&, Assimp::Collada::Mesh&) (/root/asan_assimp/bin/libassimp.so.5+0x95b3d3)
    #4 0x7ffff5ebfd2e in Assimp::ColladaParser::ReadGeometryLibrary(pugi::xml_node&) (/root/asan_assimp/bin/libassimp.so.5+0x95ad2e)
    #5 0x7ffff5eb00a0 in Assimp::ColladaParser::ReadStructure(pugi::xml_node&) (/root/asan_assimp/bin/libassimp.so.5+0x94b0a0)
    #6 0x7ffff5eafae1 in Assimp::ColladaParser::ReadContents(pugi::xml_node&) (/root/asan_assimp/bin/libassimp.so.5+0x94aae1)
    #7 0x7ffff5eadb28 in Assimp::ColladaParser::ColladaParser(Assimp::IOSystem*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) (/root/asan_assimp/bin/libassimp.so.5+0x948b28)
    #8 0x7ffff5e4f81d in Assimp::ColladaLoader::InternReadFile(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, aiScene*, Assimp::IOSystem*) (/root/asan_assimp/bin/libassimp.so.5+0x8ea81d)
    #9 0x7ffff5aeeb1f in Assimp::BaseImporter::ReadFile(Assimp::Importer*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, Assimp::IOSystem*) (/root/asan_assimp/bin/libassimp.so.5+0x589b1f)
    #10 0x7ffff5b280f8 in Assimp::Importer::ReadFile(char const*, unsigned int) (/root/asan_assimp/bin/libassimp.so.5+0x5c30f8)
    #11 0x555555596937 in Assimp::Importer::ReadFile(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, unsigned int) (/root/assimp-master/bin/Asanassimp+0x42937)
    #12 0x55555559409e in ImportModel(ImportData const&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) (/root/assimp-master/bin/Asanassimp+0x4009e)
    #13 0x55555559f0a7 in Assimp_Info(char const* const*, unsigned int) (/root/assimp-master/bin/Asanassimp+0x4b0a7)
    #14 0x555555593d30 in main (/root/assimp-master/bin/Asanassimp+0x3fd30)
    #15 0x7ffff50140b2 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x270b2)
    #16 0x55555556c35d in _start (/root/assimp-master/bin/Asanassimp+0x1835d)

AddressSanitizer can not provide additional info.
SUMMARY: AddressSanitizer: SEGV (/root/asan_assimp/bin/libassimp.so.5+0x96094c) in Assimp::ColladaParser::ReadPrimitives(pugi::xml_node&, Assimp::Collada::Mesh&, std::vector<Assimp::Collada::InputChannel, std::allocator<Assimp::Collada::InputChannel> >&, unsigned long, std::vector<unsigned long, std::allocator<unsigned long> > const&, Assimp::Collada::PrimitiveType)
==10609==ABORTING
```

# Description

```
A SEGV was discovered in assimp. The issue is being triggered in function Assimp::ColladaParser::ReadPrimitives(pugi::xml_node&, Assimp::Collada::Mesh&, std::vector<Assimp::Collada::InputChannel, std::allocator<Assimp::Collada::InputChannel> >&, unsigned long, std::vector<unsigned long, std::allocator<unsigned long> > const&, Assimp::Collada::PrimitiveType).
```

# Poc

Poc file is [this](https://github.com/NISL-SecurityGroup/NISL-BugDetection/blob/main/project/assimp/SEGV-1/Assimp__ColladaParser__ReadPrimitives_SEGV).
