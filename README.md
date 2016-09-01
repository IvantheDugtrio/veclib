# veclib
Vector library for porting SSE2 instructions to other architectures

Credit to yhcheng for first providing a PPC64 port of the Burrows-Wheeler Aligner which helped me expand on the instructions. 
PPC64 Altivec instructions are provided in vec128int.h

Below are some integer SSE2 instructions I've managed to port to Altivec using IBM's provided veclib-1.0.4
For big-endian platforms only the shift left/right operations are reversed. 

## Vector Integer Operations

### Load
| SSE2            | Altivec     | Description                              |
| --------------- | ----------- | ---------------------------------------- |
| _mm_load_si128  | vec_load1q  | Load 128 bits of integer data, aligned   |
| _mm_loadu_si128 | vec_loadu1q | Load 128 bits of integer data, unaligned |
| _mm_loadl_epi64 |             |                                          |

### Set
| SSE2              | Altivec            | Description                                           |
| ----------------- | ------------------ | ----------------------------------------------------- |
| _mm_setzero_si128 | vec_zero1q         | Set 128 integer bits to zero                          |
| _mm_set1_epi8     | vec_splat16sb      | Splat 8-bit char to 16 8-bit chars                    |
| _mm_set1_epi16    | vec_splat8sh       | Splat 16-bit short to 8 16-bit shorts                 |
| _mm_set1_epi32    | vec_splat4sw       | Splat 32-bit ints to 4 32-bit ints                    |
| _mm_set1_epi64    | vec_splat2sd       | Splat 64-bit long long to 2 64-bit long longs         |
| _mm_set1_epi64x   | vec_splat2sd?      | Splat 64-bit long long to 2 64-bit long longs         |
| _mm_set_epi8      | vec_set16sb        | Set 16 8-bit chars                                    |
| _mm_set_epi16     | vec_set8sh         | Set 8 16-bits shorts                                  |
| _mm_set_epi32     | vec_set4sw         | Set 4 32-bit ints                                     |
| _mm_set_epi64     | vec_set2sd         | Set 2 64-bit long longs                               |
|                   | vec_setreverse16sb | Set 16 8-bit chars reversed                           |
|                   | vec_setreverse8sh  | Set 8 16-bit shorts reversed                          |
|                   | vec_setreverse4sw  | Set 4 32-bit ints reversed                            |
|                   | vec_setreverse2sd  | Set 2 64-bit long longs reversed                      |
|                   | vec_Zerouppersd    | Set lower 64-bits of integer data and zero upper part |

### Store
| SSE2             | Altivec                | Description                       |
| ---------------- | ---------------------- | --------------------------------- |
| _mm_store_si128  | vec_store1q            | Store 128-bits integer, aligned   |
| _mm_storeu_si128 | vec_storeu1q           | Store 128-bits integer, unaligned |
| _mm_storel_epi64 | vec_storelower1sdof2sd | Store lower 64-bit long long      |

### Insert
| SSE2             | Altivec             | Description                                     |
| ---------------- | ------------------- | ----------------------------------------------- |
|                  | vec_insert4sw       | Inert 32-bit int                                |
|                  | vec_insert2sd       | Insert 64-bit long long                         |
|                  | vec_convert1swto1uq | Insert 32-bit int, zeroing upper                |
| _mm_insert_epi16 | vec_insert8sh       | Insert 16-bit short into one of 8 16-bit shorts |
|                  | vec_insert16sb      | Insert 8-bit unsigned char into one of 16 bytes |

### Extract
| SSE2              | Altivec                 | Description                                      |
| ----------------- | ----------------------- | ------------------------------------------------ |
|                   | vec_extract1swfrom4sw   | Extract 32-bit int                               |
|                   | vec_extract1sdfrom2sd   | Extract 64-bit long long                         |
| _mm_extract_epi16 | vec_extract8sh          | Extract 16-bit short from one of 8 16-bit shorts |
| _mm_movemask_epi8 | vec_extractupperbit16sb | Extract upper bit of 16 8-bit chars              |
|                   | vec_extractupperbit2dp  | Extract upper bit of 2 64-bit doubles            |
|                   | vec_extractlowersw      | Extract lower 32-bit int                         |

### Convert integer to integer
| SSE2 | Altivec           | Description                                                        |
| ---- | ----------------- | ------------------------------------------------------------------ |
|      | vec_packs8hto16sb | Convert 8+8 16-bit shorts to 16 8-bit chars with signed saturation |
|      | vec_packs4wto8sh  | Convert 4+4 32-bit ints to 8 16-bit shorts with signed saturation  |

### Convert floating-point to integer
| SSE2 | Altivec                       | Description                                               |
| ---- | ----------------------------- | --------------------------------------------------------- |
|      | vec_converttruncating4spto4sw | Convert 4 32-bit floats to 4 32-bit ints with truncation  |
|      | vec_convert4spto4sw           | Convert 4 32-bit floats to 4 32-bit ints                  |
|      | vec_Convert2dpto2sw           | Convert 2 64-bit doubles to 2 32-bit ints with truncation |

### Arithmetic
| SSE2           | Altivec                    | Description |
| -------------- | -------------------------- | ------------------------------------------------------------------------------------------- |
| _mm_add_epi8   | vec_add16sb                | Add 16 8-bit chars |
| _mm_add_epi16  | vec_add8sh                 | Add 8 16-bit shorts |
| _mm_add_epi32  | vec_add4sw                 | Add 4 32-bit ints |
| _mm_add_epi64  | vec_add2sd                 | Add 2 64-bit long longs |
| _mm_adds_epi8  | vec_addsaturating16sb      | Add 16 8-bit chars with signed saturation |
|                | vec_addsaturating16ub      | Add 16 8-bit chars with unsigned saturation |
| _mm_adds_epi16 | vec_addsaturating8sh       | Add 8 16-bit shorts with signed saturation |
|                | vec_addsaturating8uh       | Add 8 16-bit shorts with unsigned saturation |
| _mm_sub_epi8   | vec_subtract16sb           | Subtract 16 8-bit chars |
| _mm_sub_epi16  | vec_subtract8sh            | Subtract 8 16-bit shorts |
| _mm_sub_epi32  | vec_subtract4sw            | Subtract 4 32-bit ints |
| _mm_sub_epi64  | vec_subtract2sd            | Subtract 2 64-bit long longs |
| _mm_subs_epi8  | vec_subtractsaturating16sb | Subtract 16 8-bit chars with signed saturation |
|                | vec_subtractsaturating16ub | Subtract 16 8-bit chars with unsigned saturation |
| _mm_subs_epi16 | vec_subtractsaturating8sh  | Subtract 8 16-bit shorts with signed saturation |
|                | vec_subtractsaturating8uh  | Subtract 8 16-bit shorts with unsigned saturation |
|                | vec_multiplylower2uwto2ud  | Multiply lower 32-bit unsigned ints producing 2 64-bit unsigned long longs |
|                | vec_multiply8sh            | Multiply 8 16-bit signed shorts |
| _mm_avg_epu8   | vec_average16ub            | Average 16 8-bit unsigned chars |
| _mm_avg_epu16  | vec_average8uh             | Average 8 16-bit unsigned shorts |
| _mm_max_epi16  | vec_max8sh                 | Max 8 16-bit shorts |
| _mm_max_epu8   | vec_max16ub                | Max 16 8-bit unsigned chars |
| _mm_min_epu8   | vec_min16ub                | Min 16 8-bit unsigned chars |
| _mm_min_epi16  | vec_min8sh                 | Min 8 16-bit shorts |
|                | vec_sumabsdiffs16ub        | Sum 2 octets of absolute differences of 16 8-bit unsigned chars into 2 64-bit long longs |
|                | vec_summultiply4sh         | Multiply 4 16-bit shorts then add adjacent pairs with saturation to 4 32-bit ints |
|                | vec_Abs16sb                | Absolute value 16 8-bit chars |
|                | vec_Abs8sh                 | Absolute value 8 16-bit shorts |
|                | vec_Abs4sw                 | Absolute value 4 32-bit ints |
|                | vec_horizontalAddsaturating8sh | Horizontally add 4+4 adjacent pairs of 16-bit shorts with saturation - (a0+a1, a2+a3, a4+a5, a6+a7, b0+b1, b2+b3, b4+b5, b6+b7) |
|                | vec_horizontalSub8sh       | Horizontally subtract 4+4 adjacent pairs of 16-bit shorts to 8 16-bit shorts - (a0-a1, a2-a3, a4-a5, a6-a7, b0-b1, b2-b3, b4-b5, b6-b7) |
|                | vec_partialhorizontalsubtract2sw | Horizontally subtract 2+2 adjacent pairs of 32-bit ints to 4 32-bit ints
|                | vec_horizontalSubtractsaturating8sh | Horizontally subtract 4+4 adjacent pairs of 16-bit shorts to 8-bit shorts with saturation - (a0+a1, a2+a3, a4+a5, a6+a7, b0+b1, b2+b3, b4+b5, b6+b7) |
|                | vec_Multiply16sbthenhorizontalAddsaturating8sh | Multiply 16 8-bit u*s chars then add adjacent 16-bit products with signed saturation |
|                | vec_Multiply8shExtractUpper | Multiply 8 16-bit shorts, shift right 14, add 1 and shift right 1 to 8 16-bit shorts |
|                | vec_conditionalNegate16sb | Negate 16 8-bit chars when mask is negative, zero when zero, else copy |
|                | vec_conditionalNegate8sh  | Negate 8 16-bit shorts when mask is negative, zero when zero, else copy |
|                | vec_conditionalNegate4sw  | Negate 4 32-bit ints when mask is negative, zero when zero, else copy |
|                | vec_multiply4sw           | Multiply 4 32-bit signed ints |
|                | vec_Max4sw                | Max 4 32-bit signed ints |
|                | vec_Min4sw                | Min 4 32-bit signed ints |

### Boolean
| SSE2 | Altivec | Description |
| --- | --- | --- |
| _mm_and_si128 | vec_bitand1q | Bitwise 128-bit and |
| _mm_andnotsi128 | vec_bitandnotleft1q | Bitwise 128-bit and not (reversed) |
| _mm_or_si128 | vec_bitor1q | Bitwise 128-bit or |
| _mm_xor_si128 | vec_bitxor1q | Bitwise 128-bit xor |

### Unpack
| SSE2 | Altivec | Description |
| --- | --- | --- |
|     | vec_unpackhigh8sb | Unpack 8+8 8-bit chars from high halves and interleave |
|     | vec_unpackhigh4sh | Unpack 4+4 16-bit shorts from high halves and interleave |
|     | vec_unpacklow8sb | Unpack 8+8 8-bit chars from low halves and interleave |
|     | vec_unpacklow4sh | Unpack 4+4 16-bit shorts from low halves and interleave |
|     | vec_unpacklow2sw | Unpack 2+2 32-bit ints from low halves and interleave |
|     | vec_unpackhigh2sw | Unpack 2+2 32-bit ints from high halves and interleave |
|     | vec_unpacklow1sd | Unpack 1+1 64-bit long longs from low halves and interleave |
|     | vec_unpackhigh1sd | Unpack 1+1 64-bit long longs from high halves and interleave |

### Shift
| SSE2 | Altivec | Description |
| --- | --- | --- |
|                | vec_shiftleft8sh | Shift 8 16-bit shorts left logical |
|                | vec_shiftleft4sh | Shift 4 32-bit ints left logical |
|                | vec_shiftleft2sw | Shift 2 64-bit long longs left logical |
| _mm_slli_epi16 | vec_shiftleftimmeidate8sh | Shift 8 16-bit shorts left logical immediate |
| _mm_slli_epi32 | vec_shiftleftimmediate4sw | Shift 4 32-bit ints left logical immediate |
| _mm_slli_epi64 | vec_shiftleftimmediate2sd | Shift 2 64-bit long longs left logical immediate |
| _mm_slli_si128 | vec_shiftleftbytes1q | Shift 128-bits left logical immediate by bytes |
|                | vec_shiftright2sd | Shift 2 64-bit long longs right logical immediate |
| _mm_srli_epi16 | vec_shiftrightimmediate8sh | Shift 8 16-bit shorts right logical immediate |
| _mm_srli_epi32 | vec_shiftrightimmediate4sw | Shift 4 32-bit ints right logical immediate |
| _mm_srli_si128 | vec_shiftrightbytes1q | Shift 128-bits right logical immediate by bytes |
|                | vec_shiftrightarithmetic4wimmediate | Shift 4 32-bit ints right arithmetic |
|                | vec_shiftrightlogical2dimmediate | Shift 2 64-bit long longs right logical immediate |
|                | vec_shiftrightarithmetic8himmediate | Shift 8 16-bit shorts right arithmetic |
|                | vec_shiftrightarithmetic4sw | Shift 4 32-bit ints left logical |
|                | vec_shiftright2dqw | Shift 128+128-bits right into 128-bits |

### Permute
| SSE2 | Altivec | Description |
| --- | --- | --- |
|                   | vec_permutelower4sh | Shuffle lower 4 16-bit shorts using mask, leaving upper half unchanged |
| _mm_shuffle_epi32 | vec_permute4sw | Shuffle 4 32-bit ints using mask |
|                   | vec_permute16sb | Shuffle 16 8-bit chars using mask |

### Compare
| SSE2 | Altivec | Description |
| --- | --- | --- |
| _mm_cmpeq_epi8  | vec_compareeq16sb | Compare 16 8-bit chars for == to vector mask |
| _mm_cmpeq_epi16 | vec_compareeq8sh | Compare 8 16-bit shorts for == to vector mask |
| _mm_cmpeq_epi32 | vec_compareeq4sw | Compare 4 32-bit ints for == to vector mask |
| _mm_cmplt_epi8  | vec_comparelt16sb | Compare 16 8-bit chars for < to vector mask |
| _mm_cmplt_epi16 | vec_comparelt8sh | Compare 8 16-bit shorts for < to vector mask |
| _mm_cmplt_epi32 | vec_comparelt4sw | Compare 4 32-bit ints for < to vector mask |
| _mm_cmpgt_epi8  | vec_comparegt16sb | Compare 16 8-bit chars for > to vector mask |
| _mm_cmpgt_epi16 | vec_comparegt8sh | Compare 8 16-bit shorts for > to vector mask |
| _mm_cmpgt_epi32 | vec_comparegt4sw | Compare 4 32-bit ints for > to vector mask |

### Cast
| SSE2 | Altivec | Description |
| --- | --- | --- |
|                 | vec_cast4spto1q | Cast __m128 to __m128i |
|                 | vec_Cast2dpto4sw | Cast __m128d to __m128i |
