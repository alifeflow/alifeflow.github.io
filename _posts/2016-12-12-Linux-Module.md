## Linux内核编译流程

### 读取配置信息

编译开始前，首先要读取编译的目标平台等基本信息。这些信息可以通过make kernel-menuconfig来进行图文化的配置，也可以通过makefile直接进行设置。

在Android系统编译中，也需要编译Linux内核。Android中的内核配置信息则是通过source build/envsetup.sh来实现。在导入该文件之后，用户使用lunch （target）来读取针对某一平台的配置信息。

在Android系统中，不同平台的配置信息，分别记录在//TODO文件内。

### 获取个模块的依赖关系

系统根据之前设置的变量，进入每个子模块下的Kconfig文件。该文件记录了每一个编译选项依赖的模块（depends on），包含的模块（select）和使用条件（if）。

### 编译相关模块

在读取完信息后，系统开始编译。首先，系统根据Kconfig文件中的变量，在Makefile中查找相应的编译指令。之后，编译器根据编译指令进行相应编译。例如如果用户在子模块的Kconfig中选择了GPIOLIB指令，那么系统就会在Makefile中查找$(CONFIG_GPIO)=XXXX是否存在，然后根据XXXX指令来进行编译。

## 添加模块到Linux内核

为了把自己的模块添加到编译流程中，就需要在以上每一步加入相应的信息。

### 全局文件中增加选项

//TODO

### 在Kconfig中添加依赖

之后，要在Kconfig中增加相应的信息。要添加的信息有两部分：在什么条件下选择便宜指定模块，和编译制定模块时需要其他哪些条件。前者的语句是if XXX select YYY。后者是config YYY select ZZZ。当然，支持的指令不只有select，还可能有depend on。

例如，如果用户想在系统架构为MY_ARM的时候，编译自己的GPIO驱动，这个驱动使用了gpiolib.c。流程如下：

用户在前面加入

//TODO

之后在下部加入

//TODO config MY _GPIO select GPIOLIB

Kconfig的信息就添加完了。

### Makefile中添加编译指令

最后，需要在Makefile中添加相应的指令，以便让系统知道便宜哪些文件。这些信息和一般的Makefile文件一样。需要注意的是，系统会提供$(CONFIG_XXX)这一变量。它对应于在Kconfig中选择了XXX的情况。

还接着上面的例子讲，用户想编译gpio.c和gpiolib.c。这时，只需要在Makefile中添加：

$(CONFIG_GPIO) = gpio.o

$(CONFIG_GPIOLIB) = gpiolib.o

这样，系统就知道要编译哪些文件进入内核了。

