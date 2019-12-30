# Netty
1. dubbo——》dubbo协议-》netty-》nio-》原生JDK，I/o-》TCP+IP
2. IO模型
	1. BIO 传统同步阻塞
	2. NIO 同步非阻塞 适用于连接数多且时间短  弹幕，聊天
	3. AIO 异步非阻塞 适用于连接数多且时间长  相册，系统层面的并发
3. BIO
	1. BIO服务端代码
	```
		 public static void main(String[] args) throws Exception{
			ExecutorService threadPool = Executors.newCachedThreadPool();
			ServerSocket serverSocket = new ServerSocket(6666);
			System.out.println("服务器启动了");
			while (true){
				//监听
				Socket socket = serverSocket.accept();
				System.out.println("链接到一个客户端");
				threadPool.execute(()->{
					System.out.println("进入一个线程");
					try {
						byte[] bytes = new byte[1024];
						InputStream inputStream = socket.getInputStream();
						while (true){
							int read = inputStream.read(bytes);
							if(read<=0)break;
							System.out.println(new String(bytes,0,read));
						}
					}catch (Exception e){
						System.out.println("发生异常："+e.getMessage());
					}finally {
						System.out.println("关闭client链接");
						try {
							socket.close();
						}catch (Exception e){
							System.out.println(e.getMessage());
						}
					}
				});
			}
		}
	```
	2. 服务启动，无线程，阻塞在serverSocket.accept
	3. 有访问，无数据，阻塞在inputStream.read(bytes)
4. NIO基础
	1. 由通道，缓存区，选择器构成
	2. 客户端《1=1》缓存区《1=1》管道《1=N》选择器(1个线程多个选择器)《N=1》server服务 
	3. NIO以块进行数据处理，而Bio以流进行数据处理，所有效率高很多
	4. select 是根据 事件来切换到不同的通道上去的
	5. Buffer 就是一个内存块，底层是一个数组
	6. BIO要么是输入或输出流，而Nio的buffer是双向的，需要 flip方法切换
	7. channel也是双向的
5. buffer
	1. buff入门代码
		```
		public static void main(String[] args) {
			//创建一个buff,大小为5，可以存放5个int
			IntBuffer intBuffer = IntBuffer.allocate(5);
			//存放数据
			for(int i=0;i<intBuffer.capacity();i++)intBuffer.put(i);
			//读取数据,将buff读写切换
			intBuffer.flip();
			//读取，get内含索引
			while (intBuffer.hasRemaining())System.out.println(intBuffer.get());
		}
		```
	2. 
