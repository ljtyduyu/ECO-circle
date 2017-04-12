A small excise to  cpoy the ECO-circle chart!
===========================================================
Just a small  attempt！
--------------------

the original chart(from Economist):
<div  align="center">    
<img src="https://github.com/ljtyduyu/ECO-circle/blob/master/Image/one.png" width = "500" height = "400" alt="one" align=center />
</div>

the version copied by teacher Liu :
<div  align="center">    
<img src="https://github.com/ljtyduyu/ECO-circle/blob/master/Image/two.png" width = "500" height = "400" alt="two" align=center />
</div>


```r
library("ggplot2")
library(Cairo)
library(ggmap)
library(xlsx)
library(lubridate)
library(reshape2)
library(showtext)
library(grid)
library(plyr)
```

> this data is from the sina blog of teacher Liu(WanXiang Liu)！

```r
setwd("F:/微信公众号/公众号——数据小魔方/2017年4月/20170403")
ECOdata<-read.xlsx("ECOCircle.xlsx",sheetName="Sheet1",header=T,encoding="UTF-8",stringsAsFactors=FALSE)
```

> this step is very very important for this article!<br>
but it's not a general way(so it's just for here)<br>

```r
f<-c(99,91,84,77)
for (i in 1:nrow(ECOdata)){
m<-sum(ECOdata[i,3:6]==1)
h<-grep(1,ECOdata[i,3:6])
ECOdata[i,h+2]<-f[1:m]
}
```

> create the date variable(for the aesthetics of polar attributes attribution)

```r
newdate<-seq(from=as.Date("2014-01-01"),to=as.Date("2014-12-31"),by="1 day")
newmonth<-month(newdate)
```

```r
mydata<-data.frame(newdate,newmonth);mydata$ID<-1:nrow(mydata)
mydata$Type<-ifelse(mydata$newmonth%%2==0,"A","B")
mydata$Scale<-100
a<-seq(from=90,to=-90,length=181);b<-seq(from=90,to=-90,length=184)
mydata$Circle<-c(a,b)
```


merge the assistant  data to source data!
-----------------------------------------------

```r
mynewdata<-merge(mydata,ECOdata,by.x="newdate",by.y="date",all.x=T)
mynewdataone<-melt(na.omit(mynewdata), id.vars =names(mynewdata)[1:7],variable.name = "Class", value.name = "Fact")
```


```r
circlemonth<-seq(15,345,length=12)
circlebj<-rep(c(-circlemonth[1:3],rev(circlemonth[1:3])),2)
```


```r
mynewdataoneA<-mynewdataone[mynewdataone$Fact!=0&mynewdataone$ID<180,]
mynewdataoneA<-mynewdataoneA[!duplicated(mynewdataoneA[,1]),]
mynewdataoneB<-mynewdataone[mynewdataone$Fact!=0&mynewdataone$ID>180,]
mynewdataoneB<-mynewdataoneB[!duplicated(mynewdataoneB[,1]),]
```

-------------------------------------------

the last step :output the finall chart!
----------------------------


```r
font.add("myfont","msyhl.ttc")
CairoPNG(file="ECOCircle.png",width=1000,height=1050)
showtext.begin()
ggplot()+
geom_bar(data=mydata[mydata$Type=="A",],aes(x=ID,y=Scale),stat="identity",width=1,fill="#ECEDD1",col="#ECEDD1")+
geom_bar(data=mydata[mydata$Type=="B",],aes(x=ID,y=Scale),stat="identity",width=1,fill="#DFE0B1",col="#DFE0B1")+
geom_point(data=mynewdataone[mynewdataone$Fact!=0,],aes(x=ID,y=Fact,fill=Class),size=5,shape=21,col="white")+
ylim(-90,130)+
scale_fill_manual(limits=c("Legislative","Referendum","President","Primary"),values=c("#93A299","#CF543F","#B5AE53","#86825B"),labels=c("立法","公投","总统","初选"))+
geom_text(data=mynewdataoneA,aes(x=ID,y=max(mynewdataoneA$Fact)+20,label=State,angle=Circle),family="sans",size=5,hjust=0)+
geom_text(data=mynewdataoneB,aes(x=ID,y=max(mynewdataoneB$Fact)+20,label=State,angle=Circle),family="sans",size=5,hjust=1)+
geom_text(data=NULL,aes(x=circlemonth,y=30,label=paste0(1:12,"月"),angle=circlebj),family="myfont",size=7,hjust=.5,vjust=.5)+
guides(colour=guide_legend(title=NULL))+
coord_polar(theta="x")+
labs(title="2014年全球选举事件图",subtitle="这是一幅用心良苦的好图",caption="Source：Economics\nMake:EasyCharts",x="",y="",fill="")+
theme(
text=element_text(family="myfont"),
axis.text=element_blank(),
axis.ticks=element_blank(),
panel.background=element_blank(),
panel.grid=element_blank(),
panel.border=element_blank(),
legend.position=c(0.03,0.92),
legend.background=element_blank(),
legend.key=element_blank(),
legend.key.size=unit(1.55,'cm'),
legend.key.height=unit(1.2,'cm'),
legend.text=element_text(size=20,hjust=3,vjust=3,face="bold"),
plot.background=element_blank(),
plot.title=element_text(size=50),
plot.subtitle=element_text(size=35),
plot.caption=element_text(size=25,hjust=0),
plot.margin=unit(c(.5,.5,.5,.5),"lines"),
)
showtext.end()
dev.off()
```

the finall dispaly:
<div  align="center">    
<img src="https://github.com/ljtyduyu/ECO-circle/blob/master/Image/three.png" width = "500" height = "400" alt="three" align=center />
</div>

<div  align="center">    
<img src="https://github.com/ljtyduyu/ECO-circle/blob/master/Image/four.png" width = "500" height = "400" alt="four" align=center />
</div>

This all ,thx!
===========

-------------------------------------------------------

联系方式：
----------------------------------------------------
wechat：ljty1991  <br>
Mail:578708965@qq.com <br>
个人公众号：数据小魔方（datamofang） <br>
团队公众号：EasyCharts <br>
qq交流群：[魔方学院]553270834

个人简介：
-------------------------------------------------
**杜雨** <br>
财经专业研究僧； <br>
伪数据可视化达人； <br>
文科背景的编程小白； <br>
喜欢研究商务图表与地理信息数据可视化，爱倒腾PowerBI、SAP DashBoard、Tableau、R ggplot2、Think-cell chart等诸如此类的数据可视化软件，创建并运营微信公众号“数据小魔方”。 <br>
Mail:578708965@qq.com <br>

<div  align="center">    
<img src="https://github.com/ljtyduyu/FontMap-of-China/blob/master/Image/resume.png" width = "550" height = "300" alt="resume" align=center />
</div>

-------------------------------------------

备注信息：
----------------------------------------------------
<a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/"><img alt="知识共享许可协议" style="border-width:0" src="https://i.creativecommons.org/l/by-nc/4.0/88x31.png" /></a><br />本作品采用<a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/">知识共享署名-非商业性使用 4.0 国际许可协议</a>进行许可。
