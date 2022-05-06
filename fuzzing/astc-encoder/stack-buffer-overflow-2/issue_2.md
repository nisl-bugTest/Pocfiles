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
$ ./astcenc-native -cl crash02 out2.astc 6x6 -medium

Segmentation fault (core dumped)
```

Information obtained by using ASAN:

```
$ ./astcenc-native_asan -cl crash02 out2.astc 6x6 -medium

=================================================================
==11672==ERROR: AddressSanitizer: stack-buffer-overflow on address 0x7f901c729660 at pc 0x56099bd661c2 bp 0x7f901c725760 sp 0x7f901c725750
READ of size 4 at 0x7f901c729660 thread T1
    #0 0x56099bd661c1 in compress_symbolic_block_for_partition_2planes(astcenc_config const&, block_size_descriptor const&, image_block const&, error_weight_block const&, float, unsigned int, symbolic_compressed_block&, compression_working_buffers&) (/docker/astc_test/te/Source/astcenc-native+0xbc1c1)
    #1 0x56099bd7091a in compress_image(astcenc_context&, unsigned int, astcenc_image const&, astcenc_swizzle const&, unsigned char*) (/docker/astc_test/te/Source/astcenc-native+0xc691a)
    #2 0x56099bd77382 in astcenc_compress_image(astcenc_context*, astcenc_image*, astcenc_swizzle const*, unsigned char*, unsigned long, unsigned int) (/docker/astc_test/te/Source/astcenc-native+0xcd382)
    #3 0x56099bd26c33 in compression_workload_runner(int, int, void*) (/docker/astc_test/te/Source/astcenc-native+0x7cc33)
    #4 0x56099bd23c53 in launch_threads_helper(void*) [clone .lto_priv.0] (/docker/astc_test/te/Source/astcenc-native+0x79c53)
    #5 0x7f9021f17608 in start_thread (/lib/x86_64-linux-gnu/libpthread.so.0+0x9608)
    #6 0x7f90223a6292 in __clone (/lib/x86_64-linux-gnu/libc.so.6+0x122292)

Address 0x7f901c729660 is located in stack of thread T1 at offset 15504 in frame
    #0 0x56099bd5827f in compress_symbolic_block_for_partition_2planes(astcenc_config const&, block_size_descriptor const&, image_block const&, error_weight_block const&, float, unsigned int, symbolic_compressed_block&, compression_working_buffers&) (/docker/astc_test/te/Source/astcenc-native+0xae27f)

  This frame has 480 object(s):
    [48, 49) 'rgb_hdr' (line 97)
    [64, 65) 'alpha_hdr' (line 98)
    [80, 96) 'err_mask' (line 668)
    [112, 128) 'err_max' (line 667)
    [144, 160) 'use_ep2' (line 664)
    [176, 192) 'ep2' (line 663)
    [208, 224) 'use_ep1' (line 660)
    [240, 256) 'ep1' (line 659)
    [272, 288) 'min_ep2' (line 657)
    [304, 320) 'min_ep1' (line 656)
    [336, 352) 'rgbo_color' (line 801)
    [368, 384) 'rgbs_color' (line 800)
    [400, 416) '<unknown>'
    [432, 448) '<unknown>'
    [464, 480) '<unknown>'
    [496, 512) '<unknown>'
    [528, 544) '<unknown>'
    [560, 576) '<unknown>'
    [592, 608) '<unknown>'
    [624, 640) '<unknown>'
    [656, 672) '<unknown>'
    [688, 704) '<unknown>'
    [720, 736) '<unknown>'
    [752, 768) '<unknown>'
    [784, 800) '<unknown>'
    [816, 832) '<unknown>'
    [848, 864) '<unknown>'
    [880, 896) '<unknown>'
    [912, 928) '<unknown>'
    [944, 960) '<unknown>'
    [976, 992) '<unknown>'
    [1008, 1024) '<unknown>'
    [1040, 1056) '<unknown>'
    [1072, 1088) '<unknown>'
    [1104, 1120) '<unknown>'
    [1136, 1152) '<unknown>'
    [1168, 1184) '<unknown>'
    [1200, 1216) '<unknown>'
    [1232, 1248) '<unknown>'
    [1264, 1280) '<unknown>'
    [1296, 1312) '<unknown>'
    [1328, 1344) '<unknown>'
    [1360, 1376) '<unknown>'
    [1392, 1408) '<unknown>'
    [1424, 1440) '<unknown>'
    [1456, 1472) '<unknown>'
    [1488, 1504) '<unknown>'
    [1520, 1536) 'lane_mask'
    [1552, 1568) 'color_mask'
    [1584, 1600) '<unknown>'
    [1616, 1632) '<unknown>'
    [1648, 1664) '<unknown>'
    [1680, 1696) '<unknown>'
    [1712, 1728) '<unknown>'
    [1744, 1760) 'error_summav'
    [1776, 1792) 'current_values1'
    [1808, 1824) 'actual_values1'
    [1840, 1856) 'diff'
    [1872, 1888) 'error1'
    [1904, 1920) 'current_values2'
    [1936, 1952) 'actual_values2'
    [1968, 1984) 'error2'
    [2000, 2016) 'current_values1'
    [2032, 2048) 'actual_values1'
    [2064, 2080) 'diff'
    [2096, 2112) 'error1'
    [2128, 2144) 'current_values2'
    [2160, 2176) 'actual_values2'
    [2192, 2208) 'error2'
    [2224, 2240) '<unknown>'
    [2256, 2272) '<unknown>'
    [2288, 2304) '<unknown>'
    [2320, 2336) '<unknown>'
    [2352, 2368) '<unknown>'
    [2384, 2400) '<unknown>'
    [2416, 2432) '<unknown>'
    [2448, 2464) '<unknown>'
    [2480, 2496) '<unknown>'
    [2512, 2528) '<unknown>'
    [2544, 2560) '<unknown>'
    [2576, 2592) '<unknown>'
    [2608, 2624) '<unknown>'
    [2640, 2656) '<unknown>'
    [2672, 2688) '<unknown>'
    [2704, 2720) '<unknown>'
    [2736, 2752) '<unknown>'
    [2768, 2784) '<unknown>'
    [2800, 2816) '<unknown>'
    [2832, 2848) '<unknown>'
    [2864, 2880) '<unknown>'
    [2896, 2912) '<unknown>'
    [2928, 2944) '<unknown>'
    [2960, 2976) '<unknown>'
    [2992, 3008) '<unknown>'
    [3024, 3040) '<unknown>'
    [3056, 3072) '<unknown>'
    [3088, 3104) 'weight_idx0'
    [3120, 3136) 'weight_idx1'
    [3152, 3168) 'weight_idx2'
    [3184, 3200) 'weight_idx3'
    [3216, 3232) 'weight_val0'
    [3248, 3264) 'weight_val1'
    [3280, 3296) 'weight_val2'
    [3312, 3328) 'weight_val3'
    [3344, 3360) 'tex_weight_float0'
    [3376, 3392) 'tex_weight_float1'
    [3408, 3424) 'tex_weight_float2'
    [3440, 3456) 'tex_weight_float3'
    [3472, 3488) '<unknown>'
    [3504, 3520) '<unknown>'
    [3536, 3552) '<unknown>'
    [3568, 3584) '<unknown>'
    [3600, 3616) '<unknown>'
    [3632, 3648) '<unknown>'
    [3664, 3680) '<unknown>'
    [3696, 3712) '<unknown>'
    [3728, 3744) '<unknown>'
    [3760, 3776) '<unknown>'
    [3792, 3808) '<unknown>'
    [3824, 3840) '<unknown>'
    [3856, 3872) '<unknown>'
    [3888, 3904) '<unknown>'
    [3920, 3936) '<unknown>'
    [3952, 3968) 'weight_idx0'
    [3984, 4000) 'weight_idx1'
    [4016, 4032) 'weight_idx2'
    [4048, 4064) 'weight_idx3'
    [4080, 4096) 'weight_val0'
    [4112, 4128) 'weight_val1'
    [4144, 4160) 'weight_val2'
    [4176, 4192) 'weight_val3'
    [4208, 4224) 'tex_weight_float0'
    [4240, 4256) 'tex_weight_float1'
    [4272, 4288) 'tex_weight_float2'
    [4304, 4320) 'tex_weight_float3'
    [4336, 4352) '<unknown>'
    [4368, 4384) '<unknown>'
    [4400, 4416) '<unknown>'
    [4432, 4448) '<unknown>'
    [4464, 4480) '<unknown>'
    [4496, 4512) '<unknown>'
    [4528, 4544) '<unknown>'
    [4560, 4576) '<unknown>'
    [4592, 4608) '<unknown>'
    [4624, 4640) '<unknown>'
    [4656, 4672) '<unknown>'
    [4688, 4704) '<unknown>'
    [4720, 4736) '<unknown>'
    [4752, 4768) '<unknown>'
    [4784, 4800) '<unknown>'
    [4816, 4832) 'rgba_sum'
    [4848, 4864) 'rgba_weight_sum'
    [4880, 4896) 'scale_direction'
    [4912, 4928) 'left_sum'
    [4944, 4960) 'middle_sum'
    [4976, 4992) 'right_sum'
    [5008, 5024) 'left2_sum'
    [5040, 5056) 'middle2_sum'
    [5072, 5088) 'right2_sum'
    [5104, 5120) 'lmrs_sum'
    [5136, 5152) 'color_vec_x'
    [5168, 5184) 'color_vec_y'
    [5200, 5216) 'scale_vec'
    [5232, 5248) 'weight_weight_sum'
    [5264, 5280) 'rgbq_sum'
    [5296, 5312) 'sds'
    [5328, 5344) 'v0'
    [5360, 5376) 'v1'
    [5392, 5408) 'avg'
    [5424, 5440) 'ep0'
    [5456, 5472) 'color_det2'
    [5488, 5504) 'color_rdet2'
    [5520, 5536) 'color_mss2'
    [5552, 5568) 'ep0'
    [5584, 5600) 'ep1'
    [5616, 5632) 'p2_mask'
    [5648, 5664) 'det_mask'
    [5680, 5696) 'notnan_mask'
    [5712, 5728) 'full_mask'
    [5744, 5760) 'avg'
    [5776, 5792) 'p2_mask'
    [5808, 5824) 'notnan_mask'
    [5840, 5856) 'full_mask'
    [5872, 5888) 'color_det1'
    [5904, 5920) 'color_rdet1'
    [5936, 5952) 'color_mss1'
    [5968, 5984) 'ep0'
    [6000, 6016) 'ep1'
    [6032, 6048) 'p1_mask'
    [6064, 6080) 'det_mask'
    [6096, 6112) 'notnan_mask'
    [6128, 6144) 'full_mask'
    [6160, 6176) 'sdsm'
    [6192, 6208) 'avg'
    [6224, 6240) 'p1_mask'
    [6256, 6272) 'notnan_mask'
    [6288, 6304) 'full_mask'
    [6320, 6336) 'rgba'
    [6352, 6368) 'color_weight'
    [6384, 6400) 'left'
    [6416, 6432) 'middle'
    [6448, 6464) 'right'
    [6480, 6496) 'lmrs'
    [6512, 6528) 'left2'
    [6544, 6560) 'middle2'
    [6576, 6592) 'right2'
    [6608, 6624) 'p2_mask'
    [6640, 6656) 'color_idx'
    [6672, 6688) 'cwprod'
    [6704, 6720) 'cwiprod'
    [6736, 6752) 'm'
    [6768, 6784) 'm'
    [6800, 6816) 'result'
    [6832, 6848) 'length'
    [6864, 6880) 'm'
    [6896, 6912) 'm'
    [6928, 6944) '<unknown>'
    [6960, 6976) '<unknown>'
    [6992, 7008) '<unknown>'
    [7024, 7040) '<unknown>'
    [7056, 7072) '<unknown>'
    [7088, 7104) '<unknown>'
    [7120, 7136) '<unknown>'
    [7152, 7168) '<unknown>'
    [7184, 7200) '<unknown>'
    [7216, 7232) '<unknown>'
    [7248, 7264) '<unknown>'
    [7280, 7296) '<unknown>'
    [7312, 7328) '<unknown>'
    [7344, 7360) '<unknown>'
    [7376, 7392) '<unknown>'
    [7408, 7424) '<unknown>'
    [7440, 7456) '<unknown>'
    [7472, 7488) '<unknown>'
    [7504, 7520) '<unknown>'
    [7536, 7552) '<unknown>'
    [7568, 7584) '<unknown>'
    [7600, 7616) '<unknown>'
    [7632, 7648) '<unknown>'
    [7664, 7680) '<unknown>'
    [7696, 7712) '<unknown>'
    [7728, 7744) '<unknown>'
    [7760, 7776) '<unknown>'
    [7792, 7808) '<unknown>'
    [7824, 7840) '<unknown>'
    [7856, 7872) '<unknown>'
    [7888, 7904) '<unknown>'
    [7920, 7936) '<unknown>'
    [7952, 7968) '<unknown>'
    [7984, 8000) '<unknown>'
    [8016, 8032) '<unknown>'
    [8048, 8064) '<unknown>'
    [8080, 8096) '<unknown>'
    [8112, 8128) '<unknown>'
    [8144, 8160) '<unknown>'
    [8176, 8192) '<unknown>'
    [8208, 8224) '<unknown>'
    [8240, 8256) '<unknown>'
    [8272, 8288) '<unknown>'
    [8304, 8320) '<unknown>'
    [8336, 8352) '<unknown>'
    [8368, 8384) '<unknown>'
    [8400, 8416) '<unknown>'
    [8432, 8448) '<unknown>'
    [8464, 8480) '<unknown>'
    [8496, 8512) '<unknown>'
    [8528, 8544) '<unknown>'
    [8560, 8576) '<unknown>'
    [8592, 8608) '<unknown>'
    [8624, 8640) '<unknown>'
    [8656, 8672) '<unknown>'
    [8688, 8704) '<unknown>'
    [8720, 8736) '<unknown>'
    [8752, 8768) '<unknown>'
    [8784, 8800) '<unknown>'
    [8816, 8832) '<unknown>'
    [8848, 8864) '<unknown>'
    [8880, 8896) '<unknown>'
    [8912, 8928) '<unknown>'
    [8944, 8960) '<unknown>'
    [8976, 8992) '<unknown>'
    [9008, 9024) '<unknown>'
    [9040, 9056) '<unknown>'
    [9072, 9088) '<unknown>'
    [9104, 9120) '<unknown>'
    [9136, 9152) '<unknown>'
    [9168, 9184) '<unknown>'
    [9200, 9216) '<unknown>'
    [9232, 9248) '<unknown>'
    [9264, 9280) '<unknown>'
    [9296, 9312) '<unknown>'
    [9328, 9344) '<unknown>'
    [9360, 9376) '<unknown>'
    [9392, 9408) '<unknown>'
    [9424, 9440) '<unknown>'
    [9456, 9472) '<unknown>'
    [9488, 9504) '<unknown>'
    [9520, 9536) '<unknown>'
    [9552, 9568) '<unknown>'
    [9584, 9600) '<unknown>'
    [9616, 9632) '<unknown>'
    [9648, 9664) '<unknown>'
    [9680, 9696) '<unknown>'
    [9712, 9728) '<unknown>'
    [9744, 9760) '<unknown>'
    [9776, 9792) '<unknown>'
    [9808, 9824) '<unknown>'
    [9840, 9856) '<unknown>'
    [9872, 9888) '<unknown>'
    [9904, 9920) '<unknown>'
    [9936, 9952) '<unknown>'
    [9968, 9984) '<unknown>'
    [10000, 10016) '<unknown>'
    [10032, 10048) '<unknown>'
    [10064, 10080) '<unknown>'
    [10096, 10112) '<unknown>'
    [10128, 10144) '<unknown>'
    [10160, 10176) '<unknown>'
    [10192, 10208) '<unknown>'
    [10224, 10240) '<unknown>'
    [10256, 10272) '<unknown>'
    [10288, 10304) '<unknown>'
    [10320, 10336) '<unknown>'
    [10352, 10368) '<unknown>'
    [10384, 10400) '<unknown>'
    [10416, 10432) '<unknown>'
    [10448, 10464) '<unknown>'
    [10480, 10496) '<unknown>'
    [10512, 10528) '<unknown>'
    [10544, 10560) '<unknown>'
    [10576, 10592) '<unknown>'
    [10608, 10624) '<unknown>'
    [10640, 10656) '<unknown>'
    [10672, 10688) '<unknown>'
    [10704, 10720) '<unknown>'
    [10736, 10752) '<unknown>'
    [10768, 10784) '<unknown>'
    [10800, 10816) '<unknown>'
    [10832, 10848) '<unknown>'
    [10864, 10880) '<unknown>'
    [10896, 10912) '<unknown>'
    [10928, 10944) '<unknown>'
    [10960, 10976) '<unknown>'
    [10992, 11008) '<unknown>'
    [11024, 11040) '<unknown>'
    [11056, 11072) '<unknown>'
    [11088, 11104) '<unknown>'
    [11120, 11136) '<unknown>'
    [11152, 11168) '<unknown>'
    [11184, 11200) '<unknown>'
    [11216, 11232) '<unknown>'
    [11248, 11264) '<unknown>'
    [11280, 11296) '<unknown>'
    [11312, 11328) '<unknown>'
    [11344, 11360) '<unknown>'
    [11376, 11392) '<unknown>'
    [11408, 11424) '<unknown>'
    [11440, 11456) '<unknown>'
    [11472, 11488) '<unknown>'
    [11504, 11520) '<unknown>'
    [11536, 11552) '<unknown>'
    [11568, 11584) '<unknown>'
    [11600, 11616) '<unknown>'
    [11632, 11648) '<unknown>'
    [11664, 11680) '<unknown>'
    [11696, 11712) '<unknown>'
    [11728, 11744) '<unknown>'
    [11760, 11776) '<unknown>'
    [11792, 11808) '<unknown>'
    [11824, 11840) '<unknown>'
    [11856, 11872) '<unknown>'
    [11888, 11904) '<unknown>'
    [11920, 11936) '<unknown>'
    [11952, 11968) '<unknown>'
    [11984, 12000) '<unknown>'
    [12016, 12032) '<unknown>'
    [12048, 12064) '<unknown>'
    [12080, 12096) '<unknown>'
    [12112, 12128) '<unknown>'
    [12144, 12160) '<unknown>'
    [12176, 12192) '<unknown>'
    [12208, 12224) '<unknown>'
    [12240, 12256) '<unknown>'
    [12272, 12288) '<unknown>'
    [12304, 12320) '<unknown>'
    [12336, 12352) '<unknown>'
    [12368, 12384) '<unknown>'
    [12400, 12416) '<unknown>'
    [12432, 12448) '<unknown>'
    [12464, 12480) '<unknown>'
    [12496, 12512) 'mat0'
    [12528, 12544) 'mat1'
    [12560, 12576) 'mat2'
    [12592, 12608) 'mat3'
    [12624, 12640) 'vect'
    [12656, 12672) 'm'
    [12688, 12704) 'm'
    [12720, 12736) 'm'
    [12752, 12768) 'm'
    [12784, 12800) '<unknown>'
    [12816, 12832) '<unknown>'
    [12848, 12864) '<unknown>'
    [12880, 12896) '<unknown>'
    [12912, 12928) '<unknown>'
    [12944, 12960) '<unknown>'
    [12976, 12992) '<unknown>'
    [13008, 13024) 'plane_mask' (line 94)
    [13040, 13056) 'color_offset' (line 186)
    [13072, 13088) 'color_base' (line 187)
    [13104, 13120) 'color' (line 189)
    [13136, 13152) 'origcolor' (line 191)
    [13168, 13184) 'error_weight' (line 192)
    [13200, 13216) 'colordiff' (line 194)
    [13232, 13248) 'color_up_diff' (line 195)
    [13264, 13280) 'color_down_diff' (line 196)
    [13296, 13312) 'm'
    [13328, 13344) 'm'
    [13360, 13376) 'm'
    [13392, 13408) 'epd' (line 128)
    [13424, 13440) '<unknown>'
    [13456, 13472) '<unknown>'
    [13488, 13504) '<unknown>'
    [13520, 13536) '<unknown>'
    [13552, 13568) '<unknown>'
    [13584, 13600) '<unknown>'
    [13616, 13632) '<unknown>'
    [13648, 13664) '<unknown>'
    [13680, 13696) '<unknown>'
    [13712, 13728) '<unknown>'
    [13744, 13760) '<unknown>'
    [13776, 13792) '<unknown>'
    [13808, 13824) '<unknown>'
    [13840, 13856) '<unknown>'
    [13872, 13888) '<unknown>'
    [13904, 13920) '<unknown>'
    [13936, 13952) '<unknown>'
    [13968, 13984) '<unknown>'
    [14000, 14016) '<unknown>'
    [14032, 14048) '<unknown>'
    [14064, 14080) '<unknown>'
    [14096, 14112) '<unknown>'
    [14128, 14144) '<unknown>'
    [14160, 14176) '<unknown>'
    [14192, 14208) '<unknown>'
    [14224, 14240) '<unknown>'
    [14256, 14272) '<unknown>'
    [14288, 14304) '<unknown>'
    [14320, 14336) 'color_quant_level_mod' (line 764)
    [14352, 14368) 'color_quant_level' (line 763)
    [14384, 14400) 'block_mode_index' (line 761)
    [14416, 14432) 'idx'
    [14448, 14464) 'idx'
    [14480, 14496) 'idx'
    [14512, 14528) 'idx'
    [14544, 14560) 'idx'
    [14576, 14592) 'idx'
    [14608, 14624) 'idx'
    [14640, 14656) 'idx'
    [14672, 14688) 'idx'
    [14704, 14720) 'idx'
    [14736, 14800) 'partition_format_specifiers' (line 760)
    [14832, 14896) 'endpnt0' (line 99)
    [14928, 14992) 'endpnt1' (line 100)
    [15024, 15088) 'endpnt0f' (line 101)
    [15120, 15184) 'offset' (line 102)
    [15216, 15332) 'workscb' (line 803)
    [15376, 15504) 'dec_weights_quant_uvalue_plane1' <== Memory access at offset 15504 overflows this variable
    [15536, 15664) 'dec_weights_quant_uvalue_plane2'
    [15696, 15840) 'epm' (line 766)
    [15904, 20080) 'low_values1'
    [20336, 24512) 'high_values1'
    [24768, 28944) 'low_values2'
    [29200, 33376) 'high_values2'
    [33632, 41824) 'qwt_errors' (line 695)
    [42080, 50272) 'qwt_bitcounts' (line 694)
    [50528, 58720) 'weight_high_value2' (line 681)
    [58976, 67168) 'weight_low_value2' (line 680)
    [67424, 75616) 'weight_high_value1' (line 679)
    [75872, 84064) 'weight_low_value1' (line 678)
    [84320, 84384) 'uq_pl_weights' (line 118)
HINT: this may be a false positive if your program uses some custom stack unwind mechanism, swapcontext or vfork
      (longjmp and C++ exceptions *are* supported)
Thread T1 created by T0 here:
    #0 0x7f90224cda95 in __interceptor_pthread_create (/lib/x86_64-linux-gnu/libasan.so.6+0x57a95)
    #1 0x56099bdd10b0 in launch_threads(int, void (*)(int, int, void*), void*) [clone .constprop.0] (/docker/astc_test/te/Source/astcenc-native+0x1270b0)

SUMMARY: AddressSanitizer: stack-buffer-overflow (/docker/astc_test/te/Source/astcenc-native+0xbc1c1) in compress_symbolic_block_for_partition_2planes(astcenc_config const&, block_size_descriptor const&, image_block const&, error_weight_block const&, float, unsigned int, symbolic_compressed_block&, compression_working_buffers&)
Shadow bytes around the buggy address:
  0x0ff2838dd270: 00 00 00 00 f2 f2 f2 f2 00 00 00 00 00 00 00 00
  0x0ff2838dd280: f2 f2 f2 f2 00 00 00 00 00 00 00 00 f2 f2 f2 f2
  0x0ff2838dd290: 00 00 00 00 00 00 00 00 f2 f2 f2 f2 00 00 00 00
  0x0ff2838dd2a0: 00 00 00 00 f2 f2 f2 f2 00 00 00 00 00 00 00 00
  0x0ff2838dd2b0: 00 00 00 00 00 00 04 f2 f2 f2 f2 f2 00 00 00 00
=>0x0ff2838dd2c0: 00 00 00 00 00 00 00 00 00 00 00 00[f2]f2 f2 f2
  0x0ff2838dd2d0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0ff2838dd2e0: f2 f2 f2 f2 00 00 00 00 00 00 00 00 00 00 00 00
  0x0ff2838dd2f0: 00 00 00 00 00 00 f2 f2 f2 f2 f2 f2 f2 f2 f8 f8
  0x0ff2838dd300: f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8
  0x0ff2838dd310: f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8
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
==11672==ABORTING
```

# Description

```
A stack-buffer-overflow was discovered in astcenc. The issue is being triggered in function compress_symbolic_block_for_partition_2planes().
```

# Poc

Poc file is [this](https://github.com/NISL-SecurityGroup/NISL-BugDetection/blob/main/project/packJPG/memory leaks/memory_leaks).
