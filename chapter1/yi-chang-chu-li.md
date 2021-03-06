**JAVA异常处理机制**

![](/assets/error1import.png)

异常类型：

1. Error: Error及其子类用来描述Java运行系统中的内部错误以及资源耗尽的错误，是程序无法处理的错误
2. Exception：可以通过捕捉处理使程序继续执行，是程序自身可以处理的异常，也称为非致命性异常类。
3. 可查异常：编译器要求必须处理的异常，除了RuntimeException及其子类以外的其它异常类都属于可查异常。
4. 不可查异常：编译器不要求强制处理的异常，包括运行时异常（RuntimeException与其子类）和错误（Error）
5. 运行时异常：RuntimeException类及其子类异常，如NPE、下标越界异常等，这些异常是不可查异常。
6. 非运行时异常 （编译异常）：是RuntimeException以外的异常。从程序语法角度讲是必须进行处理的异常，如果不处理，程序就不能编译通过。如IOException、SQLException等以及用户自定义的Exception异常，一般情况下不自定义检查异常。 

处理机制:

1. 抛出异常：在执行一个方法时，如果发生异常，则这个方法生成代表该异常的一个对象，停止当前执行路径，并把异常对象提交给JRE。
2. 捕获异常：JRE得到该异常后，寻找相应的代码来处理该异常。JRE在方法的调用栈中查找，从生成异常的方法开始回溯，直到找到相应的异常处理代码为止。



