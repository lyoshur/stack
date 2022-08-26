---
title: 使用Netty创建websocket服务
description: 创建websocket服务
slug: 使用Netty创建websocket服务
date: 2022-03-06 00:00:00+0000
categories:
    - Java
tags:
    - Netty
---

## 创建websocket服务

## 依赖环境

```xml
<dependencies>
    <!--netty的依赖集合，都整合在一个依赖里面了-->
    <dependency>
        <groupId>io.netty</groupId>
        <artifactId>netty-all</artifactId>
        <version>4.1.6.Final</version>
    </dependency>
    <!--这里使用jackson反序列字节码-->
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.9.7</version>
    </dependency>
    <!--加入log4j 便于深入学习整合运行过程的一些细节-->
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>
</dependencies>
```



## 1.首先创建管理线程和工作线程组

```java
NioEventLoopGroup boss = new NioEventLoopGroup();
NioEventLoopGroup work = new NioEventLoopGroup();
```

## 2.创建服务启动器，并绑定管理线程和工作线程

```java
ServerBootstrap bootstrap = new ServerBootstrap();
bootstrap.group(boss,work);
```

## 3.将通道初始化为Nio

```java
bootstrap.channel(NioServerSocketChannel.class);
```

## 4.配置通道初始化

```java
bootstrap.childHandler(new NioWebSocketChannelInitializer());
```

```java
public class NioWebSocketChannelInitializer extends ChannelInitializer<SocketChannel> {
    @Override
    protected void initChannel(SocketChannel ch) {
        // 设置log监听器，并且日志级别为debug，方便观察运行流程
        ch.pipeline().addLast("logging", new LoggingHandler("DEBUG"));
        // 设置解码器
        ch.pipeline().addLast("http-codec", new HttpServerCodec());
        // 聚合器，使用websocket会用到
        ch.pipeline().addLast("aggregator", new HttpObjectAggregator(65536));
        // 用于大数据的分区传输
        ch.pipeline().addLast("http-chunked", new ChunkedWriteHandler());
        // 自定义的业务handler
        ch.pipeline().addLast("handler", new NioWebSocketHandler());
    }
}
```

## 5.自定义的业务handler

```java
public class NioWebSocketHandler extends SimpleChannelInboundHandler<Object> {

    private final Logger logger=Logger.getLogger(this.getClass());

    private WebSocketServerHandshaker handshaker;

    @Override
    protected void channelRead0(ChannelHandlerContext ctx, Object msg) throws Exception {
        logger.debug("收到消息："+msg);
        if (msg instanceof FullHttpRequest){
            //以http请求形式接入，但是走的是websocket
                handleHttpRequest(ctx, (FullHttpRequest) msg);
        }else if (msg instanceof  WebSocketFrame){
            //处理websocket客户端的消息
            handlerWebSocketFrame(ctx, (WebSocketFrame) msg);
        }
    }

    @Override
    public void channelActive(ChannelHandlerContext ctx) throws Exception {
        //添加连接
        logger.debug("客户端加入连接："+ctx.channel());
        ChannelSupervise.addChannel(ctx.channel());
    }

    @Override
    public void channelInactive(ChannelHandlerContext ctx) throws Exception {
        //断开连接
        logger.debug("客户端断开连接："+ctx.channel());
        ChannelSupervise.removeChannel(ctx.channel());
    }

    @Override
    public void channelReadComplete(ChannelHandlerContext ctx) throws Exception {
        ctx.flush();
    }
    private void handlerWebSocketFrame(ChannelHandlerContext ctx, WebSocketFrame frame){
        // 判断是否关闭链路的指令
        if (frame instanceof CloseWebSocketFrame) {
            handshaker.close(ctx.channel(), (CloseWebSocketFrame) frame.retain());
            return;
        }
        // 判断是否ping消息
        if (frame instanceof PingWebSocketFrame) {
            ctx.channel().write(
                    new PongWebSocketFrame(frame.content().retain()));
            return;
        }
        // 本例程仅支持文本消息，不支持二进制消息
        if (!(frame instanceof TextWebSocketFrame)) {
            logger.debug("本例程仅支持文本消息，不支持二进制消息");
            throw new UnsupportedOperationException(String.format(
                    "%s frame types not supported", frame.getClass().getName()));
        }
        // 返回应答消息
        String request = ((TextWebSocketFrame) frame).text();
        logger.debug("服务端收到：" + request);
        TextWebSocketFrame tws = new TextWebSocketFrame(new Date().toString()
                + ctx.channel().id() + "：" + request);
        // 群发
        ChannelSupervise.send2All(tws);
        // 返回【谁发的发给谁】
        // ctx.channel().writeAndFlush(tws);
    }
    /**
     * 唯一的一次http请求，用于创建websocket
     * */
    private void handleHttpRequest(ChannelHandlerContext ctx,
                                   FullHttpRequest req) {
        //要求Upgrade为websocket，过滤掉get/Post
        if (!req.decoderResult().isSuccess()
                || (!"websocket".equals(req.headers().get("Upgrade")))) {
            //若不是websocket方式，则创建BAD_REQUEST的req，返回给客户端
            sendHttpResponse(ctx, req, new DefaultFullHttpResponse(
                    HttpVersion.HTTP_1_1, HttpResponseStatus.BAD_REQUEST));
            return;
        }
        WebSocketServerHandshakerFactory wsFactory = new WebSocketServerHandshakerFactory(
                "ws://localhost:8081/websocket", null, false);
        handshaker = wsFactory.newHandshaker(req);
        if (handshaker == null) {
            WebSocketServerHandshakerFactory
                    .sendUnsupportedVersionResponse(ctx.channel());
        } else {
            handshaker.handshake(ctx.channel(), req);
        }
    }
    /**
     * 拒绝不合法的请求，并返回错误信息
     * */
    private static void sendHttpResponse(ChannelHandlerContext ctx,
                                         FullHttpRequest req, DefaultFullHttpResponse res) {
        // 返回应答给客户端
        if (res.status().code() != 200) {
            ByteBuf buf = Unpooled.copiedBuffer(res.status().toString(),
                    CharsetUtil.UTF_8);
            res.content().writeBytes(buf);
            buf.release();
        }
        ChannelFuture f = ctx.channel().writeAndFlush(res);
        // 如果是非Keep-Alive，关闭连接
        if (!isKeepAlive(req) || res.status().code() != 200) {
            f.addListener(ChannelFutureListener.CLOSE);
        }
    }
}
```

