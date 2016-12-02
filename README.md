# veclib
Vector library for porting SSE2 instructions to other architectures

Credit to yhcheng for first providing a PPC64 port of the Burrows-Wheeler Aligner which helped me expand on the instructions. 
PPC64 Altivec instructions are provided in vec128int.h which depends on veclib_types.h. 

The IBM POWER vector intrinsinc functions library can be downloaded from the [IBM developerWorks website](https://www.ibm.com/developerworks/community/groups/community/powerveclib/).

Below are some integer SSE2 instructions I've managed to port to Altivec using veclib-1.0.4
For big-endian platforms only the shift left/right operations are reversed. 

## Vector Integer Operations

### Load
| veclib | SSE2     | Altivec          | Description                                                    |
| --- | ----------- | ---------------- | -------------------------------------------------------------- |
| | _mm_load_si128 (__m128i const* mem_addr) | vec_load1q       | Load 128 bits of integer data, aligned                         |
| | _mm_loadu_si128 (__m128i const* mem_addr) | vec_loadu1q      | Load 128 bits of integer data, unaligned                       |
| | _mm_loadl_epi64 (__m128i const* mem_addr) | vec_loadlower1sd | Load 64-bits of integer data to lower part and zero upper part |

### Set
| veclib | SSE2       | Altivec            | Description                                           |
| --- | ------------- | ------------------ | ----------------------------------------------------- |
| | _mm_setzero_si128 () | vec_zero1q         | Set 128 integer bits to zero                          |
| | _mm_set1_epi8 (char a) | vec_splat16sb      | Splat 8-bit char to 16 8-bit chars                    |
| | _mm_set1_epi16 (short a) | vec_splat8sh       | Splat 16-bit short to 8 16-bit shorts                 |
| | _mm_set1_epi32 (int a) | vec_splat4sw       | Splat 32-bit ints to 4 32-bit ints                    |
| | _mm_set1_epi64 (__m64 a) | vec_splat2sd       | Splat 64-bit long long to 2 64-bit long longs         |
| | _mm_set_epi8 (char a) | vec_set16sb        | Set 16 8-bit chars                                    |
| | _mm_set_epi16 (short a) | vec_set8sh         | Set 8 16-bits shorts                                  |
| | _mm_set_epi32 (int a) | vec_set4sw         | Set 4 32-bit ints                                     |
| | _mm_set_epi64 (__m64 a) | vec_set2sd         | Set 2 64-bit long longs                               |
| | _mm_setr_epi8 (char a) | vec_setreverse16sb | Set 16 8-bit chars reversed                           |
| | _mm_setr_epi16 (short a) | vec_setreverse8sh  | Set 8 16-bit shorts reversed                          |
| | _mm_setr_epi32 (int a) | vec_setreverse4sw  | Set 4 32-bit ints reversed                            |
| | _mm_setr_epi64 (__m64 a) | vec_setreverse2sd  | Set 2 64-bit long longs reversed                      |
| | _mm_movpi64_epi64 (__m64 a) | vec_Zerouppersd    | Set lower 64-bits of integer data and zero upper part |

### Store
| veclib | SSE2      | Altivec                | Description                       |
| --- | ------------ | ---------------------- | --------------------------------- |
| | _mm_store_si128  | vec_store1q            | Store 128-bits integer, aligned   |
| | _mm_storeu_si128 | vec_storeu1q           | Store 128-bits integer, unaligned |
| | _mm_storel_epi64 | vec_storelower1sdof2sd | Store lower 64-bit long long      |

### Insert
| veclib | SSE2      | Altivec             | Description                                     |
| --- | ------------ | ------------------- | ----------------------------------------------- |
| |                  | vec_insert4sw       | Insert 32-bit int                               |
| |                  | vec_insert2sd       | Insert 64-bit long long                         |
| |                  | vec_convert1swto1uq | Insert 32-bit int, zeroing upper                |
| | _mm_insert_epi16 | vec_insert8sh       | Insert 16-bit short into one of 8 16-bit shorts |
| |                  | vec_insert16sb      | Insert 8-bit unsigned char into one of 16 bytes |

### Extract
| veclib | SSE2       | Altivec                 | Description                                      |
| --- | ------------- | ----------------------- | ------------------------------------------------ |
| |                   | vec_extract1swfrom4sw   | Extract 32-bit int                               |
| |                   | vec_extract1sdfrom2sd   | Extract 64-bit long long                         |
| | _mm_extract_epi16 | vec_extract8sh          | Extract 16-bit short from one of 8 16-bit shorts |
| | _mm_movemask_epi8 | vec_extractupperbit16sb | Extract upper bit of 16 8-bit chars              |
| |                   | vec_extractupperbit2dp  | Extract upper bit of 2 64-bit doubles            |
| |                   | vec_extractlowersw      | Extract lower 32-bit int                         |

### Convert integer to integer
| veclib | SSE2      | Altivec           | Description                                                        |
| --- | ------------ | ----------------- | ------------------------------------------------------------------ |
| | _mm_packs_epi16  | vec_packs8hto16sb | Convert 8+8 16-bit shorts to 16 8-bit chars with signed saturation |
| | _mm_packs_epi32  | vec_packs4wto8sh  | Convert 4+4 32-bit ints to 8 16-bit shorts with signed saturation  |
| | _mm_packus_epi16 |                   | Convert packed 16-bit integers from a and b to packed 8-bit integers using unsigned saturation, and store the results in dst |

### Convert floating-point to integer
| veclib | SSE2       | Altivec                       | Description                                               |
| --- | ------------- | ----------------------------- | --------------------------------------------------------- |
| | _mm_cvttps_epi32  | vec_converttruncating4spto4sw | Convert 4 32-bit floats to 4 32-bit ints with truncation  |
| | _mm_cvtps_epi32   | vec_convert4spto4sw           | Convert 4 32-bit floats to 4 32-bit ints                  |
| | _mm_cvttpd_epi32  | vec_Convert2dpto2sw           | Convert 2 64-bit doubles to 2 32-bit ints with truncation |
| | _mm_cvtpd_epi32   |                               | Convert packed double-precision (64-bit) floating-point elements in a to packed 32-bit integers, and store the results in dst |
| | _mm_cvtsi32_si128 |                               | Copy 32-bit integer a to the lower element of dst, and zero the upper element |
| | _mm_cvtsi64_si128 |                               | Copy 64-bit integer a to the lower element of dst, and zero the upper element |

### Arithmetic
| veclib | SSE2    | Altivec                    | Description |
| --- | ---------- | -------------------------- | ----------- |
| | _mm_add_epi8   | vec_add16sb                | Add 16 8-bit chars |
| | _mm_add_epi16  | vec_add8sh                 | Add 8 16-bit shorts |
| | _mm_add_epi32  | vec_add4sw                 | Add 4 32-bit ints |
| | _mm_add_epi64  | vec_add2sd                 | Add 2 64-bit long longs |
| | _mm_adds_epi8  | vec_addsaturating16sb      | Add 16 8-bit chars with signed saturation |
| | _mm_adds_epu8  | vec_addsaturating16ub      | Add 16 8-bit chars with unsigned saturation |
| | _mm_adds_epi16 | vec_addsaturating8sh       | Add 8 16-bit shorts with signed saturation |
| | _mm_adds_epu16 | vec_addsaturating8uh       | Add 8 16-bit shorts with unsigned saturation |
| | _mm_sub_epi8   | vec_subtract16sb           | Subtract 16 8-bit chars |
| | _mm_sub_epi16  | vec_subtract8sh            | Subtract 8 16-bit shorts |
| | _mm_sub_epi32  | vec_subtract4sw            | Subtract 4 32-bit ints |
| | _mm_sub_epi64  | vec_subtract2sd            | Subtract 2 64-bit long longs |
| | _mm_subs_epi8  | vec_subtractsaturating16sb | Subtract 16 8-bit chars with signed saturation |
| | _mm_subs_epu8  | vec_subtractsaturating16ub | Subtract 16 8-bit chars with unsigned saturation |
| | _mm_subs_epi16 | vec_subtractsaturating8sh  | Subtract 8 16-bit shorts with signed saturation |
| | _mm_subs_epu16 | vec_subtractsaturating8uh  | Subtract 8 16-bit shorts with unsigned saturation |
| | _mm_mul_epu32  | vec_multiplylower2uwto2ud  | Multiply lower 32-bit unsigned ints producing 2 64-bit unsigned long longs |
| | _mm_madd_epi16 | vec_multiply8sh            | Multiply 8 16-bit signed shorts |
| | _mm_mulhi_epi16 | | Multiply the packed 16-bit integers in a and b, producing intermediate 32-bit integers, and store the high 16 bits of the intermediate integers in dst |
| | _mm_mulhi_epu16 | | Multiply the packed unsigned 16-bit integers in a and b, producing intermediate 32-bit integers, and store the high 16 bits of the intermediate integers in dst |
| | _mm_mullo_epi16 | | Multiply the packed 16-bit integers in a and b, producing intermediate 32-bit integers, and store the low 16 bits of the intermediate integers in dst |
| | _mm_avg_epu8   | vec_average16ub            | Average 16 8-bit unsigned chars |
| | _mm_avg_epu16  | vec_average8uh             | Average 8 16-bit unsigned shorts |
| | _mm_max_epi16  | vec_max8sh                 | Max 8 16-bit shorts |
| | _mm_max_epu8   | vec_max16ub                | Max 16 8-bit unsigned chars |
| | _mm_min_epu8   | vec_min16ub                | Min 16 8-bit unsigned chars |
| | _mm_min_epi16  | vec_min8sh                 | Min 8 16-bit shorts |
| |                | vec_sumabsdiffs16ub        | Sum 2 octets of absolute differences of 16 8-bit unsigned chars into 2 64-bit long longs |
| |                | vec_summultiply4sh         | Multiply 4 16-bit shorts then add adjacent pairs with saturation to 4 32-bit ints |
| |                | vec_Abs16sb                | Absolute value 16 8-bit chars |
| |                | vec_Abs8sh                 | Absolute value 8 16-bit shorts |
| |                | vec_Abs4sw                 | Absolute value 4 32-bit ints |
| |                | vec_horizontalAddsaturating8sh | Horizontally add 4+4 adjacent pairs of 16-bit shorts with saturation - (a0+a1, a2+a3, a4+a5, a6+a7, b0+b1, b2+b3, b4+b5, b6+b7) |
| |                | vec_horizontalSub8sh       | Horizontally subtract 4+4 adjacent pairs of 16-bit shorts to 8 16-bit shorts - (a0-a1, a2-a3, a4-a5, a6-a7, b0-b1, b2-b3, b4-b5, b6-b7) |
| |                | vec_partialhorizontalsubtract2sw | Horizontally subtract 2+2 adjacent pairs of 32-bit ints to 4 32-bit ints
| |                | vec_horizontalSubtractsaturating8sh | Horizontally subtract 4+4 adjacent pairs of 16-bit shorts to 8-bit shorts with saturation - (a0+a1, a2+a3, a4+a5, a6+a7, b0+b1, b2+b3, b4+b5, b6+b7) |
| |                | vec_Multiply16sbthenhorizontalAddsaturating8sh | Multiply 16 8-bit u*s chars then add adjacent 16-bit products with signed saturation |
| |                | vec_Multiply8shExtractUpper | Multiply 8 16-bit shorts, shift right 14, add 1 and shift right 1 to 8 16-bit shorts |
| |                | vec_conditionalNegate16sb | Negate 16 8-bit chars when mask is negative, zero when zero, else copy |
| |                | vec_conditionalNegate8sh  | Negate 8 16-bit shorts when mask is negative, zero when zero, else copy |
| |                | vec_conditionalNegate4sw  | Negate 4 32-bit ints when mask is negative, zero when zero, else copy |
| |                | vec_multiply4sw           | Multiply 4 32-bit signed ints |
| |                | vec_Max4sw                | Max 4 32-bit signed ints |
| |                | vec_Min4sw                | Min 4 32-bit signed ints |

### Boolean
| veclib | SSE2     | Altivec             | Description                        |
| --- | ----------- | ------------------- | ---------------------------------- |
| | _mm_and_si128   | vec_bitand1q        | Bitwise 128-bit and                |
| | _mm_andnotsi128 | vec_bitandnotleft1q | Bitwise 128-bit and not (reversed) |
| | _mm_or_si128    | vec_bitor1q         | Bitwise 128-bit or                 |
| | _mm_xor_si128   | vec_bitxor1q        | Bitwise 128-bit xor                |

### Unpack
| veclib | SSE2        | Altivec           | Description                                                  |
| --- | -------------- | ----------------- | ------------------------------------------------------------ |
| | _mm_unpackhi_epi8  | vec_unpackhigh8sb | Unpack 8+8 8-bit chars from high halves and interleave       |
| | _mm_unpackhi_epi16 | vec_unpackhigh4sh | Unpack 4+4 16-bit shorts from high halves and interleave     |
| | _mm_unpacklo_epi8  | vec_unpacklow8sb  | Unpack 8+8 8-bit chars from low halves and interleave        |
| | _mm_unpacklo_epi16 | vec_unpacklow4sh  | Unpack 4+4 16-bit shorts from low halves and interleave      |
| | _mm_unpacklo_epi32 | vec_unpacklow2sw  | Unpack 2+2 32-bit ints from low halves and interleave        |
| | _mm_unpackhi_epi32 | vec_unpackhigh2sw | Unpack 2+2 32-bit ints from high halves and interleave       |
| | _mm_unpacklo_epi64 | vec_unpacklow1sd  | Unpack 1+1 64-bit long longs from low halves and interleave  |
| | _mm_unpackhi_epi64 | vec_unpackhigh1sd | Unpack 1+1 64-bit long longs from high halves and interleave |

### Shift
| veclib | SSE2     | Altivec                             | Description                                       |
| --- | ----------- | ----------------------------------- | ------------------------------------------------- |
| | _mm_sll_epi16   | vec_shiftleft8sh                    | Shift 8 16-bit shorts left logical                |
| | _mm_sll_epi32   | vec_shiftleft4sw                    | Shift 4 32-bit ints left logical                  |
| | _mm_sll_epi64   | vec_shiftleft2sd                    | Shift 2 64-bit long longs left logical            |
| | _mm_slli_epi16  | vec_shiftleftimmediate8sh           | Shift 8 16-bit shorts left logical immediate      |
| | _mm_slli_epi32  | vec_shiftleftimmediate4sw           | Shift 4 32-bit ints left logical immediate        |
| | _mm_slli_epi64  | vec_shiftleftimmediate2sd           | Shift 2 64-bit long longs left logical immediate  |
| | _mm_slli_si128  | vec_shiftleftbytes1q                | Shift 128-bits left logical immediate by bytes    |
| | _mm_srl_epi16   | vec_shiftright8sh                   | Shift 8 16-bit shorts right logical               |
| | _mm_srl_epi32   | vec_shiftright4sw                   | Shift 4 32-bit ints right logical                 |
| | _mm_srl_epi64   | vec_shiftright2sd                   | Shift 2 64-bit long longs right logical           |
| | _mm_srli_epi16  | vec_shiftrightimmediate8sh          | Shift 8 16-bit shorts right logical immediate     |
| | _mm_srli_epi32  | vec_shiftrightimmediate4sw          | Shift 4 32-bit ints right logical immediate       |
| | _mm_srli_epi64  |                                     | Shift 2 64-bit long longs right logical immediate |
| | _mm_srli_si128  | vec_shiftrightbytes1q               | Shift 128-bits right logical immediate by bytes   |
| |                 | vec_shiftrightarithmetic4wimmediate | Shift 4 32-bit ints right arithmetic              |
| |                 | vec_shiftrightlogical2dimmediate    | Shift 2 64-bit long longs right logical immediate |
| |                 | vec_shiftrightarithmetic8himmediate | Shift 8 16-bit shorts right arithmetic            |
| |                 | vec_shiftrightarithmetic4sw         | Shift 4 32-bit ints left logical                  |
| |                 | vec_shiftright2dqw                  | Shift 128+128-bits right into 128-bits            |
| | _mm_bslli_si128 |                                     | Shift a left by imm8 bytes while shifting in zeros, and store the results in dst |
| | _mm_bsrli_si128 |                                     | Shift a right by imm8 bytes while shifting in zeros, and store the results in dst |

### Permute
| veclib | SSE2         | Altivec             | Description                                                            |
| --- | --------------- | ------------------- | ---------------------------------------------------------------------- |
| | _mm_shufflehi_epi16 |                     | Shuffle 16-bit integers in the high 64 bits of a using the control in imm8. Store the results in the high 64 bits of dst, with the low 64 bits being copied from from a to dst |
| | _mm_shufflelo_epi16 | vec_permutelower4sh | Shuffle lower 4 16-bit shorts using mask, leaving upper half unchanged |
| | _mm_shuffle_epi32   | vec_permute4sw      | Shuffle 4 32-bit ints using mask                                       |
| |                     | vec_permute16sb     | Shuffle 16 8-bit chars using mask                                      |

### Compare
| veclib | SSE2     | Altivec           | Description                                   |
| --- | ----------- | ----------------- | --------------------------------------------- |
| | _mm_cmpeq_epi8  | vec_compareeq16sb | Compare 16 8-bit chars for == to vector mask  |
| | _mm_cmpeq_epi16 | vec_compareeq8sh  | Compare 8 16-bit shorts for == to vector mask |
| | _mm_cmpeq_epi32 | vec_compareeq4sw  | Compare 4 32-bit ints for == to vector mask   |
| | _mm_cmplt_epi8  | vec_comparelt16sb | Compare 16 8-bit chars for < to vector mask   |
| | _mm_cmplt_epi16 | vec_comparelt8sh  | Compare 8 16-bit shorts for < to vector mask  |
| | _mm_cmplt_epi32 | vec_comparelt4sw  | Compare 4 32-bit ints for < to vector mask    |
| | _mm_cmpgt_epi8  | vec_comparegt16sb | Compare 16 8-bit chars for > to vector mask   |
| | _mm_cmpgt_epi16 | vec_comparegt8sh  | Compare 8 16-bit shorts for > to vector mask  |
| | _mm_cmpgt_epi32 | vec_comparegt4sw  | Compare 4 32-bit ints for > to vector mask    |

### Cast
| veclib | SSE2      | Altivec          | Description             |
| --- | ------------ | ---------------- | ----------------------- |
| | _mm_castps_si128 | vec_cast4spto1q  | Cast __m128 to __m128i  |
| | _mm_castpd_si128 | vec_Cast2dpto4sw | Cast __m128d to __m128i |
