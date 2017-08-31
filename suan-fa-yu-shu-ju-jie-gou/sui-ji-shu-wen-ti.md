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

1. random\(5\) 生成 \[0 ~ 4\]随机数
2. 5 \* random\(5\) 生成 0, 5, 10, 15, 20 随机数
3. 5 \* random\(5\) + random\(5\) 生成 \[0 ~ 24\]随机数
4. 去掉21 22 23 24
5. 模7

#### 用 random\(N\) 生成 random\(M\)

```
int random(int n, int m) {
	Random random = new Random();
	int k = random.nextInt(5);
	int max = n-1;  
	while(k < m)  
	{  
	    k = k*n + random.nextInt(5);
	    max = max*n + n-1;  
	}  
	return k/(max/n);  
}
```



