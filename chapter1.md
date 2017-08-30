### finally

不管有木有异常抛出, finally在return返回前执行。finally是在return后面的表达式运算后执行的（此时并没有返回运算后的值，而是先把要返回的值保存起来，管finally中的代码怎么样，返回的值都不会改变，仍然是之前保存的值），所以函数返回值是在finally执行前确定的。

注意：finally中最好不要包含return，否则程序会提前退出，返回值不是try或catch中保存的返回值。

finally不执行的几种情况：程序提前终止如调用了System.exit, 病毒，断电

