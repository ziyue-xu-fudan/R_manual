

# R语言可视化宝典

------

## 目录

[TOC]

------

## 第一章 R语言常用可视化与生信分析技能

### 0 环境与R包准备

​	**参考阅读：[R语言中安装R包的五种方法](https://mp.weixin.qq.com/s/QVouU9dxs6i_rYRr9YI59A)**

​	**参考代码：**`housekeeping.R`

​	你需要知道，很多时候数据分析并不难，因为R语言里有着大量的程序包帮我们处理好了复杂的数据处理过程，很多时候复杂的数据处理就是因为有R包的存在只需要一行代码就可以解决。然而，装上兼容的、可以用的包有的时候并不是那么的简单，有时候装上一个有用的包就需要一下午的时间。因此，"工欲善其事，必先利其器“。我想在这里面首先帮你理一下哪些包需要我们事先安装。

​	**下面是一些后面一些可视化程序需要运用的R包。**

```{r cars}
#代码：housekeeping.R
install.packages('pheatmap')
install.packages('survminer')
install.packages('survival')
install.packages('forestplot')
install.packages('stringr')
install.packages('ggplot2')
install.packages('survival')
install.packages('survminer')
install.packages('fmsb')
install.packages('ggalluvial')
install.packages('dplyr')
if (!requireNamespace("BiocManager", quietly = TRUE))
  install.packages("BiocManager")
BiocManager::install("GSEABase",force = TRUE)
if (!requireNamespace("BiocManager", quietly = TRUE))
  install.packages("BiocManager")
BiocManager::install("BiocGenerics")
if (!requireNamespace("BiocManager", quietly = TRUE))
  install.packages("BiocManager")
BiocManager::install("parallel",force = TRUE)
```

#### 	总结R包的安装

总之，R语言有三种包需要你装，而随着你的课题开展，不同种类的包有不同的方法（如果使用不当会带来不小的麻烦）,不能小觑，所以我这里专门讲一遍。

**第一种**就是R语言相对来说比较基础的程序包，利用`install.packages()`等语句进行安装就可以了。注意括号里的包一定要带引号，比如：

```{r cars}
install.packages('ggplot2')
```

**第二种**就是在R语言[Bioconductor](https://www.bioconductor.org)平台上已经有的东西，即可能用的比较多，但是相对来说比较新，就采用`BiocManager::install()`语句

```{r cars}
BiocManager::install("parallel") 
```

![image-20230208132022069](https://gitee.com/ziyuexu_fudan/pic/raw/master/blog/image-20230208132022069.png)

​	需要注意的是，如果这边出现了这样的问题，那么就在包的后面加入一个`force=TRUE`，如：

```R
BiocManager::install("parallel",force = TRUE) 
```

![image-20230208132140373](https://gitee.com/ziyuexu_fudan/pic/raw/master/blog/image-20230208132140373.png)

```R
Installation paths not writeable.unable to update packages
	path: C:/Program Files/R/R-4.2.2/1ibrary
	packages:
		boot, class, foreign， MASS， Matrix, nlme, spatial, survival
	01d packages: DelayedArray','dplyr'
	Update al1 /some/none? [a/s/n]:
```

​	还有一种报错就是这样，你在这里面先选`a`,就是在`console`里直接打进去（就像python里一样）。如果还是报错，输入‘n’就可以了。这里面的问题主要是这里面包和包之间里面往往互相依存、互相需要调用，如果缺的话可能会导致安装失败。

​	**第三种**就是一些非常新的算法，文章刚刚发出来的时候新的算法会以r包的形式放在github上，我们就需要用`devtools`包从github下载安装相应的R包。这个东西还是很重要的，比如**[MCPcounter](https://genomebiology.biomedcentral.com/articles/10.1186/s13059-016-1070-5)、[PROGENy](https://www.nature.com/articles/s41467-017-02391-6#citeas)**,以及下文需要用到的单细胞的东西，等等算法你们胃癌没有算好的数据，万一需要你自己算的时候就会非常头疼。

```R
install.packages("devtools")
library(devtools)
devtools::install_github('dviraran/SingleR')
```

​	**很多时候，这样的方法不一定有用，往往会有明显的报错，比如：**

```R
Error: Failed to install 'SingleR' from GitHub:
  Timeout was reached: [api.github.com] Resolving timed out after 10010 milliseconds
```

或者：

```R
remotes::install_github("forestgeo/fgeo.habitat")
Downloading GitHub repo forestgeo/fgeo.habitat@master
Error in utils::download.file(url, path, method = download_method(), quiet = quiet,  : 
  cannot open URL 'https://api.github.com/repos/forestgeo/fgeo.habitat/zipball/master'
```

**这些问题我统统理解为R语言的网不好**，登录github时经常会报错，有时候开了vpn也没有用，我研究出了两个方法供你参考

1. **首先，下载并运行[FastGithub](https://github.com/dotnetcore/FastGithub/releases)软件,你应该用的是MacOS版本，这个东西可以直接监视你的电脑到github服务器的流量情况，如果你可以看到有相当多的流量跑进来，你就可以知道你的R包安装成功了！**

<img src="https://gitee.com/ziyuexu_fudan/pic/raw/master/blog/image-20230208134422702.png" alt="image-20230208134422702" style="zoom:67%;" />

2. **还有，改变你的R studio连接到Github服务器的方式：**

 ```R
options(download.file.method = "wininet")
 ```


这些方法我用过了，基本上能搞定，实在不行，就把VPN开开关关，尝试一下吧~

其他解决方案参考：

**[解决install_github安装R包时无法打开（cannot open）URL？](https://www.cnblogs.com/jessepeng/p/14077003.html)**

**[r - devtools::install_github failing in R4.0.03 - Stack Overflow](https://stackoverflow.com/questions/65421798/devtoolsinstall-github-failing-in-r4-0-03)**

**还有一点，需要你提前做的，就是需要提前把Rtools在你的电脑上装好，参考**：[单细胞测序数据分析&Rtools的安装、环境配置及其应用 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/594242915)，很多全新的包需要Rtools进行编译。

------

### 1 Figure 1A 4A 热图

好，接下来我们就一个个来看这些图在r语言是怎么出来的吧~

`参考代码：heatmap.R`

`参考数据：heatmap.csv`

![image-20230208135818430](https://gitee.com/ziyuexu_fudan/pic/raw/master/blog/image-20230208135818430.png)

**输入文件，我比较喜欢用csv的格式，这里sep=’,’不要变，同时，尽量第一列是不重复的病人ID，因为会报错的。**

```R
library(pheatmap)
setwd('D:/MIBC_dataset/Code & Sample Data')
GC<-read.table("heatmap.csv",header = T,sep=",",check.names = F,row.names = 1)
mat <- as.matrix(GC)##这里不要漏了
bk <- c(seq(0,2,by=0.01))    #设定色彩给所定的值域
pheatmap(mat,cluster_row=FALSE, cluster_col=F,show_colnames = TRUE,
color = c(colorRampPalette(colors = c("#005f73","#e9d8a6"))(length(bk)/2),colorRampPalette(colors = c("#e9d8a6","#ae2012"))(length(bk)/2)),scale='column',breaks=bk)
```

首先，你要注意，`mat`就是我们最后转换的数据，`cluster_row=FALSE`和`cluster_col=F`**保证了你最后热图输出顺序和输入数据一样，没有聚类。**
这样代码跑出来的数据在最后展示到热图的时候，`pheatmap`函数已经把你的数据标准化到了（-2，2）的区间。换句话说，如果你的数据如果落在(0,1)的范围内，需要把最后的代码改成：

```R
library(pheatmap)
setwd('D:/MIBC_dataset/Code & Sample Data')
GC<-read.table("heatmap.csv",header = T,sep=",",check.names = F,row.names = 1)
mat <- as.matrix(GC)##这里不要漏了
bk <- c(seq(0，1,by=0.01))    #设定色彩给所定的值域
pheatmap(mat,cluster_row=FALSE, cluster_col=F,show_colnames = TRUE,
         color = c(colorRampPalette(colors = c("#005f73","#e9d8a6"))(length(bk)/2),colorRampPalette(colors = c("#e9d8a6","#ae2012"))(length(bk)/2)),breaks=bk)

```

换句话说，我这里面不让函数帮我标准化数据，所以把`scale='column'`给去掉了

然后你可以继续选择配色。

**有关配色，热图的颜色都是<u>*渐变*</u>的，很难用AI调整，所以必须要在R语言中确定好。我最喜欢的网站就是[Colors-The super fast color palette generator](https://coolors.co/)，里面配色比较多，而且可以直接复制颜色的代码，来定义你热图两个端点的颜色。，比如说这里我选了**`#005f73` **作为我最低分数患者的颜色，**`#e9d8a6`作为中点，然后`#ae2012` 作为另外一个端点。同样，在我们希望展示数据绝对值时，只需要定义好两个端点和中点的颜色就可以了。

还有一点需要注意的是，虽然简单的`pheatmap(mat,cluster_row=FALSE, cluster_col=F,show_colnames = TRUE)`是可以很方便出一个热图，但是我们没有办法去定义颜色的同时，没有定义两个端点同时会让整个标准化非常的离散。举一个例子，我给你的代码会让大多数的数据落在`(-2,2)`的区间，但是如果我们有一个极端值（比如说大多数人有几十个免疫细胞浸润，我们现在发现有一个人有两百个免疫细胞浸润)，那么如果不事先定义端点（比如说`(-2,2)`)，最后的结果就会很难看，绝大多数人都因为比较少就会无法看出趋势，因为绝大多数的端点值都被刚刚那个异常值拉的太满了，就像下图这样。

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303130905325.png" alt="image-20230313090540193" style="zoom:50%;" />

#### AI美化技巧&注意事项-heatmap

在实战当中，往往都需要你把多张热图以特定顺序排到一起。拿我这张图为例，实际上是由两张热图拆开来拼在一起的。你在最后可视化的时候，**不需要提前把数据标准化**，我代码里的`scale='column'`已经让热图自然把我们的数据标准化了。有些时候，你的一些非连续变量也可以放进热图跑出来（比如说分型、死亡状态、分期等等）,然后根据颜色的深浅更改。一个很简单的快捷键就是alt+M+S+R，选中所有相同填色描边，你就可以把所有某个颜色的方块选中，然后更改他们的配色。

还有一个点，需要你注意的就是很多时候你的这个图是需要拆开来进行展示的。有些非连续变量，我们要把它拆到下面来看，钊沛师兄告诉我了一个好方法：

1. 你选中一行热图方块，按方向键往你要分离的方向按几下（记住次数）然后你会看到你的热图有一部分被拉长了
2. 然后你在尺寸栏里改成和上面一样的size,以此类推，你就能保证你的几行热图分布均匀同时，也可以让你的每行热图粗细相等。

------

### 2 Figure 1B 森林图

`参考代码：Forestplot.R`

`参考数据：Forestplot.csv`

<img src="https://gitee.com/ziyuexu_fudan/pic/raw/master/blog/image-20230208155218117.png" alt="image-20230208155218117" style="zoom: 67%;" />

**数据结构如下表所示：**

| Group  | Hazard Ratio | P Value | HR(95%CI)          | HR    | LowerCI | UpperCI |
| ------ | ------------ | ------- | ------------------ | ----- | ------- | ------- |
| Age    | 1.103        | 0.786   | 0.786(0.545-2.233) | 1.103 | 0.545   | 2.233   |
|        | 0.694        | 0.345   | 0.694(0.325-1.480) | 0.694 | 0.325   | 1.48    |
| Gender | 1.587        | 0.371   | 0.371(0.577-4.364) | 1.587 | 0.577   | 4.364   |
|        | 0.51         | 0.149   | 0.51(0.204-1.273)  | 0.51  | 0.204   | 1.273   |
| Grade  | 2.547        | 0.135   | 0.135(0.751-8.635) | 2.547 | 0.751   | 8.635   |
|        | 1.424        | 0.588   | 1.424(0.397-5.113) | 1.424 | 0.397   | 5.113   |
| LVI    | 0.907        | 0.812   | 0.812(0.407-2.023) | 0.907 | 0.407   | 2.023   |
|        | 0.36         | 0.039   | 0.36(0.136-0.952)  | 0.36  | 0.136   | 0.952   |

```R
install.packages('survminer')
install.packages('survival')
install.packages('forestplot')
install.packages('stringr')
library(survminer)
library(survival)
library(forestplot)
library(stringr)
library(forestplot)  
library(stringr)  
forest <- read.csv("forest.csv",header = T)  
attach(forest)  
#输入文件如图所示，前三列是你需要当作文本放进去的图，后面这三列才是真正重要的需要画森林图的数据
```

**这一步很重要，界定了哪些是文本内容哪些是用来画矢量图的**，未来如果你想要不同的数据，你也可以用`as.matrix(forest[,1:3])`等等方法调整。  

```R
labeltext <- as.matrix(forest[,1:3])  
```

**接下来就是作图了，实际上重要的就是三个参数`mean = HR`，`lower = LowerCI`，和`upper = UpperCI`,其他都是可以用AI调整的：**

```R
forestplot(labeltext,  # 图形文本部分              
mean = HR,  # 图形 HR 部分             
lower = LowerCI, # 95%CI下限             
upper = UpperCI, # 95%CI上限  
zero = 1.0, lwd.zero = 3, # 设置无效线的横坐标和线条宽度  
xlab = "<——Sensitivity to Atezolizumab  Resistance to Atezolizumab——>",   
boxsize = 0.2,# 设置x轴标题  
xlim = c(0, 2),  #这一行没什么用，你如果发现HR值上界太高就都改成2，画个箭头
xticks = c(0,0.5,1,1.5,2), # 设置x轴刻度ticks  
lwd.xaxis = 2) # 设置无效线的线条宽度  
#所有的结果都可以用spss直接粘过来，像下图这样哈
```

告诉你一个小窍门，`xticks = c(0,0.5,1,1.5,2)`这里就不需要改了，因为你大于2的HR值（比如在这边都拉到8.635了）没有太多的意义,最后这个森林图不可能因为一两个异常值而拉的很长的（具体看我文章的Figure 3A）

### 3 Figure 1C & Figure 2 生存曲线

`参考代码：Kaplan-meier curve.R`

`参考数据：Kaplan-meier curve.csv`

![image-20230208155400520](https://gitee.com/ziyuexu_fudan/pic/raw/master/blog/image-20230208155400520.png)

**首先，我们看一下数据结构是怎么样的：**

| ID           | OS    | Death | ACT  |
| ------------ | ----- | ----- | ---- |
| TCGA-ZF-AA5P | 12.22 | 0     | 0    |
| TCGA-ZF-AA5N | 5.52  | 1     | 1    |
| TCGA-ZF-AA5H | 29.47 | 0     | 1    |
| TCGA-ZF-AA58 | 54.18 | 0     | 1    |
| TCGA-ZF-AA56 | 8.51  | 1     | 0    |
| TCGA-ZF-AA53 | 57.86 | 0     | 1    |
| TCGA-ZF-AA52 | 35.38 | 1     | 0    |
| TCGA-ZF-AA51 | 56.31 | 0     | 1    |
| TCGA-ZF-AA4W | 60.12 | 0     | 1    |
| TCGA-ZF-AA4V | 59.33 | 0     | 1    |
| TCGA-ZF-AA4U | 8.61  | 1     | 0    |
| TCGA-ZF-AA4R | 34.04 | 1     | 1    |

接下来看一下代码：首先是

```R
library(ggplot2)
library(survival)
library(survminer)
setwd('D:/MIBC_dataset/Code & Sample Data')
dataset<-read.csv('Kaplan_meier_curve.csv',header=TRUE,sep=',',row.names=1)
#dataset<-subset(dataset,dataset$AJCC_Stage<4)
#dataset<-subset(dataset,dataset$consensusClass=='NE-like')
```

​	上面这两行被我注释掉的代码往往应用于我想给病人分分层（**比如说病人是否为II+III期患者等等**）;因为有些时候，一些系统疗法对于不同病人的应用地位和逻辑不一定一样。IV期的病人，比如说，化疗就不一定是很好的选择。

```R
c1=survfit(Surv(OS,Death) ~ ACT, data = dataset)#(生存时间和死亡+因子）
ggsurvplot(c1,risk.table=TRUE,break.time.by = 4,    #设置x轴断点
                                    pval.coord=c(25,0.1)
									,pval = TRUE
                                    ,pval.method = TRUE,xlim=c(0,24))    #设置x轴值域
```

`risk.table=TRUE`决定了是否需要风险表。

`break.time.by = 4`决定了你x轴的刻度，可以根据不同队列调整，比如说TCGA的随访时间很长，刻度就要调长一点。

`pval = TRUE,pval.method = TRUE`决定了图上是否需要P值。（注意，最后在AI调的时候需要写成 Log-rank *P* ）

需要注意的是，很有可能图像的创建的过程会把P值盖住，你需要在AI里调。

#### AI注意事项

需要注意的是，生存曲线的那个删失符号（也就是为了表示病人在随访时间点结束后仍然没有死亡)在AI里是一个`+`号，所以你不要随便改动它，就让它按照缩放的比例进行缩放就好~

### 4 Figure 3A雷达图

参考代码：`radarplot.R`

参考数据：`radarplot.csv`

![image-20230208155751113](https://gitee.com/ziyuexu_fudan/pic/raw/master/blog/image-20230208155751113.png)

```R
library(fmsb)
k=read.csv('radar.csv',sep=',',header=TRUE,fileEncoding = "UCS-2LE",row.names=1)
radarchart(k)
#这些我上次给你说过咯，这里就不再赘述了
#你要做的就是之后在AI里调就可以了
```

### 5 Figure3C 冲击图

参考代码：`Sankeyplot.R`

参考数据：`Sankeyplot.csv`

<img src="https://gitee.com/ziyuexu_fudan/pic/raw/master/blog/image-20230208155758202.png" alt="image-20230208155758202" style="zoom:67%;" />

首先看一下数据结构，这就是平常的：

| ID       | binaryResponse | IC_Level |
| -------- | -------------- | -------- |
| SAM0257b | SD/PD          | IC1      |
| SAM025b4 | SD/PD          | IC1      |
| SAM032c6 | SD/PD          | IC2+     |
| SAM0571f | SD/PD          | IC2+     |
| SAM06589 | CR/PR          | IC2+     |
| SAM0684a | CR/PR          | IC0      |
| SAM075e0 | CR/PR          | IC1      |

```R
library(ggalluvial)
library(ggplot2)
library(dplyr)
setwd('D:/MIBC_dataset/Code & Sample Data')
```

这一行代码最重要！！相当于我把整个dataset用两种特征对每个人分类统计，这是唯一一个需要直接预处理的数据

```r
Data <- group_by(data,b=binaryResponse,IC_level) %>% summarise(., count = n())
```

我们还可以看一下**分类统计**的结果是怎样的。

```r
> head(Stratified_Data)
# A tibble: 6 × 3
# Groups:   binaryResponse [2]
  binaryResponse IC_Level count
  <chr>          <chr>    <int>
1 CR/PR          IC0         13
2 CR/PR          IC1         20
3 CR/PR          IC2+        35
4 SD/PD          IC0         70
5 SD/PD          IC1         92
6 SD/PD          IC2+        67
```

可以看到，他帮你按照了`binaryResonse`和`IC_Level`统计出每类样本的数目。

`aes(axis1 = binaryResponse, axis2 = IC_level, y= count)`就意味着我们的第一根轴是`binaryResponse`，第二根轴是`IC_Level`，然后以每类样本的数目即`counts`为纵坐标。 `geom_alluvium(aes(fill =binaryResponse))`即决定了桑吉图的颜色由我的`binaryResponse`所决定。

```r
data <- read.csv("sankey.csv",header=TRUE)#装包+读数据
ggplot(data,
       aes(axis1 =binaryResponse, axis2 = IC_level,
           y= count)) +
  scale_x_discrete(limits = c("AJCC_STAGE", "SEX"), expand = c(.1, .05)) +
  geom_alluvium(aes(fill =binaryResponse)) +
  geom_stratum() + geom_text(stat = "stratum", label.strata = TRUE) +
  theme_minimal() +
  ggtitle("Patients in the IMvigor210 Cohort",
          "stratified by PD-L1 expression and RECIST response")
```

### 6 Figure3D 带点箱线图

<img src="https://gitee.com/ziyuexu_fudan/pic/raw/master/blog/image-20230208160111760.png" alt="image-20230208160111760" style="zoom:50%;" />

`参考数据：Boxplot.csv`

`参考代码：Boxplot.R`

```R
library(ggplot2)
setwd("C:/Users/px874/Desktop")
GC<-read.table("sankey.csv",header = T,sep=",",check.names = T))
#装包+读数据，这里一定要注意，你的分类变量（比如说FAT4mut/wt）,必须要用字母来写，不然会报错
ggplot(data=GC,aes(x=b7h3_cut,
                               y= CD8_ratio_Treg,
                                shape=b7h3_cut,
                                colour=b7h3_cut))+
  ylab(" CD8_ratio_Treg")+
  xlab(NULL)+
  geom_boxplot(aes(x=b7h3_cut, y= CD8_ratio_Treg),size=1)+
  geom_jitter(width =0.2,shape = 16,size=2)+
  theme_bw()+#这后面的参数都不用改，我如果要批量出图的话会直接ctrl+H替换
  theme(axis.line = element_line(size=1, colour = "black"),
        panel.grid =element_blank(),panel.border = element_blank(),
        axis.title = element_text(size=14,color = "black"),
        axis.text = element_text(size=14,face="plain",color = "black"),
        axis.text.x = element_blank(),
        legend.title = element_blank(),axis.title.y=element_text(vjust = 3),
        axis.title.x=element_text(vjust = -0.6),
        legend.position = "top",
        rect = element_rect(fill = "white",  colour = "black", size= 0.5, linetype = 1))
```

#### AI可视化技巧

这个图本身实际上不难做，但是有一点我需要你**特别注意**，未来在你做这个图的时候，你要是想要拼出来Figure 1 Figure 2 等等，这个图就需要不停地被缩放。除了需要改填色、描边之外，你需要注意你的这个图里代表每个sample的点会变成很丑的椭圆，因此，我们需要固定好每个点的形状和大小。

所以你这里需要干一件事就是选中所有的点，即`alt+S+M+R`,即（选择——相同——填色和描边）,之后再把他们都变成**大小相同的椭圆**，即`alt+C+V+E`。你就可以定义所有的点的大小和形状了~

### 7 Single Sample Gene Set Enrichment Analysis(ssGSEA)

参考代码：`ssGSEA.R`

参考数据：`fpkm.csv`;`geneset.gmt`

ssGSEA和我们的GSEA原理相似，但是和之前的行为不同，我们关注的不是两个队列的基因富集情况，而是队列中每个患者的每个通路霍华德情况。

```r
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")
BiocManager::install("limma")

if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")
BiocManager::install("GSVA")

if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")
BiocManager::install("GSEABase")

if (!requireNamespace("BiocManager", quietly = TRUE))
  install.packages("BiocManager")
BiocManager::install("BiocGenerics")

if (!requireNamespace("BiocManager", quietly = TRUE))
  install.packages("BiocManager")
BiocManager::install("parallel")
#上面三行就直接ctrl+enter跑就行了，把需要的程序包从网上下载下来
setwd('C:/Users/px874/Desktop/B7H3')          #设置工作目录
inputFile="fpkm.csv"                                         #输入文件
gmtFile="geneset1.gmt"                                           #GMT文件
#输入文件是两个，首先是基因表达矩阵，最好是.csv格式的，就是每个样本每个基因的表达量；
#还有就是gmt文件，可以用excel编辑的一个基因集。
#gmt文件可以用excel打开，你可以直接拿我给你的数据在里面改，只要是这个格式就可以了。
#引用包
library(GSVA)
library(limma)
library(GSEABase)
setwd('D:/MIBC_dataset/Code & Sample Data')
#读取输入文件，并对输入文件处理
rt=read.table(inputFile,sep=",",header=T,check.names=F)
rt=as.matrix(rt)
rownames(rt)=rt[,1]
exp=rt[,2:ncol(rt)]
dimnames=list(rownames(exp),colnames(exp))
mat=matrix(as.numeric(as.matrix(exp)),nrow=nrow(exp),dimnames=dimnames)
mat=avereps(mat)
mat=mat[rowMeans(mat)>0,]
geneSet=getGmt(gmtFile, 
               geneIdType=SymbolIdentifier())

#ssgsea分析
ssgseaScore=gsva(mat, geneSet, method='ssgsea', kcdf='Gaussian', abs.ranking=TRUE)
#定义ssGSEA score矫正函数
normalize=function(x){
  return((x-min(x))/(max(x)-min(x)))}
#对ssGSEA score进行矫正
ssgseaOut=normalize(ssgseaScore)
ssgseaOut=rbind(id=colnames(ssgseaOut),ssgseaOut)
write.table(ssgseaOut,file="ssgseaOut-sig.txt",sep="\t",quote=F,col.names=F)
#ssgseaout就是最后得到的结果。

```

`ssGSEA`是我们运用转录组方法刻画生物学行为最为简单、方便且可靠的方法，因此我十分推荐~

### 8 Supplementary Figure 3 百分比柱状图

参考代码：`percentage_barplot.R`

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202302121842331.png" alt="image-20230212184239235" style="zoom: 50%;" />

```r
ggplot(data = GC,aes(x=group1,y=proportion,fill=property))+
  geom_bar(stat = "identity",
           position = "fill")
```

### 9 GSEA分析以及相应作图

参考代码：`gsea_visualize.R`

参考数据：`gsea_visualize.csv`

<img src="https://gitee.com/ziyuexu_fudan/pic/raw/master/blog/image-20230208162139408.png" alt="image-20230208162139408" style="zoom: 50%;" />

​	GSEA分析最根本的逻辑，就是能够在两群人中间我们通过能去批量地筛选我们定义的两类人中需要的表型。比如说，我不确定*FAT4*突变会给GC患者带来怎样的影响，指导未来的分层决策，我就可以很简单地用GSEA的方法观察那些通路在*PIK3CA*mut的患者中激活、抑制。我觉得这个方面是很重要的，因为这是你生存曲线之外另外一个非常强有力的证据、指导你的课题往下开展的依据。
​	GSEA的主要原理是基因排序和富集分析。最主要的方法，大概是有两种即**软件**（*PNAS*,2005）和 `clusterProfiler`r包。后者主要是我自己也不大用，我在这里跟你介绍一下我用的方法, 我认为相对来说是用的最普遍、最方便也是最好看的方法。

#### i Recap of GSEA

1. 首先，数据的准备。

   需要注意的是，你数据的格式可能会有这样那样的问题，但是，你只需要准备两个数据，就可以实现GSEA分析，**即表达矩阵和基因集文件**。

   首先，对于表达矩阵而言，软件支持`.gct`和`.txt`两种格式，**你需要保证你的矩阵里不存在重复表达的基因，**如果不小心存在的话，你可以运用`limma`包里的`avereps`开对重复值取平均。不过，大多数情况下都不会出这样的问题，你可以直接用excel看是不是有明显的重复值。有时候由于ID匹配不佳的缘故，最后几行没有基因名称，读取矩阵就会出错。

   **同时，我们需要一个以`.gmt`为格式的基因集文件。**大多数情况下，很多基因集都可以在[GSEA | MSigDB | Human MSigDB Collections (gsea-msigdb.org)](https://www.gsea-msigdb.org/gsea/msigdb/human/collections.jsp#H)得到下载，但有些时候我们找不到我们需要的`.gmt`文件，我们就需要自己去改`.gmt`文件，如果你用excel打开它，就会长这样：

   | HALLMARK_TNFA_SIGNALING_VIA_NFKB    | JUNB    | CXCL2   | ATF3   | NFKBIA | TNFAIP3 | PTGS2  |
   | ----------------------------------- | ------- | ------- | ------ | ------ | ------- | ------ |
   | HALLMARK_HYPOXIA                    | PGK1    | PDK1    | GBE1   | PFKL   | ALDOA   | ENO2   |
   | HALLMARK_CHOLESTEROL_HOMEOSTASIS    | FDPS    | CYP51A1 | IDI1   | FDFT1  | DHCR7   | SQLE   |
   | HALLMARK_MITOTIC_SPINDLE            | ARHGEF2 | CLASP1  | KIF11  | KIF23  | ALS2    | ARF6   |
   | HALLMARK_WNT_BETA_CATENIN_SIGNALING | MYC     | CTNNB1  | JAG2   | NOTCH1 | DLL1    | AXIN2  |
   | HALLMARK_TGF_BETA_SIGNALING         | TGFBR1  | SMAD7   | TGFB1  | SMURF2 | SMURF1  | BMPR2  |
   | HALLMARK_IL6_JAK_STAT3_SIGNALING    | IL4R    | IL6ST   | STAT1  | IL1R1  | CSF2RB  | SOCS3  |
   | HALLMARK_DNA_REPAIR                 | POLR2H  | POLR2A  | POLR2G | POLR2E | POLR2J  | POLR2F |
   | HALLMARK_G2M_CHECKPOINT             | AURKA   | CCNA2   | TOP2A  | CCNB2  | CENPA   | BIRC5  |
   | HALLMARK_APOPTOSIS                  | CASP3   | CASP9   | DFFA   | CASP7  | CFLAR   | BIRC3  |
   | HALLMARK_NOTCH_SIGNALING            | JAG1    | NOTCH3  | NOTCH2 | APH1A  | HES1    | CCND1  |
   | HALLMARK_ADIPOGENESIS               | FABP4   | ADIPOQ  | PPARG  | LIPE   | DGAT1   | LPL    |

   很多时候，一个文章提出了一个基于转录组的signature，我们就可以把他提出的几个基因像上面这样，黏贴进入我们的这个`.gmt`文件。

2. 具体操作

   <img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303131030986.png" alt="image-20230313103037833" style="zoom:50%;" />

   首先，像这里一样，把需要的数据导入进去，最后点'Load these files!'就可以了。

   <img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303131032207.png" alt="image-20230313103233135" style="zoom: 50%;" />

这个就是成功的提示。

然后，我们点软件里的Run GSEA，看到这个窗口：

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303131033216.png" alt="image-20230313103359126" style="zoom:50%;" />

窗口里，`Expression dataset`就是我们刚刚放入的表达数据，然后，在`Gene sets database` 里点击我们需要的.gmt文件。当然，你也可以选择通过这边直接上网批量下载你需要的数据。

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303131043188.png" alt="image-20230313104336078" style="zoom:67%;" />

**（即Local GMX/GMT )**

接着我们定义我们的`Phenotype labels`,也就是你事先定义的两群人，比如说*PIK3CA*突变和不突变的人群，我们点击`Create an on-the-fly phenotype`,把两群人的ID黏贴进去后再点击`Apply to dataset`，我们就可得到我们需要的结果了！

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303131055055.png" alt="image-20230313105527940" style="zoom:50%;" />

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303131056876.png" style="zoom: 67%;" />

![image-20230313105831778](https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303131058902.png)

（成功的提示）

最后，你一定要注意哪一组是突变，哪一组野生，并且最后选择的是哪一组和哪一组相比，就像下面这样，如果你选反了，整个结论就是反的(如下图）：

![image-20230313110039836](https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303131100946.png)

然后把`Number of permutations`改为`100`，`Collapse/Remap to Gene Samples`改为`No_Collapse`。所有的参数，最后的结果就像这样：

![image-20230313110433020](https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303131104139.png)

点击下面的`Run`我们就可以开始了！

![image-20230313110807438](https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303131108604.png)

成功后就像这样（不要care warnings)，点进去会有一个`.html`文件，然后点击`Snapshot of enrichment results`，我们就可以看到最显著富集通路的结果。但是，需要注意的是，如果你的.gmt里有很多个基因集，那么不是所有的通路他都会显示，只有那些比较富集的结果才会帮你出个图。一般我们认为，Normalized Enrichment Score的绝对值大于1.5，p值小于0.05，就是先显著富集的结果。

但是呢，这个图却支持.jpg格式，放到文章里会很糊，所以我们采取的措施就是用R语言再加工一下。

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303131132965.png" alt="image-20230313113237885" style="zoom:50%;" />

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303131112691.png" alt="image-20230313111203508" style="zoom:50%;" />



#### ii GSEA visualization

我们在上面这个页面里，可以看到一个叫做**GSEA details**的表，我们点那个**plain text format**,就可以把那个表复制到excel里，理成下面的着数据结构。

| Symbol | RANK.IN.GENE.LIST | RUNNING.ES | pathway |
| :----: | :---------------: | :--------: | :-----: |
| HAVCR2 |         1         |   0.0272   |    a    |
| FOXP3  |         2         |   0.0541   |    a    |
| HLA-E  |        10         |   0.0795   |    a    |
|  SPI1  |        24         |   0.1037   |    a    |
| CD300A |        45         |   0.127    |    a    |
| SLAMF8 |        46         |   0.1511   |    a    |

这个`pathway`的变量需要我们注意，本身并没有这样一个变量，是我们为了作图方便，为了让不同的通路有不同的颜色。对每一个通路的基因分配相应的`pathway`就可以了，第一个通路各个基因的`pathway`属于`a`,另外一个通路所有基因的`pathway`属于`b`,以此类推。

```r
setwd('D:/MIBC_dataset')
gsea1=read.csv('gsea_visualize.csv',header=T)
```

这里读取数据的是后，不要用`row.names=1`，因为很有可能不同的通路他们的基因有重合，所以会报错。

```r
# plot
up <- ggplot(gsea1,aes(x = RANK.IN.GENE.LIST,y = RUNNING.ES,color = pathway)) +
  # 0水平线
  geom_hline(yintercept = 0,color = 'grey',size = 1) +
  # 曲线图层
  geom_line(show.legend = F,size = 1) +
  # 颜色设置
  scale_color_manual(values = c('#2A9D8F','#e9c46a','#e76f51')) +
  theme_bw(base_size = 18) +
  # 主题细节调整
  theme(panel.grid = element_blank(),
        axis.ticks.length = unit(0.25,'cm'),
        panel.border = element_rect(size = 2),
        axis.text.x = element_blank(),
        axis.title.x = element_blank(),
        axis.ticks.x = element_blank()) +
  ylab('Enrichment score') 
up
```


```r
mid <- ggplot(gsea1,aes(x = RANK.IN.GENE.LIST,y = pathway)) +
  # 线图层
  geom_vline(aes(xintercept = RANK.IN.GENE.LIST,
                 color = pathway),
             show.legend = F,size = 0.5) +
  # 颜色
  scale_color_manual(values = c('#2A9D8F','#e9c46a','#e76f51')) +
  theme_classic(base_size = 18) +
  # 分面
  facet_wrap(~pathway,ncol = 1) +
  # 主题调整
  theme(strip.background = element_blank(),
        strip.text = element_blank(),
        axis.ticks.length = unit(0.25,'cm'),
        axis.text.x = element_blank(),
        axis.title.x = element_blank(),
        axis.line.x = element_blank(),
        axis.ticks.x = element_blank(),
        axis.line.y = element_line(color = 'white'),
        axis.title.y = element_text(color = 'white'))
mid
```

#### iii GSEA Barplot

### 10 Supplementary Figure 9A瀑布图

![image-20230212163040882](https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202302121711125.png)

**参考代码**：`Waterfall.R`

**参考数据：**`MSKCC_clinical_bladder.txt MSKCC_mutation_bladder.maf`
准备工作：我们需要准备两个数据，一个是`.maf`文件，一个是`.txt`文件。
所谓`.maf`文件，全称为突变注释(Mutation Annotation File)文件，是大多数公共数据库平台、测序公司给我们的数据的格式，大概长成这样：

| Hugo_Symbol                            | Entrez_Gene_Id | Center | NCBI_Build | Chromosome | Start_Position | End_Position | Strand | Consequence        | Variant_Classification | Variant_Type | Reference_Allele | Tumor_Seq_Allele1 | Tumor_Seq_Allele2 | dbSNP_RS | dbSNP_Val_Status | Tumor_Sample_Barcode | Matched_Norm_Sample_Barcode | Match_Norm_Seq_Allele1 | Match_Norm_Seq_Allele2 | Tumor_Validation_Allele1 | Tumor_Validation_Allele2 | Match_Norm_Validation_Allele1 | Match_Norm_Validation_Allele2 | Verification_Status | Validation_Status | Mutation_Status | Sequencing_Phase | Sequence_Source | Validation_Method | Score | BAM_File | Sequencer | t_ref_count                      | t_alt_count       | n_ref_count | n_alt_count     | HGVSc          | HGVSp   | HGVSp_Short | Transcript_ID | RefSeq | Protein_position | Codons | Hotspot |
| -------------------------------------- | -------------- | ------ | ---------- | ---------- | -------------- | ------------ | ------ | ------------------ | ---------------------- | ------------ | ---------------- | ----------------- | ----------------- | -------- | ---------------- | -------------------- | --------------------------- | ---------------------- | ---------------------- | ------------------------ | ------------------------ | ----------------------------- | ----------------------------- | ------------------- | ----------------- | --------------- | ---------------- | --------------- | ----------------- | ----- | -------- | --------- | -------------------------------- | ----------------- | ----------- | --------------- | -------------- | ------- | ----------- | ------------- | ------ | ---------------- | ------ | ------- |
| PIK3CB                                 | 5291           | MSKCC  | GRCh37     | 3          | 1.38E+08       | 1.38E+08     | +      | missense_variant   | Missense_Mutation      | SNP          | C                | C                 | T                 |          |                  | P-0024731-T01-IM6    |                             |                        |                        |                          |                          |                               | Unknown                       | SOMATIC             |                   |                 | MSK-IMPACT       |                 | 158               | 181   | 606      | 0         | ENST00000289153.2:c.3151G>A      | p.Glu1051Lys      | p.E1051K    | ENST00000289153 | NM_006219.2    | 1051    | Gaa/Aaa     | 0             |        |                  |        |         |
| TP53                                   | 7157           | MSKCC  | GRCh37     | 17         | 7577539        | 7577539      | +      | missense_variant   | Missense_Mutation      | SNP          | G                | G                 | A                 |          |                  | P-0024731-T01-IM6    |                             |                        |                        |                          |                          |                               | Unknown                       | SOMATIC             |                   |                 | MSK-IMPACT       |                 | 126               | 61    | 568      | 1         | ENST00000269305.4:c.742C>T       | p.Arg248Trp       | p.R248W     | ENST00000269305 | NM_001126112.2 | 248     | Cgg/Tgg     | 0             |        |                  |        |         |
| TP53                                   | 7157           | MSKCC  | GRCh37     | 17         | 7577099        | 7577099      | +      | missense_variant   | Missense_Mutation      | SNP          | C                | C                 | T                 |          |                  | P-0024731-T01-IM6    |                             |                        |                        |                          |                          |                               | Unknown                       | SOMATIC             |                   |                 | MSK-IMPACT       |                 | 170               | 118   | 679      | 1         | ENST00000269305.4:c.839G>A       | p.Arg280Lys       | p.R280K     | ENST00000269305 | NM_001126112.2 | 280     | aGa/aAa     | 0             |        |                  |        |         |
| PIK3C2G                                | 5288           | MSKCC  | GRCh37     | 12         | 18435667       | 18435667     | +      | missense_variant   | Missense_Mutation      | SNP          | G                | G                 | A                 |          |                  | P-0024731-T01-IM6    |                             |                        |                        |                          |                          |                               | Unknown                       | SOMATIC             |                   |                 | MSK-IMPACT       |                 | 91                | 54    | 313      | 2         | ENST00000266497.5:c.652G>A       | p.Glu218Lys       | p.E218K     | ENST00000266497 | 218            | Gaa/Aaa | 0           |               |        |                  |        |         |
| AXIN1                                  | 8312           | MSKCC  | GRCh37     | 16         | 347756         | 347756       | +      | missense_variant   | Missense_Mutation      | SNP          | C                | C                 | T                 |          |                  | P-0024731-T01-IM6    |                             |                        |                        |                          |                          |                               | Unknown                       | SOMATIC             |                   |                 | MSK-IMPACT       |                 | 124               | 26    | 692      | 0         | ENST00000262320.3:c.1750G>A      | p.Ala584Thr       | p.A584T     | ENST00000262320 | NM_003502.3    | 584     | Gct/Act     | 0             |        |                  |        |         |
| RECQL4                                 | 9401           | MSKCC  | GRCh37     | 8          | 1.46E+08       | 1.46E+08     | +      | inframe_deletion   | In_Frame_Del           | DEL          | CTC              | CTC               | -                 |          |                  | P-0024731-T01-IM6    |                             |                        |                        |                          |                          |                               | Unknown                       | SOMATIC             |                   |                 | MSK-IMPACT       |                 | 124               | 19    | 647      | 0         | ENST00000428558.2:c.3139_3141del | p.Glu1047del      | p.E1047del  | ENST00000428558 | NM_004260.3    | 1047    | GAG/-       | 0             |        |                  |        |         |
| GLI1                                   | 2735           | MSKCC  | GRCh37     | 12         | 57860075       | 57860075     | +      | frameshift_variant | Frame_Shift_Del        | DEL          | G                | G                 | -                 |          |                  | P-0024731-T01-IM6    |                             |                        |                        |                          |                          |                               | Unknown                       | SOMATIC             |                   |                 | MSK-IMPACT       |                 | 161               | 63    | 717      | 8         | ENST00000228682.2:c.821delG      | p.Gly274AlafsTer6 | p.G274Afs*6 | ENST00000228682 | NM_005269.2    | 272     | tGg/tg      | 0             |        |                  |        |         |
| BCL6                                   | 604            | MSKCC  | GRCh37     | 3          | 1.87E+08       | 1.87E+08     | +      | missense_variant   | Missense_Mutation      | SNP          | G                | G                 | A                 |          |                  | P-0024731-T01-IM6    |                             |                        |                        |                          |                          |                               | Unknown                       | SOMATIC             |                   |                 | MSK-IMPACT       |                 | 225               | 92    | 497      | 1         | ENST00000232014.4:c.953C>T       | p.Pro318Leu       | p.P318L     | ENST00000232014 | NM_001130845.1 | 318     | cCc/cTc     | 0             |        |                  |        |         |
| CASP8                                  | 841            | MSKCC  | GRCh37     | 2          | 2.02E+08       | 2.02E+08     | +      | missense_variant   | Missense_Mutation      | SNP          | G                | G                 | A                 |          |                  | P-0024731-T01-IM6    |                             |                        |                        |                          |                          |                               | Unknown                       | SOMATIC             |                   |                 | MSK-IMPACT       |                 | 142               | 86    | 538      | 1         | ENST00000432109.2:c.1238G>A      | p.Arg413Gln       | p.R413Q     | ENST00000432109 | NM_033355.3    | 413     | cGa/cAa     | 0             |        |                  |        |         |
| CDKN2A                                 | 1029           | MSKCC  | GRCh37     | 9          | 21971138       | 21971138     | +      | missense_variant   | Missense_Mutation      | SNP          | C                | C                 | T                 |          |                  | P-0024731-T01-IM6    |                             |                        |                        |                          |                          |                               | Unknown                       | SOMATIC             |                   |                 | MSK-IMPACT       |                 | 53                | 87    | 253      | 1         | ENST00000304494.5:c.220G>A       | p.Asp74Asn        | p.D74N      | ENST00000304494 | NM_000077.4    | 74      | Gac/Aac     | 0             |        |                  |        |         |
| CDKN2A                                 | 1029           | MSKCC  | GRCh37     | 9          | 21971138       | 21971138     | +      | missense_variant   | Missense_Mutation      | SNP          | C                | C                 | T                 |          |                  | P-0024731-T01-IM6    |                             |                        |                        |                          |                          |                               | Unknown                       | SOMATIC             |                   |                 | MSK-IMPACT       |                 | 53                | 87    | 253      | 1         | ENST00000304494.5:c.220G>A       | p.Asp74Asn        | p.D74N      | ENST00000304494 | NM_000077.4    | 74      | Gac/Aac     | 0             |        |                  |        |         |
| CDKN2A                                 | 1029           | MSKCC  | GRCh37     | 9          | 21971138       | 21971138     | +      | missense_variant   | Missense_Mutation      | SNP          | C                | C                 | T                 |          |                  | P-0024731-T01-IM6    |                             |                        |                        |                          |                          |                               | Unknown                       | SOMATIC             |                   |                 | MSK-IMPACT       |                 | 53                | 87    | 253      | 1         | ENST00000304494.5:c.220G>A       | p.Asp74Asn        | p.D74N      | ENST00000304494 | NM_000077.4    | 74      | Gac/Aac     | 0             |        |                  |        |         |
| **首先，我们把需要的包和数据加载好：** |                |        |            |            |                |              |        |                    |                        |              |                  |                   |                   |          |                  |                      |                             |                        |                        |                          |                          |                               |                               |                     |                   |                 |                  |                 |                   |       |          |           |                                  |                   |             |                 |                |         |             |               |        |                  |        |         |

```r
setwd('D:/MIBC_dataset/Code & Sample Data')
rm(list=ls())
library(maftools)
library(RColorBrewer)#读数据，装包
data=read.table("MSKCC_clinical.txt", header=T, sep="\t", check.names=F, row.names=1)
data1=read.table('MSKCC_clinical.txt', header=T, sep="\t", check.names=F, row.names=1)
maf <- read.maf(maf="MSKCC.maf",
                clinicalData="MSKCC_clinical.txt")
```

这里一定要注意，`.maf`文件里所有的人和`clinicalData`里所有的人**一定要一一匹配**，如果不匹配的话就像我上次给你匹配的那样，用文本编辑器把`.maf`文件粘到excel筛好人再粘回去，你也可以参考**第十一章**。

```r
predefined_pathways=data.frame(
Genes=c('TP53','CDKN2A','CDKN2B','RB1','STAG2','E2F3','CDKN1A','MDM2','FBXW7','FAT1'
	'FGFR3','EGFR','ERBB3','PIK3CA','TSC1','AKT','PTEN',
	'ARID1A','KDM6A','KMT2D','KMT2C','CREBBP','EP300','KMT2A'，
       'NCOR1'),
Pathway=rep(c('TP53/ Cell Cycle',
"RTK/RAS/PI3K",
"Chromatin Remodeling",
 '随便凑的'),
c(10,3,7,1)),
stringsAsFactors = FALSE)
```

这一行代码，你需要做的是找到各个通路上重要的基因，作为panel,比如说`'FGFR3','EGFR','ERBB3','PIK3CA','TSC1','AKT','PTEN'`对应的就是`"RTK/RAS/PI3K"`，一共就是9个基因，下面也对应好了（实际上就是一个`rep()`函数，下面返回的就是一个 Genes*Pathway 的`data frame`），就像下面这样：

|  Genes  |       Pathway        |
| :-----: | :------------------: |
| *FGFR3* |     RTK/RAS/PI3K     |
| *TP53*  |   TP53/ Cell Cycle   |
| *KDM6A* | Chromatin Remodeling |

**或者，你也可以手动选择基因，存在一个变量里。**

```r
aml_genes = c('BRCA1','BRCA2','ATM','ATR','RAD51C','RAD51D','BIRP1','CHEK2','CDK12',
"MSH2","MSH6","MLH1","PMS2",
"KMT2D","KDM6A","KMT2C","KMT2A","CREBBP","EP300",
"MYC","MYCL",'EGFR','ERBB2',"ERBB3",'MAP3K','TP53','TTN','RB1','ARID1A','MUC16')
```

这一行就是你要展示突变情况的那些基因，可以随意自定义，同时，`maftools`也支持我们以我们自己定义的顺序输出结果，只要我们的`order1`里的患者和

```R
order1<-as.vector(data1$Tumor_Sample_Barcode1)
```

```r
oncoplot(maf = maf,
genes=aml_genes,#这一行和下两行行代码必须要选一个注释掉，要么选你自定义的那个基因集，要么选你top20的基因，要么选你事先预定好的通路和相应的基因。
#top=20,
#pathways=pathways
clinicalFeatures = c("mrna_tmb_35"),#clinicalFeatures就是在你输入的clincal data里
#barcode_mar=23,
#gene_mar = 4,
sortByAnnotation = T,
sortByMutation = F,
groupAnnotationBySize =T,
#colors = brewer.pal(n=7,"Paired"),
#colors = vc_cols,
keepGeneOrder=T,
sampleOrder = order1,#这个就是你事先预定好的顺序。
)
```

但是话说回来，这里面就会有一个非常有趣的问题。`maftools`在可视化突变图景时，会有一个难以解决的bug，就是他*<u>**仅仅会提取所选基因突变患者的并集**</u>*。换句话说，尽管你的`clinicalData`里选的患者和`.maf`文件里的所有患者全都一一对应，**但如果你所选的基因panel里存在患者没有在这个panel里发生突变，那么这个患者将不会出现在你最后的瀑布图中。**

比如，你有215个样本，但是在你所选的panel里，比如说你的panel有15个不同的基因，这15个基因有20个人完全没有突变，那最后你瀑布图里只有195个人。这个是非常麻烦的，意味着你后面如果说对这个瀑布图里想用一些临床样本信息进行聚类，你都要采用195人的数据。这会带来不小的麻烦，尤其是对于几个频率不高的基因而言，审稿人很容易看出来你的图人数有问题。

因此，一个简单的解决方案就是改`.maf`文件。比如，我们可以选一个没有意义的基因，让他重复这里的215次，然后把215个重复突变的`Tumor_Sample_Barcode`改成215个病人的`Tumor_Sample_Barcode`,**假装有215个病人在这里’突变了‘，然后选这个基因进去，把215个人撑满，就像下面这样。**（你也可以在我的示例数据中发现）

| NCOR1 | 9611 | MSKCC | GRCh37 | 17   | 16004630 | 16004630 | +    | stop_gained      | Nonsense_Mutation | SNP  | G    | G    | C    |      |      | P-0010079-T01-IM5 |      |      |      |      |      | Unknown | SOMATIC |      |      | MSK-IMPACT |      | 1214 | 74   | 576  | 0    | ENST00000268712.3:c.2624C>G           | p.Ser875Ter | p.S875* | ENST00000268712 | NM_006311.3 | 875  | tCa/tGa | 0    |      |      | NEWRECORD |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |
| ----- | ---- | ----- | ------ | ---- | -------- | -------- | ---- | ---------------- | ----------------- | ---- | ---- | ---- | ---- | ---- | ---- | ----------------- | ---- | ---- | ---- | ---- | ---- | ------- | ------- | ---- | ---- | ---------- | ---- | ---- | ---- | ---- | ---- | ------------------------------------- | ----------- | ------- | --------------- | ----------- | ---- | ------- | ---- | ---- | ---- | --------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| NCOR1 | 9611 | MSKCC | GRCh37 | 17   | 16004707 | 16004708 | +    | missense_variant | Missense_Mutation | DNP  | GG   | GG   | TA   |      |      | P-0010079-T01-IM5 |      |      |      |      |      | Unknown | SOMATIC |      |      | MSK-IMPACT |      | 1389 | 164  | 691  | 0    | ENST00000268712.3:c.2546_2547delinsTA | p.Ala849Val | p.A849V | ENST00000268712 | NM_006311.3 | 849  | gCC/gTA | 0    |      |      | NEWRECORD |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |
| NCOR1 | 9611 | MSKCC | GRCh37 | 17   | 16004707 | 16004708 | +    | missense_variant | Missense_Mutation | DNP  | GG   | GG   | TA   |      |      | P-0010079-T01-IM5 |      |      |      |      |      | Unknown | SOMATIC |      |      | MSK-IMPACT |      | 1389 | 164  | 691  | 0    | ENST00000268712.3:c.2546_2547delinsTA | p.Ala849Val | p.A849V | ENST00000268712 | NM_006311.3 | 849  | gCC/gTA | 0    |      |      | NEWRECORD |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |
| NCOR1 | 9611 | MSKCC | GRCh37 | 17   | 16004707 | 16004708 | +    | missense_variant | Missense_Mutation | DNP  | GG   | GG   | TA   |      |      | P-0010079-T01-IM5 |      |      |      |      |      | Unknown | SOMATIC |      |      | MSK-IMPACT |      | 1389 | 164  | 691  | 0    | ENST00000268712.3:c.2546_2547delinsTA | p.Ala849Val | p.A849V | ENST00000268712 | NM_006311.3 | 849  | gCC/gTA | 0    |      |      | NEWRECORD |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |
| NCOR1 | 9611 | MSKCC | GRCh37 | 17   | 16004707 | 16004708 | +    | missense_variant | Missense_Mutation | DNP  | GG   | GG   | TA   |      |      | P-0010079-T01-IM5 |      |      |      |      |      | Unknown | SOMATIC |      |      | MSK-IMPACT |      | 1389 | 164  | 691  | 0    | ENST00000268712.3:c.2546_2547delinsTA | p.Ala849Val | p.A849V | ENST00000268712 | NM_006311.3 | 849  | gCC/gTA | 0    |      |      | NEWRECORD |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |
| NCOR1 | 9611 | MSKCC | GRCh37 | 17   | 16004707 | 16004708 | +    | missense_variant | Missense_Mutation | DNP  | GG   | GG   | TA   |      |      | P-0010079-T01-IM5 |      |      |      |      |      | Unknown | SOMATIC |      |      | MSK-IMPACT |      | 1389 | 164  | 691  | 0    | ENST00000268712.3:c.2546_2547delinsTA | p.Ala849Val | p.A849V | ENST00000268712 | NM_006311.3 | 849  | gCC/gTA | 0    |      |      | NEWRECORD |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |
| NCOR1 | 9611 | MSKCC | GRCh37 | 17   | 16004707 | 16004708 | +    | missense_variant | Missense_Mutation | DNP  | GG   | GG   | TA   |      |      | P-0010079-T01-IM5 |      |      |      |      |      | Unknown | SOMATIC |      |      | MSK-IMPACT |      | 1389 | 164  | 691  | 0    | ENST00000268712.3:c.2546_2547delinsTA | p.Ala849Val | p.A849V | ENST00000268712 | NM_006311.3 | 849  | gCC/gTA | 0    |      |      | NEWRECORD |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |
| NCOR1 | 9611 | MSKCC | GRCh37 | 17   | 16004707 | 16004708 | +    | missense_variant | Missense_Mutation | DNP  | GG   | GG   | TA   |      |      | P-0010079-T01-IM5 |      |      |      |      |      | Unknown | SOMATIC |      |      | MSK-IMPACT |      | 1389 | 164  | 691  | 0    | ENST00000268712.3:c.2546_2547delinsTA | p.Ala849Val | p.A849V | ENST00000268712 | NM_006311.3 | 849  | gCC/gTA | 0    |      |      | NEWRECORD |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |
| NCOR1 | 9611 | MSKCC | GRCh37 | 17   | 16004707 | 16004708 | +    | missense_variant | Missense_Mutation | DNP  | GG   | GG   | TA   |      |      | P-0010079-T01-IM5 |      |      |      |      |      | Unknown | SOMATIC |      |      | MSK-IMPACT |      | 1389 | 164  | 691  | 0    | ENST00000268712.3:c.2546_2547delinsTA | p.Ala849Val | p.A849V | ENST00000268712 | NM_006311.3 | 849  | gCC/gTA | 0    |      |      | NEWRECORD |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |
| NCOR1 | 9611 | MSKCC | GRCh37 | 17   | 16004707 | 16004708 | +    | missense_variant | Missense_Mutation | DNP  | GG   | GG   | TA   |      |      | P-0010079-T01-IM5 |      |      |      |      |      | Unknown | SOMATIC |      |      | MSK-IMPACT |      | 1389 | 164  | 691  | 0    | ENST00000268712.3:c.2546_2547delinsTA | p.Ala849Val | p.A849V | ENST00000268712 | NM_006311.3 | 849  | gCC/gTA | 0    |      |      | NEWRECORD |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |
| NCOR1 | 9611 | MSKCC | GRCh37 | 17   | 16004707 | 16004708 | +    | missense_variant | Missense_Mutation | DNP  | GG   | GG   | TA   |      |      | P-0010079-T01-IM5 |      |      |      |      |      | Unknown | SOMATIC |      |      | MSK-IMPACT |      | 1389 | 164  | 691  | 0    | ENST00000268712.3:c.2546_2547delinsTA | p.Ala849Val | p.A849V | ENST00000268712 | NM_006311.3 | 849  | gCC/gTA | 0    |      |      | NEWRECORD |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |
| NCOR1 | 9611 | MSKCC | GRCh37 | 17   | 16004707 | 16004708 | +    | missense_variant | Missense_Mutation | DNP  | GG   | GG   | TA   |      |      | P-0010079-T01-IM5 |      |      |      |      |      | Unknown | SOMATIC |      |      | MSK-IMPACT |      | 1389 | 164  | 691  | 0    | ENST00000268712.3:c.2546_2547delinsTA | p.Ala849Val | p.A849V | ENST00000268712 | NM_006311.3 | 849  | gCC/gTA | 0    |      |      | NEWRECORD |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |
| NCOR1 | 9611 | MSKCC | GRCh37 | 17   | 16004707 | 16004708 | +    | missense_variant | Missense_Mutation | DNP  | GG   | GG   | TA   |      |      | P-0010079-T01-IM5 |      |      |      |      |      | Unknown | SOMATIC |      |      | MSK-IMPACT |      | 1389 | 164  | 691  | 0    | ENST00000268712.3:c.2546_2547delinsTA | p.Ala849Val | p.A849V | ENST00000268712 | NM_006311.3 | 849  | gCC/gTA | 0    |      |      | NEWRECORD |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |
| NCOR1 | 9611 | MSKCC | GRCh37 | 17   | 16004707 | 16004708 | +    | missense_variant | Missense_Mutation | DNP  | GG   | GG   | TA   |      |      | P-0010079-T01-IM5 |      |      |      |      |      | Unknown | SOMATIC |      |      | MSK-IMPACT |      | 1389 | 164  | 691  | 0    | ENST00000268712.3:c.2546_2547delinsTA | p.Ala849Val | p.A849V | ENST00000268712 | NM_006311.3 | 849  | gCC/gTA | 0    |      |      | NEWRECORD |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |

当然，你要注意我的代码，特地把*NCOR1*作为一个`'随便凑的'`的通路放进去，如果你选择不注释掉`pathways=predefined_pathways`，你就可以把图做成这样：

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202302131023029.png" alt="Rplot03" style="zoom:50%;" />

**你可以看到（如果你放大的话）,我这里面采用了一个称为*NCOR1*的基因，在`.maf`文件里像上面一样改完后，把它放到随便凑的这样一个通路里面去，这样你关心的基因里，所有215人都可以被展示出来了。但是，如果没有*NCOR1*这样一个基因被我弄了一次“假突变”，可能就会像下面这样：**

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202302131029184.png" alt="Rplot04" style="zoom:50%;" />

大概只有195个人被展示出来了，你可以看到上面这张图的标题是'Altered in 193 samples'。很多时候，如果你基因少的话（比如说只有*KMT2D,ARID1A*和*KDM6A*），只有60%左右的病人被展示了，因此就会非常麻烦，所以，在你未来要真正出ready-for-publication的图，你就必须要注意你文章里图的人数。



### 11 Merge & Match 突变数据的快速处理

**参考代码**： `Merge & Match.R`

**参考数据**：`msk_met_mutation.txt;msk_met_clinical_sample.txt`

有的时候，我们想要在比较大的数据库,尤其是像类似于`.maf`文件的数据库中找到我们需要的突变，运用excel的`VLOOKUP()`函数会很麻烦，有没有什么好的方法呢？

**为此，我专门写了一段代码,用于筛选突变。**

**以[MSK-MET](https://www.sciencedirect.com/science/article/pii/S0092867422000034?via%3Dihub)队列和AACR-GENIE队列为例**，作为一个泛癌数据库里面有几万个样本，几十万个突变，每次打开它[我电脑](https://www.zhihu.com/question/417425326)就会卡五分钟，所以，如果我希望能够快速地一键得到我想要的膀胱癌对应的样本有没有*ARID1A*（或者是其他基因）的突变，以便于我下游的分析。
因此，**我们需要两个文件**——一个文件包含了我们需要的**sample**以及其他临床信息，便于我后续处理数据，在这里，是`msk_met_clinical_sample.txt`,另外一个是所有的突变信息，就像上文讲道的`.maf`文件一样，只不过格式是`.txt`。
他们分别长这样：

`msk_met_clinical_sample.txt`

| Tumor_Sample_Barcode | PATIENT_ID | ORGAN_SYSTEM | ONCOTREE_CODE | SUBTYPE                | SUBTYPE_ABBREVIATION | SAMPLE_TYPE | PRIMARY_SITE | METASTATIC_SITE | GENE_PANEL | TUMOR_PURITY | SAMPLE_COVERAGE | IS_DIST_MET_MAPPED | MET_COUNT | MET_SITE_COUNT | CANCER_TYPE        | CANCER_TYPE_DETAILED                                        | FGA   | MSI_SCORE | MSI_TYPE | DMETS_DX_UNSPECIFIED | DMETS_DX_DIST_LN | DMETS_DX_LUNG | DMETS_DX_PLEURA | DMETS_DX_MEDIASTINUM | DMETS_DX_LIVER | DMETS_DX_BILIARY_TRACT | DMETS_DX_INTRA_ABDOMINAL | DMETS_DX_BOWEL | DMETS_DX_CNS_BRAIN | DMETS_DX_BONE | DMETS_DX_PNS | DMETS_DX_ADRENAL_GLAND | DMETS_DX_KIDNEY | DMETS_DX_BLADDER_UT | DMETS_DX_FEMALE_GENITAL | DMETS_DX_OVARY | DMETS_DX_MALE_GENITAL | DMETS_DX_SKIN | DMETS_DX_HEAD_NECK | DMETS_DX_BREAST | TMB_NONSYNONYMOUS |
| :------------------: | ---------- | ------------ | ------------- | ---------------------- | -------------------- | ----------- | ------------ | --------------- | ---------- | ------------ | --------------- | ------------------ | --------- | -------------- | ------------------ | ----------------------------------------------------------- | ----- | --------- | -------- | -------------------- | ---------------- | ------------- | --------------- | -------------------- | -------------- | ---------------------- | ------------------------ | -------------- | ------------------ | ------------- | ------------ | ---------------------- | --------------- | ------------------- | ----------------------- | -------------- | --------------------- | ------------- | ------------------ | --------------- | ----------------- |
|  P-0000004-T01-IM3   | P-0000004  | Breast       | IDC           | Breast Ductal HR+HER2- | IDC HR+HER2-         | Primary     | Breast       |                 | IMPACT341  | 50           | 428             | TRUE               | 2         | 2              | Breast Cancer      | Breast Invasive Ductal Carcinoma                            | 0.278 | 2.5       | Stable   | No                   | No               | No            | No              | No                   | Yes            | No                     | No                       | No             | No                 | Yes           | No           | No                     | No              | No                  | No                      | No             | No                    | No            | No                 | No              | 4.436621          |
|  P-0000015-T01-IM3   | P-0000015  | Breast       | IDC           | Breast Ductal HR+HER2- | IDC HR+HER2-         | Metastasis  | Breast       | Liver           | IMPACT341  | 40           | 281             | TRUE               | 8         | 8              | Breast Cancer      | Breast Invasive Ductal Carcinoma                            | 0.35  | 2.55      | Stable   | Yes                  | Yes              | Yes           | Yes             | No                   | Yes            | No                     | No                       | No             | Yes                | Yes           | No           | No                     | No              | No                  | No                      | Yes            | No                    | No            | No                 | No              | 7.764087          |
|  P-0000024-T01-IM3   | P-0000024  | Gynecologic  | UEC           | Uterine Endometrioid   | UCEC ENDO            | Metastasis  | Uterus       | Lung            | IMPACT341  | 40           | 1016            | TRUE               | 8         | 5              | Endometrial Cancer | Uterine Endometrioid Carcinoma                              | 0.388 | 0.72      | Stable   | Yes                  | No               | Yes           | No              | No                   | Yes            | No                     | Yes                      | No             | No                 | Yes           | No           | No                     | No              | No                  | No                      | No             | No                    | No            | No                 | No              | 6.654932          |
|  P-0000025-T02-IM5   | P-0000025  | Gynecologic  | USC           | Uterine Serous         | UCEC SEROUS          | Metastasis  | Uterus       | Intra-Abdominal | IMPACT410  | 30           | 1147            | TRUE               | 13        | 8              | Endometrial Cancer | Uterine Serous Carcinoma/Uterine Papillary Serous Carcinoma | 0.102 | 0.15      | Stable   | Yes                  | No               | No            | No              | No                   | Yes            | Yes                    | Yes                      | Yes            | No                 | No            | No           | Yes                    | No              | No                  | Yes                     | Yes            | No                    | No            | No                 | No              | 1.957439          |

(需要注意的是，病人对应样本的id**必须**命名为`Tumor_Sample_Barcode`，因为这是我们之后匹配的依据。)

和这样：
`msk_met_mutation.txt`

| Hugo_Symbol | Entrez_Gene_Id | Center | NCBI_Build | Chromosome | Start_Position | End_Position | Strand | Consequence        | Variant_Classification | Variant_Type | Reference_Allele | Tumor_Seq_Allele1 | Tumor_Seq_Allele2 | dbSNP_RS    | dbSNP_Val_Status  | Tumor_Sample_Barcode | Matched_Norm_Sample_Barcode | Match_Norm_Seq_Allele1 | Match_Norm_Seq_Allele2 | Tumor_Validation_Allele1 | Tumor_Validation_Allele2 | Match_Norm_Validation_Allele1 | Match_Norm_Validation_Allele2 | Verification_Status | Validation_Status | Mutation_Status | Sequencing_Phase | Sequence_Source | Validation_Method | Score | BAM_File | Sequencer                  | t_ref_count                       | t_alt_count        | n_ref_count     | n_alt_count     | HGVSc          | HGVSp    | HGVSp_Short | Transcript_ID | RefSeq | Protein_position | Codons | Exon_Number | COMMENTS | AA_MAF | AFR_MAF | ALLELE_NUM | AMR_MAF | ASN_MAF | Allele | Amino_Acid_Change | Amino_acids | BIOTYPE | CANONICAL | CCDS | CDS_position | CLIN_SIG | Comments | DISTANCE | DOMAINS | EAS_MAF | EA_MAF | ENSP | EUR_MAF | EXON | ExAC_AF | ExAC_AF_AFR | ExAC_AF_AMR | ExAC_AF_EAS | ExAC_AF_FIN | ExAC_AF_NFE | ExAC_AF_OTH | ExAC_AF_SAS | Existing_variation | FILTER | Feature | Feature_type | GENE_PHENO | GMAF | Gene | HGNC_ID | HGVS_OFFSET | HIGH_INF_POS | IMPACT | INTRON | IS_NEW | MA:FIS | MA:FImpact | MA:link.MSA | MA:link.PDB | MA:link.var | MA:protein.change | MINIMISED | MOTIF_NAME | MOTIF_POS | MOTIF_SCORE_CHANGE | PHENO | PICK | PUBMED | PolyPhen | SAS_MAF | SIFT | SOMATIC | SWISSPROT | SYMBOL | SYMBOL_SOURCE | TREMBL | TSL  | Transcript | UNIPARC | VARIANT_CLASS | all_effects | amino_acid_change | cDNA_Change | cDNA_position | cdna_change | comments | n_depth | t_depth | transcript |
| ----------- | -------------- | ------ | ---------- | ---------- | -------------- | ------------ | ------ | ------------------ | ---------------------- | ------------ | ---------------- | ----------------- | ----------------- | ----------- | ----------------- | -------------------- | --------------------------- | ---------------------- | ---------------------- | ------------------------ | ------------------------ | ----------------------------- | ----------------------------- | ------------------- | ----------------- | --------------- | ---------------- | --------------- | ----------------- | ----- | -------- | -------------------------- | --------------------------------- | ------------------ | --------------- | --------------- | -------------- | -------- | ----------- | ------------- | ------ | ---------------- | ------ | ----------- | -------- | ------ | ------- | ---------- | ------- | ------- | ------ | ----------------- | ----------- | ------- | --------- | ---- | ------------ | -------- | -------- | -------- | ------- | ------- | ------ | ---- | ------- | ---- | ------- | ----------- | ----------- | ----------- | ----------- | ----------- | ----------- | ----------- | ------------------ | ------ | ------- | ------------ | ---------- | ---- | ---- | ------- | ----------- | ------------ | ------ | ------ | ------ | ------ | ---------- | ----------- | ----------- | ----------- | ----------------- | --------- | ---------- | --------- | ------------------ | ----- | ---- | ------ | -------- | ------- | ---- | ------- | --------- | ------ | ------------- | ------ | ---- | ---------- | ------- | ------------- | ----------- | ----------------- | ----------- | ------------- | ----------- | -------- | ------- | ------- | ---------- |
| TP53        | 7157           | MSKCC  | GRCh37     | 17         | 7577121        | 7577121      | +      | missense_variant   | Missense_Mutation      | SNP          | G                | G                 | A                 | rs121913343 | P-0028912-T01-IM6 |                      |                             |                        |                        |                          |                          | Unknown                       | SOMATIC                       |                     |                   | MSK-IMPACT      |                  | 704             | 133               | 209   | 0        | ENST00000269305.4:c.817C>T | p.Arg273Cys                       | p.R273C            | ENST00000269305 | NM_001126112.2  | 273            | Cgt/Tgt  |             |               |        |                  |        |             |          |        |         |            |         |         |        |                   |             |         |           |      |              |          |          |          |         |         |        |      |         |      |         |             |             |             |             |             |             |             |                    |        |         |              |            |      |      |         |             |              |        |        |        |        |            |             |             |             |                   |           |            |           |                    |       |      |        |          |         |      |         |           |        |               |        |      |            |         |               |             |                   |             |               |             |          |         |         |            |
| FYN         | 2534           | MSKCC  | GRCh37     | 6          | 1.12E+08       | 1.12E+08     | +      | frameshift_variant | Frame_Shift_Ins        | INS          | -                | -                 | A                 |             |                   | P-0028912-T01-IM6    |                             |                        |                        |                          |                          |                               | Unknown                       | SOMATIC             |                   |                 | MSK-IMPACT       |                 | 557               | 141   | 168      | 0                          | ENST00000368678.4:c.1466_1467insT | p.Ile490HisfsTer5  | p.I490Hfs*5     | ENST00000368678 | 489            | ccc/ccTc |             |               |        |                  |        |             |          |        |         |            |         |         |        |                   |             |         |           |      |              |          |          |          |         |         |        |      |         |      |         |             |             |             |             |             |             |             |                    |        |         |              |            |      |      |         |             |              |        |        |        |        |            |             |             |             |                   |           |            |           |                    |       |      |        |          |         |      |         |           |        |               |        |      |            |         |               |             |                   |             |               |             |          |         |         |            |
| KDM5C       | 8242           | MSKCC  | GRCh37     | X          | 53246994       | 53246994     | +      | missense_variant   | Missense_Mutation      | SNP          | G                | G                 | A                 |             |                   | P-0028912-T01-IM6    |                             |                        |                        |                          |                          |                               | Unknown                       | SOMATIC             |                   |                 | MSK-IMPACT       |                 | 747               | 85    | 173      | 0                          | ENST00000375401.3:c.506C>T        | p.Ser169Phe        | p.S169F         | ENST00000375401 | NM_004187.3    | 169      | tCt/tTt     |               |        |                  |        |             |          |        |         |            |         |         |        |                   |             |         |           |      |              |          |          |          |         |         |        |      |         |      |         |             |             |             |             |             |             |             |                    |        |         |              |            |      |      |         |             |              |        |        |        |        |            |             |             |             |                   |           |            |           |                    |       |      |        |          |         |      |         |           |        |               |        |      |            |         |               |             |                   |             |               |             |          |         |         |            |
| KRAS        | 3845           | MSKCC  | GRCh37     | 12         | 25380275       | 25380275     | +      | missense_variant   | Missense_Mutation      | SNP          | T                | T                 | G                 | rs17851045  | P-0002794-T02-IM5 |                      |                             |                        |                        |                          |                          | Unknown                       | SOMATIC                       |                     |                   | MSK-IMPACT      |                  | 725             | 104               | 389   | 0        | ENST00000256078.4:c.183A>C | p.Gln61His                        | p.Q61H             | ENST00000256078 | NM_033360.2     | 61             | caA/caC  |             |               |        |                  |        |             |          |        |         |            |         |         |        |                   |             |         |           |      |              |          |          |          |         |         |        |      |         |      |         |             |             |             |             |             |             |             |                    |        |         |              |            |      |      |         |             |              |        |        |        |        |            |             |             |             |                   |           |            |           |                    |       |      |        |          |         |      |         |           |        |               |        |      |            |         |               |             |                   |             |               |             |          |         |         |            |
| RAD50       | 10111          | MSKCC  | GRCh37     | 5          | 1.32E+08       | 1.32E+08     | +      | missense_variant   | Missense_Mutation      | SNP          | T                | T                 | G                 |             |                   | P-0002794-T02-IM5    |                             |                        |                        |                          |                          |                               | Unknown                       | SOMATIC             |                   |                 | MSK-IMPACT       |                 | 749               | 134   | 372      | 0                          | ENST00000265335.6:c.266T>G        | p.Val89Gly         | p.V89G          | ENST00000265335 | 89             | gTc/gGc  |             |               |        |                  |        |             |          |        |         |            |         |         |        |                   |             |         |           |      |              |          |          |          |         |         |        |      |         |      |         |             |             |             |             |             |             |             |                    |        |         |              |            |      |      |         |             |              |        |        |        |        |            |             |             |             |                   |           |            |           |                    |       |      |        |          |         |      |         |           |        |               |        |      |            |         |               |             |                   |             |               |             |          |         |         |            |
| TP53        | 7157           | MSKCC  | GRCh37     | 17         | 7572963        | 7572963      | +      | frameshift_variant | Frame_Shift_Del        | DEL          | T                | T                 | -                 |             |                   | P-0002794-T02-IM5    |                             |                        |                        |                          |                          |                               | Unknown                       | SOMATIC             |                   |                 | MSK-IMPACT       |                 | 542               | 81    | 391      | 0                          | ENST00000269305.4:c.1146del       | p.Lys382AsnfsTer40 | p.K382Nfs*40    | ENST00000269305 | NM_001126112.2 | 382      | aaA/aa      |               |        |                  |        |             |          |        |         |            |         |         |        |                   |             |         |           |      |              |          |          |          |         |         |        |      |         |      |         |             |             |             |             |             |             |             |                    |        |         |              |            |      |      |         |             |              |        |        |        |        |            |             |             |             |                   |           |            |           |                    |       |      |        |          |         |      |         |           |        |               |        |      |            |         |               |             |                   |             |               |             |          |         |         |            |

（可以滑动的😀）

好，上代码！首先我们把需要的包和数据加载好！

```R
setwd('D:/MIBC_dataset/Code & Sample Data')
install.packages("dplyr")                          
#Install dplyr package
library(dplyr)                                   
#Load dplyr package
all_mutation_data=read.table("msk_met_mutation.txt",header = T,sep='\t',fileEncoding = 'utf-8')#所有的突变数据
all_clinical_data=read.table("msk_met_clinical_sample.txt",header = T,sep='\t',fileEncoding = 'utf-8')#需要的样本
```

在这里，作为一个泛癌队列，我现在首先希望**找到所有的膀胱尿路上皮癌的**病人，因此我希望筛选出所有的`SUBTYPE=='Bladder Urothelial'`的患者,对胃癌患者也同样适用。

```r
desired_clinical_samples=subset(all_clinical_data,all_clinical_data$SUBTYPE=='Bladder Urothelial')
#筛选患者
desired_clinical_samples_id=desired_clinical_samples[,1:2]
#提取ID
```

筛选后，现在我们的数据长这样：

```r
>head(desired_clinical_samples_id)
   Tumor_Sample_Barcode PATIENT_ID
7     P-0000034-T01-IM3  P-0000034
12    P-0000043-T02-IM3  P-0000043
15    P-0000056-T01-IM3  P-0000056
20    P-0000063-T01-IM3  P-0000063
24    P-0000068-T01-IM3  P-0000068
26    P-0000071-T01-IM3  P-0000071
````

接着，我们运用`merge()`函数对这两个数据框进行匹配合并，**就得到了所有Bladder Urothelial Carcinoma患者的所有突变**

[^1]: 这个时候把`merged_sample`写成.csv,黏贴进一个`.maf`文件，`desired_clinical_data`作为`clinicalData`，就可以用`maftools`画瀑布图了！

```r
merged_samples<merge(all_mutation_data,desired_clinical_samples_id,by.x='Tumor_Sample_Barcode',by.y='Tumor_Sample_Barcode')
```

但是这样还是不行，因为我希望能够快速得到什么人突变，什么人不突变，所以我为了避免继续用excel进行`vlookup`，**我要从这个.maf格式转化为病人x突变的矩阵**。**因此，我首先把 *ARID1A* 突变的患者找出来：**

```r
select_mutated_samples=subset(merged_samples,merged_samples$Hugo_Symbol=='ARID1A')
```

然后利用`unique()`函数再去除一个患者有多个位点突变的患者，并用`full_join()`**函数进行匹配，就可以得到我们想要的矩阵了！**

```r
exclude_duplicates=select_mutated_samples[(duplicated(select_mutated_samples$Hugo_Symbol)& duplicated(select_mutated_samples$Tumor_Sample_Barcode)),]
all_mutated_samples=exclude_duplicates[,1:2]
names(all_mutated_samples)=c('Tumor_Sample_Barcode','ARID1A')
merged_dataset=full_join(desired_clinical_samples_id,all_mutated_samples,by='Tumor_Sample_Barcode')
```

但是，**现在的数据充满了**`NA`**值**，不方便后续的处理。

```r
 Tumor_Sample_Barcode PATIENT_ID ARID1A
1    P-0000034-T01-IM3  P-0000034 ARID1A
2    P-0000043-T02-IM3  P-0000043   <NA>
3    P-0000056-T01-IM3  P-0000056   <NA>
4    P-0000063-T01-IM3  P-0000063   <NA>
5    P-0000068-T01-IM3  P-0000068   <NA>
6    P-0000071-T01-IM3  P-0000071   <NA>
```

**所以我们用我们学过的python，进行循环嵌套分支的操作,把ARID1A改成1，`<NA>`改成0！**

```r
i=1
merged_dataset[i,3]
while (i<length(merged_dataset$ARID1A)) {
  if (is.na(merged_dataset[i,3])){
    merged_dataset[i,3]=0
    i=i+1
  }
  else{
    merged_dataset[i,3]=1
    i=i+1
  }
  
}
write.csv(merged_dataset,'merged_dataset.csv')
```

现在我们再看看:

```r
> head(merged_dataset)
  Tumor_Sample_Barcode PATIENT_ID ARID1A
1    P-0000034-T01-IM3  P-0000034      1
2    P-0000043-T02-IM3  P-0000043      0
3    P-0000056-T01-IM3  P-0000056      0
4    P-0000063-T01-IM3  P-0000063      0
5    P-0000068-T01-IM3  P-0000068      0
6    P-0000071-T01-IM3  P-0000071      0
```

可以，是我们需要的结果了！

整体的逻辑大概就是长这样：

![image-20230314113333947](https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303141133119.png)

```R
Warning messages:
1: In scan(file = file, what = what, sep = sep, quote = quote, dec = dec,  :
  EOF within quoted string
2: In scan(file = file, what = what, sep = sep, quote = quote, dec = dec,  :
  number of items read is not a multiple of the number of columns
```

**一定要注意！！！**读取这种大数据，这样的`Warning messages`**必须**注意，需要在你的`all_clinical_data=read.table("msk_met_clinical_sample.txt",header = T,sep='\t',fileEncoding = 'utf-8')`中加入`quote=''`，即：

```R
all_clinical_data=read.table("msk_met_clinical_sample.txt",header = T,sep='\t',fileEncoding = 'utf-8',`quote='')
```

**否则的话数据会有明显的缺失！**

如果在基因组层面，我们希望得到一个整个的突变图景，我们就希望能把所有的基因都转化为样本为行，基因为列的矩阵，所以，我们这个代码就就要多跑几次，有几个基因我们就要跑几次，整个代码就会变成：
（参考代码：`maf_to_matrix.R`)

```R
setwd('D:/MIBC_dataset/FUSCC_Targeted_Sequencing')
setwd('D:/MIBC_dataset/msk_met_2021')
setwd('D:/MIBC_dataset/mixed_allen_2018_all')
setwd('D:/MIBC_dataset/blca_bcan_hcrn_2022')
setwd('D:/MIBC_dataset/MSK-IMPACT')

#install.packages("dplyr")                          
#Install dplyr package
library(dplyr) 
mutation_file=read.table('bladder_mutation.txt',sep='\t',header = T,quote='')
mutation_file=read.table('data_mutations.txt',quote = "",fill=T,header=T)
Panel=unique(as.data.frame(mutation_file$Hugo_Symbol))
colnames(Panel)='Hugo_Symbol'
print(Panel$Hugo_Symbol)
i=1
k=as.factor('Hugo_Symbol')
gene_list=unique(Panel$Hugo_Symbol)
i1=1
```

`Panel`告诉了我`.maf`文件中所有测过序的基因，并且储存到了`gene_list`这个向量中。我们就用`i1`这个变量对`gene_list`进行索引，然后以`gene_list`的长度作为循环长度，跑我们上面的代码。

```R
#i1 is the indicator of which gene is going to be chosen in the general loop.
Ultimate_dataset=read.table('data_clinical_sample.txt',sep='\t',header=T,quote='')
all_mutation_data=read.table("data_mutations.txt",header = T,sep='\t',fill=TRUE,quote = "")#All the mutation data in the .maf format
all_clinical_data=read.table("data_clinical_sample.txt",header = T,sep='\t',fill=TRUE,quote = "")#All the samples
while (i1<length(Panel$Hugo_Symbol)+1) {
#Load dplyr package
desired_clinical_samples=subset(all_clinical_data,all_clinical_data$ONCOTREE_CODE=='BLCA')
desired_clinical_samples_id=desired_clinical_samples
merged_samples<-merge(all_mutation_data,desired_clinical_samples_id,by.x='Tumor_Sample_Barcode',by.y='Tumor_Sample_Barcode')
select_mutated_samples=subset(merged_samples,merged_samples$Hugo_Symbol==gene_list[i1])
all_mutated_samples=select_mutated_samples[,1:2]
names(all_mutated_samples)=c('Tumor_Sample_Barcode',gene_list[i1])
all_mutated_samples=unique(all_mutated_samples)
merged_dataset=full_join(desired_clinical_samples_id,all_mutated_samples,by='Tumor_Sample_Barcode')
i=1

while (i<length(merged_dataset[,3])+1) {
  if (is.na(merged_dataset[i,3])){
    merged_dataset[i,3]=0
    i=i+1 
  }
  else{
    merged_dataset[i,3]=1
    i=i+1
  }
  
}

merged_dataset=merged_dataset%>%select(1,3)
Ultimate_dataset=full_join(Ultimate_dataset,merged_dataset,by='Tumor_Sample_Barcode')
i1=i1+1
}
write.csv(Ultimate_dataset,'merged_dataset.csv')

```

根本的原理也就是循环嵌套循环，最后`Ulitmate_dataset`就是我们需要的结果。

------

### 12 Supplementary Figure 11D GISTIC 可视化

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202302121844952.png" alt="image-20230212184414823" style="zoom: 33%;" />

参考数据：`segment_MIBC_391.txt`;`markersfile.txt`

#### i [GenePattern](https://www.genepattern.org/)运行GISTIC分析

GISTIC分析基本上是我们运用基因组数据在染色体层面上考察基因组不稳定性的一个绝佳的方案，这个算法在2011年提出，现在更新到2.0版本。可惜的是，这个东西本身需要`linux`环境，我们不能在电脑上非常轻松地跑，因此，我们需要运用GenePattern这个云服务器帮助我们解决这个问题。

![image-20230314221000711](https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303142210056.png)

在右上角`modules`搜索`GISTIC2.0`,进入以下界面：

![image-20230314221533516](https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303142215706.png)

一共需要三个文件输入，一个是`refgene file`，即正常人类的基因组，但是请注意，不同的队列不一样。TCGA是`Hg38.ucsc.add_miR.160920.refgene.mat`，我们膀胱癌用的[Miao et al, 2018](https://www.nature.com/articles/s41588-018-0200-2)队列用的就是Hg19。具体需要你自己去看文献里的标注。

第二个是`seg file`，你可以理解为拷贝数变异的`raw data`,我们可以用excel打开去看一下是一个怎样的格式。

| Sample                       | Chromosome | Start    | End      | Num_Probes | Segment_Mean |
| ---------------------------- | ---------- | -------- | -------- | ---------- | ------------ |
| TCGA-CF-A3MH-01A-11D-A20B-01 | 1          | 3301765  | 30604618 | 14759      | 0.0657       |
| TCGA-CF-A3MH-01A-11D-A20B-01 | 1          | 30604986 | 30605692 | 2          | -2.1755      |
| TCGA-CF-A3MH-01A-11D-A20B-01 | 1          | 30605990 | 32858535 | 965        | 0.0472       |
| TCGA-CF-A3MH-01A-11D-A20B-01 | 1          | 32859223 | 64840194 | 19149      | 0.0706       |
| TCGA-CF-A3MH-01A-11D-A20B-01 | 1          | 64840499 | 1.02E+08 | 22643      | 0.0537       |

可以看到，每一个基因组事件都是和sample一一对应的，所以你可以直接用`vlookup`函数来解决这个问题，把你感兴趣的人拆成两个segment文件。在我给你的sample data里有`segment_MIBC.txt`，你就可以去尝试一下~

还有就是markers file,这个基本上就是默认的，直接把我给你的`markersfile.txt`传上去就行了。

其他的数据基本上都是默认的，这上面的`cnv file`不需要填写。

之后你点击界面的`run`，我们就可以继续了！

如果成功的话，我们会看到下面几个文件，你需要把他们下载下来。

![image-20230314222832059](https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303142228206.png)

| cytoband             | 3p25.2                 | 1q23.3                   | 6p22.3                 | 1q21.3                   | 12q15                   |
| -------------------- | ---------------------- | ------------------------ | ---------------------- | ------------------------ | ----------------------- |
| q value              | 3.56E-07               | 3.56E-07                 | 7.03E-08               | 2.95E-06                 | 0.000764                |
| residual q value     | 3.56E-07               | 4.24E-06                 | 5.12E-06               | 5.97E-05                 | 0.000764                |
| wide peak boundaries | chr3:12215214-12473069 | chr1:160858496-160997003 | chr6:20580195-20595461 | chr1:151937064-152009543 | chr12:69421730-69800244 |
| genes in wide peak   | PPARG                  | F11R                     | CDKAL1                 | S100A10                  | CCT2                    |
|                      |                        | CD244                    |                        |                          | FRS2                    |
|                      |                        | ITLN1                    |                        |                          | RAB3IP                  |
|                      |                        | ITLN2                    |                        |                          | BEST3                   |
|                      |                        | LOC101928372             |                        |                          | LRRC10                  |

首先就是我们说的这个`amp_genes.conf_90.txt`,或者这个`del_genes.conf_90.txt`,**意思就是在这个人群中，我们在90%可信区间内发现了多少扩增或者缺失的基因，这个有助于你未来在可视化的时候进一步立论。**比如，我们确实看到了这里有一批人有*PPARG*的扩增，也是说明了膀胱癌的一种接近于luminal的表型，和之前报道的一致。

接下来还有一个文件叫做`scores.gistic`，我们在后面讲。

#### ii ggplot2进行GISTIC可视化

首先要了解这个图是什么意思，横坐标是染色体（或基因组？），纵坐标是G-Score（或者frequency），红色表示扩增，蓝色表示删失，如果要用`ggplot2`画这个图，那我们也要有这个数据才行！

在GISTIC2.0的输出结果中，有一个`scores.gistic`的文件，我们可以用Excel打开看看：

| Type | Chromosome | Start   | End     | #NAME? | G-score  | average amplitude | frequency |
| ---- | ---------- | ------- | ------- | ------ | -------- | ----------------- | --------- |
| Amp  | 1          | 3301765 | 3308094 | 0      | 0.090729 | 0.329663          | 0.230769  |
| Amp  | 1          | 3308771 | 3318992 | 0      | 0.092594 | 0.332877          | 0.230769  |
| Amp  | 1          | 3319477 | 3320645 | 0      | 0.091661 | 0.33541           | 0.22792   |
| Amp  | 1          | 3320988 | 3788496 | 0      | 0.089944 | 0.336622          | 0.225071  |
| Amp  | 1          | 3788662 | 3860668 | 0      | 0.087301 | 0.334703          | 0.225071  |
| Amp  | 1          | 3860818 | 3941608 | 0      | 0.086221 | 0.339135          | 0.222222  |
| Amp  | 1          | 3942772 | 3962749 | 0      | 0.087886 | 0.338643          | 0.225071  |
| Amp  | 1          | 3963763 | 4025916 | 0      | 0.088648 | 0.340148          | 0.225071  |

```r
rm(list = ls())
library(BSgenome.Hsapiens.UCSC.hg38) # 加载R包

df <- data.frame(chromName = seqnames(BSgenome.Hsapiens.UCSC.hg38), # 染色体名字
                 chromlength = seqlengths(BSgenome.Hsapiens.UCSC.hg38)# 每条染色体长度
                 )
df$chromNum <- 1:length(df$chromName) # 染色体名字纯数字版，为了和scores.gistic里面的对应

# 我们用的原始CNV文件是没有性染色体信息的
df <- df[1:22,] # 只要前22条染色体信息
df
str(df)
# 小发现，在用cumsum前要把int变成numeric
df$chromlengthCumsum <- cumsum(as.numeric(df$chromlength)) # 染色体累加长度

# 得到每条染色体从0开始的起始坐标
df$chormStartPosFrom0 <- c(0,df$chromlengthCumsum[-nrow(df)])

# 计算每条染色体中间位置坐标，用来最后加文字
tmp_middle <- diff(c(0,df$chromlengthCumsum)) / 2
df$chromMidelePosFrom0 <- df$chormStartPosFrom0 + tmp_middle

df
```

上面的都是在处理染色体的数据，接下来我们只要把我们的`scores.gistic`读取进去就可以了。

```
scores <- read.table(".scores.gistic",sep="\t",header=T,stringsAsFactors = F)
chromID <- scores$Chromosome

scores$StartPos <- scores$Start + df$chormStartPosFrom0[chromID]
scores$EndPos <- scores$End + df$chormStartPosFrom0[chromID]

library(ggplot2)
library(ggsci)

ggplot(scores, aes(StartPos, G.score))+
  geom_area(aes(group=Type, fill=factor(Type,levels = c("Del","Amp"))))+
  scale_fill_lancet(guide=guide_legend(reverse = T))+
  geom_vline(data = df ,mapping=aes(xintercept=chromlengthCumsum),linetype=2)+
  geom_text(data = df,aes(x=chromMidelePosFrom0,y=0.2,label=chromName))+
  scale_x_continuous(expand = c(0,-1000),limits = c(0,2.9e9))+
  ylim(-0.3,0.3)+
  theme_minimal()
```

他这里可视化的是`G.score`,是一个经过概率学计算的二阶数值，以反映此处的染色体事件频率，我们可以直接把它换成`Frequency`，效果雷同，但是图会更加好看~

------

### a 云雨图

参考代码：ridge_violinplot.R

参考数据：ridge_violinplot.csv

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303141130651.png" alt="image-20230314113048486" style="zoom:50%;" />

```r
# 加载包
library(devtools)
devtools::install_github("psyteachr/introdataviz")
```

这个包不是那么好装，你要记得我上面讲过的运用`devtools`装r包的注意事项哈~
（我的方法现在还没有踩过雷）

```r
library(introdataviz)
library(tidyverse)
library(ggplot2)
library(gglayer)
library(dplyr)
setwd('D:/MIBC_dataset/Code & Sample Data')
install.packages('gglayer')
setwd('D:/MIBC_dataset/Code & Sample Data')
install.packages('gglayer')
# 示例数据准备
sample_data <- read.csv("violin+ggridge+boxplot.csv")
# Calculate species richness per plot per year
theme_niwot <- function(){
  theme_bw() +
    theme(axis.text = element_text(size = 16), 
          axis.title = element_text(size = 18),
          axis.line.x = element_line(color="black"), 
          axis.line.y = element_line(color="black"),
          panel.border = element_blank(),
          panel.grid.major.x = element_blank(),                                          
          panel.grid.minor.x = element_blank(),
          panel.grid.minor.y = element_blank(),
          panel.grid.major.y = element_blank(),  
          plot.margin = unit(c(1, 1, 1, 1), units = , "cm"),
          plot.title = element_text(size = 18, vjust = 1, hjust = 0),
          legend.text = element_text(size = 12),          
          legend.title = element_blank(),                              
          legend.position = c(0.95, 0.15), 
          legend.key = element_blank(),
          legend.background = element_rect(color = "black", 
                                           fill = "transparent", 
                                           size = 2, linetype = "blank"))
}
```

需要注意的是，这个图不好画，因为他是很多个作图函数拼在一起的。所以，他这边给你新定义的`theme_niwot`不要省略，从头到尾把代码跑完就行了。

```r
boxplot_violin <- 
  ggplot(data =sample_data, 
         aes(x = mRNAcluster, y = ImmuneScore, color=mRNAcluster)) +
  geom_flat_violin(position = position_nudge(x = 0.2, y = 0), alpha = 0.8) +
  geom_point(aes(y = ImmuneScore, color=mRNAcluster), 
             position = position_jitter(width = 0.15), size = 1, alpha = 0.7) +
  geom_boxplot(width = 0.2, outlier.shape = NA, alpha = 0.8) +
  labs(y = "ImmuneScore", x = NULL) +
  guides(fill = FALSE, color = FALSE) +
  scale_y_continuous(limits = c(0,3)) +
  scale_fill_manual(values = c("#d62828", "#E07A5F",  "#3D405B", "#81B29A",'#F2CC8F')) +
  scale_colour_manual(values = c("#d62828", "#E07A5F",  "#3D405B", "#81B29A",'#F2CC8F')) +
  coord_flip() +
  theme_niwot()
boxplot_violin
```

`scale_fill_manual`和`scale_colour_manual`都是我们自己定义的颜色，有几个组就定义几个颜色哈~

### b *Zhang P et al., JITC, 2022* Mutational signature 折线图

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202302121837884.png" alt="image-20230212183700769" style="zoom: 67%;" />

参考代码：`MutationalSignature.R`

参考代码：`MutationalSignature_data.csv`
有关mutational signature，可以采取展示的方式很多，我觉得朴然师兄这里采取的方案很好，我的文章里就仅仅运用了一个简单的热图进行展示，他这个更加直观，所以我在这边

```r
#Demonstrating mutational signature by twisting lines.
library(tidyr)
dt1 <- economics
dt2 <- subset(dt1, date > as.Date("2000-1-1"))
#使用“宽数据”绘制分组折线图；
p4 <- ggplot(dt2, aes(x=date)) +
geom_line(aes(y = psavert), color = mycolor[3]) +
geom_line(aes(y = uempmed), color=mycolor[4], linetype="solid")
p4
```

### c chord plot 弦图

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202302121839118.png" alt="image-20230212183903007" style="zoom:67%;" />



参考代码: `Chordplot.R`

参考数据：`Chordplot_data.csv`

```R
## R包检测和安装
# 检查开发者工具devtools，如没有则安装
if (!require("devtools"))
  install.packages("devtools")
# 加载开发者工具devtools
library(devtools)
# 检查热图包，没有则通过github安装最新版
if (!require("circlize"))
  devtools::install_github("jokergoo/circlize")
if (!require("tidyr"))
  install.packages('tidyr') 
# 加载包
library(circlize)
library(tidyr)
```

这个包需要利用`devtools`进行安装，所以可能会出问题，参考我第0章的叙述~

```r
## 生成测试数据
# 设置随机数种子，确保数据可重复
set.seed(123)
#仿照原图创建一个11×5的随机数矩阵
mat <- matrix(runif(55, min = 1, max = 7), 11, 5)
#列名为样本名
colnames(mat) <- c("Fly", "Beetle", "Leaf", "Soil", "Waxberry")
#行名为属名
rownames(mat) <- c("Wolbachia", "Vibrionimonas", "uncultured", "Pseudomonas",
                   "others", "norank", "Lactococcus", "Enterococcus",
                   "Dysgomonas", "Bradyrhizobium", "Bartomella")
mat2 <- as.data.frame(mat)
mat2 <- cbind(rownames(mat2), mat2)
#pivot_longer函数核心参数cols决定哪些列进行转化，本例中为除第一列外所有列
df <- pivot_longer(mat2, cols = -1)
colnames(df) <- c("from", "to", "value")
## 简单和弦图绘制
pdf("plot1.pdf", width = 8, height = 6)
chordDiagram(mat)
circos.clear()
dev.off()
## 对和弦图的细节进行优化
pdf("plot2.pdf", width = 8, height = 6)
#首先对布局进行设置，设置扇形间的距离
circos.par(gap.after = c(rep(3, nrow(mat)-1), 10, rep(3, ncol(mat)-1), 10))
#调整扇形顺序与原图保持一致
orderlist <- c(rev(rownames(mat)), colnames(mat))
#设置扇形颜色，可以使扇形区域与颜色一一对应
col_list1 <- c(Fly = "#C6CF85", Beetle = "#717862", Leaf = "#943A74",
               Soil = "#7267AC", Waxberry ="#040000", Wolbachia = "#DA3918",
               Vibrionimonas = "#172F2E", uncultured = "#80B862",
               Pseudomonas = "#635368", others = "#DAEDDF", norank = "#040000",
               Lactococcus = "#0A351B", Enterococcus = "#A1CB36",
               Dysgomonas = "#172989", Bradyrhizobium = "#1C2A53", Bartomella = "#7DCDEC")
#设置link颜色,link颜色与列所代表的扇形颜色一致，因此使用column.col参数设置
col_list2 <- c(Fly = "#C6CF85", Beetle = "#717862", Leaf = "#943A74", 
               Soil = "#7267AC", Waxberry = "#040000")
chordDiagram(mat,order = orderlist, #设置扇区顺序
             grid.col = col_list1, #设置扇区颜色
             column.col = col_list2) #设置link颜色
circos.clear()
dev.off()
## 手动添加注释
pdf("plot3.pdf", width = 8, height = 6)
#首先对布局进行设置，设置扇形间的距离
circos.par(gap.after = c(rep(3, nrow(mat)-1), 10, rep(3, ncol(mat)-1), 10))
#调整扇形顺序与原图保持一致
orderlist <- c(rev(rownames(mat)), colnames(mat))
#设置扇形颜色，可以使扇形区域与颜色一一对应
col_list1 <- c(Fly = "#C6CF85", Beetle = "#717862", Leaf = "#943A74",
               Soil = "#7267AC", Waxberry = "#040000", Wolbachia = "#DA3918",
               Vibrionimonas = "#172F2E", uncultured = "#80B862",
               Pseudomonas = "#635368", others = "#DAEDDF", norank = "#040000",
               Lactococcus = "#0A351B", Enterococcus = "#A1CB36",
               Dysgomonas = "#172989", Bradyrhizobium = "#1C2A53", Bartomella = "#7DCDEC")
#设置link颜色, link颜色与列所代表的扇形颜色一致，因此使用column.col参数设置
col_list2 <- c(Fly = "#C6CF85", Beetle = "#717862", Leaf = "#943A74", 
               Soil = "#7267AC", Waxberry = "#040000")
chordDiagram(mat, order = orderlist, #设置扇区顺序
             grid.col = col_list1, #设置扇区颜色
             column.col = col_list2, #设置link颜色
             annotationTrack = "grid") #不绘制扇区注释
#添加扇形区注释
for(i in get.all.sector.index()) {
  xlim = get.cell.meta.data("xlim", sector.index = i, track.index = 1)
  circos.text(x = mean(xlim), y = 0.5, sector.index = i, 
              facing = "clockwise", niceFacing = TRUE, cex = 1, 
              col = "black", labels = i, track.index = 1)
}
circos.clear()
dev.off()
```

### d Maftools的其他性质

参考代码：`Lollipop & Additional Features of Maftools.R`

参考数据：`Maftool_clinical_annotation.txt`, `sample.maf`

和刚刚画瀑布图一样，我们需要`clinicalData`和`.maf`文件导入我们的突变数据。

```R
setwd('D:/MIBC_dataset/Code & Sample Data')
library(maftools)
bladder_clinical=read.table('data_clinical_samples.txt',sep='\t',header=T,quote='')
laml=read.maf('input.maf',
              clinicalData = bladder_clinical)
```

```r
somaticInteractions(maf = laml, top = 25, pvalue = c(0.05, 0.1))
```

这里，`somaticInteration`可以帮我们可视化不同基因间共突变的概率。和`UpSetR`里的东西不一样，它可以自动帮你算出相应的*P*值。 

```r
rainfallPlot(maf = laml, detectChangePoints = TRUE, pointSize = 0.6)
```

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303151546965.png" alt="image-20230315154618800" style="zoom:50%;" />

雨点图，可以直接可视化我们需要的患者的数据。

```r
laml.sig = oncodrive(maf = laml,minMut = 5, pvalMethod = 'zscore')
```


驱动突变基因

```r
lollipopPlot(maf = laml, gene = 'TP53')
```

棒棒糖图

```r
library(BSgenome.Hsapiens.UCSC.hg19, quietly = TRUE)
laml.tnm = trinucleotideMatrix(maf = laml, prefix = 'chr', add = TRUE, ref_genome = "BSgenome.Hsapiens.UCSC.hg19")
plotApobecDiff(tnm = laml.tnm, maf = laml, pVal = 0.2)
# Extract data for samples 'TCGA.AB.3009' and 'TCGA.AB.2933'  (Printing just 5 rows for display convenience)
subsetMaf(maf = laml, tsb = c('TCGA-AB-3009', 'TCGA-AB-2933'), mafObj = FALSE)[1:5]

laml.sub.1 = subsetMaf(maf = laml, genes ='PTEN', mafObj = TRUE,query = "Variant_Classification == 'Splice_Site'")
# Select all samples with FAB clasification M4 in clinical data 
laml_m4 = subsetMaf(maf = laml, clinQuery = "FAB_classification %in% 'M4'")
plotmafSummary(maf = laml, rmOutlier = TRUE, addStat = 'median', dashboard = TRUE, titvRaw = FALSE)
```



### e 差异基因分析&火山图

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202302122326562.png" alt="image-20230212232630428" style="zoom:50%;" />

#### i 差异基因分析

参考代码：`DifferentiallyExpressedGene.R`

参考数据：`mrna_exprSet.csv`;`group_list.txt`

很多时候，我们希望能够直接地去探索不同亚族患者之间基因表达的图景，但是又不想放到具体的通路中进行考察，因此，我们就要运用到差异基因分析的这个概念,简单来说，这个方法能让我们非常直观地看出两组患者之间那些基因高表达、低表达。

我们需要准备两个数据，一个是患者的分组状态：

| ID              | Group |
| --------------- | ----- |
| TCGA.02.0047.01 | low   |
| TCGA.02.0055.01 | high  |
| TCGA.02.2486.01 | high  |
| TCGA.06.0125.01 | low   |
| TCGA.06.0129.01 | high  |
| TCGA.06.0130.01 | high  |
| TCGA.06.0138.01 | low   |
| TCGA.06.0139.01 | low   |
| TCGA.06.0141.01 | high  |

还有一个是我们的表达矩阵，应该要用`counts`格式。

|         | TCGA-27-1830-01 | TCGA-06-5408-01 | TCGA-32-2632-01 | TCGA-06-2557-01 | TCGA-26-5135-01 | TCGA-06-0156-01 |
| ------- | --------------- | --------------- | --------------- | --------------- | --------------- | --------------- |
| A1BG    | 54              | 11              | 25              | 30              | 35              | 91              |
| A1CF    | 0               | 2               | 0               | 2               | 1               | 1               |
| A2M     | 102814          | 30727           | 43819           | 74341           | 36326           | 59025           |
| A2ML1   | 133             | 445             | 577             | 216             | 39              | 240             |
| A3GALT2 | 14              | 5               | 1               | 12              | 12              | 14              |
| A4GALT  | 254             | 169             | 330             | 211             | 365             | 89              |
| A4GNT   | 4               | 3               | 3               | 2               | 8               | 6               |

之后，我们把需要的包和数据加载好:

```R
rm(list = ls())
setwd('C:/Users/lenovo/Desktop/DEG')
BiocManager::install('DESeq2')
library(DESeq2)
library(limma)
library(edgeR)
expr = read.csv("mRNA_exprSet.csv",sep = ',',header=T)  
head(expr)
group=read.table('group_list.txt',sep='\t',header=T)
```

然后，我们要从`group`这个对象中提取出不同组别的患者的id

```r
group1=as.vector(subset(group,group$condition=='low')[,1])
group2=as.vector(subset(group,group$condition=='high')[,1])

expr = avereps(expr[,-1],ID = expr$X) # 自定义
expr = expr[rowMeans(expr)>1,] # 自定义
library(stringr)
tumor <- colnames(expr)[as.integer(substr(colnames(expr),14,15)) < 10]
normal <- colnames(expr)[as.integer(substr(colnames(expr),14,15)) >= 10]
```

按照这个方法，我们把患者重新分组：

```r
group1_sample <- expr[,group1]
group2_sample <- expr[,group2]
```

重构数据后，我们就可以开始分析了！这些代码最后都是在整理数据，直接按住`cmd`+`enter`跑就可以了

```r
exprSet_by_group <- cbind(group1_sample,group2_sample)
group_list <- c(rep('group1',ncol(group1_sample)),rep('group2',ncol(group2_sample)))

save(exprSet_by_group, group_list, file = 'exprSet_by_group_list.Rdata')
data = exprSet_by_group
group_list = factor(group_list)

design <- model.matrix(~0+group_list)
rownames(design) = colnames(data)
colnames(design) <- levels(group_list)
DGElist <- DGEList( counts = data, group = group_list)
## Counts per Million or Reads per Kilobase per Million
keep_gene <- rowSums( cpm(DGElist) > 1 ) >= 2 ## 自定义
table(keep_gene)
DGElist <- DGElist[ keep_gene, keep.lib.sizes = FALSE ]

DGElist <- calcNormFactors( DGElist )
DGElist <- estimateGLMCommonDisp(DGElist, design)
DGElist <- estimateGLMTrendedDisp(DGElist, design)
DGElist <- estimateGLMTagwiseDisp(DGElist, design)

fit <- glmFit(DGElist, design)
results <- glmLRT(fit, contrast = c(-1, 1)) 
nrDEG_edgeR <- topTags(results, n = nrow(DGElist))
nrDEG_edgeR <- as.data.frame(nrDEG_edgeR)
head(nrDEG_edgeR)
padj = 0.01 # 自定义
foldChange= 2 # 自定义
nrDEG_edgeR_signif  = nrDEG_edgeR[(nrDEG_edgeR$FDR < padj & 
                                     (nrDEG_edgeR$logFC>foldChange | nrDEG_edgeR$logFC<(-foldChange))),]
nrDEG_edgeR_signif = nrDEG_edgeR_signif[order(nrDEG_edgeR_signif$logFC),]
write.csv(nrDEG_edgeR,'DEG.csv')
```

最后这个`nrDEG_edgeR`就是我们要的结果，我们可以看到这个`DEG.csv`的结果是是长成这样的：

|          | logFC    | logCPM   | LR       | PValue   | FDR      |
| -------- | -------- | -------- | -------- | -------- | -------- |
| CD274    | 2.024339 | 2.937082 | 131.7343 | 1.71E-30 | 2.81E-26 |
| RHBG     | -4.86182 | 2.689377 | 90.39811 | 1.95E-21 | 1.60E-17 |
| DNAH8    | 4.978054 | 1.616644 | 72.81304 | 1.43E-17 | 7.80E-14 |
| CAPN6    | -4.86423 | 3.596339 | 69.51804 | 7.57E-17 | 3.11E-13 |
| HIST1H1B | -3.35048 | -0.54024 | 69.07289 | 9.49E-17 | 3.12E-13 |
| HIST1H1E | -2.95577 | 0.218993 | 68.49106 | 1.27E-16 | 3.49E-13 |
| COMP     | 3.86388  | 0.699132 | 61.41051 | 4.63E-15 | 1.09E-11 |


#### ii 火山图

参考代码：`VolcanoPlot.R`
参考数据:`DEG.csv`

```R
devtools::install_github("BioSenior/ggVolcano")
library(ggVolcano)
deg_data=read.table('DEG.csv',sep=',',header=T)
```

这里要注意，`ggVolcano`包需要从GitHub上安装，你需要参考我的第0章。

```r
# use the function -- add_regulate to add a regulate column 
# to the DEG result data. 
data <- add_regulate(deg_data, log2FC_name = "logFC",
                     fdr_name = "PValue",log2FC = 1, fdr = 0.05)

# plot
ggvolcano(data, x = "log2FoldChange", y = "padj",
          label = "gene_id", label_number = 10, output = FALSE)
```

最后的结果，你可以看到只要你的变量的名字叫`log2FoldChange`和`padj`就可以了。

### f 韦恩图(Venn plot)

![image-20230212184101511](https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202302121841565.png)

参考代码：`VennPlot.R`

```r
# 创建示例数据
set1 <- sample(LETTERS, size = 20)
set2 <- sample(LETTERS, size = 20)
set3 <- sample(LETTERS, size = 20)
set4 <- sample(LETTERS, size = 20)
set5 <- sample(LETTERS, size = 20)
set6 <- sample(LETTERS, size = 20)
library(VennDiagram)
library(scales)

venn.diagram(
  x = list(set1 = set1, set2 = set2,
           set3 = set3, set4 = set4),# 可以换名，letters1 = set1，这样输出图片中就会换名
  filename = './output/4sets.tiff', # 输出图片路径
  col = 'black',
  fill = brewer_pal(palette = 'Set2')(4) # 填充颜色，用的是RColorBrewer包中的调色板
)
```



### g UpsetR教程

参考代码：`UpsetR.R`

参考数据：`UpsetR.txt`

![image-20230315103535326](https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303151035973.png)

`UpsetR`的本质，就是一个**高阶的、可统计、排序并比较频数的高级韦恩图**，用来展示共突变图景再好不过了。哪些患者有基因组数据、哪些患者有临床病理数据、哪些患者有转录组数据；哪些患者有PIK3CA的扩增、哪些患者有突，哪些患者两个都有，都可以一目了然**（上次做这张图被徐老师狠狠地夸赞了哈哈哈）。**

首先，我们可以看一下导入的数据的结构，很简单，就是以突变为列，样本名为行的数据：

| ID       | TERT | TP53 | MLL2 | ARID1A | FGFR3 | CDKN2A | KDM6A | RB1  |
| -------- | ---- | ---- | ---- | ------ | ----- | ------ | ----- | ---- |
| SAM0011a | 1    | 1    | 1    | 0      | 1     | 0      | 0     | 0    |
| SAM00c84 | 1    | 0    | 1    | 0      | 0     | 0      | 0     | 0    |
| SAM02416 | 1    | 1    | 0    | 0      | 0     | 1      | 0     | 0    |
| SAM03c07 | 0    | 1    | 0    | 1      | 0     | 0      | 0     | 0    |
| SAM059ab | 1    | 1    | 0    | 0      | 0     | 1      | 0     | 0    |
| SAM06e32 | 1    | 0    | 0    | 0      | 1     | 0      | 1     | 0    |
| SAM07653 | 1    | 1    | 1    | 1      | 0     | 0      | 1     | 1    |
| SAM07cda | 0    | 1    | 1    | 0      | 0     | 0      | 0     | 0    |
| SAM0af9b | 0    | 0    | 0    | 0      | 0     | 1      | 0     | 0    |
| SAM0dc99 | 0    | 0    | 1    | 1      | 1     | 1      | 0     | 0    |

```r
setwd('D:/MIBC_dataset/IMvigor210')
if(!require(UpSetR))install.packages('UpSetR')
library(UpSetR)
library(ggplot2)
mutations <- read.csv( 'merged_dataset.csv', header=T, sep = ",")
upset(mutations, nsets = 8, number.angles = 30, point.size = 3.5, line.size = 2, 
      mainbar.y.label = "Genre Intersections", sets.x.label = 'Mutated Samples',
      sets=c('TP53','KDM6A','ARID1A','FGFR3','MLL2','PIK3CA','RB1'))
upset(mutations, group.by = "sets")
```

第一个参数就是我们需要的一个数据框，`sets`告诉R语言我们需要哪些对象，就可以了。我看了看市面上的教程,`upset`里面有很多杂七杂八的函数，比如说调整粗细、大小等等等等，但是我总觉得没有AI里调来的方便。但是，我觉得需要调的就是

### h 利用musicatk计算mutational signature

参考文献： [Chevalier et al, *Cancer Res*, 2021](https://pubmed.ncbi.nlm.nih.gov/34625425/)

参考数据：

Mutational signature是我非常看好而且关注的一个小方向，通过病因学的推断我们可以刻画整个的基因组的情况。可惜的是，很多文献都没有进行完善的报道，往往也没有ready-to-use的数据可供我们使用。因此，为了得到更多有意思的结论，我们往往都需要自己进行计算。

可以用来计算mutational signature的包很多，比如MutationPatterns(我讲的那篇文献里就是用这个方法计算)；deConsturctSigs(我自己文章里的数据就是从这里出来的)。在这里，我推荐运用Musicatk进行计算。这个包既可以支持我**们从头推断（*de novo* induction)**需要的mutational signature，把我们自己定义的mutational signature和已有的COSMIC 2.0数据库进行**比对**（Cosine similarity)，同时也可以傻乎乎的利用COSMIC2.0数据库计算我们需要的curated signature。除此之外，它还可以有强大的降维和可视化功能，帮助我们展示我们的结果。（可以参考我讲的那篇文献：2022-Comprehensive molecular characterization reveals genomic and transcriptomic subtypes for metastatic urothelial carcinoma.）比如你的课题，一定会涉及到PIK3CA和二阶基因组事件的关系，如何正确、高效地计算并且可视化mutational signature,变成了非常重要的问题。

我们需要准备的数据非常简单，我们只需要`.maf`文件就可以了。需要注意的是，`.maf`**文件尽量要筛出单癌种的数据，否则降维时会有明显的批次效应。**

现在，我们来仔细看看这个东西怎么用的。
我参考了官方的教程：https://camplab.net/musicatk/v1.6.0/index.html
**首先，我们需要准备好包和数据--这个包很好装，然后准备好相应的`.maf`文件就可以了。**

```r
setwd('D:/MIBC_dataset/Code & Sample Data')
BiocManager::install('musicatk')
library(musicatk)
input <- 'msk_met_urothelial_carcinoma.maf'
```

然后，我们需要定义好我们需要的reference genome。你打开maf文件就可以看到相应的数据，就在数据的`NCBI_Build`的一栏里。换句话说，突变都是相对的，对于不同的正常的reference genome 而言，在这之后，这个包就会调用函数帮你构建`musica`对象了！

```r
variants <- extract_variants_from_maf_file(maf_file = iunput)
g <- select_genome("GRCh37")
musica <- create_musica(x = variants, genome = g)
build_standard_table(musica, g = g, table_name = "SBS96")
```

需要注意的是，这里`table_name`,不是一个可以任意设定的参数。一般来说 ，我们需要的只有`SBS96`。

```r
musica <- create_musica(x = variants, genome = g)
build_standard_table(musica, g = g, table_name = "SBS96")
```

好了，现在我们有了一个`musica`对象，首先我们要把那些突变数量很少的患者剔除，作者认为，突变数目过少的患者会带来噪声，虽然我不这么认为。因此，这一步我觉得是可有可无的。

```r
musica_filter <- subset_musica_by_counts(musica, table_name = "SBS96", num_counts = 10)
```

接下来，你的`musica`对象就变成了`musica_filter`了。接下来，你可以有两种路可以走，来算出来我们需要的mutational signature。

#### *de novo* discovery of mutational signature

`musicatk`给我们的结果往往只支持COSMIC 2.0数据库的复现，没有做到COSMIC 3.0那么细致。（虽然我感觉对我们来说也够用了）,但是呢，很多人都觉得这两个数据库都没有那么靠谱，所以你可以看到很多人讨论mutational signature都是自己从头定义（*de novo* discovery)，然后再和COSMIC 3.0或者2.0去比，找到两者的相似性，作出合理的解释。所以，在你的课题中，你完全可以去先自己定义的COSMIC signature，然后再去想怎么往下推。

因此，对于我们刚刚得到的`musica`对象`musica_filter`,我们可以自己先定义需要的mutational signature。

```r
result_discov <- discover_signatures(musica_filter,
                            table_name = "SBS96",
                            num_signatures =6, 
                            algorithm = "nmf")
```

然后，这里作者会算出两个绝对值，`exposure`和`signature`，`exposure`告诉我，每个病人每种发现的signature有多少的暴露强度；`signature`告诉我,对每个我们新定义的signature而言，我们之前定义的96中sbs模式每种里面有多少的贡献。如果你跑一下，数据大概就长这样：

|         | Signature1 | Signature2 | Signature3 | Signature4 | Signature5 | Signature6 |
| ------- | ---------- | ---------- | ---------- | ---------- | ---------- | ---------- |
| C>A_ACA | 3.33E-09   | 0.026541   | 0.000727   | 2.82E-20   | 4.92E-09   | 1.64E-16   |
| C>A_ACC | 0.059182   | 5.03E-20   | 0.000971   | 2.82E-20   | 0.001591   | 2.92E-20   |
| C>A_ACG | 0.037268   | 5.03E-20   | 5.41E-07   | 3.59E-10   | 3.50E-20   | 9.56E-10   |
| C>A_ACT | 5.17E-20   | 0.013641   | 0.016594   | 2.82E-20   | 0.004584   | 2.92E-20   |
| C>A_CCA | 0.008318   | 0.015371   | 0.001929   | 0.005972   | 0.002887   | 3.51E-06   |
| C>A_CCC | 0.016282   | 0.012951   | 4.38E-20   | 2.82E-20   | 0.006352   | 2.92E-20   |
| C>A_CCG | 0.034114   | 5.03E-20   | 4.38E-20   | 2.01E-09   | 0.003052   | 2.92E-20   |
| C>A_CCT | 5.17E-20   | 0.017822   | 0.009069   | 2.82E-20   | 0.02682    | 3.28E-10   |
| C>A_GCA | 1.40E-10   | 0.013346   | 1.96E-11   | 7.48E-12   | 6.08E-11   | 4.52E-09   |
| C>A_GCC | 0.016346   | 5.03E-20   | 0.003762   | 2.82E-20   | 0.001274   | 2.92E-20   |

同样，我们需要探索一下当下新的mutational signature是否和COSMIC数据库里的结果有一致性，接下来就大概是这样：

```r
compare_cosmic_v2(result_discov, threshold = 0.8)
```

输出的结果，大概就是这样

```r
compare_cosmic_v2(result_discov, threshold = 0.8)
     cosine x_sig_index y_sig_index x_sig_name  y_sig_name
2 0.9912607           3          10 Signature3 Signature10
6 0.9492346           6          13 Signature6 Signature13
3 0.9327385           4           2 Signature4  Signature2
4 0.9019126           5           1 Signature5  Signature1
5 0.8716022           5           6 Signature5  Signature6
1 0.8334956           2          11 Signature2 Signature11
```

最后`cosine`就是相似度（解释见下图），就是我们需要的参数。可以看到，在我们运用的队列(MSKMetropism)里，最显著的突变过程就是`Signature10`和`Signature13`,他们分别和Polymerase Epsilon和APOBEC相关突变有显著的病因学关系，后者也验证了我们对尿路上皮癌基因组的一个刻画结果。

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303082004192.webp" alt="Cosine Similarity - Understanding the math and how it works? (with python)" style="zoom:33%;" />

你可以看到，所谓cosine similarity就是利用高中数学中两个向量夹角的概念来体现的。我们可以把每个新定义的signature和cosmic数据库里的signature看作一个96维的向量，用来比对他们的相似性。

#### 利用cosmic数据库进行相应的推算

如果刚开始，我们不想去从头定义我们需要的mutational signature，那么我们可以直接利用cosmic数据库里的结果进行相应的计算。

```R
# Load COSMIC V2 data
data("cosmic_v2_sigs")

# Predict pre-existing exposures using the "lda" method
result_cosmic_selected_sigs <- predict_exposure(musica = musica_filter, table_name = "SBS96", signature_res = cosmic_v2_sigs,signatures_to_use =  c(1, 2, 4, 6, 7, 13)，algorithm = "lda")
```

在得到我们需要的数据后，我们可以将其标准化后输出，就像这样：

```r
expos.prop <- prop.table(expos, margin = 2)
write.csv(expos.prop,file = 'musicatk.csv')
```

**这个实际上就是我们一直想要的数据！！**

#### musicatk可视化

对于我们刚刚发现的signature而言，我们可以直接用`plot_signature`这个函数输出，画出来期刊上一样的结果！

```r
plot_signatures(result_discov)
```

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303082225077.png" alt="image-20230308222500429" style="zoom:33%;" />

同样，对于相应的COSMIC 2.0的数据库，我们也可以采用同样的计算和可视化策略，比如，我们可以对每个样本统计一下他们的突变是从哪里来的。![image-20230308222717667](https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303082227779.png)

> (颜色改一改，Ready for publication!)

同样，`musicatk`还可以支持我们用更高阶的方法对我们要的数据进行展示——多元降维！

```r
create_umap(result_discov)
plot_umap(result_discov)
```

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303082232496.png" alt="image-20230308223203265" style="zoom: 25%;" />

而且，我们在这个时候，还可以把相应的临床样本的注释导入我们的`musica`对象，结果就像下面这样。

![image-20230308225948826](https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303082259922.png)

也就是：

```r
samp_annot(musica, "Tumor_Type") <- k[,"ONCOTREE_CODE"]
plot_umap(result_cosmic_selected_sigs, color_by = "annotation", annotation = "Tumor_Type")
```

看到吧，在泛癌队列里，`bladder cancer`的APOBEC相关的mutational signature明显富集，和过往报道相似。

需要注意的是，这个`samp_annot`函数并不是那么好解决，原因在于，`musicatk`会在很多步骤中都会筛掉一些突变数据不全、有可能导致噪声的样本，所以，你在根据你sample的注释（比如说某个基因的突变状态、响应性等等）你的`musicatk`对象前，你都需要看一下哪些样本还在你的`musicatk`里面，所以，你需要用`sample_name`的这个函数，输出`musica`对象中的名字，然后再输入你的annotation。

### i ROC曲线

什么样的指标能够精准地判断接受免疫检查点抑制剂的患者治疗的相应性？受试者工作曲线（Recipient Operation Curve)可以帮我们了解不同指标的预测效能。

横坐标1-Specificity（FPR）是特异性，即假阳性率（False Positive Rate, FPR）；纵坐标Sentivity(TPR) 是敏感性，即真阳性率（True Positive Rate, TPR），也可以将ROC曲线理解成反映敏感性与特异性之间关系的曲线；AUC（Area under roc Curve）是指ROC曲线下的面积，为了量化模型好坏或预测模型准确性。

图中不同颜色的线代表不同的模型，形成的曲线即为不同模型的受试曲线。理论上来说如果AUC值越接近于1，说明曲线下方面积越大，表明预测模型的准确率越高，反之则说明预测模型的准确率较低。如果曲线越接近左上角，即横坐标越小，纵坐标越大，表明预测预测模型的准确率越高。

常规的ROC，我们目标的变量是一个二元变量。患者是否有响应

#### 1 常规ROC

```R
library(pROC)      # 加载pROC包
library(ggplot2)  # 调用ggplot2包以利用ggroc函数
```

为了方便，我们采用内置的数据库，`aSAH(This dataset summarizes several clinical and one laboratory variable of 113 patients with an aneurysmal subarachnoid hemorrhage.)`来探讨这个问题。

首先，我们看一下这个数据库长什么样？

```R
> data(aSAH)
> head(aSAH)
   gos6 outcome gender age wfns s100b  ndka
29    5    Good Female  42    1  0.13  3.01
30    5    Good Female  37    1  0.14  8.54
31    5    Good Female  42    1  0.10  8.09
32    5    Good Female  27    1  0.04 10.42
33    1    Poor Female  42    3  0.13 17.40
34    1    Poor   Male  48    2  0.10 12.75
```

这样，我们创建了roc对象`roc1`和`roc2`,用以分别评估蛋白`s100b`和`ndka`预测蛛网膜下腔出血`outcome`的性能。

```R
roc1 <- roc(aSAH$outcome,aSAH$s100b)
auc(roc1)
roc2 <- roc(aSAH$outcome, aSAH$ndka)
auc(roc2)
```

然后，我们就可以把ROC曲线画出来，像下面这样：

```r
plot(roc1, # roc1换为roc2，更改参数可绘制roc2曲线
     print.auc=TRUE,print.auc.x=0.5,print.auc.y=0.5, # 图像上输出AUC值,坐标为（x，y）
     auc.polygon=TRUE, auc.polygon.col="skyblue", # 设置ROC曲线下填充色
     max.auc.polygon=TRUE, # 填充整个图像
     grid=c(0.1,0.2), grid.col=c("green", "red"), # 设置间距为0.1，0.2，线条颜色
     print.thres=TRUE, print.thres.cex=0.8,  # 图像上输出最佳截断值，字体缩放0.8倍
     legacy.axes=TRUE)  # 使横轴从0到1，表示为1-特异度
```

![image-20230315145659094](https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303151457337.png)

然后，我们可以添加第二条ROC曲线：

```R
plot.roc(roc2,
         add=T,  # 增加曲线
         col="#252A34", # 曲线颜色为红色
         print.thres=TRUE, print.thres.cex=0.9,  # 图像上输出最佳截断值，字体缩放倍数
         print.auc=TRUE, print.auc.x=0.4,print.auc.y=0.4,
         # 图像上输出AUC值,坐标为（x，y）
         smooth = F)  # 绘制不平滑曲线
```

![image-20230315153607718](https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303151536896.png)

这两个蛋白的预测效能是否有差异呢？我们需要用统计学的方法加以检验，并且可以以图例的方式添加到它上面去：

```R
testobj <- roc.test(roc1,roc2)   # 检验两条曲线
text(0.8, 0.2, labels=paste("P value =", format.pval(testobj$p.value)), adj=c(0, .5)) # 在图上添加P值
legend(0.35,0.30,  # 图例位置
       bty = "n",  # 图例样式，默认为 "o"
       title="",   # 引号内添加图例标题
       legend=c("roc1","roc2"),  # 添加分组
       col=c("#FF2E63","#252A34"),  # 颜色跟前面一致
       lwd=2)  # 线条粗细
```



## 第二章 单细胞基础算法和可视化工具

### 1 数据前期处理，t-SNE降维与可视化

参考代码：`Seurat.R`

​	**这些代码并不是我自己写的，[我把市面上比较好的课学了一遍](https://pan.baidu.com/s/1bVowNybsw3F050FPcn9UiA?pwd=0321)（提取码：0321），浓缩了一下，加了点东西，用给你的数据可以直接跑通。但是，我认为还是需要花一些篇幅讲解一下这个东西是怎么工作的。**

第一个part我主要会详细地讲一下单细胞结果运算的基本逻辑，我学到今天，感觉单细胞算法和我们平常对转录组和基因组的分析有很大的不同。简单来说，绕不开一个叫做`Seurat`对象的东西。`Seurat`包是我们在运用单细胞数据中最重要的包，任何单细胞数据，无论是降维聚类、可视化方案或者是某一个亚群的细胞的annotation都被储存在一个`Seurat`对象里面。其实和上面我提到的`musicatk`对象一样，所有的结果都是针对了整个`musicatk`对象，我们把图啊什么的画出来。而比如说我们算ssGSEA，或者画一个图，都是**面向过程**的编程逻辑，而对于单细胞数据而言，我们更倾向于是**面向对象**的一个逻辑。

你如果感兴趣的话，**可以参考[这篇推送](https://zhuanlan.zhihu.com/p/75265007)**来了解什么叫做面向过程，什么叫做面向对象。

这一个部分的教程的数据来源于[Unravelling subclonal heterogeneity and aggressive disease states in TNBC through single-cell RNA-seq,*Nat Commun*，2018](https://www.nature.com/articles/s41467-018-06052-0#Sec10)，**纳入了六名原发性三阴性乳腺癌患者的样本**。

```R
install.packages("devtools")
library(devtools)
options(download.file.method = "wininet")
devtools::install_github('dviraran/SingleR')
```

```r
###################################04.数据前期处理和矫正###################################
#读取数据
library(limma)
library(Seurat)
library(dplyr)
library(magrittr)

setwd('D:/MIBC_dataset/Singlecell/04-07.Seurat')             #设置工作目录

#读取文件，并对重复基因取均值
rt=read.table("geneMatrix.txt",sep="\t",header=T,check.names=F)
rt=as.matrix(rt)
rownames(rt)=rt[,1]
exp=rt[,2:ncol(rt)]
dimnames=list(rownames(exp),colnames(exp))
data=matrix(as.numeric(as.matrix(exp)),nrow=nrow(exp),dimnames=dimnames)
data=avereps(data)
```

我们读的数据基本上就是和之前转录组的结果差不多，就是一个样本乘以基因的矩阵。需要注意的是，现在每个样本就是一个细胞。

```r
#将矩阵转换为Seurat对象，并对数据进行过滤
pbmc <- CreateSeuratObject(counts = data,project = "seurat", min.cells = 3, min.features = 50, names.delim = "_",)
```

现在我们建立了一个`Seurat`对象，这个对象的**数据结构**非常的重要！接下来我们要去探讨这些问题。
首先我们需要做一件事情，要去确保我们每个细胞都是我们需要的肿瘤细胞、免疫细胞或者间质细胞，因此我们需要确定测序数据确实是正常的基因数据。

```r
#使用PercentageFeatureSet函数计算线粒体基因的百分比
pbmc[["percent.mt"]] <- PercentageFeatureSet(object = pbmc, pattern = "^MT-")
pdf(file="04.featureViolin.pdf",width=10,height=6)           #保存基因特征小提琴图
VlnPlot(object = pbmc, features = c("nFeature_RNA", "nCount_RNA", "percent.mt"), ncol = 3)
dev.off()
```

![image-20230314101515907](https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303141015112.png)

```r
pbmc <- subset(x = pbmc, subset = nFeature_RNA > 50 & percent.mt < 5)    #对数据进行过滤

#测序深度的相关性绘图
pdf(file="04.featureCor.pdf",width=10,height=6)              #保存基因特征相关性图
plot1 <- FeatureScatter(object = pbmc, feature1 = "nCount_RNA", feature2 = "percent.mt",pt.size=1.5)
plot2 <- FeatureScatter(object = pbmc, feature1 = "nCount_RNA", feature2 = "nFeature_RNA",pt.size=1.5)
CombinePlots(plots = list(plot1, plot2))
dev.off()
```

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303141020833.png" alt="image-20230314102053740" style="zoom:50%;" />

你可以看到，我们的数据效果还是不错的。首先，基本上就是没有线粒体基因被发现；其次，我们的基因数据里，`nFeature_RNA`代表每个细胞所检测到的基因数目，`nCount_RNA`代表每个细胞的UMI数目，让我们了解数据集的复杂性（每个UMI检测到的基因越多，我们的数据越复杂）。二者明显成正比，可以看出我们测序是对的！

```r
#对数据进行标准化
pbmc <- NormalizeData(object = pbmc, normalization.method = "LogNormalize", scale.factor = 10000)
#提取那些在细胞间变异系数较大的基因
pbmc <- FindVariableFeatures(object = pbmc, selection.method = "vst", nfeatures = 1500)
#输出特征方差图
top10 <- head(x = VariableFeatures(object = pbmc), 10)
pdf(file="04.featureVar.pdf",width=10,height=6)              #保存基因特征方差图
plot1 <- VariableFeaturePlot(object = pbmc)
plot2 <- LabelPoints(plot = plot1, points = top10, repel = TRUE)
CombinePlots(plots = list(plot1, plot2))
dev.off()
```

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303141029111.png" alt="image-20230314102933993" style="zoom:50%;" />

接下来我们要做的事情，就是去看哪些基因最能够区分我们的细胞，`FindVariableFeatures`函数会给我们的`pbmc`对象增加了一个属性。当然，这样的标注是很粗放的，但是我们可以明显地看到，比如说这里面的*SPP1*，之前很多文章都发现*SPP1*阳性的TAM有很深远的意义。我自己在看膀胱癌数据的时候，一眼就看到这张图最上面的一个基因就是*PPARG*,它是一个转录因子决定了膀胱肿瘤向Basal还是Luminal方向发展。


```r
###################################05.PCA主成分分析###################################
##PCA分析
pbmc=ScaleData(pbmc)                     #PCA降维之前的标准预处理步骤
pbmc=RunPCA(object= pbmc,npcs = 20,pc.genes=VariableFeatures(object = pbmc))     #PCA分析

#绘制每个PCA成分的相关基因
pdf(file="05.pcaGene.pdf",width=10,height=8)
VizDimLoadings(object = pbmc, dims = 1:4, reduction = "pca",nfeatures = 20)
dev.off()

#主成分分析图形
pdf(file="05.PCA.pdf",width=6.5,height=6)
DimPlot(object = pbmc, reduction = "pca")
dev.off()

#主成分分析热图
pdf(file="05.pcaHeatmap.pdf",width=10,height=8)
DimHeatmap(object = pbmc, dims = 1:4, cells = 500, balanced = TRUE,nfeatures = 30,ncol=2)
dev.off()

#每个PC的p值分布和均匀分布
pbmc <- JackStraw(object = pbmc, num.replicate = 100)
pbmc <- ScoreJackStraw(object = pbmc, dims = 1:20)
pdf(file="05.pcaJackStraw.pdf",width=8,height=6)
JackStrawPlot(object = pbmc, dims = 1:20)
dev.off()
```

这里你不需要太过关注，`pca`降维方法是一种线性降维方法，只不过是希望能够粗略地观察哪一些基因可以成为我们新定义的细胞亚群。绝大多数的单细胞的可视化策略，都是运用`tsne`或者`umap`的方法进行解决。

```r
library(Matrix)

###################################06.TSNE聚类分析和marker基因###################################
##TSNE聚类分析
pcSelect=20
pbmc <- FindNeighbors(object = pbmc, dims = 1:pcSelect)                #计算邻接距离
pbmc <- FindClusters(object = pbmc, resolution = 0.5)                  #对细胞分组,优化标准模块化
pbmc <- RunTSNE(object = pbmc, dims = 1:pcSelect)                      #TSNE聚类
pdf(file="06.TSNE.pdf",width=6.5,height=6)
TSNEPlot(object = pbmc, pt.size = 2, label = TRUE) #TSNE可视化
dev.off()
write.table(pbmc$seurat_clusters,file="06.tsneCluster.txt",quote=F,sep="\t",col.names=F)
```

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303141125933.png" alt="image-20230314112534842" style="zoom:50%;" />


```R
##寻找差异表达的特征
logFCfilter=0.5
adjPvalFilter=0.05
pbmc.markers <- FindAllMarkers(object = pbmc,
                               only.pos = FALSE,
                               min.pct = 0.25,
                               logfc.threshold = logFCfilter)
sig.markers=pbmc.markers[(abs(as.numeric(as.vector(pbmc.markers$avg_logFC)))>logFCfilter & as.numeric(as.vector(pbmc.markers$p_val_adj))<adjPvalFilter),]
write.table(sig.markers,file="06.markers.xls",sep="\t",row.names=F,quote=F)

top10 <- pbmc.markers %>% group_by(cluster) %>% top_n(n = 10, wt = avg_log2FC)
#绘制marker在各个cluster的热图
pdf(file="06.tsneHeatmap.pdf",width=12,height=9)
DoHeatmap(object = pbmc, features = top10$gene) + NoLegend()
dev.off()
```

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303141107386.png" alt="image-20230314110723023" style="zoom: 25%;" />

```r
#绘制marker的小提琴图
pdf(file="06.markerViolin.pdf",width=10,height=6)
VlnPlot(object = pbmc, features = c("IGLL5", "MBOAT1"))
dev.off()
```

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303141113488.png" alt="image-20230314111300403" style="zoom:50%;" />

```R
#绘制marker在各个cluster的散点图
pdf(file="06.markerScatter.pdf",width=10,height=6)
FeaturePlot(object = pbmc, features = c("IGLL5", "MBOAT1"),cols = c("white", "red"))
dev.off()
```

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303141122288.png" alt="image-20230314112227137" style="zoom:50%;" />

```R
#绘制marker在各个cluster的气泡图
pdf(file="06.markerBubble.pdf",width=12,height=6)
cluster10Marker=c("MBOAT1", "NFIB", "TRPS1", "SOX4", "CNN3", "PIM2", "MZB1", "MS4A1", "ELK2AP", "IGLL5")
DotPlot(object = pbmc, features = cluster10Marker)
dev.off()
```

<img src="https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303141123461.png" alt="image-20230314112321320" style="zoom:50%;" />

看到了吗？这和我们杂志上看到的图就很像了！`FeaturePlot`告诉我们的是我们降维聚类下每个细胞表达marker基因的情况，`DotPlot`告诉我们的是每个细胞亚群表达目标marker基因的情况。`DoHeatmap`告诉我们每一个细胞亚群中哪些基因高表达、低表达，你未来肯定可以用的到。

```R
###################################07.注释细胞类型###################################
library(SingleR)
counts<-pbmc@assays$RNA@counts
clusters<-pbmc@meta.data$seurat_clusters
ann=pbmc@meta.data$orig.ident
singler = CreateSinglerObject(counts, annot = ann, "pbmc", min.genes = 0,
                              species = "Human", citation = "",
                              ref.list = list(), normalize.gene.length = F, variable.genes = "de",
                              fine.tune = F, do.signatures = T, clusters = clusters, do.main.types = T,
                              reduce.file.size = T, numCores = 1)
singler$seurat = pbmc
singler$meta.data$xy = pbmc@reductions$tsne@cell.embeddings
clusterAnn=singler$singler[[2]]$SingleR.clusters.main$labels
write.table(clusterAnn,file="07.clusterAnn.txt",quote=F,sep="\t",col.names=F)
write.table(singler$other,file="07.cellAnn.txt",quote=F,sep="\t",col.names=F)

#准备monocle分析需要的文件
monocle.matrix=as.matrix(pbmc@assays$RNA@data)
monocle.matrix=cbind(id=row.names(monocle.matrix),monocle.matrix)
write.table(monocle.matrix,file="07.monocleMatrix.txt",quote=F,sep="\t",row.names=F)
monocle.sample=as.matrix(pbmc@meta.data)
monocle.sample=cbind(id=row.names(monocle.sample),monocle.sample)
write.table(monocle.sample,file="07.monocleSample.txt",quote=F,sep="\t",row.names=F)
monocle.geneAnn=data.frame(gene_short_name = row.names(monocle.matrix), row.names = row.names(monocle.matrix))
monocle.geneAnn=cbind(id=row.names(monocle.geneAnn),monocle.geneAnn)
write.table(monocle.geneAnn,file="07.monocleGene.txt",quote=F,sep="\t",row.names=F)
write.table(singler$other,file="07.monocleClusterAnn.txt",quote=F,sep="\t",col.names=F)
write.table(sig.markers,file="07.monocleMarkers.txt",sep="\t",row.names=F,quote=F)
```

回到现在，我们大概就能理解Seurat对象的格式了。他会自动识别那些细胞来源于哪些样本，存储在`meta.data`中，并且不需要我们另起炉灶，新建一个列表或者数据框处理差异表达基因、再另外去降维，都被整合在了一个`Seurat`对象中。

### 2 monocle 伪时序分析

`参考代码：pseudotime_analysis.R`

通过伪时序分析，我们可以看出特定细胞亚群的分化方向。

```r
#if (!requireNamespace("BiocManager", quietly = TRUE))
install.packages("BiocManager")
BiocManager::install("monocle")

library(monocle)
setwd("D:/MIBC_dataset/Singlecell/08.monocle")               #设置工作目录
monocle.matrix=read.table("07.monocleMatrix.txt",sep="\t",header=T,row.names=1,check.names=F)
monocle.sample=read.table("07.monocleSample.txt",sep="\t",header=T,row.names=1,check.names=F)
monocle.geneAnn=read.table("07.monocleGene.txt",sep="\t",header=T,row.names=1,check.names=F)
marker=read.table("07.monocleMarkers.txt",sep="\t",header=T,check.names=F)

#将Seurat结果转换为monocle需要的细胞矩阵，细胞注释表和基因注释表表
data <- as(as.matrix(monocle.matrix), 'sparseMatrix')
pd<-new("AnnotatedDataFrame", data = monocle.sample)
fd<-new("AnnotatedDataFrame", data = monocle.geneAnn)
cds <- newCellDataSet(data, phenoData = pd, featureData = fd)

#给其中一列数据重命名
names(pData(cds))[names(pData(cds))=="seurat_clusters"]="Cluster"
pData(cds)[,"Cluster"]=paste0("cluster",pData(cds)[,"Cluster"])

#添加细胞聚类数据
clusterRt=read.table("07.monocleClusterAnn.txt",header=F,sep="\t",check.names=F)
clusterAnn=as.character(clusterRt[,2])
names(clusterAnn)=paste0("cluster",clusterRt[,1])
pData(cds)$cell_type2 <- plyr::revalue(as.character(pData(cds)$Cluster),clusterAnn)

#伪时间分析流程
cds <- estimateSizeFactors(cds)
cds <- estimateDispersions(cds)

cds <- setOrderingFilter(cds, marker$gene)
plot_ordering_genes(cds)
cds <- reduceDimension(cds, max_components = 2,reduction_method = 'DDRTree')
cds <- orderCells(cds)
pdf(file="cluster.trajectory.pdf",width=6.5,height=6)
plot_cell_trajectory(cds, color_by = "Cluster")
dev.off()
pdf(file="cellType.trajectory.pdf",width=6.5,height=6)
plot_cell_trajectory(cds,color_by = "cell_type2")
dev.off()
trace('project2MST', edit = T, where = asNamespace("monocle"))
```

参考：[解决monocle2的orderCells报错的两种方法](https://mp.weixin.qq.com/s/4PCWD1dRMYIWKPL-okWhBw)

![image-20230315154254845](https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303151542033.png)

## 第三章 单细胞复现实战

### a 初级版--以Bischoff P et al, *Oncogene*, 2021(NSCLC)为例

参考代码：`Single cell LUAD.R`

#### Chapter 1

```r
#####################预处理

# 加载R包
library(Seurat)
library(dplyr)
library(reticulate)
library(sctransform)
library(cowplot)
library(ggplot2)
library(viridis)
library(tidyr)
library(magrittr)
library(reshape2)
library(readxl)
library(progeny)
library(readr)
library(stringr)

#设定阈值
nFeature_lower <- 500
nFeature_upper <- 10000
nCount_lower <- 1000
nCount_upper <- 100000
pMT_lower <- 0
pMT_upper <- 30
pHB_lower <- 0
pHB_upper <- 5

theme_set(theme_cowplot())

#设定配色方案
use_colors <- c(
  Tumor = "brown2",
  Normal = "deepskyblue2",
  G1 = "#46ACC8",
  G2M = "#E58601",
  S = "#B40F20",
  Epithelial = "seagreen",
  Immune = "darkgoldenrod2",
  Stromal = "steelblue",
  p018 = "#E2D200",
  p019 = "#46ACC8",
  p023 = "#E58601",
  p024 = "#B40F20",
  p027 = "#0B775E",
  p028 = "#E1BD6D",
  p029 = "#35274A",
  p030 = "#F2300F",
  p031 = "#7294D4",
  p032 = "#5B1A18",
  p033 = "#9C964A",
  p034 = "#FD6467")

# 加载数据和质量控制

###工作目录根据自己的文件夹做一定调整，例如../,./data/
### 样本列表，利用read_excel
samples <- read_excel("./metadata/patients_metadata.xlsx", range = cell_cols("A:A")) %>% .$sample_id

### 批量载入标准的cellranger文件，阅读原文获取数据,为了大家运行便利，我们读6个腺癌进来，3正常，3肿瘤
### 为什么我知道是腺癌请看metadata.excel文件
### 15:20用于挑选出sample的范围
### 使用paste0函数进行准确定位到某个文件夹
### 注意read10X的三联文件，matrix,features,barcodes
### cellranger为公司返还的一般形式，这里对完全0基础的同学而言是最大的理解障碍
for (i in seq_along(samples)[15:20]){
  assign(paste0("scs_data", i), Read10X(data.dir = paste0("./cellranger/", samples[i], "/filtered_feature_bc_matrix")))
}

### 创建seurat对象，也是创建6个对象
for (i in seq_along(samples)[15:20]){
  assign(paste0("seu_obj", i), CreateSeuratObject(counts = eval(parse(text = paste0("scs_data", i))), project = samples[i], min.cells = 3))
}

### merge一键融合，从15-20个样本
seu_obj <- merge(seu_obj15, y = c(seu_obj16, seu_obj17, seu_obj18, seu_obj19, seu_obj20), add.cell.ids = samples[15:20], project = "lung")

### 计算线粒体等基因的比例
seu_obj <- PercentageFeatureSet(seu_obj, pattern = "^MT-", col.name = "pMT")
seu_obj <- PercentageFeatureSet(seu_obj, pattern = "^HBA|^HBB", col.name = "pHB")
seu_obj <- PercentageFeatureSet(seu_obj, pattern = "^RPS|^RPL", col.name = "pRP")

qcparams <- c("nFeature_RNA", "nCount_RNA", "pMT", "pHB", "pRP")
for (i in seq_along(qcparams)){
  print(VlnPlot(object = seu_obj, features = qcparams[i], group.by = "orig.ident", pt.size = 0))
}
for (i in seq_along(qcparams)){
  print(RidgePlot(object = seu_obj, features = qcparams[i], group.by = "orig.ident"))
}
### 批量看计算结果，很不错
VlnPlot(seu_obj, features = c("nFeature_RNA", "nCount_RNA", "pMT"), pt.size = 0, group.by = "orig.ident", ncol = 1, log = T)
ggsave2("QC.pdf", path = "./result_3v3/", width = 20, height = 20, units = "cm")
remove(seu_obj15)
remove(seu_obj16)
remove(seu_obj17)
remove(seu_obj18)
remove(seu_obj19)
remove(seu_obj20)


remove(scs_data15)
remove(scs_data16)
remove(scs_data17)
remove(scs_data18)
remove(scs_data19)
remove(scs_data20)


# 过滤
# 定义两个过滤作图函数
qc_std_plot_helper <- function(x) x + 
  scale_color_viridis() +
  geom_point(size = 0.01, alpha = 0.3)

qc_std_plot <- function(seu_obj) {
  qc_data <- as_tibble(FetchData(seu_obj, c("nCount_RNA", "nFeature_RNA", "pMT", "pHB", "pRP")))
  plot_grid(
    
    qc_std_plot_helper(ggplot(qc_data, aes(log2(nCount_RNA), log2(nFeature_RNA), color = pMT))) + 
      geom_hline(yintercept = log2(nFeature_lower), color = "red", linetype = 2) +
      geom_hline(yintercept = log2(nFeature_upper), color = "red", linetype = 2) +
      geom_vline(xintercept = log2(nCount_lower), color = "red", linetype = 2) +
      geom_vline(xintercept = log2(nCount_upper), color = "red", linetype = 2),
    qc_std_plot_helper(ggplot(qc_data, aes(log2(nCount_RNA), log2(nFeature_RNA), color = pHB))) + 
      geom_hline(yintercept = log2(nFeature_lower), color = "red", linetype = 2) +
      geom_hline(yintercept = log2(nFeature_upper), color = "red", linetype = 2) +
      geom_vline(xintercept = log2(nCount_lower), color = "red", linetype = 2) +
      geom_vline(xintercept = log2(nCount_upper), color = "red", linetype = 2),
    qc_std_plot_helper(ggplot(qc_data, aes(log2(nCount_RNA), log2(nFeature_RNA), color = pRP))) + 
      geom_hline(yintercept = log2(nFeature_lower), color = "red", linetype = 2) +
      geom_hline(yintercept = log2(nFeature_upper), color = "red", linetype = 2) +
      geom_vline(xintercept = log2(nCount_lower), color = "red", linetype = 2) +
      geom_vline(xintercept = log2(nCount_upper), color = "red", linetype = 2),
    
    qc_std_plot_helper(ggplot(qc_data, aes(log2(nCount_RNA), pMT, color = nFeature_RNA))) + 
      geom_hline(yintercept = pMT_lower, color = "red", linetype = 2) +
      geom_hline(yintercept = pMT_upper, color = "red", linetype = 2) +
      geom_vline(xintercept = log2(nCount_lower), color = "red", linetype = 2) +
      geom_vline(xintercept = log2(nCount_upper), color = "red", linetype = 2),
    qc_std_plot_helper(ggplot(qc_data, aes(log2(nCount_RNA), pHB, color = nFeature_RNA))) + 
      geom_hline(yintercept = pHB_lower, color = "red", linetype = 2) +
      geom_hline(yintercept = pHB_upper, color = "red", linetype = 2) +
      geom_vline(xintercept = log2(nCount_lower), color = "red", linetype = 2) +
      geom_vline(xintercept = log2(nCount_upper), color = "red", linetype = 2),
    qc_std_plot_helper(ggplot(qc_data, aes(log2(nCount_RNA), pRP, color = nFeature_RNA))) + 
      geom_vline(xintercept = log2(nCount_lower), color = "red", linetype = 2) +
      geom_vline(xintercept = log2(nCount_upper), color = "red", linetype = 2),
    
    
    qc_std_plot_helper(ggplot(qc_data, aes(log2(nFeature_RNA), pMT, color = nCount_RNA))) + 
      geom_hline(yintercept = pMT_lower, color = "red", linetype = 2) +
      geom_hline(yintercept = pMT_upper, color = "red", linetype = 2) +
      geom_vline(xintercept = log2(nFeature_lower), color = "red", linetype = 2) +
      geom_vline(xintercept = log2(nFeature_upper), color = "red", linetype = 2),
    qc_std_plot_helper(ggplot(qc_data, aes(log2(nFeature_RNA), pHB, color = nCount_RNA))) + 
      geom_hline(yintercept = pHB_lower, color = "red", linetype = 2) +
      geom_hline(yintercept = pHB_upper, color = "red", linetype = 2) +
      geom_vline(xintercept = log2(nFeature_lower), color = "red", linetype = 2) +
      geom_vline(xintercept = log2(nFeature_upper), color = "red", linetype = 2),
    qc_std_plot_helper(ggplot(qc_data, aes(log2(nFeature_RNA), pRP, color = nCount_RNA))) + 
      geom_vline(xintercept = log2(nFeature_lower), color = "red", linetype = 2) +
      geom_vline(xintercept = log2(nFeature_upper), color = "red", linetype = 2),
    
    qc_std_plot_helper(ggplot(qc_data, aes(pRP, pMT, color = nCount_RNA))) + 
      geom_hline(yintercept = pMT_lower, color = "red", linetype = 2) +
      geom_hline(yintercept = pMT_upper, color = "red", linetype = 2),
    qc_std_plot_helper(ggplot(qc_data, aes(pRP, pMT, color = nFeature_RNA))) + 
      geom_hline(yintercept = pMT_lower, color = "red", linetype = 2) +
      geom_hline(yintercept = pMT_upper, color = "red", linetype = 2),
    
    
    ggplot(gather(qc_data, key, value), aes(key, value)) +
      geom_violin() +
      facet_wrap(~key, scales = "free", ncol = 5),
    
    ncol = 3, align = "hv"
  )
}


## 过滤前

seu_obj_unfiltered <- seu_obj

# 作图，有点慢，但是一般可以出来
qc_std_plot(seu_obj_unfiltered)

ggsave2("before_filtering.pdf", path = "./result_3v3/", width = 30, height = 30, units = "cm")
## 过滤
seu_obj <- subset(seu_obj_unfiltered, subset = nFeature_RNA > nFeature_lower & nFeature_RNA < nFeature_upper & nCount_RNA > nCount_lower & nCount_RNA < nCount_upper & pMT < pMT_upper & pHB < pHB_upper)

#有点慢，同样可以出
qc_std_plot(seu_obj)
ggsave2("after_filtering.pdf", path = "./result_3v3/", width = 30, height = 30, units = "cm")

# 看过滤了多少
seu_obj_unfiltered
seu_obj
##################### SCTransform提供了去除深度的策略###############################
#利用seurat的SCTransform消除深度差异，非常推荐的标准化的方法，之后保存下次用！
seu_obj <- SCTransform(seu_obj, verbose = T, vars.to.regress = c("nCount_RNA", "pMT"), conserve.memory = T)

saveRDS(seu_obj, file = "scRNA_SCTransform.RDS")
```

#### Chapter 2 混合样本的初次聚类：Epithelial, Stromal and Immune

```r
# 加载R包
library(Seurat)
library(dplyr)
library(reticulate)
library(sctransform)
library(cowplot)
library(ggplot2)
library(viridis)
library(tidyr)
library(magrittr)
library(reshape2)
library(readxl)
library(progeny)
library(readr)
library(stringr)
seu_obj=readRDS('scRNA_SCTransform.RDS')


#降维
seu_obj <- RunPCA(seu_obj)
###根据折线何时平缓选择PC数，一般10~20
ElbowPlot(seu_obj, ndims = 50)

seu_obj <- RunUMAP(seu_obj, dims = 1:15, verbose = T)
seu_obj <- RunTSNE(seu_obj, dims = 1:15, verbose = T)
seu_obj <- FindNeighbors(seu_obj, dims = 1:15)
library(ggplot2)
for (i in c(0.2, 0.3, 0.4, 0.5, 1, 2)) {
  seu_obj <- FindClusters(seu_obj, resolution = i)
  print(DimPlot(seu_obj, reduction = "tsne") + labs(title = paste0("resolution: ", i)))
}

###简单做图看下
DimPlot(seu_obj, group.by = "orig.ident",reduction = 'umap')
```

![image-20230314155225679](https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303141552851.png)

```R

#主要的细胞类型注释——区分上皮、间质、免疫细胞

### 选定marker,这边纳入了上皮marker如EPCAM，间质如VWF，免疫如PTPRC，KIT是间叶特征marker
mainmarkers <- c("PECAM1", "VWF", "ACTA2", "JCHAIN", "MS4A1", "PTPRC", "CD68", "KIT", "EPCAM", "CDH1", "KRT7", "KRT19")
dir.create('annotation')
dev.off()

### 对每个marker基因作图并观察,这里建议画umap更清楚一些
for (i in seq_along(mainmarkers)) {
  FeaturePlot(seu_obj, features = mainmarkers[i], coord.fixed = T, order = T, cols = cividis(10))
  ggsave2(paste0("FeaturePlot_mainmarkers_", mainmarkers[i], ".png"), path = "./annotation", width = 10, height = 10, units = "cm")
}
```

需要再次强调一下，`FeaturePlot`直接作用于我们的`Seurat`对象，可以很直观地在UMAP plot中看到marker gene的表达情况。

**Quick quiz:高亮的细胞是哪些？（组胚复习**

![image-20230314160740639](https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303141607740.png)

```R
### 输出下面两个图，做对比，同时结合一下前面每个基因的featureplot，即通过手头的三个资料去分析每个cluster的细胞大类
### 手工注释，resolution并不太重要，我们就选择0.2为例，共19个cluster，但是很多cluster实际上一样
### 但是建议大一点的resolution，否则可能混淆。
DotPlot(seu_obj, features = mainmarkers, group.by = "SCT_snn_res.0.2") + 
  coord_flip() + 
  scale_color_binned()
ggsave2("DotPlot_mainmarkers.png", path = "./annotation", width = 10, height = 5)
DimPlot(seu_obj, group.by = "SCT_snn_res.0.2", label = T, label.size = 5)
ggsave2("DimPlot_all_clusters.png", path = "./annotation", width = 5, height = 5)
### 仔细观察，发现0，10，15，17上皮，1-9，11，12，18免疫细胞，13,14,16间质，你需要同时考虑到Umap的接近程度和marker的表达。
Idents(seu_obj) <- seu_obj$SCT_snn_res.0.2
annotation_curated_main <- read_excel("./annotation/annotation_main.xlsx")
new_ids_main <- annotation_curated_main$main_cell_type
names(new_ids_main) <- levels(seu_obj)
#加入seurat对象中
seu_obj <- RenameIdents(seu_obj, new_ids_main)
seu_obj@meta.data$Main_cell_type <- Idents(seu_obj)

DimPlot(seu_obj, label = T, label.size = 5,group.by="Main_cell_type" ,
        cols= c("seagreen","darkgoldenrod2","steelblue"))
#出正式图
ggsave2("DimPlot_main_cell_type.pdf", path = "./annotation", width = 7, height = 6)
saveRDS(seu_obj, file = "scRNA_main_annotated.RDS")
```

![image-20230314161725848](https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303141617964.png)

最后的结果就是这样，我们通过事先定义的几个marker，比如*vWF*,*PCAM1*等，定义了我们当下的细胞亚群。这一章的目的，其实就是为了在一些细胞中我们通过一些既定的marker确认可以将多个病人、几万个细胞的样本可以被分割为基质、免疫和上皮三个亚群。

#### Chapter3

```r
# 增加进病人信息
seu_obj=readRDS('scRNA_main_annotated.RDS')
metatable <- read_excel("./metadata/patients_metadata.xlsx")
### 从seurat对象里提取orig.ident(病人)
metadata <- FetchData(seu_obj, "orig.ident")
metadata$cell_id <- rownames(metadata)
metadata$sample_id <- metadata$orig.ident
### dplyr中的left_join合并
metadata <- left_join(x = metadata, y = metatable, by = "sample_id")
### 重新加行名
rownames(metadata) <- metadata$cell_id

### 通过AddMetaData直接补入15列
seu_obj <- AddMetaData(seu_obj, metadata = metadata)

# 细胞周期运算，简单过

s.genes <- cc.genes$s.genes
g2m.genes <- cc.genes$g2m.genes

score_cc <- function(seu_obj) {
  seu_obj <- CellCycleScoring(seu_obj, s.genes, g2m.genes)
  seu_obj@meta.data$CC.Diff <- seu_obj@meta.data$S.Score - seu_obj@meta.data$G2M.Score
  return(seu_obj)
}

seu_obj <- score_cc(seu_obj)

#看上去还可以
FeatureScatter(seu_obj, "G2M.Score", "S.Score", group.by = "Phase", pt.size = .1) +
  coord_fixed(ratio = 1)
# 提取子集，重归一化

### saveRDS(seu_obj, file = "scRNA_main_anno_add_pt.RDS")

### Idents设置最主要的区分因子
Idents(seu_obj) <- seu_obj@meta.data$Main_cell_type
epi <- subset(seu_obj, idents = "Epithelial")
imm <- subset(seu_obj, idents = "Immune")
str <- subset(seu_obj, idents = "Stromal")

#也可以这样
#Cells.sub <- subset(seu_obj@meta.data, Main_cell_type =='Immune',)
#scRNAsub <- subset(seu_obj, cells=row.names(Cells.sub))


### 因为总量变了，所以需要重新归一化，否则分布将被打破
epi <- ScaleData(epi)
imm <- ScaleData(imm)
str <- ScaleData(str)

saveRDS(epi, file = "epi.RDS")
saveRDS(imm, file = "imm.RDS")
saveRDS(str, file = "str.RDS")
#作图：不同注释方式的Umap
### 修改group.by
DimPlot(seu_obj, group.by = "tissue_type", cols = use_colors, pt.size = 0.1)
ggsave2("区分肿瘤正常.png", path = "./result_3v3/", width=7 ,height=6)

###区分病人但不区分N/T，32号病人有些特别？我们现按下不表
DimPlot(seu_obj, group.by = "patient_id", cols = use_colors, pt.size = 0.1)
ggsave2("区分病人.png", path = "./result_3v3/",  width=7 ,height=6)

DimPlot(seu_obj, group.by = "main_cell_type", cols = use_colors, pt.size = 0.1)
ggsave2("区分细胞类群.png", path = "./result_3v3/",  width=7 ,height=6)
# 作图：各病人细胞分布图
### FetchData拿metadata数据出来
### 可以看到32号病人的上皮激增
cell_types <- FetchData(seu_obj, vars = c("sample_id", "main_cell_type", "tissue_type"))
ggplot(data = cell_types) + 
  geom_bar(mapping = aes(x = sample_id, fill = main_cell_type, ), position = "fill", width = 0.75) +
  scale_fill_manual(values = use_colors) +
  coord_flip()
ggsave2("细胞分布图.pdf", path = "./result_3v3/", width=5 ,height=5)
```

#### Chapter4

```r
library(Seurat)
library(dplyr)
library(reticulate)
library(sctransform)
library(cowplot)
library(ggplot2)
library(viridis)
library(tidyr)
library(magrittr)
library(reshape2)
library(readxl)
library(readr)
library(stringr)
library(progeny)
library(scales)

theme_set(theme_cowplot())

use_colors <- c(
  Tumor = "brown2",
  Normal = "deepskyblue2",
  G1 = "#46ACC8",
  G2M = "#E58601",
  S = "#B40F20",
  Epithelial = "seagreen",
  Immune = "darkgoldenrod2",
  Stromal = "steelblue",
  p018 = "#E2D200",
  p019 = "#46ACC8",
  p023 = "#E58601",
  p024 = "#B40F20",
  p027 = "#0B775E",
  p028 = "#E1BD6D",
  p029 = "#35274A",
  p030 = "#F2300F",
  p031 = "#7294D4",
  p032 = "#5B1A18",
  p033 = "#9C964A",
  p034 = "#FD6467")


#加载数据

epi <- readRDS("epi.RDS")
imm <- readRDS("imm.RDS")
str <- readRDS("str.RDS")


# 主要亚群重聚类

### 上皮重聚类

epi <- RunPCA(epi)
ElbowPlot(epi,  ndims = 50)


epi <- RunUMAP(epi, dims = 1:20)
epi <- FindNeighbors(epi, dims = 1:20)
for (i in c(0.2, 0.3, 0.4, 0.5, 1, 2)) {
  epi <- FindClusters(epi, resolution = i)
  print(DimPlot(epi, reduction = "umap", label = T) + labs(title = paste0("resolution: ", i)))
}

### 我们依然选择比较大的resolution，手动注释没有关系
### 控制在20个cluster左右即可
Idents(epi) <- epi@meta.data$SCT_snn_res.1


### 免疫重聚类

imm <- RunPCA(imm)
ElbowPlot(imm,  ndims = 50)


imm <- RunUMAP(imm, dims = 1:20)
imm <- FindNeighbors(imm, dims = 1:20)
for (i in c(0.2, 0.3, 0.4, 0.5, 1, 2)) {
  imm <- FindClusters(imm, resolution = i)
  print(DimPlot(imm, reduction = "umap") + labs(title = paste0("resolution: ", i)))
}
### 控制在20个cluster左右即可
Idents(imm) <- imm@meta.data$SCT_snn_res.0.4


### 基质重聚类

str <- RunPCA(str)
ElbowPlot(str, ndims = 50)


str <- RunUMAP(str, dims = 1:20)
str <- FindNeighbors(str, dims = 1:20)
for (i in c(0.2, 0.3, 0.4, 0.5, 1, 2)) {
  str <- FindClusters(str, resolution = i)
  print(DimPlot(str, reduction = "umap") + labs(title = paste0("resolution: ", i)))
}
### 细胞不多，可以少点，随意
Idents(str) <- str@meta.data$SCT_snn_res.2

# 疾病特异性细胞亚群
### 正常和肿瘤的上皮对比
### 发现确实多了一群
DimPlot(epi, group.by = "SCT_snn_res.1", label = T, repel = T, split.by = "tissue_type")

ggsave2("正常-肿瘤上皮.png", path = "./result_3v3/", width = 30, height = 15, units = "cm")
### 比较正常和肿瘤细胞比例柱形图

epi_clusters <- FetchData(epi, vars = c("SCT_snn_res.1", "tissue_type"))

count_tumor <- epi_clusters %>% filter(tissue_type == "Tumor") %>% count() %>% as.numeric()
count_normal <- epi_clusters %>% filter(tissue_type == "Normal") %>% count() %>% as.numeric()
### 每个cluster计数总数
epi_counts <- epi_clusters %>% group_by(tissue_type) %>% count(SCT_snn_res.1)
### 除以总count数，是朴素的比例计算
proportion_tumor <- epi_counts %>% filter(tissue_type == "Tumor") %>% mutate(proportion = n/count_tumor)
proportion_normal <- epi_counts %>% filter(tissue_type == "Normal") %>% mutate(proportion = n/count_normal)

### 组合，最后一行很有意思，认为哪群比例高就属于哪群
proportion_epi <- full_join(proportion_normal, proportion_tumor, by = "SCT_snn_res.1") %>% 
  mutate(proportion.x = ifelse(is.na(proportion.x), 0,  proportion.x)) %>%  
  mutate(proportion.y = ifelse(is.na(proportion.y), 0,  proportion.y)) %>%
  mutate(tissue_type.x = "Normal") %C>%
  mutate(tissue_type.y = "Tumor") %>%
  mutate(cluster_type = ifelse(proportion.x > proportion.y, "Normal", "Tumor"))

### 每个细胞的详细归属cluster
cluster_type_data <- left_join(x = epi_clusters, y = proportion_epi, by = "SCT_snn_res.1")
rownames(cluster_type_data) <- rownames(epi_clusters)

### 加入metadata中
epi <- AddMetaData(epi, select(cluster_type_data, cluster_type))


### 柱状图

n1 <- select(proportion_epi, c(tissue_type.x, SCT_snn_res.1, proportion.x)) %>%
  mutate(tissue_type = tissue_type.x) %>% 
  mutate(proportion = proportion.x) %>%
  mutate(tissue_type.x = NULL) %>%
  mutate(proportion.x = NULL)
t1 <- select(proportion_epi, c(tissue_type.y, SCT_snn_res.1, proportion.y)) %>%
  mutate(tissue_type = tissue_type.y) %>% 
  mutate(proportion = proportion.y) %>%
  mutate(tissue_type.y = NULL) %>%
  mutate(proportion.y = NULL)

proportion_epi2 <- rbind(n1, t1)

ggplot(proportion_epi2, aes(fill = tissue_type, y = proportion, x = SCT_snn_res.1)) +
  geom_bar(stat = "identity", position = "dodge", width = 0.7) +
  scale_fill_manual(values = use_colors)

ggsave2("正常-肿瘤上皮比较.png", path = "result_3v3/", width = 40, height = 20, units = "cm")

### 根据下面的图，我们可以明确地识别出肿瘤上皮中哪些类群是新增加的
### 也就是异型增生的上皮细胞——癌细胞
DimPlot(epi, group.by = "cluster_type",split.by = 'tissue_type' ,cols = use_colors, pt.size = 0.1)
ggsave2("新增的肿瘤细胞.png", path = "result_3v3/", width = 8, height = 4)
# 详细细胞类型注释
# 先画一些landscape，了解一下再聚类的质量
### 我们需要了解到的是，不同细胞注释当然是要结合起来看的
### 另外，我们对免疫细胞加入一些阴性marker也很重要
### 髓系
myeloid_markers <- c("S100A12", "FCN1", "S100A8", "S100A9", "CD14", "CTSS","VCAN", "LYZ", 
                     "MARCO", "FCGR1A", "C1QA", "APOC1", "LGMN", "CTSB", "FCGR3A", 
                     "MAFB", "MAF", "CX3CR1", "ITGAM", "CSF1R",
                     "FABP4", "MCEMP1", 
                     "IL1B", "CXCL8", 
                     "APOE", "CD163", "C1QB", "C1QC", 
                     "FCER1A", "CD1C", "CLEC9A", 
                     "LILRA4", "CLEC4C", "JCHAIN", "IL3RA", "NRP1", 
                     "CLEC10A", "PTCRA", "CCR7", "LAMP3", 
                     "ITGAX", "CD68", "MKI67", "CDK1", "EPCAM")

### T细胞
tcell_nk_markers <- c("CD3E", "CD4", "FOXP3", "IL7R", "IL2RA", "CD40LG", 
                      "CD8A", "CCL5", "NCR1", "NKG7", "GNLY", "NCAM1", 
                      "KLRD1", "KLRB1", "CD69", "KLRG1", 
                      "MKI67", "CDK1", "EPCAM")


### B细胞
bcell_plasma_mast_markers <- c("MS4A1", "CD19", "CD79A", "JCHAIN", "IGHA1", 
                               "IGHA2", "IGHG1", "IGHG2", "IGHG3", "IGHG4", 
                               "IGKC", "IGLC2", "IGLC3", "CPA3", "KIT", "MS4A2", 
                               "GATA2",  "MKI67", "CDK1", "EPCAM")

### Dotplot是最关键的手动注释的工具
DotPlot(imm, features = myeloid_markers, group.by = "SCT_snn_res.0.4") + coord_flip()
ggsave2("髓系标记热图.png", path = "./result_3v3/", width = 20, height = 30, units = "cm")

DotPlot(imm, features = tcell_nk_markers, group.by = "SCT_snn_res.0.4") + coord_flip()
ggsave2("DotPlot_T_NK_markers.png", path = "./result_3v3/", width = 20, height = 20, units = "cm")

DotPlot(imm, features = bcell_plasma_mast_markers, group.by = "SCT_snn_res.0.4") + coord_flip()
ggsave2("DotPlot_B_Plasma_markers.png", path = "./result_3v3/", width = 20, height = 20, units = "cm")

DimPlot(imm, group.by = "SCT_snn_res.0.4", label = T, split.by = "tissue_type")
ggsave2("正常肿瘤病人免疫细胞分群.png", path = "./result_3v3/", width =10, height =5)

DimPlot(imm, group.by = "patient_id", cols = use_colors)
ggsave2("病人免疫细胞分群.png", path = "./result_3v3/", width = 10, height = 5)
```

#### Chapter 5

```r
library(Seurat)
library(dplyr)
library(reticulate)
library(sctransform)
library(cowplot)
library(ggplot2)
library(viridis)
library(tidyr)
library(magrittr)
library(reshape2)
library(readxl)
library(readr)
library(stringr)
library(progeny)
library(scales)

theme_set(theme_cowplot())

use_colors <- c(
  Tumor = "brown2",
  Normal = "deepskyblue2",
  G1 = "#46ACC8",
  G2M = "#E58601",
  S = "#B40F20",
  Epithelial = "seagreen",
  Immune = "darkgoldenrod2",
  Stromal = "steelblue",
  p018 = "#E2D200",
  p019 = "#46ACC8",
  p023 = "#E58601",
  p024 = "#B40F20",
  p027 = "#0B775E",
  p028 = "#E1BD6D",
  p029 = "#35274A",
  p030 = "#F2300F",
  p031 = "#7294D4",
  p032 = "#5B1A18",
  p033 = "#9C964A",
  p034 = "#FD6467")


#加载数据，由于上次结果我们没有保存，所以还是需要重新读取一次

epi <- readRDS("epi.RDS")
imm <- readRDS("imm.RDS")
str <- readRDS("str.RDS")

# 重新降维聚类

### 上皮重聚类

epi <- RunPCA(epi)
ElbowPlot(epi,  ndims = 50)


epi <- RunUMAP(epi, dims = 1:20)
epi <- FindNeighbors(epi, dims = 1:20)
for (i in c(0.2, 0.3, 0.4, 0.5, 1, 2)) {
  epi <- FindClusters(epi, resolution = i)
  print(DimPlot(epi, reduction = "umap", label = T) + labs(title = paste0("resolution: ", i)))
}

### 我们依然选择比较大的resolution，手动注释没有关系
### 控制在20个cluster左右即可
Idents(epi) <- epi@meta.data$SCT_snn_res.1


### 免疫重聚类

imm <- RunPCA(imm)
ElbowPlot(imm,  ndims = 50)


imm <- RunUMAP(imm, dims = 1:20)
imm <- FindNeighbors(imm, dims = 1:20)
for (i in c(0.01,0.2, 0.3, 0.4, 0.5, 1)) {
  imm <- FindClusters(imm, resolution = i)
  print(DimPlot(imm, reduction = "umap") + labs(title = paste0("resolution: ", i)))
}
### 控制在20个cluster左右即可
Idents(imm) <- imm@meta.data$SCT_snn_res.0.4


### 基质重聚类

str <- RunPCA(str)
ElbowPlot(str, ndims = 50)


str <- RunUMAP(str, dims = 1:20)
str <- FindNeighbors(str, dims = 1:20)
for (i in c(0.2, 0.3, 0.4, 0.5, 1, 2)) {
  str <- FindClusters(str, resolution = i)
  print(DimPlot(str, reduction = "umap") + labs(title = paste0("resolution: ", i)))
}
#细胞不多，可以少点
Idents(str) <- str@meta.data$SCT_snn_res.2

# 细胞类型注释的marker寻找
### 我们在之前的推文已经说过，一定要记得去找原文！
### 根据文章，使用Habermann et al.参考基因，
#https://www.biorxiv.org/content/10.1101/753806v1

habermann_epi <- c("ABCA3", "SFTPB", "SFTPC", "AGER", "PDPN",  "KRT5", "TRP63", "NGFR", "SCGB1A1", "MUC5B", "KRT17", "FOXJ1", "TMEM190", "CAPS", "CHGA", "CALCA", "ASCL1", "PTPRC", "EPCAM")

habermann_imm <- c("CD3E", "CD4", "FOXP3", "IL7R", "IL2RA", "CD40LG", "CD8A", "CCL5", "NCR1", "KLRB1", "NKG7", "LYZ", "CD68", "ITGAX", "MARCO", "FCGR1A", "FCGR3A", "C1QA", "APOC1", "S100A12", "FCN1", "S100A9", "CD14", "FCER1A", "CD1C", "CD16", "CLEC9A", "LILRA4", "CLEC4C", "JCHAIN", "IGHG1", "IGLL5", "MS4A1", "CD19", "CD79A", "CPA3", "KIT", "MKI67", "CDK1", "EPCAM")

habermann_oth <- c("VWF", "PECAM1", "CCL21", "PROX1", "ACTA2", "MYH11", "PDGFRB", "WT1", "UPK3B", "LUM", "PDGFRA", "MYLK", "HAS1", "PLIN2", "FAP", "PTPRC", "EPCAM")

### 免疫marker基因定细胞类型（Habermann et al，配合参考文献中Tata等人）
### "SCT_snn_res.0.5"到底选多少呢？
### 这需要根据后面已知亚群是否被混淆
### 例如Treg Foxp3在0.5时才被清晰地显现出来，0.4却不行(包含在大群的细胞里面了)
### 因此我需要放弃0.4，选择0.5
### 你可以自己featureplot去体会一下0.4和0.5带来的Treg的微妙差异
DotPlot(imm, features = habermann_imm, group.by = "SCT_snn_res.0.5") + coord_flip()
ggsave2(filename = '免疫marker基因的dotplot为了注释.png',path = "./result_3v3/", width =10, height =15)
DimPlot(imm,group.by =  "SCT_snn_res.0.5",label = T,split.by = 'tissue_type')
ggsave2(filename = '正常肿瘤病人免疫细胞分群为了注释.png',path = "./result_3v3/", width =14, height =7)

### FOXP3帮助你确定一下resolution
#FeaturePlot(imm, features =c('FOXP3'),split.by = 'tissue_type')
# 详细细胞类型注释（正式）###########################################
### 先上免疫细胞,思路就是marker基因再加肿瘤正常对比
###0，4，12特征明显，普通T细胞
###1，3，10，13，14，20 正常里多，肿瘤里少 结合CD68 所以是正常肺泡巨噬（需要思维）
###2 明显NK
###5，7 普通T，分两群是因为res太高，看图便知，其实是一群
###6，18 CD8T，肿瘤飙升
###8，19 肿瘤升，CD14阳，Monocyte-derived macrophages
###9 明显Myeloid dendritic cells
###11 结合marker明显Monocytes，正常多，因为肿瘤里会刺激分化
###15 明显B
###16 明显Treg
###17 浆细胞
###21 明显Plasmacytoid dendritic cells
### 载入注释
annotation_curated_imm <- read_excel("./annotation/annotation_imm.xlsx")
imm_anno <- imm
new_ids_imm <- annotation_curated_imm$cell_type_imm
Idents(imm_anno) <- imm_anno@meta.data$SCT_snn_res.0.5
names(new_ids_imm) <- levels(imm_anno)
imm_anno <- RenameIdents(imm_anno, new_ids_imm)
imm_anno@meta.data$cell_type_imm <- Idents(imm_anno)
imm_anno <- subset(imm_anno, subset = cell_type_imm != "Epithelial_contamination")
imm_anno <- ScaleData(imm_anno)
saveRDS(imm_anno, file="imm_anno.rds")
```

#### Chapter 6

```r
### 加载R包
library(Seurat)
library(dplyr)
library(reticulate)
library(sctransform)
library(cowplot)
library(ggplot2)
library(viridis)
library(tidyr)
library(magrittr)
library(reshape2)
library(readxl)
library(stringr)
library(cowplot)
library(scales)
library(readr)
library(progeny)
library(gplots)
library(tibble)
library(grid)
library(rlang)

theme_set(theme_cowplot())

### 设计配色，细胞类型注释已经好了，所以根据的是上辑第五篇的注释结果
use_colors <- c(
  Tumor = "brown2",
  Normal = "deepskyblue2",
  G1 = "#46ACC8",
  G2M = "#E58601",
  S = "#B40F20",
  Epithelial = "seagreen",
  Immune = "darkgoldenrod2",
  Stromal = "steelblue",
  p032 = "#5B1A18",
  p033 = "#9C964A",
  p034 = "#FD6467",
  Alveolar_Macrophages = "#6bAEd6",
  `Monocyte-derived macrophages`= "#fff500",
  Monocytes= "#FA9FB5",
  `Myeloid dendritic cells`= "#DD3497",
  Plasmacytoid_dendritic_cells= "#7A0177",
  `Conventional_T_cells`= "#c2e699",
  `Regulatory T cells`= "#006837",
  `CD8+_T_cells`= "#bcbddc",
  NK_cells= "#4a1486",
  B_cells= "#969696",
  Plasma_cells= "#636363")


#载入数据
saveRDS(imm_anno,file='imm_anno.RDS')
imm_anno <- readRDS("imm_anno.RDS")

imm_anno@meta.data$cell_type_imm <- ordered(imm_anno@meta.data$cell_type_imm, levels = c("Alveolar_Macrophages","Monocyte-derived macrophages", "Monocytes", "Myeloid dendritic cells","Plasmacytoid_dendritic_cells","Conventional_T_cells","Regulatory T cells","CD8+_T_cells","NK_cells",
                                                                                         "B_cells","Plasma_cells"))


### 画一些图帮我们回忆一下这个数据集,例如我们看到肿瘤正常确实差的大
DimPlot(imm_anno, group.by = "tissue_type", cols = use_colors)
dir.create('./result_3v3/analysis')
ggsave2("DimPlot_imm_Normal_Tumor.pdf", path = "./result_3v3/analysis", width = 8, height = 7)


DimPlot(imm_anno, group.by = "cell_type_imm", split.by = "tissue_type", cols = use_colors, pt.size = 0.5)
ggsave2("DimPlot_umap.pdf", path = "./result_3v3/analysis", width = 10, height = 5)

DotPlot(imm_anno, features = c("CD68", "LYZ", "FABP4", "MARCO", "LGMN", "CSF1R", "CD14", "S100A12", "FCN1", "CD1C", "FCER1A", "LILRA4", "IL3RA", "KIT", "GATA2", "CD3E", "CD4", "FOXP3", "IL2RA", "CD8A", "NKG7", "KLRD1", "MS4A1", "CD79A", "JCHAIN", "IGKC", "MKI67"), group.by = "cell_type_imm") + 
  theme(axis.text.x = element_text(angle = 45, hjust = 1)) +
  coord_flip() + 
  scale_color_viridis()
ggsave2("注释后dotplot_可以展示.pdf", path = "./result_3v3/analysis", width = 8, height = 7)
### 画一下好看的最终UMAP图
DimPlot(imm_anno, group.by = "cell_type_imm", split.by = "tissue_type", cols = use_colors, pt.size = 0.5)
ggsave2("DimPlot_umap.pdf", path = "./result_3v3/analysis", width = 10, height = 5)
### dotplot，已经有细胞了，所以这步是帮助我们确认一下结果
DotPlot(imm_anno, features = c("CD68", "LYZ", "FABP4", "MARCO", "LGMN", "CSF1R", "CD14", "S100A12", "FCN1", "CD1C", "FCER1A", "LILRA4", "IL3RA", "KIT", "GATA2", "CD3E", "CD4", "FOXP3", "IL2RA", "CD8A", "NKG7", "KLRD1", "MS4A1", "CD79A", "JCHAIN", "IGKC", "MKI67"), group.by = "cell_type_imm") + 
  theme(axis.text.x = element_text(angle = 45, hjust = 1)) +
  coord_flip() + 
  scale_color_viridis()
### 发现非常漂亮！
ggsave2("注释后dotplot_可以展示.pdf", path = "./result_3v3/analysis", width = 8, height = 7)
```

#### Chapter 7

```r
### 加载R包
library(Seurat)
library(dplyr)
library(reticulate)
library(sctransform)
library(cowplot)
library(ggplot2)
library(viridis)
library(tidyr)
library(magrittr)
library(reshape2)
library(readxl)
library(stringr)
library(cowplot)
library(scales)
library(readr)
library(progeny)
library(gplots)
library(tibble)
library(grid)
library(rlang)

theme_set(theme_cowplot())

### 设计配色，细胞类型注释已经好了，所以根据的是上辑第五篇的注释结果
use_colors <- c(
  Tumor = "brown2",
  Normal = "deepskyblue2",
  G1 = "#46ACC8",
  G2M = "#E58601",
  S = "#B40F20",
  Epithelial = "seagreen",
  Immune = "darkgoldenrod2",
  Stromal = "steelblue",
  p032 = "#5B1A18",
  p033 = "#9C964A",
  p034 = "#FD6467",
  Alveolar_Macrophages = "#6bAEd6",
  `Monocyte-derived macrophages`= "#fff500",
  Monocytes= "#FA9FB5",
  `Myeloid dendritic cells`= "#DD3497",
  Plasmacytoid_dendritic_cells= "#7A0177",
  `Conventional_T_cells`= "#c2e699",
  `Regulatory T cells`= "#006837",
  `CD8+_T_cells`= "#bcbddc",
  NK_cells= "#4a1486",
  B_cells= "#969696",
  Plasma_cells= "#636363")


#载入数据
### 数据在阅读原文里有，当然更建议你自行从第一步制作下来
#saveRDS(imm_anno,file='imm_anno.RDS')
imm_anno <- readRDS("imm_anno.RDS")

imm_anno@meta.data$cell_type_imm <- ordered(imm_anno@meta.data$cell_type_imm, levels = c("Alveolar_Macrophages","Monocyte-derived macrophages","Monocytes", "Myeloid dendritic cells","Plasmacytoid_dendritic_cells","Conventional_T_cells","Regulatory T cells","CD8+_T_cells", "NK_cells", "B_cells","Plasma_cells"))


# 分群分析############
# 免疫细胞分淋巴系和髓系

###淋巴细胞亚群
imm_lympho <- subset(imm_anno, subset = cell_type_imm %in% c("Conventional_T_cells",
                                                             "CD8+_T_cells",
                                                             "Regulatory T cells",
                                                             "NK_cells",
                                                             "B_cells",
                                                             "Plasma_cells"))
### 记住重新scale！
imm_lympho <- ScaleData(imm_lympho)


### 髓系亚群
imm_myelo <- subset(imm_anno, subset = cell_type_imm %in% c("Alveolar_Macrophages",
                                                            "Monocyte-derived macrophages",
                                                            "Monocytes",
                                                            "Myeloid dendritic cells",
                                                            "Plasmacytoid_dendritic_cells"))
### 记住重新scale
imm_myelo <- ScaleData(imm_myelo)

### FetchData快速获得附加信息
### 淋巴系
lympho_counts <- FetchData(imm_lympho, vars = c("tissue_type", "cell_type_imm", "sample_id", "patient_id")) %>%  
  mutate(tissue_type = factor(tissue_type, levels = c("Tumor", "Normal")))


# 细胞比例图
### count根据patient_id，和tissue_type来统计总数，相当于excel分类汇总了
lympho_counts_tbl <- lympho_counts %>%
  dplyr::count(cell_type_imm, patient_id, tissue_type)
write_csv(lympho_counts_tbl, path = "./result_3v3/analysis/lympho_counts_tbl.csv")


### 髓系也一样
myelo_counts <- FetchData(imm_myelo, vars = c("tissue_type", "cell_type_imm", "sample_id", "patient_id")) %>%  
  mutate(tissue_type = factor(tissue_type, levels = c("Tumor", "Normal"))) 

myelo_counts_tbl <- myelo_counts %>%
  dplyr::count(cell_type_imm, patient_id, tissue_type)
write_csv(myelo_counts_tbl, path = "./result_3v3/analysis/lmyelo_counts_tbl.csv")

ggplot(data = lympho_counts, aes(x = tissue_type, fill = cell_type_imm)) +
  geom_bar(position = "fill") +
  scale_fill_manual(values = use_colors) +
  coord_flip() +
  scale_y_reverse()
ggsave2("barplot_lymphoid.pdf", path = "./result_3v3/analysis/", width = 8, height = 2)

ggplot(data = myelo_counts, aes(x = tissue_type, fill = cell_type_imm)) +
  geom_bar(position = "fill") +
  scale_fill_manual(values = use_colors) +
  coord_flip() +
  scale_y_reverse()
ggsave2("barplot_myeloid.pdf", path = "./result_3v3/analysis/", width = 8, height = 2)

#病人的异质性可见一斑，这是只有细胞比例图才可以揭示的现象！
lympho_counts %>%
  filter(tissue_type == "Tumor") %>%
  ggplot(aes(x = sample_id, fill = cell_type_imm)) +
  geom_bar(position = "fill") +
  scale_fill_manual(values = use_colors) +
  coord_flip() +
  scale_y_reverse()
ggsave2("lymphoid_per_patient.pdf", path = "./result_3v3/analysis/", width = 8, height = 2.5)

myelo_counts %>%
  filter(tissue_type == "Tumor") %>%
  ggplot(aes(x = sample_id, fill = cell_type_imm)) +
  geom_bar(position = "fill") +
  scale_fill_manual(values = use_colors) +
  coord_flip() +
  scale_y_reverse()
ggsave2("barplot_myeloid_per_patient.pdf", path = "./result_3v3/analysis/", width = 8, height = 2.5)

lympho_counts %>%
  filter(tissue_type == "Normal") %>%
  ggplot(aes(x = sample_id, fill = cell_type_imm)) +
  geom_bar(position = "fill") +
  scale_fill_manual(values = use_colors) +
  coord_flip() +
  scale_y_reverse()
ggsave2("normal_lymphoid.pdf", path = "./result_3v3/analysis/", width = 8, height =2.5)

myelo_counts %>%
  filter(tissue_type == "Normal") %>%
  ggplot(aes(x = sample_id, fill = cell_type_imm)) +
  geom_bar(position = "fill") +
  scale_fill_manual(values = use_colors) +
  coord_flip() +
  scale_y_reverse()
ggsave2("normal_myeloid.pdf", path = "./result_3v3/analysis/", width =8, height =2.5)
```

#### Chapter 8

```r
### 加载R包
library(Seurat)
library(dplyr)
library(reticulate)
library(sctransform)
library(cowplot)
library(ggplot2)
library(viridis)
library(tidyr)
library(magrittr)
library(reshape2)
library(readxl)
library(stringr)
library(cowplot)
library(scales)
library(readr)
library(progeny)
library(gplots)
library(tibble)
library(grid)
library(rlang)

theme_set(theme_cowplot())

### 设计配色，细胞类型注释已经好了，所以根据的是上辑第五篇的注释结果
use_colors <- c(
  Tumor = "brown2",
  Normal = "deepskyblue2",
  G1 = "#46ACC8",
  G2M = "#E58601",
  S = "#B40F20",
  Epithelial = "seagreen",
  Immune = "darkgoldenrod2",
  Stromal = "steelblue",
  p032 = "#5B1A18",
  p033 = "#9C964A",
  p034 = "#FD6467",
  Alveolar_Macrophages = "#6bAEd6",
  `Monocyte-derived macrophages`= "#fff500",
  Monocytes= "#FA9FB5",
  `Myeloid dendritic cells`= "#DD3497",
  Plasmacytoid_dendritic_cells= "#7A0177",
  `Conventional_T_cells`= "#c2e699",
  `Regulatory T cells`= "#006837",
  `CD8+_T_cells`= "#bcbddc",
  NK_cells= "#4a1486",
  B_cells= "#969696",
  Plasma_cells= "#636363")

# 差异基因
### 巨噬细胞差异基因

imm_macro <- subset(imm_anno, subset = cell_type_imm %in% c("Alveolar_Macrophages",
                                                            "Monocyte-derived macrophages"))

### 重scale
imm_macro <- ScaleData(imm_macro)

Idents(imm_macro) <- imm_macro@meta.data$cell_type_imm

macro_markers <- FindAllMarkers(imm_macro, only.pos = T, min.pct = 0.25, min.diff.pct = 0.25)

# group_by和top_n组合
top_macro_markers <- macro_markers %>% group_by(cluster) %>% top_n(10, wt = avg_log2FC)

#devtools::install_github("elliefewings/DoMultiBarHeatmap") 

#DoMultiBarHeatmap提供多层注释，但这个包安装较难，必须版本均不报warning错，量力而装
DoMultiBarHeatmap::DoMultiBarHeatmap(imm_macro, features = top_macro_markers$gene, group.by = "cell_type_imm", 
                                     additional.group.by = 'tissue_type',draw.lines = F) +scale_fill_viridis()

ggsave2("macro_marker_gene.pdf", path = "./result_3v3/", width = 10, height = 9)

### T细胞差异基因

#T细胞亚群
imm_T <- subset(imm_anno, subset = cell_type_imm %in% c("Conventional_T_cells",
                                                        "CD8+_T_cells",
                                                        "Regulatory T cells"))

imm_T <- ScaleData(imm_T)

Idents(imm_T) <- imm_T@meta.data$cell_type_imm

markers_T <- FindAllMarkers(imm_T, only.pos = T, min.pct = 0.25, min.diff.pct = 0.25)

top_markers_T <- markers_T %>% group_by(cluster) %>% top_n(10, wt = avg_log2FC)

DoMultiBarHeatmap(imm_T, features = top_markers_T$gene, group.by = "cell_type_imm", additional.group.by = "tissue_type", draw.lines = F) +
  scale_fill_viridis()


ggsave2("T_cell_marker_gene.pdf", path = "./result_3v3/", width = 10, height = 9)
```

#### Chapter 9

```r
library(Seurat)
library(dplyr)
library(reticulate)
library(sctransform)
library(cowplot)
library(ggplot2)
library(viridis)
library(tidyr)
library(magrittr)
library(reshape2)
library(readxl)
library(stringr)
library(cowplot)
library(scales)
library(readr)
library(progeny)
library(gplots)
library(tibble)
library(grid)
library(rlang)

theme_set(theme_cowplot())

use_colors <- c(
  Tumor = "brown2",
  Normal = "deepskyblue2",
  G1 = "#46ACC8",
  G2M = "#E58601",
  S = "#B40F20",
  Epithelial = "seagreen",
  Immune = "darkgoldenrod2",
  Stromal = "steelblue",
  p032 = "#5B1A18",
  p033 = "#9C964A",
  p034 = "#FD6467",
  Alveolar_Macrophages = "#6bAEd6",
  `Monocyte-derived macrophages`= "#fff500",
  Monocytes= "#FA9FB5",
  `Myeloid dendritic cells`= "#DD3497",
  Plasmacytoid_dendritic_cells= "#7A0177",
  `Conventional_T_cells`= "#c2e699",
  `Regulatory T cells`= "#006837",
  `CD8+_T_cells`= "#bcbddc",
  NK_cells= "#4a1486",
  B_cells= "#969696",
  Plasma_cells= "#636363")



imm_anno <- readRDS("imm_anno.RDS")

imm_anno@meta.data$cell_type_imm <- ordered(imm_anno@meta.data$cell_type_imm, levels = c("Alveolar_Macrophages","Monocyte-derived macrophages","Monocytes","Myeloid dendritic cells","Plasmacytoid_dendritic_cells","Conventional_T_cells","Regulatory T cells","CD8+_T_cells","NK_cells",
                                                                                         "B_cells", "Plasma_cells"))


# 分析点之一：对巨噬细胞分析M1,M2表型通路
### genelist文件在data文件夹中
###导入M1
m1m2_pws <- read_lines("./data/CLASSICAL_M1_VS_ALTERNATIVE_M2_MACROPHAGE_UP.gmt") %>%
  lapply(str_split, "\\t") %>% 
  unlist(recursive = F) %>% 
  lapply(function(x) setNames(list(x[-c(1:2)]), x[1])) %>% 
  unlist(recursive = F)

### 导入M2
m1m2_pws <- append(m1m2_pws, read_lines("./data/CLASSICAL_M1_VS_ALTERNATIVE_M2_MACROPHAGE_DN.gmt") %>%
                     lapply(str_split, "\\t") %>% 
                     unlist(recursive = F) %>% 
                     lapply(function(x) setNames(list(x[-c(1:2)]), x[1])) %>% 
                     unlist(recursive = F))

### 运用addmodulescore函数，加入富集分数到metadata中
imm_anno <- AddModuleScore(object = imm_anno, features = m1m2_pws, name = c("m1up", "m1dn"), nbin = 12)


### 比较通路，当然这个没有显著性
VlnPlot(imm_anno, features = c("m1up1", "m1dn2"), 
        group.by = "cell_type_imm", pt.size = 0, idents = c("Alveolar_Macrophages",
                                                            "Monocyte-derived macrophages"), cols = use_colors)
ggsave2(filename = 'M1M2.pdf',path = './result_3v3/analysis/',width = 8,height = 7)


### 从下图我们看到单核来源的巨噬是M1表型的（略高），这个也提示我们不是所有的肿瘤相关巨噬都是M2表型的！

### 如果你跑了别的通路，如HALLMARK通路
#VlnPlot(imm_anno, features = c("HALLMARK_INFLAMMATORY_RESPONSE31",
#                               "HALLMARK_ALLOGRAFT_REJECTION46",
#                               "HALLMARK_INTERFERON_GAMMA_RESPONSE19",
#                               "HALLMARK_TNFA_SIGNALING_VIA_NFKB1",
#                               "m1up1", 
#                               "m1dn2"), 
#        group.by = "cell_type_imm", pt.size = 0, ncol = 3, idents = c("Alveolar_Macrophages",

# 想设置显著性怎么办呢？
### 设置比较组
### 
comparisons <- list(c("Alveolar_Macrophages",
                      "Monocyte-derived macrophages"))
library(ggpubr)

### 难点在于我们的得分在metadata而非表达矩阵中，所以需要把它添加到表达矩阵中，再利用Vlnpubr包装函数
### 运行包装函数
Vlnpubr <- function(seo, gene_signature, file_name, test_sign,group_my,label  = "p.signif"){
  plot_case1 <- function(signature, y_max = NULL){
    VlnPlot(seo , features = signature,
            pt.size = 0.1, 
            group.by =  group_my, 
            y.max = y_max # add the y-axis maximum value - otherwise p-value hidden
    ) + stat_compare_means(comparisons = test_sign, label = label ) + NoLegend()
  }
  plot_list <- list()
  y_max_list <- list()
  for (gene in gene_signature) {
    plot_list[[gene]] <- plot_case1(gene)
    y_max_list[[gene]] <- max(plot_list[[gene]]$data[[gene]]) # get the max no. for each gene
    plot_list[[gene]] <- plot_case1(gene, y_max = (y_max_list[[gene]] + 1) )
  }
  cowplot::plot_grid(plotlist = plot_list)
  #file_name <- paste0(file_name, "_r.png")
  #ggsave(file_name, width = 14, height = 8)
}

### 我们的思路是新建一个scRNA，避免被破坏
scRNA=imm_anno
scRNA@assays$SCT@scale.data=rbind(scRNA@assays$SCT@scale.data,(scRNA$m1up1))
scRNA@assays$SCT@scale.data=rbind(scRNA@assays$SCT@scale.data,(scRNA$m1dn2))

scRNA_macro <- subset(scRNA, subset = cell_type_imm %in% c("Alveolar_Macrophages",
                                                           "Monocyte-derived macrophages"))
scRNA_macro=ScaleData(scRNA_macro)

### 出图
Vlnpubr(scRNA_macro,gene_signature = c('m1dn2','m1up1'), test_sign = comparisons,group_my = 'cell_type_imm')
ggsave2(filename = 'signa_M1M2.pdf',path = './result_3v3/analysis/',width = 8,height = 7)

### 我们终于加上了显著性

#分析点2：T耗竭和杀伤力分析

### 读取耗竭Marker
T_exhausted <- read_excel("./data/CD8_T_cells_exhausted.xlsx", skip = 1)
### 毒性marker
cytotoxicity <- c("PRF1", "IFNG", "GNLY", "NKG7", "GZMB", "GZMA", "GZMH", "KLRK1", "KLRB1", "KLRD1", "CTSW", "CST7")

T_cell_markers <- list(T_exhausted$GeneSymbol, cytotoxicity)

imm_T <- AddModuleScore(imm_T, features = T_cell_markers, name = c("exhaustion", "cytotoxicity"), nbin = 12)

VlnPlot(imm_T, features = c("cytotoxicity2", "exhaustion1"), pt.size = 0, group.by = "cell_type_imm", cols = use_colors, idents = c("Conventional_T_cells",
                                                                                                                                    "CD8+_T_cells",
                                                                                                                                    "Regulatory T cells"), 
        ncol = 1)

ggsave2("T_cell_exhaustion_cytotxicity.pdf", path = "./result_3v3/analysis/", width = 4, height = 6)
```

#### Chapter 10

```r
{
  library(Seurat)
  library(dplyr)
  library(reticulate)
  library(sctransform)
  library(cowplot)
  library(ggplot2)
  library(viridis)
  library(tidyr)
  library(magrittr)
  library(reshape2)
  library(readxl)
  library(stringr)
  library(cowplot)
  library(scales)
  library(readr)
  library(progeny)
  library(gplots)
  library(tibble)
  library(grid)
  library(rlang)
  
  theme_set(theme_cowplot())
  
  use_colors <- c(
    
    Alveolar_Macrophages = "#6bAEd6",
    `Monocyte-derived macrophages`= "#fff500",
    Monocytes= "#FA9FB5",
    `Myeloid dendritic cells`= "#DD3497",
    Plasmacytoid_dendritic_cells= "#7A0177",
    `Conventional_T_cells`= "#c2e699",
    `Regulatory T cells`= "#006837",
    `CD8+_T_cells`= "#bcbddc",
    NK_cells= "#4a1486",
    B_cells= "#969696",
    Plasma_cells= "#636363")
  
}

imm_anno <- readRDS("imm_anno.RDS")

imm_anno@meta.data$cell_type_imm <-
  ordered(
    imm_anno@meta.data$cell_type_imm, levels = c("Alveolar_Macrophages",  "Monocyte-derived macrophages",
                                                 "Monocytes","Myeloid dendritic cells",
                                                 "Plasmacytoid_dendritic_cells","Conventional_T_cells",
                                                 "Regulatory T cells","CD8+_T_cells",
                                                 "NK_cells", "B_cells",
                                                 "Plasma_cells"))




### 细化分析的开端是发现（感觉）某群细胞有多个亚群，如下面的图发现单核来源巨噬有多群
DimPlot(imm_anno,reduction = 'umap',split.by = 'tissue_type',label = T,cols = use_colors)

# 对巨噬细胞分析M1,M2表型通路
### 第九节中也有类似的操作，但是第九节是肺泡巨噬和单核巨噬的比较，本节是单核巨噬细胞内部的比较
### 实际上同一群细胞内部小亚群比较更有意思
###导入M1
m1m2_pws <- read_lines("./data/CLASSICAL_M1_VS_ALTERNATIVE_M2_MACROPHAGE_UP.gmt") %>%
  lapply(str_split, "\\t") %>% 
  unlist(recursive = F) %>% 
  lapply(function(x) setNames(list(x[-c(1:2)]), x[1])) %>% 
  unlist(recursive = F)

### 导入M2
m1m2_pws <- append(m1m2_pws, read_lines("./data/CLASSICAL_M1_VS_ALTERNATIVE_M2_MACROPHAGE_DN.gmt") %>%
                     lapply(str_split, "\\t") %>% 
                     unlist(recursive = F) %>% 
                     lapply(function(x) setNames(list(x[-c(1:2)]), x[1])) %>% 
                     unlist(recursive = F))

### 运用addmodulescore函数，加入富集分数到metadata中
imm_anno <- AddModuleScore(object = imm_anno, features = m1m2_pws, name = c("m1up", "m1dn"), nbin = 12)




### 提出单核来源巨噬来分析macrophage（mdm）
scRNA_mdm <- subset(imm_anno, subset = cell_type_imm %in% c("Monocyte-derived macrophages"))
scRNA_mdm=ScaleData(scRNA_mdm)

### 重新降维聚类
scRNA_mdm <- RunPCA(scRNA_mdm)
ElbowPlot(scRNA_mdm,  ndims = 50)

library(ggplot2)
scRNA_mdm<- RunUMAP(scRNA_mdm, dims = 1:20)
scRNA_mdm <- FindNeighbors(scRNA_mdm, dims = 1:20)
for (i in c(0.01,0.2, 0.3, 0.4, 0.5, 1)) {
  scRNA_mdm <- FindClusters(scRNA_mdm, resolution = i)
  print(DimPlot(scRNA_mdm, reduction = "umap") + labs(title = paste0("resolution: ", i)))
}
### cluster数自己决定，不要太多即可,否则相似的群多
table(scRNA_mdm$SCT_snn_res.0.2)
Idents(scRNA_mdm) <-scRNA_mdm@meta.data$SCT_snn_res.0.2

DimPlot(scRNA_mdm,reduction = 'umap',group.by = 'SCT_snn_res.0.2',label = T,split.by = 'tissue_type')
ggsave2(filename = 'Macrophage亚群.pdf',path = './result_3v3/analysis',width = 8,height = 4)

### 不同mdm小亚群比较通路
VlnPlot(scRNA_mdm, features = c("m1up1", "m1dn2"), 
        group.by = "SCT_snn_res.0.2", pt.size = 0, 
        cols = use_colors)
### 第四群有一个显著的M1活化
ggsave2(filename = 'M1M2巨噬细胞细分.pdf',path = './result_3v3/analysis/',width = 8,height =4)

#### 第4群CCL5+，其实还有CD8A+，大家认为，这是一群新的巨噬，还是由于细胞污染呢~
FeaturePlot(scRNA_mdm,features = 'CCL5',cols = viridis(10),label = T)
ggsave2(filename = 'CCL5_mdm.pdf',path = './result_3v3/analysis/',width = 5,height =4.5)

### 据此我们可以继续定义细胞亚群，例如cluster1为C1Q+的巨噬细胞！

### 单核细胞来源的巨噬细胞间具有较大异质性，所以考虑再分别注释

mdm_markers <- FindAllMarkers(scRNA_mdm, only.pos = T, min.pct = 0.25, min.diff.pct = 0.25)

# group_by和top_n组合
top_mdm_markers <- mdm_markers %>% group_by(cluster) %>% top_n(10, wt = avg_log2FC)

DoHeatmap(scRNA_mdm,features=top_mdm_markers$gene,group.by = 'SCT_snn_res.0.2')+scale_fill_viridis()
ggsave2(filename = '热图细分.pdf',path = './result_3v3/analysis/',width = 10,height =9)
```

#### Chapter11

```r
# 加载包
library(seurat)
library(ggplot2)
library(Seurat)
library(dplyr)
library(reticulate)
library(sctransform)
library(cowplot)
library(viridis)
library(tidyr)
library(magrittr)
library(reshape2)
library(readxl)
library(stringr)
library(cowplot)
library(scales)
library(tibble)
library(gplots)
library(RColorBrewer)

theme_set(theme_cowplot())

#配色方案
use_colors <- c(
  Tumor = "brown2",
  Normal = "deepskyblue2",
  G1 = "#46ACC8",
  G2M = "#E58601",
  S = "#B40F20",
  Epithelial = "seagreen",
  Immune = "darkgoldenrod2",
  Stromal = "steelblue",
  p032 = "#5B1A18",
  p033 = "#9C964A",
  p034 = "#FD6467",
  CNN = "chartreuse4",
  CNA = "orange")

epi <- readRDS("epi.RDS")
epi <- RunPCA(epi)
ElbowPlot(epi,  ndims = 50)


epi <- RunUMAP(epi, dims = 1:20)
epi <- FindNeighbors(epi, dims = 1:20)
for (i in c(0.2, 0.3, 0.4, 0.5, 1, 2)) {
  epi <- FindClusters(epi, resolution = i)
  print(DimPlot(epi, reduction = "umap", label = T) + labs(title = paste0("resolution: ", i)))
}

### 我们依然选择比较大的resolution，手动注释没有关系
### 控制在20个cluster左右即可
Idents(epi) <- epi@meta.data$SCT_snn_res.1

### 细胞类型注释对于上皮而言我们并不喜欢，因为很多上皮类型我们几乎是没听过的
### 我们更关注哪些是肿瘤细胞,我们要记住我们曾经认为，normal里比例高的细胞就是normal
### tumor里比例高的就是tumor
### 大家可以运行下面的代码回忆一下
epi_clusters <- FetchData(epi, vars = c("SCT_snn_res.1", "tissue_type"))

count_tumor <- epi_clusters %>% filter(tissue_type == "Tumor") %>% count() %>% as.numeric()
count_normal <- epi_clusters %>% filter(tissue_type == "Normal") %>% count() %>% as.numeric()
### 每个cluster计数总数
epi_counts <- epi_clusters %>% group_by(tissue_type) %>% count(SCT_snn_res.1)
### 除以总count数，是朴素的比例计算
proportion_tumor <- epi_counts %>% filter(tissue_type == "Tumor") %>% mutate(proportion = n/count_tumor)
proportion_normal <- epi_counts %>% filter(tissue_type == "Normal") %>% mutate(proportion = n/count_normal)

### 组合，最后一行很有意思，认为哪群比例高就属于哪群
proportion_epi <- full_join(proportion_normal, proportion_tumor, by = "SCT_snn_res.1") %>% 
  mutate(proportion.x = ifelse(is.na(proportion.x), 0,  proportion.x)) %>%  
  mutate(proportion.y = ifelse(is.na(proportion.y), 0,  proportion.y)) %>%
  mutate(tissue_type.x = "Normal") %>%
  mutate(tissue_type.y = "Tumor") %>%
  mutate(cluster_type = ifelse(proportion.x > proportion.y, "Normal", "Tumor"))

### 每个细胞的详细归属cluster
cluster_type_data <- left_join(x = epi_clusters, y = proportion_epi, by = "SCT_snn_res.1")
rownames(cluster_type_data) <- rownames(epi_clusters)

### 加入metadata中
epi <- AddMetaData(epi, select(cluster_type_data, cluster_type))


### 柱状图

n1 <- select(proportion_epi, c(tissue_type.x, SCT_snn_res.1, proportion.x)) %>%
  mutate(tissue_type = tissue_type.x) %>% 
  mutate(proportion = proportion.x) %>%
  mutate(tissue_type.x = NULL) %>%
  mutate(proportion.x = NULL)
t1 <- select(proportion_epi, c(tissue_type.y, SCT_snn_res.1, proportion.y)) %>%
  mutate(tissue_type = tissue_type.y) %>% 
  mutate(proportion = proportion.y) %>%
  mutate(tissue_type.y = NULL) %>%
  mutate(proportion.y = NULL)

proportion_epi2 <- rbind(n1, t1)

ggplot(proportion_epi2, aes(fill = tissue_type, y = proportion, x = SCT_snn_res.1)) +
  geom_bar(stat = "identity", position = "dodge", width = 0.7) +
  scale_fill_manual(values = use_colors)

ggsave2("正常-肿瘤上皮比较.png", path = "result_3v3/", width = 40, height = 20, units = "cm")

### 根据下面的图，我们可以明确地识别出肿瘤上皮中哪些类群是新增加的
### 也就是异型增生的上皮细胞——癌细胞
DimPlot(epi, group.by = "cluster_type",split.by = 'tissue_type' ,cols = use_colors, pt.size = 0.1)
ggsave2("新增的肿瘤细胞.png", path = "result_3v3/", width = 8, height = 4)
#### 其他还有什么方法剥离出肿瘤上皮（癌变上皮）呢，拷贝数推断值得学习
```

#### Chapter 12

```r
## 加载R包
library(seurat)
library(ggplot2)
library(Seurat)
library(dplyr)
library(reticulate)
library(sctransform)
library(cowplot)
library(viridis)
library(tidyr)
library(magrittr)
library(reshape2)
library(readxl)
library(stringr)
library(cowplot)
library(scales)
library(tibble)
library(gplots)
library(RColorBrewer)

theme_set(theme_cowplot())

#color scheme
use_colors <- c(
  Tumor = "brown2",
  Normal = "deepskyblue2",
  G1 = "#46ACC8",
  G2M = "#E58601",
  S = "#B40F20",
  Epithelial = "seagreen",
  Immune = "darkgoldenrod2",
  Stromal = "steelblue",
  p032 = "#5B1A18",
  p033 = "#9C964A",
  p034 = "#FD6467",
  CNN = "chartreuse4",
  CNA = "orange")

epi <- readRDS("epi.RDS")
epi <- RunPCA(epi)
ElbowPlot(epi,  ndims = 50)


epi <- RunUMAP(epi, dims = 1:20)
epi <- FindNeighbors(epi, dims = 1:20)
for (i in c(0.2, 0.3, 0.4, 0.5, 1, 2)) {
  epi <- FindClusters(epi, resolution = i)
  print(DimPlot(epi, reduction = "umap", label = T) + labs(title = paste0("resolution: ", i)))
}

### 我们依然选择比较大的resolution，手动注释没有关系
### 控制在20个cluster左右即可
Idents(epi) <- epi@meta.data$SCT_snn_res.1


meta_tbl <- as_tibble(FetchData(epi, c("cell_id", "sample_id", "main_cell_type", "tissue_type", "patient_id")))

#瞄一眼
table(meta_tbl$main_cell_type)
table(meta_tbl$tissue_type)

cols.use <- c(
  p032 = "#6fc48c",
  p033 = "#6fcbcd",
  p034 = "#35c5f3",
  Normal = "steelblue",
  Tumor= "red",
  CNA = "red",
  CNN = "grey"
)

pids <- sort(unique(epi$patient_id))
# 瞄一眼
pids_with_tumor <- FetchData(epi, c("tissue_type", "patient_id")) %>% as_tibble() %>% distinct(tissue_type, patient_id) %>% filter(tissue_type == "Tumor") %>% pull(patient_id) %>% sort
```

#### Chapter 13

```r

##  准备infercnv的输入文件---------------------------------

## 1.准备原始count矩阵(raw count matrix, 简称rcm)
rcm <- epi@assays$RNA@counts
dim(rcm)

## 2.准备细胞注释文件(annotation file ,简称af)
## 结果文件中，我们的malignant指的是肿瘤组织取的样
## normal指的是正常组织取的样
## 理论上，肿瘤组织取得样品有癌上皮和正常上皮两种可能
af <- FetchData(epi, c("main_cell_type", "tissue_type", "patient_id", "cell_id")) %>%
  # filter(cell_id %in% cell_subset) %>%
  mutate(cell_type_infercnv = ifelse(tissue_type != "Normal", paste0("malignant_", patient_id), paste0("normal_", patient_id)),
         cell_id = str_replace_all(cell_id, ":", "_")) %>% 
  select(cell_id, cell_type_infercnv) %>% 
  as_tibble
dir.create('infercnv')
write.table(af, file = "./infercnv/annotation.txt", sep = "\t", col.names = F, row.names = F)
### 3.准备基因坐标文件gene order file ,gof
### 下载网站:https://data.broadinstitute.org/Trinity/CTAT/cnv/
library(readr)
gof <- read_tsv("./infercnv//gencode_v21_gen_pos.complete.txt", col_names = c("gene", "chr", "start", "end"))
### 处理下，以前有|
gof <- separate(gof,col='gene',into ='gene',sep = '\\|')
gof <- gof[!duplicated(gof$gene),]
common_genes <- intersect(gof$gene, rownames(rcm))
### 把原来的换掉
write.table(gof,file ='./infercnv/gencode_v21_gen_pos.complete_modi.txt',col.names = F,sep = '\t',row.names = F,quote = F)


## 4. 指定参考分组，选择normal为参考组！tumor组相对于normal组比较（实际上是相对值）
baseline_cell_types <- sort(unique(af$cell_type_infercnv)[!str_detect(unique(af$cell_type_infercnv), "malignant")])


#BiocManager::install('infercnv',update = F,ask=F)
library(dendextend)
library(infercnv)

# 构建cnv对象
cnv_obj <- infercnv::CreateInfercnvObject(
  raw_counts_matrix = rcm,
  annotations_file = "./infercnv/annotation.txt",
  gene_order_file = "./infercnv//gencode_v21_gen_pos.complete_modi.txt",
  ref_group_names = baseline_cell_types
) 

### 运行infercnv，运行速度比较缓慢
## 会过滤到很多低表达基因，序列长度标准化，log标准化,平滑，重中心化，聚类等
cnv_obj <- infercnv::run(
  cnv_obj, cutoff=0.1, out_dir="./infercnv/", 
  cluster_by_groups=T, denoise=T, HMM=F, 
  num_threads=parallel::detectCores()
)

write_rds(cnv_obj, "./infercnv/infercnv_results.rds")
## 可以加载运行结果，节约时间
cnv_obj <- readRDS("./infercnv/infercnv_results.rds")

expr <- cnv_obj@expr.data

colnames(af)=c('cell_id','group')
head(af)

gene <- rownames(expr)
gof=as.data.frame(gof)
rownames(gof)=gof$gene
# 取交集
sub_gof <-  gof[intersect(gene,gof$gene),]
expr=expr[intersect(gene,gof$gene),]


### 针对CNV推断的结果聚类
set.seed(123456)
### epi有20个cluster,此处我们也设20个好了
kmeans.result <- kmeans(t(expr), 20)
km <- data.frame(km_cluster=kmeans.result$cluster)
km$cell_id=rownames(km)

#合并km和分组信息
km=km%>%inner_join(af,by="cell_id") 
# 按照聚类结果排序
km_ordered=km[order(km$km_cluster),]
rownames(km_ordered)=km_ordered$cell_id
km_ordered$cell_id=NULL
km_ordered$km_cluster=factor(km_ordered$km_cluster) #将km_group转换为因子
head(km_ordered)
library(tidyverse)
library(ComplexHeatmap)
library(circlize)

## 准备画复杂热图
top_anno <- HeatmapAnnotation(foo = anno_block(gp = gpar(fill = "NA",col="NA"), labels = 1:22,labels_gp = gpar(cex = 1.5)))
color_v=c(RColorBrewer::brewer.pal(8, "Dark2")[1:8], RColorBrewer::brewer.pal(8, "RdGy")[1:8],
          RColorBrewer::brewer.pal(8, "Blues")[1:4])
names(color_v)=as.character(1:20)
left_anno <- rowAnnotation(df = km_ordered,col=list(group=c("normal_p032"="#0072b1","normal_p033"="#0072b3",
                                                            "normal_p034"="#0072b5",'malignant_p032'='#bc3c25',
                                                            'malignant_p033'='#bc3c27','malignant_p034'='#bc3c29'),km_cluster=color_v))

pdf("complexheatmap.pdf",width = 20,height =15)
ht = Heatmap(t(expr)[rownames(km_ordered),], 
             col = colorRamp2(c(0.4,1,1.6), c("#377EB8","#F0F0F0","#E41A1C")), #如果是10x的数据，这里的刻度会有所变化
             cluster_rows = F,cluster_columns = F,show_column_names = F,show_row_names = F,
             column_split = factor(sub_gof$chr, paste("chr",1:22,sep = "")), #这一步可以控制染色体顺序，即使你的基因排序文件顺序是错的
             column_gap = unit(2, "mm"),
             
             heatmap_legend_param = list(title = "Modified expression",direction = "vertical",title_position = "leftcenter-rot",at=c(0.4,1,1.6),legend_height = unit(3, "cm")),
             
             top_annotation = top_anno,left_annotation = left_anno, #添加注释
             row_title = NULL,column_title = NULL)

draw(ht)
dev.off()
##依托于正常细胞作为参考，我们可以推断8，16是正常的细胞
## 也就是说，8，16虽然也出现在肿瘤组织中，但他可能是肿瘤组织的正常细胞
km_ordered$malignant= ifelse(km_ordered$km_cluster == 8 |km_ordered$km_cluster == 16, 'Normal','Malignant')
km_ordered_add=km_ordered[colnames(epi),]

epi$malignant=km_ordered_add$malignant


epi$km_cluster=km_ordered_add$km_cluster

## 根据inferCNV的result来画图
DimPlot(epi,reduction = 'umap',split.by = 'tissue_type',group.by = 'malignant')
ggsave2("是否恶性（是否存在CNV）.pdf", path = "./result_3v3/analysis/epi_analysis/", width = 10,height=5)

## 
DimPlot(epi,reduction = 'umap',split.by = 'tissue_type',group.by = 'km_cluster',label = T)
ggsave2("km-cluster.pdf", path = "./result_3v3/analysis/epi_analysis/", width = 10,height=5)

### 结果覆盖原来的epi文件，你需要保存！！！！！！！
saveRDS(epi,file ='epi.RDS')
```

#### Chapter 13

```r
### 加载R包
library(seurat)
library(ggplot2)
library(Seurat)
library(dplyr)
library(reticulate)
library(sctransform)
library(cowplot)
library(viridis)
library(tidyr)
library(magrittr)
library(reshape2)
library(readxl)
library(stringr)
library(cowplot)
library(scales)
library(tibble)
library(gplots)
library(RColorBrewer)

theme_set(theme_cowplot())

#color scheme
use_colors <- c(
  Tumor = "brown2",
  Normal = "deepskyblue2",
  G1 = "#46ACC8",
  G2M = "#E58601",
  S = "#B40F20",
  Epithelial = "seagreen",
  Immune = "darkgoldenrod2",
  Stromal = "steelblue",
  p032 = "#5B1A18",
  p033 = "#9C964A",
  p034 = "#FD6467",
  CNN = "chartreuse4",
  CNA = "orange")

epi <- readRDS("epi.RDS")

### 再来重复十一节的方法，对每个cluster normal多就认为是正常上皮，tumor多就认为是癌细胞
epi_clusters <- FetchData(epi, vars = c("SCT_snn_res.1", "tissue_type"))

count_tumor <- epi_clusters %>% filter(tissue_type == "Tumor") %>% count() %>% as.numeric()
count_normal <- epi_clusters %>% filter(tissue_type == "Normal") %>% count() %>% as.numeric()
### 每个cluster计数总数
epi_counts <- epi_clusters %>% group_by(tissue_type) %>% count(SCT_snn_res.1)
### 除以总count数，是朴素的比例计算
proportion_tumor <- epi_counts %>% filter(tissue_type == "Tumor") %>% mutate(proportion = n/count_tumor)
proportion_normal <- epi_counts %>% filter(tissue_type == "Normal") %>% mutate(proportion = n/count_normal)

### 组合，最后一行很有意思，认为哪群比例高就属于哪群
proportion_epi <- full_join(proportion_normal, proportion_tumor, by = "SCT_snn_res.1") %>% 
  mutate(proportion.x = ifelse(is.na(proportion.x), 0,  proportion.x)) %>%  
  mutate(proportion.y = ifelse(is.na(proportion.y), 0,  proportion.y)) %>%
  mutate(tissue_type.x = "Normal") %>%
  mutate(tissue_type.y = "Tumor") %>%
  mutate(cluster_type = ifelse(proportion.x > proportion.y, "Normal", "Tumor"))

### 每个细胞的详细归属cluster
cluster_type_data <- left_join(x = epi_clusters, y = proportion_epi, by = "SCT_snn_res.1")
rownames(cluster_type_data) <- rownames(epi_clusters)

### 加入metadata中
epi <- AddMetaData(epi, select(cluster_type_data, cluster_type))


### 比较十一节(比例判断法）、十二节（inferCNV）两种方法
### tissue_type就是取样来源（table）的左列，normal取自正常，tumor取自肿瘤
### cluster_type就是比例判断法的结果，包括normal(作者认为的正常上皮）和tumor（作者认为的癌细胞）
### malignant就是inferCNV的结果，包括maligant（我们认为的癌细胞）和Normal（我们认为的正常上皮）
table(epi$tissue_type,epi$cluster_type)
table(epi$tissue_type,epi$malignant)

### 注意:两种方法都无法避免在正常组织中出现恶性细胞这种“不太正常”的事请！！！！！！！！！！！！！！
### 但是确实inferCNV的误判可能更小，因为只有6个看着明显错误的细胞，而比例推断法有15个

## 获得每个人病人的癌细胞marker基因
### 提取作者认为的恶性上皮
epi_tumor <- subset(epi, subset = cluster_type %in% c('Tumor'))

epi_tumor <- ScaleData(epi_tumor)


###tumor cell marker genes

Idents(epi_tumor) <- epi_tumor@meta.data$patient_id

markers <- FindAllMarkers(epi_tumor, only.pos = T, min.pct = 0.25, min.diff.pct = 0.25)

top_TC_markers <- markers %>% group_by(cluster) %>% top_n(10, wt = avg_log2FC)

DoHeatmap(epi_tumor, features = top_TC_markers$gene, group.by = "patient_id", draw.lines = F, group.colors = use_colors) +
  scale_fill_viridis()


ggsave2("HeatMap_Tumor1.pdf", path = "./result_3v3/analysis/epi_analysis/", width = 30, height = 30)

### 提取我们认为的恶性上皮


epi_tumor <- subset(epi, subset = malignant %in% c('Malignant'))
epi_tumor <- ScaleData(epi_tumor)


###tumor cell marker genes

Idents(epi_tumor) <- epi_tumor@meta.data$patient_id

markers <- FindAllMarkers(epi_tumor, only.pos = T, min.pct = 0.25, min.diff.pct = 0.25)

top_TC_markers <- markers %>% group_by(cluster) %>% top_n(10, wt = avg_log2FC)


## 相对而言我们的inferCNV的更好看些，p34病人恶性上皮更多些
DoHeatmap(epi_tumor, features = top_TC_markers$gene, group.by = "patient_id", draw.lines = F, group.colors = use_colors) +
  scale_fill_viridis()


ggsave2("HeatMap_Malignant.pdf", path = "./result_3v3/analysis/epi_analysis/", width = 30, height = 30)
```

#### Chapter 14

```r
# 上皮通路分析
library(seurat)
library(ggplot2)
library(Seurat)
library(dplyr)
library(reticulate)
library(sctransform)
library(cowplot)
library(viridis)
library(tidyr)
library(magrittr)
library(reshape2)
library(readxl)
library(stringr)
library(cowplot)
library(scales)
library(tibble)
library(gplots)
library(RColorBrewer)

theme_set(theme_cowplot())

#color scheme
use_colors <- c(
  Tumor = "brown2",
  Normal = "deepskyblue2",
  G1 = "#46ACC8",
  G2M = "#E58601",
  S = "#B40F20",
  p032 = "#5B1A18",
  p033 = "#9C964A",
  p034 = "#FD6467")

epi <- readRDS("epi.RDS")


# 1. hallmark50通路分析和progeny分析

library(readr)
broad_pws <- read_lines("./data/h.all.v6.2.symbols.gmt") %>%
  lapply(str_split, "\\t") %>% 
  unlist(recursive = F) %>% 
  lapply(function(x) setNames(list(x[-c(1:2)]), x[1])) %>% 
  unlist(recursive = F)

epi <- AddModuleScore(object = epi, features = broad_pws, name = names(broad_pws))

# 简单可视化
FeaturePlot(epi,features = 'HALLMARK_TNFA_SIGNALING_VIA_NFKB1',split.by = 'sample_id' )
### 当然也可以对imm_anno和后面的str
#imm_anno <- AddModuleScore(object = imm_anno, features = broad_pws, name = names(broad_pws))
#str_anno <- AddModuleScore(object = str_anno, features = broad_pws, name = names(broad_pws), nbin = 12)

# progeny signatures分析
### PROGENy主要是用来对一些经典的肿瘤通路进行分析
### 能做bulk和单细胞数据
### 运行很快
library(progeny)
epi <- progeny(epi, scale = F, organism="Human", top=500, perm=1, return_assay=T)
epi <- ScaleData(epi, assay = "progeny")

#imm_anno <- progeny(imm_anno, scale = F, organism="Human", top=500, perm=1, return_assay=T)
#imm_anno <- ScaleData(imm_anno, assay = "progeny")
#str_anno <- progeny(str_anno, scale = F, organism="Human", top=500, perm=1, return_assay=T)
#str_anno <- ScaleData(str_anno, assay = "progeny")


# 2.progeny病人异质性

### 提取我们认为的恶性上皮(InferCNV，具体看上一节)
### 之所以不用原文的(比例推断法，具体看上一节）是因为按照原文34病人没有什么肿瘤细胞，这显然是不科学的
epi_tumor <- subset(epi, subset = malignant%in% c('Malignant'))
epi_tumor <- ScaleData(epi_tumor)

progeny_scores <- as.data.frame(t(GetAssayData(epi_tumor, assay = "progeny", slot = "scale.data")))
progeny_scores$cell_id <- rownames(progeny_scores)
progeny_scores <- gather(progeny_scores, Pathway, Activity, -cell_id)

cells_clusters <- FetchData(epi_tumor, c("sample_id", "malignant")) %>% filter(str_detect(sample_id, "t"))
cells_clusters$cell_id <- rownames(cells_clusters)

progeny_scores <- inner_join(progeny_scores, cells_clusters)

summarized_progeny_scores <- progeny_scores %>% 
  group_by(Pathway, sample_id) %>% 
  summarise(avg = mean(Activity), std = sd(Activity)) %>%
  pivot_wider(id_cols = Pathway, names_from = sample_id, values_from = avg) %>%
  column_to_rownames("Pathway") %>%
  as.matrix()

heatmap.2(summarized_progeny_scores, trace = "none",density.info = "none", col = bluered(100))
### 完美且结果符合原文，异质性非常显著
ggsave2("progeny热图.pdf", path = "./result_3v3/analysis/epi_analysis/", width = 5, height = 5)
# 3. 有丝分裂活性评估
mitotic_activity <- FetchData(epi_tumor, c("tissue_type", "malignant", "Phase", "sample_id")) %>%
  mutate(sample_id = factor(sample_id, levels = c("p034t", "p033t", "p032t")))

ggplot(mitotic_activity, aes(x = sample_id, fill = Phase)) +
  geom_bar(position = "fill", width = 0.75) +
  scale_fill_manual(values = use_colors) +
  coord_flip()
ggsave2("有丝分裂活性评估.pdf", path = "./result_3v3/analysis/epi_analysis/", width = 5,height=3)
```



### b 高级版--以Kumar V et al, *Can*cer Discov*, 2022(GC)为例*

这是新加坡国立大学发表的一个数据库，有40个患者的单细胞数据，张朴然的文章里就用到了这个数据库（虽然我不知道他是怎么跑的下这个代码的）。

![image-20230315152758138](https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303151527582.png)

```R
##read the data
rm(list = ls()) 
options(stringsAsFactors = F) 
BiocManager::install('scRNAstat')
library(scRNAstat) 
library(Seurat)
library(ggplot2)
library(clustree)
library(cowplot)
library(dplyr)
dir='D:/GSE183904_RAW' 
samples=list.files( dir ,pattern = 'gz')
samples 
library(data.table)
sceList = lapply(samples,function(pro){ 
  # pro=samples[1] 
  print(pro)
  ct=fread(file.path( dir ,pro),data.table = F)
  ct[1:4,1:4]
  rownames(ct)=ct[,1]
  ct=ct[,-1]
  sce=CreateSeuratObject(counts =  ct ,
                         project =  gsub('.csv.gz','',strsplit(pro,'_')[[1]][2]),
                         min.cells = 5,
                         min.features = 300,)
  
  return(sce)
})
names(sceList)  
library(stringr)
samples=gsub('.csv.gz','',str_split(samples,'_',simplify = T)[,2])
samples
names(sceList) =  samples
sceList
a1=sceList[1]
lapply(names(sceList) , function(x){  
  # x=names(sceList)[1]
  print(x)
  sce=sceList[[x]]
  sce
  dir.create( x )
  sce = basic_qc(sce=sce,org='human',
                 dir = x)  
  sce
  sce = basic_filter(sce)  
  sce = basic_workflow(sce,dir = x)   
  markers_figures <- basic_markers(sce,
                                   org='human',
                                   group='seurat_clusters',
                                   dir = x)
  p_umap = DimPlot(sce,reduction = 'umap',  
                   group.by = 'seurat_clusters',
                   label.box = T,  label = T,repel = T)
  p=p_umap+markers_figures[[1]]
  print(p)
  ggsave(paste0('umap_markers_for_',x,'.pdf'),width = 12,height = 9)
  #save(p,file = paste0('umap_markers_for_',x,'.Rdata'))
  
})
```

上面的代码相当于帮我们完成了质控，这个`sceList`变量你可以理解为一个由40个`Seurat`对象构成的列表。很遗憾，我拿我电脑根本跑不动这个数据，因为光是第一步，合并seurat对象，我电脑就花了半个小时把40例患者样品合并，我不知道我们这边有没有相应的服务器，但是光用电脑去跑这个代码肯定会很慢很慢很慢

### C CellChat教程

![image-20230315132830263](https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303151328604.png)





![image-20230315132921384](https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303151329565.png)

![image-20230315141318173](https://dataset-1316830340.cos.ap-shanghai.myqcloud.com/202303151413421.png)

（未完待续，让我研究一下……）

