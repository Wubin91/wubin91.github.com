---
layout: post
title: GNU Radio C++ API Reference 源码解析
category: 软件无线电
tagline: by wubin
tag: [gnuradio, C++]
---

##Modules

###Byte Operations

如其名，该类中的模块都是对字节或者比特位的一些打包和转换操作，原理比较简单，无非是一下与或非和移位操作。

####[pack_k_bits](http://gnuradio.org/doc/doxygen/classgr_1_1blocks_1_1kernel_1_1pack__k__bits.html)

#####作用：Converts a vector of bytes with 1 bit in the LSB to a byte with k relevent bits.

#####Public 成员函数

######1.pack_k_bits (unsigned k): make a pack_k_bits object.

* gr::blocks::kernel::pack_k_bits::pack_k_bits (unsigned k)

* Parameters:

  * k numbers of bits to be packed.

######2.~pack_k_bits (): 析构函数.

* gr::blocks::kernel::pack_k_bits::~pack_k_bits ()

######3.void pack (unsigned char *bytes, const unsigned char *bits, int nbytes) const: Perform the packing.

* This block performs no bounds checking. It assumes that the input, in, has of length k*nbytes and that the output vector, out, has nbytes available for writing.

* gr::blocks::kernel::pack_k_bits::pack (unsigned char *bytes, const unsigned char *bits, int nbytes) const

* parameters:

	* bytes:	Output vector (k-bits per byte) of the unpacked data

	* bits:	The input vector of bits to pack

	* nbytes The number of output bytes

######4.void pack_rev (unsigned char *bytes, const unsigned char *bits, int nbytes) const: Same as pack ( ) but reverses the bits.

######5.int k () const: 返回设置好的 k 值.

#####例如：

* k = 4 时，in = [0,1,0,1, 0x81,0x00,0x00,0x00]，out = [0x05, 0x08]；

* k = 8 时，in = [1,1,1,1, 0,1,0,1, 0,0,0,0, 1,0,0,0] out = [0xf5, 0x08]。

#####源码解析：

{% highlight C++ %}
pack_k_bits::pack_k_bits(unsigned k) : d_k(k) {
  if(d_k == 0)
    throw std::out_of_range("pack_k_bits: k must be > 0");
}

pack_k_bits::~pack_k_bits() {

}

void pack_k_bits::pack(unsigned char *bytes, const unsigned char *bits, int nbytes) const {
  for(int i = 0; i < nbytes; i++) {  // nbyte 为指定要输出的 byte 数
    bytes[i] = 0x00;
      for(unsigned int j = 0; j < d_k; j++) {
        bytes[i] |= (0x01 & bits[i*d_k+j])<<(d_k-j-1);  //通过移位操作获得 bytes 的每一位的值，输入 bits[n] 是一个很长的数组
      }
    }
}

void pack_k_bits::pack_rev(unsigned char *bytes, const unsigned char *bits, int nbytes) const {
  for(int i = 0; i < nbytes; i++) {
      bytes[i] = 0x00;
      for(unsigned int j = 0; j < d_k; j++) {
        bytes[i] |= (0x01 & bits[i*d_k+j])<<j;  //与上面的 pack 函数类似，只是将 8 位 bits 的顺序逆反
      }
  }
}
{% endhighlight %}

####[pack_k_bits_bb](http://gnuradio.org/doc/doxygen/classgr_1_1blocks_1_1pack__k__bits__bb.html)

作用：Converts a stream of bytes with 1 bit in the LSB to a byte with k relevent bits.

pack_k_bits_bb 的作用与 pack_k_bits 类似，只是一个处理 vector，一个处理 stream。观察　pack_k_bits_bb 源码，能发现其实它也是调用 pack_k_bits 来处理数据的，这里不再赘述。

####[repack_bits_bb](http://gnuradio.org/doc/doxygen/classgr_1_1blocks_1_1repack__bits__bb.html)

作用：Repack k bits from the input stream onto l bits of the output stream.

#####成员函数

static sptr gr::blocks::repack_bits_bb::make (int k，int l = 8, const std::string &tsb_tag_key = "", bool align_output = false, endianness_t endianness = GR_LSB_FIRST)

* parameters:

	* k	Number of relevant bits on the input stream

	* l	Number of relevant bits on the output stream

	* tsb_tag_key	If not empty, this is the key for the length tag.

	* align_output	If tsb_tag_key is given, this controls if the input or the output is aligned.

	* endianness	The endianness of the output data stream (LSB or MSB).

####[unpack_k_bits](http://gnuradio.org/doc/doxygen/classgr_1_1blocks_1_1kernel_1_1unpack__k__bits.html)

作用：Converts a byte with k relevent bits to k output bytes with 1 bit in the LSB. 它是 pack_k_bits 的逆过程。

例如：

* 当 k = 4 时，in = [0xf5, 0x08], out = [0,1,0,1, 1,0,0,0]

* 当 k = 8 时，in = [0xf5, 0x08], out = [1,1,1,1,1,0,0,1, 0,0,0,0,1,0,0,0]

