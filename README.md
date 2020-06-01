<div class="section" id="redis">
<h1>如何阅读 Redis 源码？<a class="headerlink" href="#redis" title="Permalink to this headline">¶</a></h1>
<p>在这篇文章中，
我将向大家介绍一种我认为比较合理的 Redis 源码阅读顺序，
希望可以给对 Redis 有兴趣并打算阅读 Redis 源码的朋友带来一点帮助。</p>
<div class="section" id="id1">
<h2>第 1 步：阅读数据结构实现<a class="headerlink" href="#id1" title="Permalink to this headline">¶</a></h2>
<p>刚开始阅读 Redis 源码的时候，
最好从数据结构的相关文件开始读起，
因为这些文件和 Redis 中的其他部分耦合最少，
并且这些文件所实现的数据结构在大部分算法书上都可以了解到，
所以从这些文件开始读是最轻松的、难度也是最低的。</p>
<p>下表列出了 Redis 源码中，
各个数据结构的实现文件：</p>
<table class="align-default table" border="0">
<colgroup>
<col style="width: 68%">
<col style="width: 32%">
</colgroup>
<thead>
<tr class="row-odd"><th class="head"><p>文件</p></th>
<th class="head"><p>内容</p></th>
</tr>
</thead>
<tbody>
<tr class="row-even"><td><p><code class="docutils literal notranslate"><span class="pre">sds.h</span></code> 和 <code class="docutils literal notranslate"><span class="pre">sds.c</span></code></p></td>
<td><p>Redis 的动态字符串实现。</p></td>
</tr>
<tr class="row-odd"><td><p><code class="docutils literal notranslate"><span class="pre">adlist.h</span></code> 和 <code class="docutils literal notranslate"><span class="pre">adlist.c</span></code></p></td>
<td><p>Redis 的双端链表实现。</p></td>
</tr>
<tr class="row-even"><td><p><code class="docutils literal notranslate"><span class="pre">dict.h</span></code> 和 <code class="docutils literal notranslate"><span class="pre">dict.c</span></code></p></td>
<td><p>Redis 的字典实现。</p></td>
</tr>
<tr class="row-odd"><td><p><code class="docutils literal notranslate"><span class="pre">redis.h</span></code> 中的 <code class="docutils literal notranslate"><span class="pre">zskiplist</span></code> 结构和 <code class="docutils literal notranslate"><span class="pre">zskiplistNode</span></code> 结构，
以及 <code class="docutils literal notranslate"><span class="pre">t_zset.c</span></code> 中所有以 <code class="docutils literal notranslate"><span class="pre">zsl</span></code> 开头的函数，
比如 <code class="docutils literal notranslate"><span class="pre">zslCreate</span></code> 、 <code class="docutils literal notranslate"><span class="pre">zslInsert</span></code> 、 <code class="docutils literal notranslate"><span class="pre">zslDeleteNode</span></code> ，等等。</p></td>
<td><p>Redis 的跳跃表实现。</p></td>
</tr>
<tr class="row-even"><td><p><code class="docutils literal notranslate"><span class="pre">hyperloglog.c</span></code> 中的 <code class="docutils literal notranslate"><span class="pre">hllhdr</span></code> 结构，
以及所有以 <code class="docutils literal notranslate"><span class="pre">hll</span></code> 开头的函数。</p></td>
<td><p>Redis 的 HyperLogLog 实现。</p></td>
</tr>
</tbody>
</table>
</div>
<div class="section" id="id2">
<h2>第 2 步：阅读内存编码数据结构实现<a class="headerlink" href="#id2" title="Permalink to this headline">¶</a></h2>
<p>在阅读完和数据结构有关的文件之后，
接下来就应该阅读内存编码（encoding）数据结构了。</p>
<p>和普通的数据结构一样，
内存编码数据结构基本上是独立的，
不和其他模块耦合，
但是区别在于：</p>
<ul class="simple">
<li><p>上一步要读的数据结构，
比如双端链表、字典、HyperLogLog，
在算法书上或者相关的论文上都可以找到资料介绍。</p></li>
<li><p>而内存编码数据结构却不容易找到相关的资料，
因为这些数据结构都是 Redis 为了节约内存而专门开发出来的，
换句话说，
这些数据结构都是特制（adhoc）的，
除了 Redis 源码中的文档之外，
基本上找不到其他资料来了解这些特制的数据结构。</p></li>
</ul>
<p>不过话又说回来，
虽然内存编码数据结构是 Redis 特制的，
但它们基本都和内存分配、指针操作、位操作这些底层的东西有关，
读者只要认真阅读源码中的文档，
并在有需要时，
画图来分析这些数据结构，
那么要完全理解这些内存编码数据结构的运作原理并不难，
当然这需要花一些功夫。</p>
<p>下表展示了 Redis 源码中，
各个内存编码数据结构的实现文件：</p>
<table class="align-default table" border="0">
<colgroup>
<col style="width: 50%">
<col style="width: 50%">
</colgroup>
<thead>
<tr class="row-odd"><th class="head"><p>文件</p></th>
<th class="head"><p>内容</p></th>
</tr>
</thead>
<tbody>
<tr class="row-even"><td><p><code class="docutils literal notranslate"><span class="pre">intset.h</span></code> 和 <code class="docutils literal notranslate"><span class="pre">intset.c</span></code></p></td>
<td><p>整数集合（intset）数据结构。</p></td>
</tr>
<tr class="row-odd"><td><p><code class="docutils literal notranslate"><span class="pre">ziplist.h</span></code> 和 <code class="docutils literal notranslate"><span class="pre">ziplist.c</span></code></p></td>
<td><p>压缩列表（zip list）数据结构。</p></td>
</tr>
</tbody>
</table>
</div>
<div class="section" id="id3">
<h2>第 3 步：阅读数据类型实现<a class="headerlink" href="#id3" title="Permalink to this headline">¶</a></h2>
<p>在完成以上两个阅读步骤之后，
我们就读完了 Redis 六种不同类型的键（字符串、散列、列表、集合、有序集合、HyperLogLog）的所有底层实现结构了。</p>
<p>接下来，
为了知道 Redis 是如何通过以上提到的数据结构来实现不同类型的键，
我们需要阅读实现各个数据类型的文件，
以及 Redis 的对象系统文件，
这些文件包括：</p>
<table class="align-default table" border="0">
<colgroup>
<col style="width: 56%">
<col style="width: 44%">
</colgroup>
<thead>
<tr class="row-odd"><th class="head"><p>文件</p></th>
<th class="head"><p>内容</p></th>
</tr>
</thead>
<tbody>
<tr class="row-even"><td><p><code class="docutils literal notranslate"><span class="pre">object.c</span></code></p></td>
<td><p>Redis 的对象（类型）系统实现。</p></td>
</tr>
<tr class="row-odd"><td><p><code class="docutils literal notranslate"><span class="pre">t_string.c</span></code></p></td>
<td><p>字符串键的实现。</p></td>
</tr>
<tr class="row-even"><td><p><code class="docutils literal notranslate"><span class="pre">t_list.c</span></code></p></td>
<td><p>列表键的实现。</p></td>
</tr>
<tr class="row-odd"><td><p><code class="docutils literal notranslate"><span class="pre">t_hash.c</span></code></p></td>
<td><p>散列键的实现。</p></td>
</tr>
<tr class="row-even"><td><p><code class="docutils literal notranslate"><span class="pre">t_set.c</span></code></p></td>
<td><p>集合键的实现。</p></td>
</tr>
<tr class="row-odd"><td><p><code class="docutils literal notranslate"><span class="pre">t_zset.c</span></code> 中除 <code class="docutils literal notranslate"><span class="pre">zsl</span></code> 开头的函数之外的所有函数。</p></td>
<td><p>有序集合键的实现。</p></td>
</tr>
<tr class="row-even"><td><p><code class="docutils literal notranslate"><span class="pre">hyperloglog.c</span></code> 中所有以 <code class="docutils literal notranslate"><span class="pre">pf</span></code> 开头的函数。</p></td>
<td><p>HyperLogLog 键的实现。</p></td>
</tr>
</tbody>
</table>
</div>
<div class="section" id="id4">
<h2>第 4 步：阅读数据库实现相关代码<a class="headerlink" href="#id4" title="Permalink to this headline">¶</a></h2>
<p>在读完了 Redis 使用所有底层数据结构，
以及 Redis 是如何使用这些数据结构来实现不同类型的键之后，
我们就可以开始阅读 Redis 里面和数据库有关的代码了，
它们分别是：</p>
<table class="align-default table" border="0">
<colgroup>
<col style="width: 55%">
<col style="width: 45%">
</colgroup>
<thead>
<tr class="row-odd"><th class="head"><p>文件</p></th>
<th class="head"><p>内容</p></th>
</tr>
</thead>
<tbody>
<tr class="row-even"><td><p><code class="docutils literal notranslate"><span class="pre">redis.h</span></code> 文件中的 <code class="docutils literal notranslate"><span class="pre">redisDb</span></code> 结构，
以及 <code class="docutils literal notranslate"><span class="pre">db.c</span></code> 文件。</p></td>
<td><p>Redis 的数据库实现。</p></td>
</tr>
<tr class="row-odd"><td><p><code class="docutils literal notranslate"><span class="pre">notify.c</span></code></p></td>
<td><p>Redis 的数据库通知功能实现代码。</p></td>
</tr>
<tr class="row-even"><td><p><code class="docutils literal notranslate"><span class="pre">rdb.h</span></code> 和 <code class="docutils literal notranslate"><span class="pre">rdb.c</span></code></p></td>
<td><p>Redis 的 RDB 持久化实现代码。</p></td>
</tr>
<tr class="row-odd"><td><p><code class="docutils literal notranslate"><span class="pre">aof.c</span></code></p></td>
<td><p>Redis 的 AOF 持久化实现代码。</p></td>
</tr>
</tbody>
</table>
<div class="topic">
<p class="topic-title">选读</p>
<p>Redis 有一些独立的功能模块，
这些模块可以在完成第 4 步之后阅读，
它们包括：</p>
<table class="align-default table" border="0">
<colgroup>
<col style="width: 56%">
<col style="width: 44%">
</colgroup>
<thead>
<tr class="row-odd"><th class="head"><p>文件</p></th>
<th class="head"><p>内容</p></th>
</tr>
</thead>
<tbody>
<tr class="row-even"><td><p><code class="docutils literal notranslate"><span class="pre">redis.h</span></code> 文件的 <code class="docutils literal notranslate"><span class="pre">pubsubPattern</span></code> 结构，以及 <code class="docutils literal notranslate"><span class="pre">pubsub.c</span></code> 文件。</p></td>
<td><p>发布与订阅功能的实现。</p></td>
</tr>
<tr class="row-odd"><td><p><code class="docutils literal notranslate"><span class="pre">redis.h</span></code> 文件的 <code class="docutils literal notranslate"><span class="pre">multiState</span></code> 结构以及 <code class="docutils literal notranslate"><span class="pre">multiCmd</span></code> 结构，
<code class="docutils literal notranslate"><span class="pre">multi.c</span></code> 文件。</p></td>
<td><p>事务功能的实现。</p></td>
</tr>
<tr class="row-even"><td><p><code class="docutils literal notranslate"><span class="pre">sort.c</span></code></p></td>
<td><p><code class="docutils literal notranslate"><span class="pre">SORT</span></code> 命令的实现。</p></td>
</tr>
<tr class="row-odd"><td><p><code class="docutils literal notranslate"><span class="pre">bitops.c</span></code></p></td>
<td><p><code class="docutils literal notranslate"><span class="pre">GETBIT</span></code> 、 <code class="docutils literal notranslate"><span class="pre">SETBIT</span></code> 等二进制位操作命令的实现。</p></td>
</tr>
</tbody>
</table>
</div>
</div>
<div class="section" id="id5">
<h2>第 5 步：阅读客户端和服务器的相关代码<a class="headerlink" href="#id5" title="Permalink to this headline">¶</a></h2>
<p>在阅读完数据库实现代码，
以及 RDB 和 AOF 两种持久化的代码之后，
我们可以开始阅读客户端和 Redis 服务器本身的实现代码，
和这些代码有关的文件是：</p>
<table class="align-default table" border="0">
<colgroup>
<col style="width: 58%">
<col style="width: 42%">
</colgroup>
<thead>
<tr class="row-odd"><th class="head"><p>文件</p></th>
<th class="head"><p>内容</p></th>
</tr>
</thead>
<tbody>
<tr class="row-even"><td><p><code class="docutils literal notranslate"><span class="pre">ae.c</span></code> ，以及任意一个 <code class="docutils literal notranslate"><span class="pre">ae_*.c</span></code> 文件（取决于你所使用的多路复用库）。</p></td>
<td><p>Redis 的事件处理器实现（基于 Reactor 模式）。</p></td>
</tr>
<tr class="row-odd"><td><p><code class="docutils literal notranslate"><span class="pre">networking.c</span></code></p></td>
<td><p>Redis 的网络连接库，负责发送命令回复和接受命令请求，
同时也负责创建/销毁客户端，
以及通信协议分析等工作。</p></td>
</tr>
<tr class="row-even"><td><p><code class="docutils literal notranslate"><span class="pre">redis.h</span></code> 和 <code class="docutils literal notranslate"><span class="pre">redis.c</span></code> 中和单机 Redis 服务器有关的部分。</p></td>
<td><p>单机 Redis 服务器的实现。</p></td>
</tr>
</tbody>
</table>
<p>如果读者能完成以上 5 个阅读步骤的话，
那么恭喜你，
你已经了解了单机的 Redis 服务器是怎样处理命令请求和返回命令回复，
以及是 Redis 怎样操作数据库的了，
这是 Redis 最重要的部分，
也是之后继续阅读多机功能的基础。</p>
<div class="topic">
<p class="topic-title">选读</p>
<p>Redis 有一些独立的功能模块，
这些模块可以在完成第 5 步之后阅读，
它们包括：</p>
<table class="align-default table" border="0">
<colgroup>
<col style="width: 29%">
<col style="width: 71%">
</colgroup>
<thead>
<tr class="row-odd"><th class="head"><p>文件</p></th>
<th class="head"><p>内容</p></th>
</tr>
</thead>
<tbody>
<tr class="row-even"><td><p><code class="docutils literal notranslate"><span class="pre">scripting.c</span></code></p></td>
<td><p>Lua 脚本功能的实现。</p></td>
</tr>
<tr class="row-odd"><td><p><code class="docutils literal notranslate"><span class="pre">slowlog.c</span></code></p></td>
<td><p>慢查询功能的实现。</p></td>
</tr>
<tr class="row-even"><td><p><code class="docutils literal notranslate"><span class="pre">monitor.c</span></code></p></td>
<td><p>监视器功能的实现。</p></td>
</tr>
</tbody>
</table>
</div>
</div>
<div class="section" id="id6">
<h2>第 6 步：阅读多机功能的实现<a class="headerlink" href="#id6" title="Permalink to this headline">¶</a></h2>
<p>在弄懂了 Redis 的单机服务器是怎样运作的之后，
就可以开始阅读 Redis 多机功能的实现代码了，
和这些功能有关的文件为：</p>
<table class="align-default table" border="0">
<colgroup>
<col style="width: 26%">
<col style="width: 74%">
</colgroup>
<thead>
<tr class="row-odd"><th class="head"><p>文件</p></th>
<th class="head"><p>内容</p></th>
</tr>
</thead>
<tbody>
<tr class="row-even"><td><p><code class="docutils literal notranslate"><span class="pre">replication.c</span></code></p></td>
<td><p>复制功能的实现代码。</p></td>
</tr>
<tr class="row-odd"><td><p><code class="docutils literal notranslate"><span class="pre">sentinel.c</span></code></p></td>
<td><p>Redis Sentinel 的实现代码。</p></td>
</tr>
<tr class="row-even"><td><p><code class="docutils literal notranslate"><span class="pre">cluster.c</span></code></p></td>
<td><p>Redis 集群的实现代码。</p></td>
</tr>
</tbody>
</table>
<p>注意，
因为 Redis Sentinel 用到了复制功能的代码，
而集群又用到了复制和 Redis Sentinel 的代码，
所以在阅读这三个模块的时候，
记得先阅读复制模块，
然后阅读 Sentinel 模块，
最后才阅读集群模块，
这样理解起来就会更得心应手。</p>
<p>如果你连这三个模块都读完了的话，
那么恭喜你，
你已经读完了 Redis 单机功能和多机功能的所有代码了！</p>
<p>下图总结了本文介绍的阅读顺序：</p>
digraph {

    node [shape = plaintext]

    datastruct [label = "数据结构\n（sds、adlist、dict、t_zset、hyperloglog）"]

    encoding_datastruct [label = "内存编码数据结构\n（intset、ziplist）"]

    object [label = "数据类型\n（object、t_string、t_list、t_hash、t_set、t_zset、hyperloglog）"]

    db [label = "数据库相关\n（db、notify、rdb、aof）"]

    client_and_server [label = "客户端与服务器相关\n（ae、networking、redis）"]

    multi_server [label = "多机功能\n（replication、sentinel、cluster）"]

    //

    datastruct -&gt; encoding_datastruct -&gt; object -&gt; db -&gt; client_and_server -&gt; multi_server

}</div>
<div class="section" id="id7">
<h2>结语<a class="headerlink" href="#id7" title="Permalink to this headline">¶</a></h2>
<p>Redis 的设计非常简洁、优美、精巧和高效，
任何人只要愿意去阅读它的代码的话，
应该都会有所收获的。</p>
<p>希望这篇文章能够给想要阅读 Redis 代码的朋友们带来一些帮助，
也欢迎各位随时和我讨论 Redis 源码方面的问题，
或者跟我分享各位阅读 Redis 源码的心得和经验。</p>
<p>另外我的 <a class="reference external" href="https://github.com/huangz1990/redis-3.0-annotated">Redis 源码注释</a> 项目以及 <a class="reference external" href="http://redisbook.com/">《Redis 设计与实现》</a> 一书对于理解 Redis 的源代码应该也会有所帮助，
有兴趣的朋友可以自行了解该项目/书本。</p>
<div class="line-block">
<div class="line">黄健宏（huangz）</div>
<div class="line">2014.7.28</div>
</div>
</div>
</div>