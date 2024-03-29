<!-- TITLE: ePl Sample -->

<!-- SUBTITLE: A quick summary of ePl Sample -->

# #ePL 示例

```java
/******************************************************************************
 *

 * Data Type Declarations
 *
 *  - At least 1 declaration is required
 *  - Each data type can only be declared at most once
 *  - Total size of declared memory usage cannot exceed 1MB
 *
 *  array_int    <number of ints>;
 *  array_uint   <number of uints>;
 *  array_long   <number of longs>;
 *  array_ulong  <number of ulongs>;
 *  array_float  <number of floats>;
 *  array_double <number of double>;
 *
 *****************************************************************************/

array_uint   1000;


/******************************************************************************
 *

 * Storage Declarations
 *
 *  - Storage declarations are optional
 *  - The Unsigned Int array must be declared before declaring storage
 *  - If used, both storage_sz & storage_idx are required and must fit
 *    within the u[] size.
 *
 *  submit_sz <number of unsigned ints to store>;
 *  submit_idx <starting index of u[] to extract storage from>;
 *
 *****************************************************************************/

submit_sz 32;
submit_idx 0;


/******************************************************************************
 *

 * User Defined Functions
 *
 *  - Optional
 *  - Names can be made up of numbers (0-9), letters (a-z), and underscores '_'
 *  - Names cannot begin with any reserved word in the ElasticPL language
 *
 *  function <function name> { }
 *
 *****************************************************************************/

function example_function_1 {
  // Add some code here - This is just dummy code that creates some random #s
  repeat (u[100], 32, 32) {               // Repeat 32 times using u[100] as loop counter
    u[0] = ( m[4] <<< u[100] ) ^ u[100];  // m[4] - m[8] have random unsigned ints
  }
}

function example_function_2 {
  // Add some code here - This is just dummy code that adds a random #
  repeat (u[100], 32, 32) {  // Repeat 32 times using u[100] as loop counter
    u[0] = u[0] + m[5];      // m[5] is a random unsigned int
  }
}


/******************************************************************************
 *

 * Main Function
 *
 *  - Main Function is required
 *  - Must call verify() or contain verify_bty and verify_pow
 *  - This is the entry point for the ElasticPL job
 *
 *  function main { }
 *
 *****************************************************************************/

function main {

  // When using storage you may want to do some special processing on Round 0
  if( m[1] == 0 ) { // m[1] contains the Round Number
    example_function_1();
  }
  else {
    // Add some code here - Just copies storage to u[0] - u[31]
    repeat (u[100], 32, 32) {  // Repeat 32 times using u[100] as loop counter
      u[0] = s[u[100]];        // s[0] - s[31] contain the stored values
    }
  }

  // Call an example function
  example_function_2();

  verify();

}


/******************************************************************************
 *

 * Verify Function
 *
 *  - Verify Function contains the logic to determine if a Bounty Solution
 *    has been found, and is required
 *  - It must contain a verify_bty and a verify_pow calls
 *
 *  function verify { }
 *
 *****************************************************************************/

function verify {
  u[200] = u[0] + u[1];
  verify_bty(u[200] < 1000);
  verify_pow(u[0], u[1], u[2], u[3]);

}
```