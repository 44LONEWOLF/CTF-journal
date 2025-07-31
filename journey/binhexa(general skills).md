This challenge is really good if you want to go deeper on binary operation.

I tried to do it the "Using Terminal" way and that's what we are going to talk about.

In the challenge, there are 6 operations required to perform. We will see them one by one.

We will take 1010 and 1010 as examples in this write-up.

**1. Addition and Multiplication of binaries**

On the terminal, you run ``` echo "ibase=2; obase=2; 1010+1010" | bc ```  

  Where - ``` ibase=2 ``` and ``` obase=2 ``` means the input is binary and the output is binary, respectively.
        
  - ``` bc ``` means basic calculator.
If you want to multiply the binaries, you replace the + by *

**2. AND and OR**

This is easier if you know about Logic gates you can read about them [here](https://www.geeksforgeeks.org/physics/logic-gates/).

On the terminal, you can run ``` echo $((2#1010 | 2#1010)) ``` to find the OR value.

On the terminal, you can run ``` echo $((2#1010 & 2#1010)) ``` to find the AND value.

**3. Left and Right Shifts(<< and >>)**

If you convert the binaries to decimal, you definitely can find the shifts.

After converting to decimal, you multiply the number by 2 the power of the bit to find Left Shift and then convert the result to binary.

Shift by - 1 bit -> x2^1 which is 2
         - 2 bit -> x2^2 which is 4
         - 3 bit -> x2^3 which is 8

Let's shift 1001 to the left by 2 bit using this method.

1010 = 10, 10 X 2^2 = 10 X 4 = 40, 40 -> 101000

To do all this just by using terminal, you can run ``` echo $((2#1010 << 2))  # shift left by 2 bits ```
         
Or you can divide it by 2 the power of the bit to find Right Shift and then convert the result to binary.

Shift by - 1 bit -> /2^1 which is 2
         - 2 bit -> /2^2 which is 4
         - 3 bit -> /2^3 which is 8

Let's shift 1001 to the right by 1 bit using this method.

 1010 = 10, 10 / 2^1 = 10 / 2 = 5, 5 -> 101

To do all this just by using terminal, you can run ``` echo $((2#1010 >> 1))  # shift to right by 1 bits ```

### You may not be able to run these command while the program is running. So you can use online converters. 
### But you should definitely know how to perform these. I recommend you to understand what is under the hood.


        
