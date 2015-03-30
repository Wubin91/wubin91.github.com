---
layout: post
title: "gnuradio benchmark 详解"
category: 软件无线电
tag: gnuradio
---

##1. 接收子程序 benchmark_rx.py


运行 ./benchmark_rx.py -h 可以看到有很多需要配置的参数，并不需要每一项都进行配置，因为有些参数已经给出了默认值。但是一般至少需要制定 接收频率（-f），子板（已取消？）和比特速率（-r），所以我们可以输入：


     {% highlight C++ %}
     ./benchmark_rx.py -f 900e6 -R A -r 400e3
     {% endhighlight %}


表示接收频率为 900MHz，选择 A 板进行接收 400kbit/s 的数据信息，此时默认的调制方式为 gmsk。需要注意的是抽取率 -d DECIM，每符号的样值数 -S SAMPLES_PER_SYMBOL，以及比特速率 -r BITRATE，FPGA 的时钟频率为 MASTER_CLOCK_RATE 和 调制方式这五者之间的关系。


假设对应调制方式每个符号对应的比特数为 BITS_PER_SYMBOL，那么：

	{% highlight C++ %}
    * BITRATE = (MASTER_CLOCK_RATE / DECIM / SAMPLES_PER_SYMBOL) * BITS_PER_SYMBOL
    {% endhighlight %}


而 pick_bitrate.py 就是为了计算满足关系式的比特速率，如果给出的参数不满足这一关系式，pick_bitrate.py 会自动的调整以最接近所配置的 BITRATE 值。


##2. 发送子程序 benchmark_tx.py

     
和 benchmark_rx.py 几乎相同，只是指定子板采用 -T 表示发送子板：


     {% highlight C++ %}
     ./benchmark_tx.py -f 900e6 -T B -r 400e6
     {% endhighlight %}


##3. receive_path.py


从 receive_path.py 开始，我们接触到 gr.hier_block2 所衍生的类，gr.hier_block2 类是各种组合信号处理模块的基类，由它可以衍生出各种组合的信号处理模块（由于大部分信号处理模块需要多种模块的组合使用，所以 gr.hier_block2 是 gnuradio 中最关键的类之一），class receive_path 就是其子类之一，声明 gr.hier_block2 子类的基本步骤为（以 receive_path为例）：


* （1）首先确定类名 class receive_path(gr.hier_block2)：


* （2）然后确定该类的构建函数所带的参数


* （3）由于该类对应的都是信号处理模块，所以接下来需要定义该类对应模块的输入输出端口：


     {% highlight C++ %}
     gr.hier_block2.__init__(self, “receive_path”,    
                           gr.io_signature(0,0,0), #Input signature
                           gr.io_signature(0,0,0)) #Ouput signature
     {% endhighlight %}


gr.io_signature(a,b,size)定义端口的宽度，a,b表示二维输出向量的维度，而size表示每一个输出值的数据位数，所以其最终的数据宽度是 a*b*size


* （4）添加组合模块所需的各种模块，然后定义其连接关系，需要注意的是第一级的输入端口位宽必须和组合模块声明的端口位宽一致，最后一级输出的模块的端口位宽必须和定义的输出端口位宽一致，在这里由于没有输入输出，所以定义的位宽都是0。


* （5）最后定义的是该组合模块对应的一些函数，以便实现对该模块的灵活的控制，和查看该模块的一些参数。


####总结来看，一个gr.hier_block2衍生类的定义可以简单地归纳为：


     {% highlight C++ %}
     class module_name(gr.hier_block2):
          def __init__(self,p1,p2,p3):
               gr.hier_block2.__init__(self,”module_name”,
                                       gr.io_signature(Ina,Inb,Insize),
                                       gr.io_signature(Outa,Outb,Outsize))
               submodule1 = xxxxx
               submodule2 = xxxxx
               submodule3 = xxxxx
               self.connect(submodule1,submodule2,submodule3)
          def cope1(self,p4)
               xxxxx
          def get1(self,p5)
               xxxxx
               return xxxxx
    {% endhighlight %}


##4. self.packet_receiver


它是核心的接收模块，但是blks2.demod_pkts在digital文件夹里面找不到定义文件，在哪里呢？—— /gnuradio-core/src/python/gnuradio/blks2impl/pkt.py中


    {% highlight C++ %}
     class demo_pkts(gr.hier_block2):
          ...
    {% endhighlight %}


demod_pkts包括self._demodulator = demodulator 是解调模块，它就是负责信号解调，将采样值变成0、1比特。self.correlator = gr.correlate_access_code_bb(access_code,threshold) 是一个相关器，是实现相关捕获的，它有两个参数，一个是门限，一个就是用于同步的access_code = packet_utils.default_access_code，其值可以在 packet_utils.py 中找到：


default_access_code = con_packed_binary_string_to_1_0_string(‘\xAC\xDD\xA4\xE2\xF2\x8C\x20\xFC')

就是1010 1100 1101 1101 1010 0100 1110 0010 1111 0010 1000 1100 0010 0000 1111 1100，应该是64位的伪码序列


self.framer_sink = gr.framer_sink_1(self._rcvd_pktq)将接收的数据封装成帧。而self._cvd_pktq = gr.msg_queue() 就是一个数据链表，用于存放接收到的数据包，最后将各模块按顺序连接，即可实现信号的解调，帧同步，和封帧


    {% highlight C++ %}
	self.connect(self,self._demodulator,self.correlator,self.framer_sink)
	{% endhighlight %}


信号的流程为：基带样值进入到解调模块，得到0，1比特流，流入相关器，当出现相关峰值，则将后续的比特流传入framer_sink中，framer_sink将一个个数据包添加到self._rcvd_pktq = gr.msg_queue()链表中去。
     

接下来还有一个关键模块，虽然没有和输入输出端口相连接，但是却能够调用callback函数，它就是：


self._watcher = _queue_watcher_thread(self._cvd_pktq，callback)，这个模块一直在查看self._rcvd_pktq链表是否有节点，如果为空，那么不做任何处理，如果链表不为空，那么就调用callback函数，同时将当前节点作为参数交予callback函数，然后删除该节点。


demod_pkt的callback是由receive_path中callback赋值的，receive_path的callback是my_top_block赋值的，而my_top_block的是在main函数定义中定义的rx_callback，所以每收到一个数据包，都会触发一次rx_callback函数。


##5. demodulator


对于demod_pkts中的demodulator具体是调用了哪一个模块，还是需要回到benchmark_rx.py中去，


	{% highlight C++ %}
    demods = modulation_utils.type_1_demods()
    {% endhighlight %}


然后根据options.modulation来选择options制定的调制方式


	{% highlight C++ %}
    tb = my_top_block(demods[options.modulation],rx_callback,options)
    {% endhighlight %}


首先我们可以找到modulation_utils.py文件，其中定义了type_1_demods


    {% highlight C++ %}
     _type_1_demodulators = {}

     def type_1_demods():
          return _type_1_demodulators
     def add_type_1_demo(name,demo_class):
          _type_1_demodulators[name] = demod_class
    {% endhighlight %}


而我们发现就是一个{}，但是我们直接在demods = modulation_utils.type_1_demods()后面增加print demods 直接输出，我们可以看到输出一组字符串：


{‘desk’:’class gnu radio.blk2impl.dpsk.dpsk_demod’,’d8psk’:’class gnu radio.blk2impl.d8psk.d8psk_demod’...}
     type_1_mods 或 type_1_demods 将比特流输入复数样值输出的这样的调制模块和复数样值输入比特流输出的解调模块归为一类。

