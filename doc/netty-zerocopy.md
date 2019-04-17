# Netty零拷贝

### zero copy
所谓零拷贝即在操作数据时不需要将数据buffer从一个内存区域拷贝到另一个内存区域，少了一次内存拷贝CPU的效率也就得到了提升。

#### 系统级零拷贝
系统级别的零拷贝，是避免数据在用户态和内核态之间来回拷贝。比如Linux提供的mmap指令，通过将用户空间的内存地址映射到内核空间，操作用户空间这段内存区域即可反应到内核空间，内核空间的对应内存区域修改也会反应到用户空间，从而达到数据不需要在用户空间与内核空间来回拷贝，提升了数据传输效率。

#### Netty零拷贝
Netty的零拷贝完全是用户空间的，主要包含如下几个方面：
1. Netty提供了`CompositeByBuf`类，可将多个ByteBuf合并成为一个逻辑上的ByteBuf，从而避免了多个ByteBuf之间的内存拷贝
2. 通过wrap操作可将byte[]、ByteBuf、ByteBuffer等包装成一个Netty ByteBuf对象，进而避免拷贝操作
3. ByteBuf支持slice操作，可将ByteBuf分解为多个共享同一个存储区域的ByteBuf，避免内存拷贝
4. 通FileRegion封装的`FileChannel.transferTo`实现文件传输，可直接将文件缓冲区的数据发送到目标`channel`，避免传统通过循环write方式导致内存拷贝的问题。

