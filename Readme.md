# 黑苹果安装注意事项
## 思路
核心思路可以表述为
1. 在 U盘 上分出引导分区和安装分区
2. 使用系统工具，制作安装分区
3. 使用 clover configuration 的工具来对 clover 进行配置，写入 U盘 的引导分区
4. 目标电脑用 U盘 引导，安装 
5. 硬盘安装
6. 将 U盘 上已经被验证的引导分区内容，拷贝到磁盘上的引导分区，使得系统在引导时，自动加载所需的驱动、文件等等
## 准备
1. 选择合适的系统。  
这个主要取决于哪个系统版本的显卡驱动是稳定的。可以参考链接 [macvidcards](http://www.macvidcards.com/drivers.html)，这里介绍的非常详细
2. 选择合适的硬件   
有几个方面可以考虑
    - 主板是否支持  
    是否内置 WiFi 模块？亦或是蓝牙？  
    是否有 M.2 接口？  
    等等
    - CPU 架构是否支持  
    现在主流的架构是 skylake，也是比较容易得到支持的；反而 Haswell 要落后一些
    - 显卡的支持程度  
    现在黑苹果的主流配置都是 N卡，AMD 几乎是没法装的，难度很大  
    
   可以参考 [孫志貴的文章](https://zhuanlan.zhihu.com/p/23856630)
3. 准备好 U盘、已经安装好的 Mac 系统，用来制作引导盘
## clover-configuration 配置：
关于这个配置，网上有很多写的比较详细的帖子了，可作为参考
- [图拉丁-从零开始装黑苹果-百度贴吧-10.12](http://tieba.baidu.com/p/5179866130?see_lz=1)
- [孫志貴-GB-BNi7HG4-950-macOS-10.13](https://gitee.com/shikisuen/GB-BNi7HG4-950-macOS-10.13/blob/master/Readme-MandarinSimplified.MD)
- [黑苹果社区-macOS High Sierra 10.13.x 最新u盘安装黑苹果双系统安装教程-10.13](https://osx.cx/macos-high-sierra-10-13-xhackintosh-installation-tutorial.html#respond)
- [简书-介绍 clover-configuration 里的配置项都是干嘛的](https://www.jianshu.com/p/b156b0177a24)

几个比较重要的点有：
- 打开 verbose  
方便看日志，定位问题
- 打开 nv-disable  
这个是针对于所有无法自驱动的N卡
- CPU 采用 skylake 适合机型为 14.2 和 17.1 (原理可参考[孫志貴的文章](https://zhuanlan.zhihu.com/p/23856630))
- **关于那几个码必须弄对**(也可以参考另一篇文章 [SMBIOS 算码指南＋韧体五项指南](https://zhuanlan.zhihu.com/p/29841671))
- 10.13 及以上 需要一个 apfs 的 efi，可以直接从苹果系统里(同样可以参考[孫志貴的文章](https://gitee.com/shikisuen/GB-BNi7HG4-950-macOS-10.13/blob/master/Readme-MandarinSimplified.MD))
- 驱动什么的装载 kext 的 10.13 和 others 两个文件夹下(因为我也不知道实际用哪个)  
一定要装的驱动有(可以参考这篇文章 [HACKINTOSH DOWNLOAD KEXTS](https://hackintosher.com/kexts/))
    - FakeSMC.kext (**必装**)  
    用于骗系统说你的硬件是苹果官方的
    - Lilu.kext (**必装**)  
    很多驱动的依赖插件，装了省很多事儿
    - NvidiaGraphicsFixup.kext (优先级高)  
    因为很多时候先科驱动都会出问题，所以这个是大有可能要装的  
    - 网卡驱动 (可以慢慢琢磨)   
    这个取国外大神写的两个网卡驱动，正常官网装是很麻烦的，好在现在集成网卡都差别不大  
    [Fork of Mieze's Realtek RTL8111 Network Driver by RehabMan](https://github.com/RehabMan/OS-X-Realtek-Network)  
    [RealtekRTL8100-Binary 2.0.0](https://www.insanelymac.com/forum/files/file/259-realtekrtl8100-binary/)


  剩下的基本上就是缺什么装什么了

## 过程中：
乏善可陈  
主要需要注意的地方是：
- 会重启三次
    - 第一次用 U盘
    - 第二次用 硬盘
    - 最后一次 硬盘

## 安装结束
如果前期顺利的话，非常方便的就可以进入苹果的界面了。  
这里有一些注意事项
- 装完了记得要把显卡开关打开，否则你装了显卡驱动会出问题，黑屏什么的

### 问题解决
1. 显卡驱动装完后发现画面卡顿  
非常不幸，Mac 上本来就不是 NVIDA 的主场，而且 NVIDA 技术也有限。所以准备工作至关重要，十分关系到使用体验。  
但是如果功课没做好，这里还有两个解决办法，强制将驱动进行降级。  
    1. 脚本降级法。使用牛人写的脚本来包装降级。链接于此 [修正 macOS 10.13.3 Nvidia driver lag 問題](https://hiraku.tw/2018/01/4717/)  
    使用时只需要把包改为你能用的最近的稳定包即可，可以查询准备步骤中的那个显卡支持网站来获取连接。  
    亲测可用  
    2. 使用 [NvidiaWebDriverRepackager](https://github.com/Pavo-IM/NvidiaWebDriverRepackager) 来实现强制降级。  
    可参考 [pcbeta-【已解决】有什么办法让10.13.3用上104的n卡驱动吗](http://bbs.pcbeta.com/viewthread-1774309-1-1.html) 和 [pcbeta-升级10.13.3后更新最新的N卡驱动后系统卡顿的解决办法](http://bbs.pcbeta.com/viewthread-1774494-1-1.html)  
    私以为第一种方式应该是包了一层这个 Repackager (没有看源码，没有验证)

2. 以太网卡无法识别  
上面两种方法挨个试，总有一款适合你。  
这件事情的核心在于，你的网卡是否太老或者太新什么的。只能靠 Google 找办法了

3. clover 的分辨率不行  
开机主板设置里，将 BIOS 设置改为「仅 UEFI」，理论上就可以了  

## 收尾
一切结束后，再去做一些提升体验的事情
- 去掉 verbose
- 调整 clover 的加载背景
- 精简 clover 的引导项

## TODO：
- 如何给系统升级
- 如何在 App Store 和 iCloud 层面上可使用 Apple ID
- 声卡安装
