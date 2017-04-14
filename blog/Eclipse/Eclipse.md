# Eclipse C/C++

## add -lasound/-lpcap in Eclipse compiler.

-->Menu-->Project-->Propertites-->C/C++ Build-->Settings-->ToolSettings-->GCC C Linker-->Libraries-->Libraries(-l)-->add

## add -lpthread in Eclipse compiler.

When use thread function in linux g++. we need to include pthread.h. this lib dont include default lib. we need to manual add this lib. so, we need to add -lpthread when compile thread code.

same with `add -lasound/-lpcap in Eclipse coompiler`.

## reference

#### thread program

<www.runoob.com/cplusplus/cpp-multithreading.html>
