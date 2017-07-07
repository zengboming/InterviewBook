#### 交换两个数

```
class MyClass {
	private int num;
	public void setNum(int num) { this.num = num; }
	public int  getNum() { return num; }
}
public static void swap(MyClass a,MyClass b){
	int tmp=a.getNum();
	a.setNum(b.getNum());
	b.setNum(tmp);
}
```



