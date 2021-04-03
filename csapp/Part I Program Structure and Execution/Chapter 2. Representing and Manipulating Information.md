> Three most important representation of numbers

- unsigned encoding

  greater or equal to zero

- two's-complement encoding

  most common way to represent signed integers

- floating-point

## 2.1 Information Storage

- 1 byte = 8 bits, bytes are the smallest addressable **unit of memory**
- A machine-level program views memory as a very large array of bytes, referred to as **virtual memory**.
- Every byte of memory is identified by a unique number, known as its **address**
- the set of all possible address is known as the **virtual address space**.

### 2.1.1 Hexadecimal Notation

0x11 = 00010001

### 2.1.2 Words

Every computer has  a word size

Most personal computers today have a 32-bit word size. This limits the virtual address space to 4GB.

There are some high-end machines with 64-bit word sizes.

| C declaration | 32-bit | 64-bit |
| ------------- | ------ | ------ |
| char          | 1      | 1      |
| short int     | 2      | 2      |
| int           | 4      | 4      |
| long int      | 4      | 8      |
| long long int | 4      | 8      |
| char *        | 4      | 8      |
| float         | 4      | 4      |
| double        | 8      | 8      |

> Big endian and Little endian

![WeChat1b32f87fe941d3d527fb47de394d9e11.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp2i3bwjhvj31e00hw0yr.jpg)

