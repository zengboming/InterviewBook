## 两个对象值相同\(x.equals\(y\) == true\)，但却可有不同的 hash code，这句话对不对？ {#两个对象值相同xequalsy--true，但却可有不同的-hash-code，这句话对不对？}

答：不对，如果两个对象 x 和 y 满足 x.equals\(y\) == true，它们的哈希码（hash code）应当相同。 Java 对于 eqauls 方法和 hashCode 方法是这样规定的：**\(1\)如果两个对象相同（equals 方法返回 true ），那么它们的 hashCode 值一定要相同**；**\(2\)如果两个对象的 hashCode 相同，它们并不一定相同**。当然，你未必要按照要求去做，但是如果你违背了上述原则就会发现在使用容器时，相同的对象可以出现在 Set 集合中，同时增加新元素的效率会大大下降（对于使用哈希存储的系统，如果哈希码频繁的冲突将会造成存取性能急剧下降）。**\(3\)如果对象的equals方法的比较操作所用到的信息没有被修改,那么对同一个对象调用多次,hashCode方法都必须始终如一地返回同一个整数**.+

补充：关于 equals 和 hashCode 方法，很多 Java 程序都知道，但很多人也就是仅仅知道而已，在 Joshua Bloch 的大作《Effective Java》中是这样介绍 equals 方法的：首先 equals 方法必须满足自反性（x.equals\(x\) 必须返回true）、对称性（x.equals\(y\) 返回true时，y.equals\(x\) 也必须返回 true）、传递性（x.equals\(y\)和y.equals\(z\)都返回 true 时，x.equals\(z\)也必须返回true）和一致性（当x和y引用的对象信息没有被修改时，多次调用x.equals\(y\)应该得到同样的返回值），而且对于任何非 null 值的引用 x，x.equals\(null\) 必须返回 false。实现高质量的 equals 方法的**诀窍**包括：

```
**使用 == 操作符检查“参数是否为这个对象的引用”**；
**使用 instanceof 操作符检查“参数是否为正确的类型”**；
**对于类中的关键属性，检查参数传入对象的属性是否与之相匹配**；
**编写完 equals 方法后，问自己它是否满足对称性、传递性、一致性**；
**重写 equals 时总是要重写 hashCode**；
**不要将 equals 方法参数中的 Object 对象替换为其他的类型，在重写时不要忘掉 @Override 注解**。
```



