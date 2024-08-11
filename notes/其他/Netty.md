## netty
先看简单代码示例

```java
public class MyServer {
    public static void main(String[] args) throws Exception {
        //创建两个线程组 boosGroup、workerGroup
        EventLoopGroup bossGroup = new NioEventLoopGroup();
        EventLoopGroup workerGroup = new NioEventLoopGroup();
        try {
            //创建服务端的启动对象，设置参数
            ServerBootstrap bootstrap = new ServerBootstrap();
            //设置两个线程组boosGroup和workerGroup
            bootstrap.group(bossGroup, workerGroup)
                //设置服务端通道实现类型    
                .channel(NioServerSocketChannel.class)
                //设置线程队列得到连接个数    
                .option(ChannelOption.SO_BACKLOG, 128)
                //设置保持活动连接状态    
                .childOption(ChannelOption.SO_KEEPALIVE, true)
                //使用匿名内部类的形式初始化通道对象    
                .childHandler(new ChannelInitializer<SocketChannel>() {
                        @Override
                        protected void initChannel(SocketChannel socketChannel) throws Exception {
                            //给pipeline管道设置处理器
                            socketChannel.pipeline().addLast(new MyServerHandler());
                        }
                    });//给workerGroup的EventLoop对应的管道设置处理器
            System.out.println("java技术爱好者的服务端已经准备就绪...");
            //绑定端口号，启动服务端
            ChannelFuture channelFuture = bootstrap.bind(6666).sync();
            //对关闭通道进行监听
            channelFuture.channel().closeFuture().sync();
        } finally {
            bossGroup.shutdownGracefully();
            workerGroup.shutdownGracefully();
        }
    }
}
```

### Bootstrap&ServerBootstrap
#### ServerBootstrap
返回self对象的方法
- group()，可传入一个或两个EventLoopGroup
- channel()传入NioServerSocketChannel和NioSocketChannel的区别
- channelFactory()自定义channel工厂的应用场景
- option()
- childOption,如果没有子group，这个参数有用吗
- handler()
- childHandler()
- attr()
- childAttr()
其他方法
- bind()
- config()


AbstractBootstrap
	Bootstrap
	ServerBootstrap

### EventLoopGroup
执行任务的组件，可以有不同的实现，最常见的就是NioEventLoopGroup，非阻塞的实现。
NioEventLoopGroup中包含多个NioEventLoop





向EventLoopGroup提交任务的大致过程
![[NioEventLoopGroup.svg]]


Netty推荐的线程模型中，使用bossGroup去处理TCP连接请求，使用workerGroup去处理IO事件。

> [!NOTE] 业务中耗时的任务如何处理？
> 如果业务处理中有耗时的任务，也可以调用workerGroup中的线程去处理，或再使用一个线程池。
> 注意：一个channel绑定到一个Selector上，channel中的handler是按顺序在一个线程中执行的。



> 实际上bossGroup中有多个NioEventLoop线程，每个NioEventLoop绑定一个端口，也就是说，如果程序只需要监听1个端口的话，bossGroup里面只需要有一个NioEventLoop线程就行了。


> [!faq] 为什么要用一个线程组(bossGroup)去处理服务器连接(TCP连接请求)？
> 使用不同的NioEventLoop监听不同的端口，监听不同的端口可以处理不同的协议，监听关闭请求等。多次调用bind方法绑定多个端口。


> [!faq] 为什么要用一个线程组(workerGroup)去处理IO事件？
> 如果有100万个连接（NioSocketChannel）都注册在一个Selector实例上，轮询一次就会很耗费时间，如果有100个Selector实例分别监听100万个连接，每个Selector实例只需要监听10000个连接，轮询造成的资源浪费相对小。


> 在监听一个端口的情况下，一个NioEventLoop通过一个NioServerSocketChannel监听端口，处理TCP连接。后端多个工作线程NioEventLoop处理IO事件。每个Channel绑定一个NioEventLoop线程，1个NioEventLoop线程关联一个selector来为多个注册到它的Channel监听IO就绪事件。NioEventLoop是单线程执行，保证Channel的pipline在单线程中执行，保证了ChannelHandler的执行顺序。

#### NioEventLoop
每个NioEventLoop中维护一个Selector实例，一个Selector上可以注册多个Channel
每个NioEventLoop中维护一个任务队列，队列还可以选择延时队列。
每个NioEventLoop中有一个工作线程，启动后会循环从队列中获取任务

### Channel
netty中的Channel及其子类没有使用java nio中的类，而是重新设计的
Channel
	NioSocketChannel
	NioServerSocketChannel
#### ChannelFuture
`ChannelFuture`是异步操作的返回结果
`ChannelFuture`对象包含关联的Channel对象
Channel对象有closeFuture()方法，用来关闭channel

#### Future&Promise


> [!faq] `bootstrap.bind(1234).sync()`中sync()方法的作用是什么？
> sync()是同步的意思
> bind()提供用于服务端或者客户端绑定服务器地址和端口号，默认是异步启动。如果加上sync()方法则是同步绑定。
> sync()调用Object.wait()进入等待，任务完成后通过notifyAll()唤醒；说明调用sync()后，当前线程阻塞，后续代码不会继续执行。

[[DefaultChannelPromise.png]]

#### ChannelPipeline
每个channel都包含一个channelPipeline，channelPipeline中包含handler链表（准确说应该是ChannelHandlerContext链表）
![[Pasted image 20240808144317.png]]
### Handler

ChannelInboundHandler
ChannelOutboundHandler

在使用Handler的过程中，需要注意：

1. ChannelInboundHandler之间的传递，通过调用 ctx.fireChannelRead(msg) 实现；调用ctx.write(msg) 将传递到ChannelOutboundHandler。
2. ctx.write()方法执行后，需要调用flush()方法才能令它立即执行。
3. ChannelOutboundHandler 在注册的时候需要放在最后一个ChannelInboundHandler之前，否则将无法传递到ChannelOutboundHandler。

在客户端的业务Handler继承的是**SimpleChannelInboundHandler**，而在服务器端继承的是**ChannelInboundHandlerAdapter**。

#### ChannelInboundHandlerAdapter
- channelRead
- channelReadComplete

客户端和服务的实现心跳机制，比如测试Agent一段时间没有和服务器联系，就将其标记为不可用

```java
/**
 *  netty服务器的监听 处理器
 * 
 * @author flm 2017年10月27日
 */
public class IOHandler extends ChannelInboundHandlerAdapter {

    private static Logger log = Logger.getLogger(IOHandler.class);

    //netty AttributeKey 相对于 web session【重要】
    public static final AttributeKey<DeviceSession> KEY = AttributeKey.valueOf("IO"); 

    private Producer producer;
    
    
    public IOHandler(Producer producer){
        this.producer=producer;
    }

/**
     * 读取数据
     */
    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
        
        DeviceSession session = ctx.channel().attr(KEY).get();     // 检测是否 自己注册的 客户端
        
        ByteBuf buffer=(ByteBuf) msg;
        
        if (buffer == null||session == null) {
            closeConnection(ctx); // 关闭连接
        }
        
        MsgEntity msgEntity = new MsgEntity(buffer); // 解码  buffer 封装  msgEntity
        log.info("# Accept Client data :"+msgEntity.toString());
        
        if (MsgType.UNKNOW == msgEntity.getMsgType()) {
            log.info("# 客户端 发送数据 类型未定义... :"+msgEntity.toString());
            return;
        }
        
        if(!session.isActivity()){
            session.setActivity(true);
            session.setImei(msgEntity.getImei());
            SessionManager.getSingleton().addClient(session);
        }
        producer.putData(msgEntity);
        
    }

    
    
    /**
     * 客户端 注册
     */
    @Override
    public void channelRegistered(ChannelHandlerContext ctx) throws Exception {
        super.channelRegistered(ctx);
        
        log.info(String.format("# client registered...：   %s ...", ctx.channel()));
        
        DeviceSession session = new DeviceSession(ctx.channel());
        // 绑定客户端到SOCKET
        ctx.channel().attr(KEY).set(session);
    }

    
    /**
     * 客户端 失去连接
     */
    @Override
    public void channelInactive(ChannelHandlerContext ctx) throws Exception
    {
        super.channelInactive(ctx);
        
        log.info(String.format("# client out... : %s", ctx.channel()));
        DeviceSession session = ctx.channel().attr(KEY).getAndSet(null);
        
        // 移除  session 并删除 该客户端
        SessionManager.getSingleton().removeClient(session, true);
        
        if(session.getDeviceID() != null)
        {
          //  producer.onData(new Request(new RootMessage(MessageType.LOGOUT, null, null), session));
        }
        
    }

   
    /**
     * 心跳机制  用户事件触发
     */
    @Override
    public void userEventTriggered(ChannelHandlerContext ctx, Object evt) throws Exception
    {
        if (evt instanceof IdleStateEvent)
        {
            IdleStateEvent e = (IdleStateEvent) evt;
            
            //检测 是否 这段时间没有和服务器联系
            if (e.state() == IdleState.ALL_IDLE)
            {
                //检测心跳
                checkIdle(ctx);
            }
        }
        
        super.userEventTriggered(ctx, evt);
    }
    

  /**
     * 报错 处理事件
     */
    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause)
            throws Exception {
        
        log.error("# 客户端连接  Netty 出错...");
        cause.printStackTrace();
        //关闭连接
        closeConnection(ctx);
    }
```