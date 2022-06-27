# golang

1. golang 也是跨平台，他的跨平台其实是 cross-compiler，针对不同平台有不同的编译器，编译出来直接的可执行文件（二进制文件）。  





## gopl读书笔记

1. go语言使用package组织的。 每个package由一个目录和目录下的一堆 .go 文件组成。 main package比较特殊，它定义一个可执行的程序。  Package main 的 方法main 就是程序的入口。
2. import引包的时候，多引会直接编译出错。
3. golang的标准库基本上都是平台无关的，例如os.Args用来读输入参数，不管mac、linux、window都适用
4.  := 是简洁版变量声明，类型是推测出来的。 如 i:=0
5. 





