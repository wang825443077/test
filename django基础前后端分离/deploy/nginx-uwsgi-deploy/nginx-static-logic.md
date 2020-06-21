# Nginx部署静态数据

ubuntu 中 nginx 配置方式: 

1. 解压: 

	```python
	tar -zxvf nginx-1.8.1.tar.gz
	```

2. 进入解压目录:

	```python
	cd nginx-1.8.1
	```

3. 配置:

	```python
	./configure --prefix=/usr/local/nginx
	```

4. 编译:

	```python
	make
	```

5. 安装:

	```python
	sudo make install
	```

	