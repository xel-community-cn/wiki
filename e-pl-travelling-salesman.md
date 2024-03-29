<!-- TITLE: ePl Travelling Salesman -->

<!-- SUBTITLE: A quick summary of ePl Travelling Salesman -->

# ePL旅行推销员问题（TSP）

```java
/******************************************************************************
 *

 * Travelling Salesman Problem - Demo 1
 *
 * Name:        TSP_ATT48_BF
 * Desc:        48 Captitals of the US
 * Diminsions:  48
 * Weight Type: ATT - Pseudo-Euclidean distance
 * Algorithm:   Brute Force
 *
 * Memory Map:
 *   Round Number:          m[ 10]
 *   Random Input:          m[  0]
 *   Cost Matrix Vars:      u[ 20] - u[ 23]
 *   X Dist & Y Dist:       i[  0] - i[  1]
 *   Distance Point A to B  f[  0]
 *   Algorithm Variables:   u[ 30] - u[ 34]
 *   Calculated Distance:   u[ 40]
 *   Loop Counters:         u[ 90] - u[ 91]
 *   Point Data:            u[100] - u[195]
 *   Path Data:             u[200] - u[248]
 *   Cost Matrix:           u[300] - u[2602]
 *
 *****************************************************************************/

array_int   2;
array_uint  2700;
array_float 1;

submit_sz  49;
submit_idx 200;


// Initialize VM Memory With TSP Point Data
function init_point_data {

    // Each TSP Point Has An X & Y Value
    // For Example, Point 1 Has X = u[100] & Y = u[101]
    u[100] = 6734;
    u[101] = 1453;
    u[102] = 2233;
    u[103] = 10;
    u[104] = 5530;
    u[105] = 1424;
    u[106] = 401;
    u[107] = 841;
    u[108] = 3082;
    u[109] = 1644;
    u[110] = 7608;
    u[111] = 4458;
    u[112] = 7573;
    u[113] = 3716;
    u[114] = 7265;
    u[115] = 1268;
    u[116] = 6898;
    u[117] = 1885;
    u[118] = 1112;
    u[119] = 2049;
    u[120] = 5468;
    u[121] = 2606;
    u[122] = 5989;
    u[123] = 2873;
    u[124] = 4706;
    u[125] = 2674;
    u[126] = 4612;
    u[127] = 2035;
    u[128] = 6347;
    u[129] = 2683;
    u[130] = 6107;
    u[131] = 669;
    u[132] = 7611;
    u[133] = 5184;
    u[134] = 7462;
    u[135] = 3590;
    u[136] = 7732;
    u[137] = 4723;
    u[138] = 5900;
    u[139] = 3561;
    u[140] = 4483;
    u[141] = 3369;
    u[142] = 6101;
    u[143] = 1110;
    u[144] = 5199;
    u[145] = 2182;
    u[146] = 1633;
    u[147] = 2809;
    u[148] = 4307;
    u[149] = 2322;
    u[150] = 675;
    u[151] = 1006;
    u[152] = 7555;
    u[153] = 4819;
    u[154] = 7541;
    u[155] = 3981;
    u[156] = 3177;
    u[157] = 756;
    u[158] = 7352;
    u[159] = 4506;
    u[160] = 7545;
    u[161] = 2801;
    u[162] = 3245;
    u[163] = 3305;
    u[164] = 6426;
    u[165] = 3173;
    u[166] = 4608;
    u[167] = 1198;
    u[168] = 23;
    u[169] = 2216;
    u[170] = 7248;
    u[171] = 3779;
    u[172] = 7762;
    u[173] = 4595;
    u[174] = 7392;
    u[175] = 2244;
    u[176] = 3484;
    u[177] = 2829;
    u[178] = 6271;
    u[179] = 2135;
    u[180] = 4985;
    u[181] = 140;
    u[182] = 1916;
    u[183] = 1569;
    u[184] = 7280;
    u[185] = 4899;
    u[186] = 7509;
    u[187] = 3239;
    u[188] = 10;
    u[189] = 2676;
    u[190] = 6807;
    u[191] = 2993;
    u[192] = 5185;
    u[193] = 3258;
    u[194] = 3023;
    u[195] = 1942;

}

// Create TSP Cost Matrix
// This Creates A 48x48 Matrix With The Cost (Distance) Between Each Point
function create_cost_matrix {

    u[20] = 100;    // Index of x[i] - Point A (Start At Point 0) 
    u[21] = 100;    // Index of y[i] - Point B (Start At Point 0) 
    u[22] = 300;    // Index of dij - Cost Matrix Element
    u[23] = 0;      // tij - Rounded Distance Between A&B
    i[0] = 0;       // xd - X Distance
    i[1] = 0;       // yd - Y Distance
    f[0]  = 0;      // rij - Pseudo-Euclidean Distance Between A &B

    // Outer Loop - Point A = 0 to 48
    // u[90] is the loop counter
    // 48 is # of iterations
    // 48 is # of iterations for WCET Calc
    repeat(u[90], 48, 48) {

        // Inner Loop - Point B = 0 to 48
        // u[91] is the loop counter
        repeat(u[91], 48, 48) {

            // When Point A = Point B, Distance = 0
            if(u[21] == u[20]) {
                u[21] += 2;   // Move To Next Point B
                u[u[22]] = 0; // Distance To Self Is Zero
                u[22]++;      // Move To Next Matrix Element
                continue;
            }

            // Calculate Distance From Point A To Point B
            i[0] = u[u[21]] - u[u[20]];                             // xd
            i[1] = u[u[21] + 1] - u[u[20] + 1];                     // yd
            f[0] = sqrt(((i[0]*i[0]) + (i[1]*i[1])) / 10.0);        // rij
            u[23] = (f[0] + 0.5);                                   // tij - equivalent of nint function
            if (u[23]< f[0])
                u[u[22]] = u[23] + 1;                               // dij
            else
                u[u[22]] = u[23];                                   // dij

            u[21] += 2;     // Move To Next Point B
            u[22]++;        // Move To Next Matrix Element
        }

        u[20] += 2;         // Increment Point A
        u[21] = 100;        // Reset Point B
    }

}

function main {

    init_point_data();
    create_cost_matrix();

    // Initialize The Path ( Points 0 To 48 In Sequential Order)
    // Path Variables Start At u[200]
    // u[90] is the loop counter
    repeat(u[90], 48, 48) {
        u[200 + u[90]] = u[90];
    }

    // Randomize The Path
    u[200] = 0;                             // Start At Point Zero
    u[248] = 0;                             // End At Point Zero
    u[30] = 47;                             // Path Array Offset - Start At Final Point In Path
    repeat(u[90], 47, 47) {
        u[31] = (abs(m[0]) % u[30]) + 1;    // Use m[0] to create a random number between 0 and 47
        u[32] = u[200 + u[31]];             // Index of first point in path to swap
        u[200 + u[31]] = u[200 + u[30]];    // Index of second point in path to swap
        u[200 + u[30]] = u[32];             // Swap points
        u[30]--;                            // Move to next point in path
    }

    // Brute Force Logic
    u[40] = 0;  // Total Distance
    repeat(u[90], 48, 48) {
        u[33] = u[200 + u[90]];                     // Cost Matrix Row
        u[34] = u[200 + u[90] + 1];                 // Cost Matrix Column
        u[40] += u[(300 + (u[33] * 48) + u[34])];   // Lookup Point A to Point B distance in Cost Matrix and add to total distance
    }

    // Bounty Is Rewarded When Total Distance &lt; 11,000
    verify_bty (u[40] <11000);

    // POW Is Rewarded When The MD5 Hash Of Summed Path Is Less Than Target
    u[50] = u[200] + u[201] + u[202] + u[203] + u[204] + u[205] + u[206] + u[207] + u[208] + u[209] + u[210] + u[211];
    u[51] = u[212] + u[213] + u[214] + u[215] + u[216] + u[217] + u[218] + u[219] + u[220] + u[221] + u[222] + u[223];
    u[52] = u[224] + u[225] + u[226] + u[227] + u[228] + u[229] + u[230] + u[231] + u[232] + u[233] + u[234] + u[235];
    u[53] = u[236] + u[237] + u[238] + u[239] + u[240] + u[241] + u[242] + u[243] + u[244] + u[245] + u[246] + u[247];
    verify_pow (u[50], u[51], u[52], u[53]);

}

// In this example, the miner submits the solution (Path that meets Bounty or POW criteria)
// Therefore, there is no need for the randomization logic in the verify function.
// u[200]-u[248] is updates with the submitted path and the solution is validated by the node.
function verify {

    // No need to create a cost matrix for the verify function
    // As we only need the distances for the submitted path
    init_point_data();

    // Total Distance
    u[40] = 0;

    // Loop through submited path to calculate total distance
    repeat(u[90], 48, 48) {

        u[20] = u[200 + u[90]];         // Set Point A
        u[21] = u[201 + u[90]];         // Set Point B      

        // Calculate Distance From Point A To Point B
        i[0] = u[100 + (u[21] * 2)] - u[100 + (u[20] * 2)];     // xd
        i[1] = u[101 + (u[21] * 2)] - u[101 + (u[20] * 2)];     // yd
        f[0] = sqrt(((i[0]*i[0]) + (i[1]*i[1])) / 10.0);        // rij
        u[23] = (f[0] + 0.5);                                   // tij - equivalent of nint function
        if (u[23] &lt; f[0])
            u[40] += u[23] + 1;                                 // dij
        else
            u[40] += u[23];                                     // dij

    }

    // Bounty Is Rewarded When Total Distance &lt; 11,000
    verify_bty (u[40] &lt; 11000);

    // POW Is Rewarded When The MD5 Hash Of Summed Path Is Less Than Target
    u[50] = u[200] + u[201] + u[202] + u[203] + u[204] + u[205] + u[206] + u[207] + u[208] + u[209] + u[210] + u[211];
    u[51] = u[212] + u[213] + u[214] + u[215] + u[216] + u[217] + u[218] + u[219] + u[220] + u[221] + u[222] + u[223];
    u[52] = u[224] + u[225] + u[226] + u[227] + u[228] + u[229] + u[230] + u[231] + u[232] + u[233] + u[234] + u[235];
    u[53] = u[236] + u[237] + u[238] + u[239] + u[240] + u[241] + u[242] + u[243] + u[244] + u[245] + u[246] + u[247];
    verify_pow (u[50], u[51], u[52], u[53]);

}
```