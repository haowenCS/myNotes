
# HDD

硬盘基本知识（磁头、磁道、扇区、柱面） https://www.cnblogs.com/jswang/p/9071847.html
- > 硬盘中一般会有多个`盘片`组成，每个盘片包含`两个面`，每个盘面都对应地有一个读/写`磁头`。受到硬盘整体体积和生产成本的限制，盘片数量都受到限制，一般都在5片以内。盘片的编号自下向上从0开始，如最下边的盘片有0面和1面，再上一个盘片就编号为2面和3面。
- > 下图显示的是一个盘面，盘面中一圈圈灰色***同心圆***为一条条`磁道`，从圆心向外画直线，可以将磁道划分为若干个弧段，每个磁道上一个***弧段***被称之为一个`扇区`（图践绿色部分）。***扇区是磁盘的最小组成单元***，通常是512字节。（由于不断提高磁盘的大小，部分厂商设定每个扇区的大小是4096字节）
- > 硬盘通常由重叠的一组盘片构成，每个盘面都被划分为数目相等的磁道，并从外缘的“0”开始编号，具有相同编号的磁道形成一个圆柱，称之为磁盘的`柱面`。***磁盘的柱面数与一个盘面上的磁道数***是相等的。由于每个盘面都有自己的磁头，因此，***盘面数等于总的磁头数***。
- > 存储容量 ＝ 磁头数 × 磁道(柱面)数 × 每道扇区数 × 每扇区字节数
- > 每个磁道的扇区数一样是说的老的硬盘，外圈的密度小，内圈的密度大，每圈可存储的数据量是一样的。新的硬盘数据的密度都一致，这样磁道的周长越长，扇区就越多，存储的数据量就越大。
- > 读写一次磁盘信息所需的时间可分解为：寻道时间、（旋转）延迟时间、（数据）传输时间。为提高磁盘传输效率，软件应着重考虑减少寻道时间和延迟时间。

# 垃圾叠瓦盘(SMR)

叠瓦磁记录 https://zh.wikipedia.org/wiki/%E5%8F%A0%E7%93%A6%E7%A3%81%E8%AE%B0%E5%BD%95
- > **`叠瓦磁记录`**（英语：Shingled magnetic recording，**`SMR`**，直译为 **`分层磁记录`**），是一种用于硬盘驱动器的磁存储数据记录技术，可提高存储密度和每个驱动器的整体存储容量。***常规的硬盘驱动器通过写入彼此平行而不重叠的磁道来记录数据（`垂直磁记录`，`PMR`）***。而叠瓦磁记录技术的硬盘写入的新磁道则与先前写入的磁道部分重叠，从而使先前的磁道更窄，因此能拥有更高的磁道密度。由此可以看出，使用叠瓦磁技术的磁道相互重叠，与用作屋顶的瓦片堆叠方式类似。***<ins>我们之所以能这样做，是因为磁盘写入磁头由于物理上的原因比读取磁头宽上许多，因而由正常方式写入的磁道宽度远比读取磁头所需的磁道宽度来得宽</ins>***。
- > 由于磁道存在重叠，叠瓦磁盘的写入过程较为复杂。***如果我们随机写入一个磁道，由于写入磁头的宽度比磁道宽，因此写入会影响到临近磁道；如果这个临近磁道有数据，这些数据就也需要重写以免数据被破坏，依此类推。因此，SMR 磁盘一般分成很多块只能追加数据（顺序写入）的区域（Zone），这和固态硬盘的闪存页管理类似***。使用“设备管理”（device-managed）方式的 SMR 磁盘通过内部固件处理了 SMR 磁盘复杂的写入问题，从而对用户封装了 SMR 磁盘的复杂性，令用户可以像使用 PMR 硬盘一样随机写入 SMR 硬盘。其他 SMR 磁盘则使用“主机管理”（host-managed）方式，需要操作系统识别 SMR 磁盘并拥有能对 SMR 磁盘进行正确顺序写入的驱动程序才能被正常使用。

叠瓦式硬盘！究竟是技术的退步？还是商人的套路。 - 昊先生的文章 - 知乎 https://zhuanlan.zhihu.com/p/110076298

这些叠瓦盘不要买，机械硬盘推荐 - 狐小妖DIY装机的文章 - 知乎 https://zhuanlan.zhihu.com/p/364555979 
