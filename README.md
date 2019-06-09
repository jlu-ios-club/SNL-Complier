# SNL-Complier

* 实现了全iOS平台（2019-06）的适配，准备上架结果被拒绝说功能太少（useless），界面样式详见实验报告。
* 页面上方的Projects是我本来想写但没有完成的功能，如果你想要完善并上架，可以试着添加上述功能。

参考：(程序猿的事那能叫抄吗？那叫借用灵感！)
1. [界面灵感](https://github.com/LiuChangFreeman/C--Compiler)
2. [文法修正、集合求解参照](https://github.com/QiaoSteven/SNL-Compiler)
3. [LL1的DFS生成参照](https://github.com/code4lala/snl)
4. [BUG修改参照](https://github.com/Gwzlchn/SNL-Compiler)
5. [画树形参照](https://github.com/Heliovic)
6. [资料参照](https://github.com/YouthLin/SNL-Compiler)

## SNL语言的特点

SNL(Small Nested Language)语言是我们自行定义的教学模型语言，它是一种类PASCAL的“高级”程序设计语言。SNL语言的数据结构比较丰富，除了整型、字符型等简单数据类型外，还有数组、记录等结构数据类型，过程允许嵌套定义，允许递归调用。SNL语言基本上包含了高级程序设计语言的所有常用的成分，具备了高级程序设计语言的基本特征，实现SNL的编译器，可以涉及到绝大多数编译技术。通过对SNL语言编译程序的学习，我们可以更加深入更加全面的掌握编译程序的构造原理。但为了教学方便起见，略去了高级程序设计语言的一些复杂成分，如文件、集合、指针的操作等。

## SNL语言的词法

### 语言的字符表

程序是由字符组成的，每一种语言都对应一个字符表。SNL语言的字符表定义如下：

<字符表> ::= 

a | b | c | d | e | f | g | h | i | j | k | l | m | n | o | p | q | r | s | t | u | v | w | x | y |z | 

A| B | C | D | E | F | G | H | I | J | K | L | M | N | O | P | Q | R | S | T | U | V | W | X| Y | Z|

0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 |

\+ | - | * | / | < | = | ( | ) | [ | ] | . | ; |EOF | 空白字符 |{ | }| ′ | : 

注：在程序中，英文字母区分大小写；保留字只能由小写字母组成。

### 单词的巴科斯范式

#### SNL编译系统的单词符号分类如下:

* 标识符		  		( ID ) 
* 保留字		  		(它是标识符的子集, if,repeat,read,write，…)
* 无符号整数	  		( INTC )
* 单字符分界符	  	( + , - , * , / , < ,= ,( , ) , [ , ] , . , ; , EOF ,空白字符 )
* 双字符分界符	  	( := )
* 注释头符      		( { )
* 注释结束符			( } )
* 字符起始和结束符	( ‘ )
* 数组下标界限符		( .. )

#### 上述各类符号的巴科斯范式如下：

< 标识符 > 		     ::=   字母 { 字母 | 数字 }

< 无符号整数 > 	     ::=   数字 { 数字 }

< 单字符分界符 >     ::=   + | - | *| / | ( | ) | [ | ] | ; | . | < | = | EOF | 空白字符 

< 双字符分界符 >     ::=   : =

< 注释头符号 > 	     ::=   {

< 注释结束符号 > 	 ::=   }

< 字符标示符 >       ::=   ′

< 数组下标界限符 >   ::=   ..

<字母> 		     	 ::=   a | b | c | d | e | f | g | h | i | j | k | l | m | n | o | p | q | r | s | t | 

u | v | w | x | y |z | A| B | C | D | E | F | G | H | I | J | K | L | 

M | N | O | P | Q | R | S | T | U |V | W | X| Y | Z

<数字> 		     	 ::=   0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9

## SNL语言的语法

### 语法的非形式说明

一个SNL程序是由程序头、声明部分和程序体组成的。声明部分包括类型声明、变量声明和过程声明。SNL语言的语法规定可以声明整型（integer）、字符类型（char）、数组类型以及记录类型的类型标识符和变量。过程声明包括过程头、过程内部声明和过程体部分，过程声明内部还可以嵌套声明内层过程。程序体由语句序列构成，语句包括空语句、赋值语句、条件语句、循环语句、输入输出语句、过程调用语句和返回语句。表达式分为简单算术表达式和关系表达式。

1．程序头的形式是：关键字program 后面跟着程序名标识符；

2．类型定义的形式是：类型名标识符=类型定义，其中类型定义可以是类型名或者是结构类型定义，类型名可以是基本类型，或者是前面已经定义的一个类型标识符；

3．变量声明的形式是：类型名后面跟着用逗号隔开的变量标识符序列；

4．过程声明的形式是：关键字procedure跟着过程名标识符，以及参数声明、类型定义、变量说明、内层过程声明和程序体；

5．程序体的形式是：以关键字begin开头，关键字end结尾，中间是用分号隔开的语句序列（注意最后一条语句后不加分号），最后用“.”标志程序体的结束。 

## 语法正确的代码

```pascal
{实现冒泡排序算法的SNL程序}
program  bubble
var  integer  i,j,num;
     array [1..20] of integer  a;

procedure  q(integer num);
var  integer i,j,k;
     integer t;
begin
  i:=1;
   while i < num do
     j:=num-i+1;
     k:=1;
     while k<j  do
    	if a[k+1] < a[k]  
then   
	t:=a[k];
		a[k]:=a[k+1];
		a[k+1]:=t
         else  temp:=0
         fi;   
     k:=k+1
     endwh;
  i:=i+1
  endwh
end

begin
   read(num);
   i:=1;
   while i<(num+1)  do
     read(j);
     a[i]:=j;
     i:=i+1
   endwh;
   q(num);
   i:=1;
   while  i<(num+1) do 
       write(a[i]);
       i:=i+1
   endwh
end.
```

