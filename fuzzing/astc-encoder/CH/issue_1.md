# Version

```
astcenc v3.2.0, 64-bit sse2
```

# Environment

Ubuntu 18.04，64 bit

# Command

**Compile test program:**

```c++
$ mkdir build
$ cd build
$ cmake -G "Unix Makefiles" -DCMAKE_BUILD_TYPE=Release ..
$ make -j8
```

**Compile test program with address sanitizer:**

- Update Makefile:

```
$ set(CMAKE_C_COMPILER "/usr/bin/gcc")
$ set(CMAKE_CXX_COMPILER "/usr/bin/g++")
$ set(CMAKE_C_FLAGS "-fsanitize=address")
$ set(CMAKE_CXX_FLAGS "-fsanitize=address")
```

- Compile program:

```
$ mkdir -p asan_build
$ cd asan_build
$ cmake -G "Unix Makefiles" -DCMAKE_BUILD_TYPE=Release ..
$ make -j8
```

# Result

The result of running without ASAN:

```
$ ./astcenc-native -cl crash01.png out1.astc 6x6 -medium

Segmentation fault (core dumped)
```

Information obtained by using ASAN:

```
$ ./astcenc-native_asan -cl crash01.png out1.astc 6x6 -medium

==10804==ERROR: AddressSanitizer: stack-buffer-overflow on address 0x7f5352492320 at pc 0x5602c3a3f015 bp 0x7f53524921e0 sp 0x7f53524921d0
READ of size 1 at 0x7f5352492320 thread T1
    #0 0x5602c3a3f014 in encode_ise(quant_method, unsigned int, unsigned char const*, unsigned char*, unsigned int) (/docker/astc_test/te/Source/astcenc-native+0xee014)
    #1 0x5602c39eb36c in symbolic_to_physical(block_size_descriptor const&, symbolic_compressed_block const&, physical_compressed_block&) (/docker/astc_test/te/Source/astcenc-native+0x9a36c)
    #2 0x5602c3a19b07 in compress_image(astcenc_context&, unsigned int, astcenc_image const&, astcenc_swizzle const&, unsigned char*) (/docker/astc_test/te/Source/astcenc-native+0xc8b07)
    #3 0x5602c3a1e382 in astcenc_compress_image(astcenc_context*, astcenc_image*, astcenc_swizzle const*, unsigned char*, unsigned long, unsigned int) (/docker/astc_test/te/Source/astcenc-native+0xcd382)
    #4 0x5602c39cdc33 in compression_workload_runner(int, int, void*) (/docker/astc_test/te/Source/astcenc-native+0x7cc33)
    #5 0x5602c39cac53 in launch_threads_helper(void*) [clone .lto_priv.0] (/docker/astc_test/te/Source/astcenc-native+0x79c53)
    #6 0x7f5357c45608 in start_thread (/lib/x86_64-linux-gnu/libpthread.so.0+0x9608)
    #7 0x7f53580d4292 in __clone (/lib/x86_64-linux-gnu/libc.so.6+0x122292)

Address 0x7f5352492320 is located in stack of thread T1 at offset 96 in frame
    #0 0x5602c39ea13f in symbolic_to_physical(block_size_descriptor const&, symbolic_compressed_block const&, physical_compressed_block&) (/docker/astc_test/te/Source/astcenc-native+0x9913f)

  This frame has 3 object(s):
    [32, 48) 'weightbuf' (line 146)
    [64, 96) 'values_to_encode' (line 247) <== Memory access at offset 96 overflows this variable
    [128, 192) 'weights' (line 160)
HINT: this may be a false positive if your program uses some custom stack unwind mechanism, swapcontext or vfork
      (longjmp and C++ exceptions *are* supported)
Thread T1 created by T0 here:
    #0 0x7f53581fba95 in __interceptor_pthread_create (/lib/x86_64-linux-gnu/libasan.so.6+0x57a95)
    #1 0x5602c3a780b0 in launch_threads(int, void (*)(int, int, void*), void*) [clone .constprop.0] (/docker/astc_test/te/Source/astcenc-native+0x1270b0)

SUMMARY: AddressSanitizer: stack-buffer-overflow (/docker/astc_test/te/Source/astcenc-native+0xee014) in encode_ise(quant_method, unsigned int, unsigned char const*, unsigned char*, unsigned int)
Shadow bytes around the buggy address:
  0x0feaea48a410: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0feaea48a420: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0feaea48a430: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0feaea48a440: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0feaea48a450: 00 00 00 00 00 00 00 00 f1 f1 f1 f1 00 00 f2 f2
=>0x0feaea48a460: 00 00 00 00[f2]f2 f2 f2 00 00 00 00 00 00 00 00
  0x0feaea48a470: f3 f3 f3 f3 00 00 00 00 00 00 00 00 00 00 00 00
  0x0feaea48a480: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0feaea48a490: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0feaea48a4a0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0feaea48a4b0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
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
==10804==ABORTING
```

# Description

```
A stack-buffer-overflow was discovered in astcenc.The issue is being triggered in function encode_ise().
```

# Poc

Poc file is [this](https://github.com/nisl-bugTest/Pocfiles/blob/main/fuzzing/astc-encoder/CH/crash01).
