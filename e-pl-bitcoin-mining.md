<!-- TITLE: ePl Bitcoin Mining -->

<!-- SUBTITLE: A quick summary of ePl Bitcoin Mining -->

# ePL 比特币挖矿（注：SHA256算法的演示）

```java
/******************************************************************************
 *

 * SHA256 Example
 *
 * Name:    SHA256_BTC.epl
 * Desc:    Demo Of SHA256 Algo Using BTC 80 Byte Header and SHA256d 
 *
 * Memory Map:
 *   Round Number:          m[ 10]
 *   Padded Block Header:   u[  0] - u[ 32]
 *   Hash 1 (Midstate):     u[ 40] - u[ 47]
 *   Hash 2 (Final):        u[ 50] - u[ 57]
 *   K:                     u[100] - u[163]
 *   H:                     u[200] - u[208]
 *   w:                     u[300] - u[363]
 *   Temp Variables:        u[400] - u[415]
 *   a-h:                   u[500] - u[507]
 *
 *****************************************************************************/

array_uint 1000;

// s0 := (w[i-15] rightrotate 7) xor (w[i-15] rightrotate 18) xor (w[i-15] rightshift 3)
// s1 := (w[i-2] rightrotate 17) xor (w[i-2] rightrotate 19) xor (w[i-2] rightshift 10)
// w[i] := w[i-16] + s0 + w[i-7] + s1
function init_w {

    // w[i-15]
    u[400] = u[301 + u[99]];

    // w[i-2]
    u[401] = u[314 + u[99]];

    // s0
    u[402] = ((u[400] >>> 7) ^ (u[400] >>> 18) ^ (u[400] >> 3));

    // s1
    u[403] = ((u[401] >>> 17) ^ (u[401] >>> 19) ^ (u[401] >> 10));

    // w[i]
    u[316 + u[99]] = u[300 + u[99]] + u[402] + u[309 + u[99]] + u[403];
}

// S1 := (e rightrotate 6) xor (e rightrotate 11) xor (e rightrotate 25)
// ch := (e and f) xor ((not e) and g)
// temp1 := h + S1 + ch + k[i] + w[i]
// S0 := (a rightrotate 2) xor (a rightrotate 13) xor (a rightrotate 22)
// maj := (a and b) xor (a and c) xor (b and c)
// temp2 := S0 + maj
function compress {

    // S1
    u[410] = ((u[504] >>> 6) ^ (u[504] >>> 11) ^ (u[504] >>> 25));

    // ch
    u[411] = ((u[504] & u[505]) ^ (~u[504] & u[506]));

    // temp1
    u[412] = u[507] + u[410] + u[411] + u[100 + u[99]] + u[300 + u[99]];

    // S0
    u[413] = ((u[500] >>> 2) ^ (u[500] >>> 13) ^ (u[500] >>> 22));

    // maj
    u[414] = ((u[500] & u[501]) ^ (u[500] & u[502]) ^ (u[501] & u[502]));

    // temp2
    u[415] = u[413] + u[414];

    // Update a-h
    u[507] = u[506];
    u[506] = u[505];
    u[505] = u[504];
    u[504] = u[503] + u[412];
    u[503] = u[502];
    u[502] = u[501];
    u[501] = u[500];
    u[500] = u[412] + u[415];
}

function sha256 {

    // Initialize w[16] - w[63] (Use u[99] As Counter)
    repeat(u[99], 48, 48) {
        init_w();
    }

    // Initialize a - h
    u[500] = u[200];    // a
    u[501] = u[201];    // b
    u[502] = u[202];    // c
    u[503] = u[203];    // d
    u[504] = u[204];    // e
    u[505] = u[205];    // f
    u[506] = u[206];    // g
    u[507] = u[207];    // h

    // Compress Current Chunk
    repeat(u[99], 64, 64) {
        compress();
    }

    // Add Compressed Chunk To Hash Value
    u[200] += u[500];
    u[201] += u[501];
    u[202] += u[502];
    u[203] += u[503];
    u[204] += u[504];
    u[205] += u[505];
    u[206] += u[506];
    u[207] += u[507];

}

function main {

    // In this example, the full SHA256d algo is needed to verify results.
    // Also, because the algo is small / fast enough (less than WCET = TBD);
    // the logic does not need to be broken into pieces and the complete logic
    // will fit in the verify function.
    // Therfore, the only logic needed in the main function is to call the
    // verify function.

    
    u[0] = 0x01000000;
    u[1] = 0x00000000;
    u[2] = 0x00000000;
    u[3] = 0x00000000;
    u[4] = 0x00000000;
    u[5] = 0x00000000;
    u[6] = 0x00000000;
    u[7] = 0x00000000;
    u[8] = 0x00000000;
    u[9] = 0x3BA3EDFD;
    u[10] = 0x7A7B12B2;
    u[11] = 0x7AC72C3E;
    u[12] = 0x67768F61;
    u[13] = 0x7FC81BC3;
    u[14] = 0x888A5132;
    u[15] = 0x3A9FB8AA;
    u[16] = 0x4B1E5E4A;
    u[17] = 0x29AB5F49;
    u[18] = 0xFFFF001D;
    u[19] = m[10];  // Nonce = Round Number
    u[20] = 0x80000000;
    u[21] = 0x00000000;
    u[22] = 0x00000000;
    u[23] = 0x00000000;
    u[24] = 0x00000000;
    u[25] = 0x00000000;
    u[26] = 0x00000000;
    u[27] = 0x00000000;
    u[28] = 0x00000000;
    u[29] = 0x00000000;
    u[30] = 0x00000000;
    u[31] = 0x00000280;

    // Reset H Each Round
    init_H();

    // Copy First 512 Bit Chunk To Be Hashed Into w[0] - w[15]
    u[300] = u[0];
    u[301] = u[1];
    u[302] = u[2];
    u[303] = u[3];
    u[304] = u[4];
    u[305] = u[5];
    u[306] = u[6];
    u[307] = u[7];
    u[308] = u[8];
    u[309] = u[9];
    u[310] = u[10];
    u[311] = u[11];
    u[312] = u[12];
    u[313] = u[13];
    u[314] = u[14];
    u[315] = u[15];

    // Hash First 512 Bit Chunk
    sha256();

    // Copy Second 512 Bit Chunk To Be Hashed Into w[0] - w[15]
    u[300] = u[16];
    u[301] = u[17];
    u[302] = u[18];
    u[303] = u[19];
    u[304] = u[20];
    u[305] = u[21];
    u[306] = u[22];
    u[307] = u[23];
    u[308] = u[24];
    u[309] = u[25];
    u[310] = u[26];
    u[311] = u[27];
    u[312] = u[28];
    u[313] = u[29];
    u[314] = u[30];
    u[315] = u[31];

    // Hash Second 512 Bit Chunk
    sha256();

    // Store Hash1 (For Debugging Only)
    u[40] = u[200];
    u[41] = u[201];
    u[42] = u[202];
    u[43] = u[203];
    u[44] = u[204];
    u[45] = u[205];
    u[46] = u[206];
    u[47] = u[207];

    // Reset Hash
    init_H();

    // Copy Hash1 & Padding Into w[0] - w[15]
    u[300] = u[40];
    u[301] = u[41];
    u[302] = u[42];
    u[303] = u[43];
    u[304] = u[44];
    u[305] = u[45];
    u[306] = u[46];
    u[307] = u[47];
    u[308] = 0x80000000;
    u[309] = 0x00000000;
    u[310] = 0x00000000;
    u[311] = 0x00000000;
    u[312] = 0x00000000;
    u[313] = 0x00000000;
    u[314] = 0x00000000;
    u[315] = 0x00000100;

    // Hash Hash1
    sha256();

    // Store Hash2 (For Debugging Only)
    u[50] = u[200];
    u[51] = u[201];
    u[52] = u[202];
    u[53] = u[203];
    u[54] = u[204];
    u[55] = u[205];
    u[56] = u[206];
    u[57] = u[207];

    // Bounty Is Rewarded When h7 Of Hash2 Is Zero
    // For This Example h7 Equals Zero When u[19] = 0x1DAC2B7C;
    verify_bty (u[57] == 0)

    // POW Is Rewarded When The MD5 Hash Of h7, h6, h5 & h4 Is Less Than Target
    verify_pow (u[57], u[56], u[55], u[54])
}

function init_K {
    u[100] = 0x428a2f98;
    u[101] = 0x71374491;
    u[102] = 0xb5c0fbcf;
    u[103] = 0xe9b5dba5;
    u[104] = 0x3956c25b;
    u[105] = 0x59f111f1;
    u[106] = 0x923f82a4;
    u[107] = 0xab1c5ed5;
    u[108] = 0xd807aa98;
    u[109] = 0x12835b01;
    u[110] = 0x243185be;
    u[111] = 0x550c7dc3;
    u[112] = 0x72be5d74;
    u[113] = 0x80deb1fe;
    u[114] = 0x9bdc06a7;
    u[115] = 0xc19bf174;
    u[116] = 0xe49b69c1;
    u[117] = 0xefbe4786;
    u[118] = 0x0fc19dc6;
    u[119] = 0x240ca1cc;
    u[120] = 0x2de92c6f;
    u[121] = 0x4a7484aa;
    u[122] = 0x5cb0a9dc;
    u[123] = 0x76f988da;
    u[124] = 0x983e5152;
    u[125] = 0xa831c66d;
    u[126] = 0xb00327c8;
    u[127] = 0xbf597fc7;
    u[128] = 0xc6e00bf3;
    u[129] = 0xd5a79147;
    u[130] = 0x06ca6351;
    u[131] = 0x14292967;
    u[132] = 0x27b70a85;
    u[133] = 0x2e1b2138;
    u[134] = 0x4d2c6dfc;
    u[135] = 0x53380d13;
    u[136] = 0x650a7354;
    u[137] = 0x766a0abb;
    u[138] = 0x81c2c92e;
    u[139] = 0x92722c85;
    u[140] = 0xa2bfe8a1;
    u[141] = 0xa81a664b;
    u[142] = 0xc24b8b70;
    u[143] = 0xc76c51a3;
    u[144] = 0xd192e819;
    u[145] = 0xd6990624;
    u[146] = 0xf40e3585;
    u[147] = 0x106aa070;
    u[148] = 0x19a4c116;
    u[149] = 0x1e376c08;
    u[150] = 0x2748774c;
    u[151] = 0x34b0bcb5;
    u[152] = 0x391c0cb3;
    u[153] = 0x4ed8aa4a;
    u[154] = 0x5b9cca4f;
    u[155] = 0x682e6ff3;
    u[156] = 0x748f82ee;
    u[157] = 0x78a5636f;
    u[158] = 0x84c87814;
    u[159] = 0x8cc70208;
    u[160] = 0x90befffa;
    u[161] = 0xa4506ceb;
    u[162] = 0xbef9a3f7;
    u[163] = 0xc67178f2;
}

function init_H {
    u[200] = 0x6a09e667;
    u[201] = 0xbb67ae85;
    u[202] = 0x3c6ef372;
    u[203] = 0xa54ff53a;
    u[204] = 0x510e527f;
    u[205] = 0x9b05688c;
    u[206] = 0x1f83d9ab;
    u[207] = 0x5be0cd19;
}
```