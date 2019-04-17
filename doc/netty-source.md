# Netty4 源码分析
分析之前先来看看Netty的一个简单的demo
```
//EchoServer.java
package com.sdw.soft.demo.netty;

import io.netty.bootstrap.ServerBootstrap;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioServerSocketChannel;
import io.netty.handler.logging.LogLevel;
import io.netty.handler.logging.LoggingHandler;

/**
 * @author: shangyd
 * @create: 2019-04-04 17:04:14
 **/
public class EchoServer {

    public void init(){
        EventLoopGroup bossGroup = new NioEventLoopGroup(1);
        EventLoopGroup workerGroup = new NioEventLoopGroup();

        try {
            ServerBootstrap b = new ServerBootstrap();
            b.group(bossGroup, workerGroup)
                    .channel(NioServerSocketChannel.class)
                    .option(ChannelOption.SO_BACKLOG, 100)
                    .handler(new LoggingHandler(LogLevel.INFO))
                    .childHandler(new ChannelInitializer<SocketChannel>() {
                        @Override
                        protected void initChannel(SocketChannel ch) throws Exception {
                            ChannelPipeline pipeline = ch.pipeline();
                            pipeline.addLast(new LoggingHandler(LogLevel.INFO));
                            pipeline.addLast(new EchoServerHandler());
                        }
                    });
            ChannelFuture f = b.bind(8089).sync();
            f.channel().closeFuture().sync();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            bossGroup.shutdownGracefully();
            workerGroup.shutdownGracefully();
        }
    }

    public static void main(String[] args) {
        new EchoServer().init();
    }

    public class EchoServerHandler extends ChannelInboundHandlerAdapter {

        @Override
        public void channelRegistered(ChannelHandlerContext ctx) throws Exception {
            super.channelRegistered(ctx);

        }
    }
}

package com.sdw.soft.demo.netty;

import io.netty.bootstrap.Bootstrap;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioSocketChannel;
import io.netty.handler.logging.LogLevel;
import io.netty.handler.logging.LoggingHandler;

import java.net.SocketAddress;
//EchoClient.java
/**
 * @author: shangyd
 * @create: 2019-04-04 17:26:28
 **/
public class EchoClient {

    public void init() {
        EventLoopGroup group = new NioEventLoopGroup();

        try {
            Bootstrap b = new Bootstrap();
            b.group(group)
                    .channel(NioSocketChannel.class)
                    .option(ChannelOption.TCP_NODELAY, true)
                    .handler(new ChannelInitializer<SocketChannel>() {
                        @Override
                        protected void initChannel(SocketChannel ch) throws Exception {
                            ChannelPipeline p = ch.pipeline();
                            p.addLast(new LoggingHandler(LogLevel.INFO));
                            p.addLast(new EchoClientHandler());
                        }
                    });
            ChannelFuture f = b.connect("127.0.0.1", 8089).sync();
            ChannelFuture channelFuture = f.channel().closeFuture();
            channelFuture.sync();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            group.shutdownGracefully();
        }

    }
    public static void main(String[] args) {
        new EchoClient().init();
    }
    public class EchoClientHandler extends ChannelOutboundHandlerAdapter {
        @Override
        public void connect(ChannelHandlerContext ctx, SocketAddress remoteAddress, SocketAddress localAddress, ChannelPromise promise) throws Exception {
            super.connect(ctx, remoteAddress, localAddress, promise);
        }
    }
}
```
之前的一篇关于([java nio源码分析](java-nio-1.md)),我们分析了java nio大概的api以及其底层的原理。其实Netty是针对java nio进行封装的一个网络框架，里面并没有什么新颖的技术。
`ServerSocketChannel`、`SocketChannel`封装成了`NioServerSocketChannel`、`NioSocketChannel`。Netty引用了线程池的概念，实际使用过程中我们一般会定义`bossGroup`和`workerGroup`,其实际主要的类为`NioEventLoopGroup`、`NioEventLoop`。关于`bossGroup`他是用来接收请求的线程池，线程池中的线程即`NioEventLoop`（NioEventLoop本身也是一个线程池，只不过池中只有一个线程，这个从NioEventLoop的父类`SingleThreadEventLoop`以及`SingleThreadEventExecutor`名字也能看出来）。这里需要注意的是我们翻过源码应该能够注意到`NioEventLoopGroup`如下构造器：
```
/**
     * Create a new instance using the specified number of threads, {@link ThreadFactory} and the
     * {@link SelectorProvider} which is returned by {@link SelectorProvider#provider()}.
     */
    public NioEventLoopGroup(int nThreads) {
        this(nThreads, (Executor) null);
    }
```
很明显这个构造器的参数是可以指定线程数量的，但是很遗憾当设置`bossGroup`时，一般设置为1，因为其这里使用其他参数是无效的，因为`EventExecutorChooser`仅仅会选择其中一个，作为Acceptor线程来执行accpet操作，Netty只有在`bind`操作的时候才会创建一个`ServerSocketChannel`然后注册到`bossGroup`内部的EventLoop中，仍热是单线程负责ServerSocketChannel的accept工作，`bossGroup`里的多线程也只是为了支持能够绑定多个端口。具体的源码逻辑我们从`NioEventLoopGroup`的构造器入手即可看出：
```
public NioEventLoopGroup() {
    this(0);
}
public NioEventLoopGroup(int nThreads, ThreadFactory threadFactory) {
    this(nThreads, threadFactory, SelectorProvider.provider());
}
public NioEventLoopGroup(
        int nThreads, ThreadFactory threadFactory, final SelectorProvider selectorProvider) {
    this(nThreads, threadFactory, selectorProvider, DefaultSelectStrategyFactory.INSTANCE);
}
public NioEventLoopGroup(int nThreads, ThreadFactory threadFactory,
    final SelectorProvider selectorProvider, final SelectStrategyFactory selectStrategyFactory) {
    super(nThreads, threadFactory, selectorProvider, selectStrategyFactory, RejectedExecutionHandlers.reject());
}
protected MultithreadEventLoopGroup(int nThreads, ThreadFactory threadFactory, Object... args) {
//这里注意DEFAULT_EVENT_LOOP_THREADS的静态代码块中计算线程池大小
    super(nThreads == 0 ? DEFAULT_EVENT_LOOP_THREADS : nThreads, threadFactory, args);
}
protected MultithreadEventExecutorGroup(int nThreads, ThreadFactory threadFactory, Object... args) {
//这里的ThreadPerTaskExecutor从名字就很容易看出其executor的作用
    this(nThreads, threadFactory == null ? null : new ThreadPerTaskExecutor(threadFactory), args);
}
//!!!构造器的核心逻辑来了。。。
protected MultithreadEventExecutorGroup(int nThreads, Executor executor,
                                            EventExecutorChooserFactory chooserFactory, Object... args) {
        if (nThreads <= 0) {
            throw new IllegalArgumentException(String.format("nThreads: %d (expected: > 0)", nThreads));
        }
		//检查executor是否为null 并进行初始化
        if (executor == null) {
            executor = new ThreadPerTaskExecutor(newDefaultThreadFactory());
        }
		//构造EventLoop数组 即线程数组
        children = new EventExecutor[nThreads];

        for (int i = 0; i < nThreads; i ++) {
            boolean success = false;
            try {
            //构造EventLoop
                children[i] = newChild(executor, args);
                success = true;
            } catch (Exception e) {
                // TODO: Think about if this is a good exception type
                throw new IllegalStateException("failed to create a child event loop", e);
            } finally {
                if (!success) {
                    for (int j = 0; j < i; j ++) {
                        children[j].shutdownGracefully();
                    }

                    for (int j = 0; j < i; j ++) {
                        EventExecutor e = children[j];
                        try {
                            while (!e.isTerminated()) {
                                e.awaitTermination(Integer.MAX_VALUE, TimeUnit.SECONDS);
                            }
                        } catch (InterruptedException interrupted) {
                            // Let the caller handle the interruption.
                            Thread.currentThread().interrupt();
                            break;
                        }
                    }
                }
            }
        }
		//根据数字长度来选择eventExecutorChooser
        chooser = chooserFactory.newChooser(children);

        final FutureListener<Object> terminationListener = new FutureListener<Object>() {
            @Override
            public void operationComplete(Future<Object> future) throws Exception {
                if (terminatedChildren.incrementAndGet() == children.length) {
                	//线程全部结束 才termination
                    terminationFuture.setSuccess(null);
                }
            }
        };

        for (EventExecutor e: children) {
            e.terminationFuture().addListener(terminationListener);
        }

        Set<EventExecutor> childrenSet = new LinkedHashSet<EventExecutor>(children.length);
        Collections.addAll(childrenSet, children);
        readonlyChildren = Collections.unmodifiableSet(childrenSet);
    }
 //NioEventLoopGroup
@Override
protected EventLoop newChild(Executor executor, Object... args) throws Exception {
    return new NioEventLoop(this, executor, (SelectorProvider) args[0],
        ((SelectStrategyFactory) args[1]).newSelectStrategy(), (RejectedExecutionHandler) args[2]);
}
//NioEventLoop
NioEventLoop(NioEventLoopGroup parent, Executor executor, SelectorProvider selectorProvider,
             SelectStrategy strategy, RejectedExecutionHandler rejectedExecutionHandler) {
    super(parent, executor, false, DEFAULT_MAX_PENDING_TASKS, rejectedExecutionHandler);
    if (selectorProvider == null) {
        throw new NullPointerException("selectorProvider");
    }
    if (strategy == null) {
        throw new NullPointerException("selectStrategy");
    }
    provider = selectorProvider;
    final SelectorTuple selectorTuple = openSelector();
    selector = selectorTuple.selector;
    unwrappedSelector = selectorTuple.unwrappedSelector;
    selectStrategy = strategy;
}
```
通过`NioEventLoop`构造器的初始化逻辑，我们能看到每个EventLoop会持有一个自己的`Selector`对象。（每个`NioEventLoopGroup`会持有一个`SelectorProvider`对象）


