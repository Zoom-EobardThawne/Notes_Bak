# fs4412 工程编译例子

## 工程文件目录结构

- fs4412

  > - driver
  >
  >   > interrupt.c
  >   >
  >   > key.c
  >   >
  >   > ...
  >
  > - include
  >
  >   > exynos_4412.h
  >   >
  >   > exynos_setup.h
  >
  > - lib
  >
  >   > libc.a
  >   >
  >   > libgcc.a
  >   >
  >   > libm.a
  >   >
  >   > libnosys.a
  >
  > - start
  >
  >   > start.S
  >
  > - config.mk
  >
  > - main.c
  >
  > - Makefile

## Makefile 代码

```makefile
CROSS_COMPILE = arm-none-eabi-
CC  = $(CROSS_COMPILE)gcc
LD  = $(CROSS_COMPILE)ld
OBJCOPY = $(CROSS_COMPILE)objcopy
APP = fs4412.elf
BIN = fs4412.bin
CINCLUDES = -I ./include

LDFLAGS+= -static -L ./lib -lc -lm -lnosys
LDFLAGS+= -static -L ./lib -lgcc

include config.mk

$(APP):start/start.o main.o  $(COBJS)
	$(LD) -Ttext=0x40000000 $^ -o $@ $(LDFLAGS)
	$(OBJCOPY) -O binary $(APP) $(BIN)
		
%.o:%.S
	$(CC) -c $< -o $@

%.o:%.c
	$(CC) -c $< -o $@  $(CINCLUDES)

.PHONY:clean
clean:
	-rm ./main.o $(BIN) $(APP) ./start/start.o
	-rm ./$(COBJS)

```



```makefile
# config.mk
COBJS+= driver/led.o
COBJS+= driver/pwm_beep.o
COBJS+= driver/music.o
COBJS+= driver/uart.o
COBJS+= driver/interrupt.o
COBJS+= driver/key_interrupt.o
COBJS+= driver/adc_interrupt.o
COBJS+= driver/iic_con.o
COBJS+= driver/MPU6050.o

```

运行命令 `make`

执行伪目标 `clean` : `make clean`



# 多文件(模块化)编程

> ## 概念
>
> 模块化编程，就是多文件编程。把不同功能的函数封装到不同的文件中。一个.c文件和一个.h文件被称为一个模块。
>
> 开发C程序时，稍微大型的项目就需要使用多文件开发（模块化编程）。当代码量较大功能较复杂时，单一文件程序会使得文件非常巨大，代码量非常大，成千上万行的代码在一个文件中不便于修改和维护，因此需要将不同的功能模块放在不同的文件中。
>
> 在团队合作开发时，也需要多文件开发。各个功能模块分成多个文件同时编辑，可以有效的提高团队开发的分工协作效率。
>
> ![](https://cdn.jsdelivr.net/gh/Zoom-EobardThawne/MyPicGo@main/img/mulfile_coding.png)
>
> ## 模块化开发步骤
>
> 以需要开发两个函数的c程序为例：
> 1. 创建两个.c文件，function1.c和function2.c
> 2. 分别在两个.c文件内定义需要的函数
> 3. 创建两个.h文件，文件名要与两个.c文件一致，即function1.h和function2.h
> 4. 在两个.h文件中分别包含各自所需的库文件，声明在.c中定义的函数
> 5. 在main.c文件中包含两个.h文件，在main函数中直接调用两个自定义函数



# Makefile规则

## 依赖关系

```makefile
target ... : prerequisities..    //目标文件... : 依赖文件...
command //命令
...
...
clean:
	rm *.o ...
```

>  **target** 为要生成的目标文件，可以是： Object File、可执行文件、标签（Label)
>
>  **prerequisities**: 是 生成target 所需要的目标或文件
>
>  **command** 是make 执行的命令
>
>  **clean** 是一个动作标签



## make如何工作

### make的工作原理

**make根据文件的时间戳，也就是文件的新旧，来判断是否更新**

> prerequisites中如果有一个以上的文件比 target 文件要新的话，或者target不存在
>
> command 所定义的命令就会被执行

**make 不管命令如何工作，只管执行所定义的命令**

> command 一定要以一个 Tab 键作为开头

**`clean :`不是一个文件，只是一个动作名，`:` 后面什么都没有，make 不会自动去找文件的依赖性**，也就**不会自动执行其后所定义的命令**



### makefile 中使用变量

```makefile
edit : main.o kbd.o command.o display.o \ 
insert.o search.o files.o utils.o 

cc -o edit main.o kbd.o command.o display.o \ 
insert.o search.o files.o utils.o

#使用变量
objects = main.o kbd.o command.o display.o \ 
insert.o search.o files.o utils.o 
 
edit : $(objects) 
cc -o edit $(objects)
```



### make自动推导

```makefile
objects = main.o kbd.o command.o display.o \ 
insert.o search.o files.o utils.o 
 
edit : $(objects) 
cc -o edit $(objects) 

# make会自动根据xx.o 推导它的依赖文件xx.c, 自动添加.c在依赖关系中，并且 cc -c xx.c也会被推导出来
main.o : defs.h 
kbd.o : defs.h command.h 
command.o : defs.h command.h 
display.o : defs.h buffer.h 
insert.o : defs.h buffer.h 
search.o : defs.h buffer.h 
files.o : defs.h buffer.h command.h 
utils.o : defs.h 
 
.PHONY : clean 
clean : 
rm edit $(objects)
```



### 清空目标文件的规则

```makefile
clean:
rm edit $(objects)

# 更为稳健的做法
.PHONY : clean
clean :
-rm edit $(objects)
```

**.PHONY** 意思表示 clean 是一个“**伪目标**”，。而在 **rm 命令前面加了一个小减号**的意思就是，也许**某些文件出现问题，但不要管，继续做后面的事**。当然，clean 的规则不要放在文件的开头，不然，这就会变成 make 的默认目标，相信谁也不愿意这样。不成文的规矩是——“**clean 从来都是放在文件的最后**”。 



# Makefile 总述

## Makefile 的五大机制

### **显式规则**

显式规则说明了，如何生成一个或多的的目标文件。这是由 Makefile 的书写者明显指出，要生成的文件，文件的依赖文件，生成的命令。

### **隐晦规则**

由于我们的 make 有自动推导的功能，所以隐晦的规则可以让我们比较粗糙地简略地书写Makefile，这是由 make 所支持的。 

### **变量的定义**

在 Makefile 中我们要定义一系列的变量，变量一般都是字符串，这个有点你 C 语言中的宏，当 Makefile 被执行时，其中的变量都会被扩展到相应的引用位置上。 

### **文件指示**

其包括了三个部分，一个是在一个 Makefile 中引用另一个 Makefile，就像 C 语言中的include 一样；另一个是指根据某些情况指定 Makefile 中的有效部分，就像 C 语言中的预编译`#if` 一样；还有就是定义一个多行的命令。有关这一部分的内容，我会在后续的部分中讲述。 

### **注释**

Makefile 中只有行注释，和 UNIX 的 Shell 脚本一样，其注释是用“`#`”字符，这个就像C/C++中的“`//`”一样。如果你要在你的 Makefile 中使用“#”字符，可以用反斜框进行转义，如：`\#`。



## Makefile 文件名

默认make 在当前目录默认寻找 指定名字的makefile文件

大多数make 支持 "makefile" "Makefile" 两种默认文件名 ，建议使用 "Makefile"

GUN的make 可以识别 GUNmakefile

 指定特定的Makefile：

> - `make -f Make.Linux` 或 `make --f Make.AIX`



## 引用其他Makefile

- 语法：**`include <filename>`**

> \<filename> 可以是当前操作系统Shell的文件模式（可以包含路径和通配符）
>
> **`include`** 前可以有空字符，**但是绝不能是 `[Tab]`**

e.g. 有a.mk 、b.mk 、c.mk 、foo.make 和一个变量 `$(bar) `包含了 e.mk 和 f.mk

```makefile
include foo.make a.mk b.mk c.mk e.mk f.mk 
# < ==== >
include foo.make *.mk $(bar)
```



- 默认make 会在当前目录首先寻找，如果当前目录没有：

> 1. ```makefile
>    # 指定目录的参数
>    make -I
>    make --include-dir
>    ```
>
> 2. ```makefile
>    -include <filename>
>    # 无论 include 过程中出现什么错误，都不要报错继续执行。和其它版本 make 兼容的相关命令是 
>    sinclude <filename>
>    ```
>
>    - 如果目录\<prefix>/include（一般是：/usr/local/bin 或/usr/include），存在的话，make 也会去找。如果有文件没有找到的话，make 会生成一条警告信息，但不会马上出现致命错误。
>    - make 会继续载入其它的文件，一旦完成 makefile 的读取，make 会再重试这些没有找到，或是不能读取的文件。
>    - 如果还是不行，make 才会出现一条致命信息。如果你想让 make 不理那些无法读取的文件，而继续执行，你可以在 include 前加一个减号“-”。 



## 环境变量 MAKEFILES

- 环境变量 MAKEFILES ，make将其值当作一个类似 `include` 的动作

- 环境变量中的值，是其他的Makefile，空格分隔

### 与include 的区别

- 环境变量引入的Makefile 的 “目标(target)”不会起作用

- 环境变量中定义的文件出错，make也不理会



**！建议不要使用 MAKEFILES 环境变量**



## make工作步骤

GUN 的make工作的执行步骤如下：（其他make类似）

> 1. 读入所有的 Makefile。 
>
> 2. 读入被 include 的其它 Makefile。 
>
> 3. 初始化文件中的变量。 
>
> 4. 推导隐晦规则，并分析所有规则。 
>
> 5. 为所有的目标文件创建依赖关系链。 
>
> 6. 根据依赖关系，决定哪些目标要重新生成。 
>
> 7. 执行生成命令。 

- 1 ~ 5 为第一阶段：
- 6 ~ 7 为第二阶段：



# 书写规则

