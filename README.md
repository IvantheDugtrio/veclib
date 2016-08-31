# veclib
Vector library for porting SSE2 instructions to other architectures

Credit to yhcheng for first providing a PPC64 port of Burrows-Wheeler Aligner which helped me expand on the instructions. 
PPC64 Altivec instructions are provided in vec128int.h

Below are some integer SSE2 instructions I've managed to port to Altivec using IBM's provided veclib-1.0.4
For big-endian platforms only the shift left/right operations are reversed. 

# Integer

Load
| SSE2 | Altivec |
|-----------------|-------------|
| _mm_load_si128 | vec_load1q |
| _mm_loadu_si128 | vec_loadu1q |
| _mm_loadl_epi64 |  |

Set
| SSE2 | Altivec |
|-------------------|---------------|
| _mm_setzero_si128 | vec_zero1q | Set 128 integer bits to zero
| _mm_set1_epi8 | vec_splat16sb | Splat 8-bit char to 16 8-bit chars
| _mm_set1_epi16 | vec_splat8sh | Splat 16-bit short to 8 16-bit shorts
| _mm_set1_epi32 | vec_splat4sw | Splat 32-bit ints to 4 32-bit ints
| _mm_set1_epi64 | vec_splat2sd | Splat 64-bit long long to 2 64-bit long longs
| _mm_set1_epi64x | vec_splat2sd? | Splat 64-bit long long to 2 64-bit long longs
| _mm_set_epi8 | vec_set16sb | Set 16 8-bit chars
| _mm_set_epi16 | vec_set8sh | Set 8 16-bits shorts
| _mm_set_epi32 | vec_set4sw | Set 4 32-bit ints
| _mm_set_epi64 | vec_set2sd | Set 2 64-bit long longs
| _mm_set_epi64x | vec_set2sd? | Set 2 64-bit long longs
|  | vec_setreverse16sb | Set 16 8-bit chars reversed
|  | vec_setreverse8sh | Set 8 16-bit shorts reversed
|  | vec_setreverse4sw | Set 4 32-bit ints reversed
|  | vec_setreverse2sd | Set 2 64-bit long longs reversed
|  | vec_Zerouppersd | Set lower 64-bits of integer data and zero upper part

Store
| SSE2 | Altivec |
|------------------|------------------------|
| _mm_store_si128 | vec_store1q | Store 128-bits integer, aligned
| _mm_storeu_si128 | vec_storeu1q | Store 128-bits integer, unaligned
| _mm_storel_epi64 | vec_storelower1sdof2sd | Store lower 64-bit long long

Insert
| SSE2 | Altivec |
|------------------|---------------|
|  | vec_insert4sw | Inert 32-bit int
|  | vec_insert2sd | Insert 64-bit long long
|  | vec_convert1swto1uq | Insert 32-bit int, zeroing upper
| _mm_insert_epi16 | vec_insert8sh | Insert 16-bit short into one of 8 16-bit shorts
|  | vec_insert16sb | Insert 8-bit unsigned char into one of 16 bytes

Extract
| SSE2 | Altivec |
| ------------------|-------------------------|
|  | vec_extract1swfrom4sw | Extract 32-bit int
|  | vec_extract1sdfrom2sd | Extract 64-bit long long
| _mm_extract_epi16 | vec_extract8sh | Extract 16-bit short from one of 8 16-bit shorts
| _mm_movemask_epi8 | vec_extractupperbit16sb | Extract upper bit of 16 8-bit chars
|  | vec_extractupperbit2dp | Extract upper bit of 2 64-bit doubles
|  | vec_extractlowersw | Extract lower 32-bit int

Convert integer to integer
| SSE2 | Altivec |
|-|-------------------|
|  | vec_packs8hto16sb | Convert 8+8 16-bit shorts to 16 8-bit chars with signed saturation
|  | vec_packs4wto8sh | Convert 4+4 32-bit ints to 8 16-bit shorts with signed saturation

Convert floating-point to integer
| SSE2 | Altivec |
|-|-------------------------------|
|  | vec_converttruncating4spto4sw | Convert 4 32-bit floats to 4 32-bit ints with truncation
|  | vec_convert4spto4sw | Convert 4 32-bit floats to 4 32-bit ints
|  | vec_Convert2dpto2sw | Convert 2 64-bit doubles to 2 32-bit ints with truncation

Arithmetic
| SSE2 | Altivec |
|----------------|-------------------------------|
| _mm_add_epi8 | vec_add16sb | Add 16 8-bit chars
| _mm_add_epi16 | vec_add8sh | Add 8 16-bit shorts
| _mm_add_epi32 | vec_add4sw | Add 4 32-bit ints
| _mm_add_epi64 | vec_add2sd | Add 2 64-bit long longs
| _mm_adds_epi8 | vec_addsaturating16sb | Add 16 8-bit chars with signed saturation
|  | vec_addsaturating16ub | Add 16 8-bit chars with unsigned saturation
| _mm_adds_epi16 | vec_addsaturating8sh | Add 8 16-bit shorts with signed saturation
|  | vec_addsaturating8uh | Add 8 16-bit shorts with unsigned saturation
| _mm_sub_epi8 | vec_subtract16sb | Subtract 16 8-bit chars
| _mm_sub_epi16 | vec_subtract8sh | Subtract 8 16-bit shorts
| _mm_sub_epi32 | vec_subtract4sw | Subtract 4 32-bit ints
| _mm_sub_epi64 | vec_subtract2sd | Subtract 2 64-bit long longs
| _mm_subs_epi8 | vec_subtractsaturating16sb | Subtract 16 8-bit chars with signed saturation
|  | vec_subtractsaturating16ub | Subtract 16 8-bit chars with unsigned saturation
| _mm_subs_epi16 | vec_subtractsaturating8sh | Subtract 8 16-bit shorts with signed saturation
|  | vec_subtractsaturating8uh | Subtract 8 16-bit shorts with unsigned saturation
|  | vec_multiplylower2uwto2ud | Multiply lower 32-bit unsigned ints producing 2 64-bit unsigned long longs
|  | vec_multiply8sh | Multiply 8 16-bit signed shorts
| _mm_avg_epu8 | vec_average16ub | Average 16 8-bit unsigned chars
| _mm_avg_epu16 | vec_average8uh | Average 8 16-bit unsigned shorts
| _mm_max_epi16| vec_max8sh | Max 8 16-bit shorts
| _mm_max_epu8 | vec_max16ub | Max 16 8-bit unsigned chars
| _mm_min_epu8 | vec_min16ub | Min 16 8-bit unsigned chars
| _mm_min_epi16 | vec_min8sh | Min 8 16-bit shorts
|  | vec_sumabsdiffs16ub | Sum 2 octets of absolute differences of 16 8-bit unsigned chars into 2 64-bit long longs
|  | vec_summultiply4sh | Multiply 4 16-bit shorts then add adjacent pairs with saturation to 4 32-bit ints
|  | vec_Abs16sb | Absolute value 16 8-bit chars
|  | vec_Abs8sh | Absolute value 8 16-bit shorts
|  | vec_Abs4sw | Absolute value 4 32-bit ints
TODO
