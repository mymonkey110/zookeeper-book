开始之前，你要先下载Zookeeper的发行版。Zookeeper是一个Apache项目，托管在[http:\/\/zookeeper.apache.org](http://zookeeper.apache.org)上。如果你打开这个链接，你最终会得到一个文件名类似于zookeeper-3.4.5.tar.gz的压缩文件。在Linux、Mac OS X 或者其他类UNIX系统，你可以使用一下命令去解压这个发行版：

`#tar -xvzf zookeeper-3.4.5.tar.gz`

如果你使用Windows系统，你需要使用如WinZip这样的解压工具来解压。

你需要安装Java，运行Zookeeper需要Java6.

在发行版的目录的中你会找到一个_bin_目录，它包含了启动Zookeeper所需的脚本。这些脚本以.sh结尾，用以在UNIX平台\(Linux,Mac OS X等等\)上运行，还有以.cmd结尾的脚本是为Windows系统准备的。_conf_目录有配置文件_。lib_目录包含Java Jar包，它们是运行Zookeeper所需的第三方包。后面我们会涉及到你解压发行版的目录。我们把这个目录称之为{PATH\_TO\_ZK}。







