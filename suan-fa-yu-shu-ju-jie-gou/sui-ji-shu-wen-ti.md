#### 用 random\(5\) 生成 random\(7\)

```
int random2() {
	int temp;
	Random random = new Random();
	do {
		temp = 5 * (random.nextInt(5)) + random.nextInt(5);
	} while (temp >= 21);
	return temp%7;
}
```



