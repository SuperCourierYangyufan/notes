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
