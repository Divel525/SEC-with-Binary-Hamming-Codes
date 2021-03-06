# SEC-with-Binary-Hamming-Codes
Single-error-correction of messages with binary Hamming Code in Quartus VHDL

## Table of Contents
- [Objective](#objective)
- [What's included](#whats-included)
- [Theoretical Approach](#theoretical-approach)
    - [Encoder](#encoder)
    - [Decoder](#decoder)
- [Implementation](#implementation)
    - [Encoder block diagram](#encoder-block-diagram)
    - [Decoder block diagram](#decoder-block-diagram)
- [Component Simulation](#component-simulation)
    - [Encoder simulation](#encoder-simulation)
    - [Control Unit simulation](#control-unit-simulation)
    - [Decoder simulation](#decoder-simulation)
- [Improvements](#improvements)
- [Tools](#tools)
- [Testing](#testing)
- [Creators](#creators)

## Objective

This is the first assignment of the Advanced Computer Architecture (ACA) course lectured by professor António Borges.

The objective of the project involves the implementation of the Hamming code for 1-bit error correction with the triplet [15, 11, 3], where:
- n (number of bits of the encoded message) = 15
- k (number of information bits) = 11
- d (Hamming distance) = 3.

And in order to closely simulate and describe the hardware, the structural modeling style was adopted.

The module:

- encoder is implemented with a sequential digital circuit, from the schematics given by the professor
- decoder is implemented with a combinatorial digital circuit

## What's included
```
Decoder/
    +-- and_2.vhd 	
    +-- decoder.vhd 	
    +-- decoder.vwf
    +-- xnor_2.vhd
    +-- xor_2.vhd
Encoder/
    +-- Flip_flop_bundle.vhd 	
    +-- and_2.vhd 	
    +-- and_4.vhd
    +-- control_unit.vhd 	
    +-- control_unit.vwf
    +-- counter_4bit.vhd 	
    +-- counter_4bits.vhd 	
    +-- encoder.vhd
    +-- encoder.vwf
    +-- flipFlopDSimul.vhd 	
    +-- mux2_1.vhd 	
    +-- mux4_1.vhd 	
    +-- nand_2.vhd 	
    +-- not_1.vhd 	
    +-- or_2.vhd 	
    +-- rom.vhd
    +-- rom.vwf 	
    +-- xnor_2.vhd
    +-- xor_2.vhd
```

## Theoretical Approach

The values for the *n* and *k* variables are calculated based on the number of redundant bits *r* = 4 addressed by the problem:

- length of the encoded message:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![f1]

- length of the message:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![f2]

[f1]: https://chart.apis.google.com/chart?cht=tx&chl=n=2^r-1
[f2]: https://chart.apis.google.com/chart?cht=tx&chl=k=n-r

### Encoder

The encoding process of a message is described by the formula:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![f3]

where:

- *x* is the encoded message
- *m* is the message to be encoded
- *G* is the *k* x *n* generator matrix

To get the generator matrix, the following formula was used:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![f4]

where:
- ![f5] is the *k* x *k* identity matrix

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![id_matrix_Ik]

- matrix A is composed of all permutations, of the redundant bits, with 2 or more bits equal to '1' (each column represents one permutation). The permutation order must be the same across both the encoder and decoder:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![matrix_a]

- the transpose of matrix A can now be obtained:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![matrix_a_transpose]

Concatenating both matrices results in the generator matrix used to produce the encoded message:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![g_matrix]

The multiplication of the message by the last four columns of the G matrix resolves the 4 redundant bits. This operation is the same as using the four parity equations obtained from the A matrix:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![parity_check_matrix_get_bits]

[f3]: https://chart.apis.google.com/chart?cht=tx&chl=x=m*G
[f4]: https://chart.apis.google.com/chart?cht=tx&chl=G=[I_k|A^T]
[f5]: https://chart.apis.google.com/chart?cht=tx&chl=I_k
[id_matrix_Ik]: https://github.com/Diogo525/SEC-with-Binary-Hamming-Codes/blob/master/images/identity_matrix_Ik.png
[matrix_a]: https://github.com/Diogo525/SEC-with-Binary-Hamming-Codes/blob/master/images/matrix_A.png
[matrix_a_transpose]: https://github.com/Diogo525/SEC-with-Binary-Hamming-Codes/blob/master/images/transpose_of_matrix_A.png
[g_matrix]: https://github.com/Diogo525/SEC-with-Binary-Hamming-Codes/blob/master/images/g_matrix.png
[parity_check_matrix_get_bits]: https://github.com/Diogo525/SEC-with-Binary-Hamming-Codes/blob/master/images/parity_check_equations.png

### Decoder

The decoding process is described by the formula:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![decoder_eq]

where:

- *y* is the received message, possibly with transmission errors, and ![y_t] is the transpose of *y*. This message is 15 bits long because it contains the four redundant bits.

- matrix H is obtained by:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![matrix_h]

Matrix H is therefore:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![matrix_h_values]

The result of multiplying the *H* matrix by ![y_t] allows for the detection of transmission errors:

- if all four parity bits are '0', then the message has no errors

- if one or more parity bits are '1', then the message has one or more errors

[y_t]: https://chart.apis.google.com/chart?cht=tx&chl=y^T
[decoder_eq]: https://chart.apis.google.com/chart?cht=tx&chl=ParityBits=[H*y^T]
[matrix_h]: https://chart.apis.google.com/chart?cht=tx&chl=H=[A|I_r]
[matrix_h_values]: https://github.com/Diogo525/SEC-with-Binary-Hamming-Codes/blob/master/images/h_matrix.png

## Implementation

### Encoder block diagram

![encoder_schem]

### Decoder block diagram

![decoder_schem]

[encoder_schem]: https://github.com/Diogo525/SEC-with-Binary-Hamming-Codes/blob/master/images/encoder_schematics.png
[decoder_schem]: https://github.com/Diogo525/SEC-with-Binary-Hamming-Codes/blob/master/images/decoder_schematics.bmp

## Component simulation

Simulation of the main components of the project:

### Encoder simulation

![encoder_sim]

[encoder_sim]: https://github.com/Diogo525/SEC-with-Binary-Hamming-Codes/blob/master/images/encoder_vwf.png

### Control Unit simulation

![control_unit_sim]

[control_unit_sim]: https://github.com/Diogo525/SEC-with-Binary-Hamming-Codes/blob/master/images/control_unit_vwf.png

### Decoder simulation

![decoder_sim]

[decoder_sim]: https://github.com/Diogo525/SEC-with-Binary-Hamming-Codes/blob/master/images/decoder_vwf.png

## Improvements

The decoder can be further improved by reducing the number of XOR operations required to calculate the parity bits.

## Tools

The software **Quartus Prime Lite Edition v18.1** was used to develop this project.

## Testing

For testing purposes it's required the creation of new simulation files. This is because the version of Quartus used stores the absolute path of the components, used by the top level entity, in the .vwf files. 

## Creators

**Diogo Guedes**

- <https://github.com/Diogo525>

**Miguel Carvalho**

- to be filled
