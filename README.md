# FPGA浅谈 —— 康复所 陶睿鹏  



## 大纲
- FPGA发展史及其现状
- FPGA应用领域及场景
- FPGA开发流程
- VHDL/Verilog语言简介
- 简单电路设计举例
	- 简单逻辑门
	- 状态机

## FPGA发展史及其现状
### 什么是FPGA？
FPGA（Field-Programmable Gate Array），中文正式名称为**现场可编程门阵列**。本质上仍属于集成电路范畴，其特点在于可以根据设计人员/消费者的意愿在出厂后进行重新配置，这也其名称中**现场可编程**的由来。一般来说，FPGA的重新配置是通过HDL（Hardware Description Language），也就是硬件描述语言来完成的，目前应用最广泛的硬件描述语言是VHDL和Verilog两种。

FPGA由一系列的**可编程逻辑模块（PLB）**，和一系列可以重复配置的内部链接组成。并且在大多数FPGA中，逻辑模块会同时包含存储元件，例如简单的触发器或完整的内存模块。

#### PLB简介
![](https://upload.wikimedia.org/wikipedia/commons/1/1c/FPGA_cell_example.png)

一个典型的PLB如图所示，由以下几个部分组成:
- **一个4输入的查找表（4-input LUT）**
- **一个全加器（FA）**
- **一个D触发器**

4输入查找表由两个3输入查找表和一个多路选择器（MUX）组成。正常模式下，两个3输入查找表连接在一起构成一个4输入查找表。在运算模式下，他们的输出被送给全加器（FA）。模式的选择由中间的多路选择器完成。通过控制右侧的多路选择器，可以得到同步或异步输出。在实际情况下，部分或全部的全加器（FA）也可以通过查找表进行实现以节省空间。

### FPGA发展史
![XC2064](https://i.imgur.com/dyVqgHB.jpg)

1985年，Xilinx公司推出的全球第一款FPGA产品XC2064怎么看都像是一只“丑小鸭”——采用2μm工艺，包含64个逻辑模块和85000个晶体管，门数量不超过1000个。22年后的2007年，FPGA业界双雄Xilinx和Altera公司纷纷推出了采用最新65nm工艺的FPGA产品，其门数量已经达到千万级，晶体管个数更是超过10亿个。一路走来，FPGA在不断地紧跟并推动着半导体工艺的进步——2001年采用150nm工艺、2002年采用130nm工艺，2003年采用90nm工艺，2006年采用65nm工艺。当今最先进的FPGA工艺已经达到16nm。

逻辑门数量：
- 1982：8192
- 1987：9000
- 1992：600000
- 21世纪初：数百万个
- 2013：5千万个

市场：
- 1987：1400万美元
- 1993：3亿8千5百万美元
- 2005：19亿美元
- 2010：27亿美元
- 2013：54亿美元
- 2020：98亿美元 :sunglasses:


## FPGA应用领域及场景

### FPGA与ASIC相比具有的的优势
- 成本低，灵活性高。通过使用 FPGA 能够执行 ASIC 能够执行的任何逻辑功能。不仅如此， FPGA 的独特优势在于芯片制造完成之后，还能更新芯片功能，这在许多应用都是理想需求。 FPGA 比 ASIC 更具成本效益，这是因为客户能够按照自身需求对 FPGA 进行编程，而不是像 ASIC 那样，必须联系销售商，才可设计和搭建一个满足其需求的 ASIC。
- 降低设计风险，提高设计速度。如果选择 FPGA 进行系统设计，这将给设计师带来更大的配置能力，同时降低开发时间表受到影响的风险。这是因为，设计师能够在不影响其余设计的条件下，修改小部分的 FPGA 设计。

### FPGA应用场景
由于FPGA具有数据吞吐量大，并行数据处理能力强的特点。使其在非常多的领域有着广泛地运用，例如图像处理，科学计算，通信处理等。在这些场景下，FPGA在降低开发难度和成本的同时，能够极大提升整个系统的处理能力和功率效率。据报道，微软已经在自家的 Microsoft Azure 云服务平台上使用FPGA对服务进行加速，同时建立了基于 FPGA 的深度学习项目。同时，FPGA 在无人驾驶系统中也有着出色的表现。英特尔公司副总裁兼可编程解决方案事业部总经理Dan McNamara就无人驾驶技术在国际消费电子展（CES）的展示情况进行了点评。他认为，电装公司选择英特尔FPGA，以可编程芯片与CPU相结合，可以让汽车变得更智能、更安全。

## FPGA开发流程
1. **创建功能框图：**

 ![](https://i.imgur.com/Dqe8KUm.png)

2. **利用现有 IP 组件代替功能块：** FPGA 制造商经过多年实践，已经知道大多数系统应该具有很多同类的功能。例如 ：网络数据 I/O 接口、图形处理以及微处理器等功能是普遍需要的，因此让每个系统设计人员重复设计这些组件显得意义不大。比较合理的做法应该是让这些功能类型“开包即用”。近年来， FPGA 制造商已经逐渐将这些常见功能或知识产权类组件（IP）加入他们的产品。这些 IP 组件可以是嵌入芯片的硬件、提供给用户的软件，或者就是 FPGA用户可以加入可编程逻辑的硬件设计。利用现有 IP 组件，替换框图中的部分功能块，工作即可简单完成。下图实线矩形所示为使用嵌入芯片的硬件和可编程逻辑 IP 组件达到的效果 ：“实施硬件”和“验证硬件”等许多环节已经代您完成。另外，图中的虚线矩形所示为现有 IP 组件与设计框图的配合情况。这里，现有 IP 组件替代了部分应用程序和中间件的编写工作，实现了相关功能。

![](https://i.imgur.com/aLSpCe8.png)

3. **为缺失的功能块编码：** 对于那些仍然需要详细设计的功能块，设计师将使用 HDL 语言。Verilog 是实现 FPGA 设计的常用 HDL 语言。Verilog 语句与常用的通用型编程语言 C 语言非常相似。但是，不同在于 C 语言用于定义在计算机上运行的程序， Verilog、 VHDL 以及其他硬件描述语言描述的是设计师想要在FPGA中创建的硬件，由逻辑门、寄存器和导线组成的相互连接的网络。您在编写 Verilog 程序时，可以使用简单的文本编辑器按照正确的语法来编写自己的程序。在 HDL 语言设计编写完成之后，下一步是编译 HDL 设计。在FPGA 编程里，综合工具将 HDL 语言设计作为输入内容，将其转化成由逻辑门、寄存器和导线组成的网络，并且这个网络被配置成实现 HDL 语言描述的功能。然后，将通过其他工序，挑选将在FPGA 中使用的特定逻辑门、寄存器和导线，并且创建一个编程文件，该编程文件将在 FPGA 启动后，对 FPGA 进行配置。

4. **验证系统设计：** FPGA 设计调试通常在模拟环境下进行。模拟器是一些软件应用程序，这种应用程序通常可以模拟设计行为。模拟过程通过软件实现，并且通过软件，能够看到每个寄存器的表现。然后，再将设计放入 FPGA。代码的调试和验证通常反复进行，直至确信 HDL 代码按照预想运行。开发人员大都使用一种叫作“Testbench”的工具，来验证 FPGA 在现实世界能否正常运行。 Testbench 是由开发人员设计的，将软件仿真与实际硬件相结合，构成了实际的系统模型。

5. **将系统映射到 FPGA 硬件中：** 以上组件经过综合后，必须载入 FPGA，以执行系统的逻辑门。

6. **在系统内验证设计：** 将经过测试的设计载入原型板上的目标 FPGA，接通系统电源，然后验证每个部分的运行是否达到预期。


## VHDL/Verilog语言简介
都属于HDL（硬件描述语言），以文本形式来描述数字系统硬件的结构和行为的语言，用它可以表示逻辑电路图、逻辑表达式，还可以表示数字逻辑系统所完成的逻辑功能。 Verilog HDL和VHDL是世界上最流行的两种硬件描述语言。我们在这里主要介绍Verilog:smile:。

使用Verilog描述硬件的基本设计单元是模块（module）。构建复杂的电子电路，主要是通过模块的相互连接调用来实现的。模块被包含在关键字module、endmodule之内。实际的电路元件。Verilog中的模块类似C语言中的函数，它能够提供输入、输出端口，可以实例调用其他模块，也可以被其他模块实例调用。模块中可以包括组合逻辑部分、过程时序部分。例如，四选一的多路选择器，就可以用模块进行描述。它具有两个位选输入信号、四个数据输入，一个输出端，在Verilog中可以表示为：
```verilog
module mux (out, select, in0, in1, in2, in3);
	input [1:0] select; //位选输入信号
	input in0, in1, in2, in3;
	output out;
	/*
		具体的寄存器传输级代码
	*/
endmodule
```
Verilog所用到的所有变量都属于两个基本的类型：**wire** (线网类型）和 **reg**（寄存器类型）：
wire与我们实际使用的电线类似, reg与之不同，它可以保存当前的数值，直到另一个数值被赋值给它。在保持当前数值的过程中，不需要驱动源对它进行作用。关于选择wire类型还是reg类型，需要符合一定的规定。模块的输入端口可以与外界的wire或reg类型的变量连接，但是这个模块输出端口只能连接到外界的wire。再简单点，就是在两个模块的信号连接点，提供信号的一方可以是reg或者wire，但是接受信号的一方只能是wire。此外，在initial、always过程代码块中赋值的变量必须是reg类型的。:sparkles:

## 简单电路设计
### 简单逻辑门（与门）
**与门真值表：**

| a | b | c | 
| - | :-: | -: | 
| 0 | 0 | 0 | 
| 0 | 1 | 0 | 
| 1 | 0 | 0 |
| 1 | 1 | 1 |

**Verilog代码：**
``` verilog
module gate(input a, input b, output c);

assign c=a&b;

endmodule
```

**门级电路图：**
![](https://i.imgur.com/p0D3DSE.png)

**测试代码（Testbench）：**
```verilog
module gate_test;
	//输入
	reg a;
	reg b;
	
	//输出
	wire c;
	
	//初始化待测试模块（UUT）
	gate uut (
		.a(a),
		.b(b),
		.c(c)
	);

	initial begin
		//初始输入
		a = 0;
		b = 0;
		
		//等待100纳秒
		#100;
		a = 0;
		b = 1;

		#100;
		a = 1;
		b = 0;

		#100;
		a = 1;
		b = 1;
	end

endmodule
```

**测试结果:**

![](https://i.imgur.com/aJanpZE.png)



### 简单状态机
**目标：** 实现一个状态机，能够检测序列110:sunglasses:

**状态转换图：** 

![](https://i.imgur.com/hmSGfa7.png)

**状态机代码：**
```verilog
`timescale 1ns / 1ps
//////////////////////////////////////////////////////////////////////////////////
// Company: 
// Engineer: 
// 
// Create Date:    18:55:11 12/17/2017 
// Design Name: 
// Module Name:    sm 
// Project Name: 
// Target Devices: 
// Tool versions: 
// Description: 
//
// Dependencies: 
//
// Revision: 
// Revision 0.01 - File Created
// Additional Comments: 
//
//////////////////////////////////////////////////////////////////////////////////
module sm(
    input a,
    input clk,
	 input reset,
    output reg b
    );

parameter [3:0] IDLE = 4'b0001, S1 = 4'b0010, S2 = 4'b0100,  S3 = 4'b1000;

reg [3:0] current_state, next_state;
		  
always @ (posedge clk) begin
	if(reset)
		current_state <= IDLE;
	else
		current_state <= next_state;
end

always @(current_state, a) begin	
	case(current_state)
		IDLE: begin
			if(!a)
				next_state = IDLE;
			else
				next_state = S1;
		end
		
		S1: begin
			if(!a)
				next_state = IDLE;
			else
				next_state = S2;
		end
		
		S2: begin
			if(!a)
				next_state = S3;
			else
				next_state = S2;
		end
		
		S3: begin
			if(!a)
				next_state = IDLE;
			else
				next_state = S1;
		end
		
		default: next_state = IDLE;
	endcase
end

always @(current_state) begin
	case (current_state) 
		IDLE: begin
			b <= 1'b0;
		end
		
		S1: begin
			b <= 1'b0;
		end
		
		S2: begin
			b <= 1'b0;
		end
		
		S3: begin
			b <= 1'b1;
		end
		
		default: b <= 1'b0;
	endcase
end
endmodule
```
**测试结果：**

![](https://i.imgur.com/5Tpu5qQ.png)


