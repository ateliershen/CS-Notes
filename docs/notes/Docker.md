<!-- GFM-TOC -->
* [一、解決的問題](#一解決的問題)
* [二、與虛擬機的比較](#二與虛擬機的比較)
* [三、優勢](#三優勢)
* [四、使用場景](#四使用場景)
* [五、鏡像與容器](#五鏡像與容器)
* [參考資料](#參考資料)
<!-- GFM-TOC -->


# 一、解決的問題

由於不同的機器有不同的操作系統，以及不同的庫和組件，在將一個應用部署到多臺機器上需要進行大量的環境配置操作。

Docker 主要解決環境配置問題，它是一種虛擬化技術，對進程進行隔離，被隔離的進程獨立於宿主操作系統和其它隔離的進程。使用 Docker 可以不修改應用程序代碼，不需要開發人員學習特定環境下的技術，就能夠將現有的應用程序部署在其它機器上。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/011f3ef6-d824-4d43-8b2c-36dab8eaaa72-1.png" width="400px"/> </div><br>

# 二、與虛擬機的比較

虛擬機也是一種虛擬化技術，它與 Docker 最大的區別在於它是通過模擬硬件，並在硬件上安裝操作系統來實現。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/be608a77-7b7f-4f8e-87cc-f2237270bf69.png" width="500"/> </div><br>

## 啟動速度

啟動虛擬機需要先啟動虛擬機的操作系統，再啟動應用，這個過程非常慢；

而啟動 Docker 相當於啟動宿主操作系統上的一個進程。

## 佔用資源

虛擬機是一個完整的操作系統，需要佔用大量的磁盤、內存和 CPU 資源，一臺機器只能開啟幾十個的虛擬機。

而 Docker 只是一個進程，只需要將應用以及相關的組件打包，在運行時佔用很少的資源，一臺機器可以開啟成千上萬個 Docker。

# 三、優勢

除了啟動速度快以及佔用資源少之外，Docker 具有以下優勢：

## 更容易遷移

提供一致性的運行環境。已經打包好的應用可以在不同的機器上進行遷移，而不用擔心環境變化導致無法運行。

## 更容易維護

使用分層技術和鏡像，使得應用可以更容易複用重複的部分。複用程度越高，維護工作也越容易。

## 更容易擴展

可以使用基礎鏡像進一步擴展得到新的鏡像，並且官方和開源社區提供了大量的鏡像，通過擴展這些鏡像可以非常容易得到我們想要的鏡像。

# 四、使用場景

## 持續集成

持續集成指的是頻繁地將代碼集成到主幹上，這樣能夠更快地發現錯誤。

Docker 具有輕量級以及隔離性的特點，在將代碼集成到一個 Docker 中不會對其它 Docker 產生影響。

## 提供可伸縮的雲服務

根據應用的負載情況，可以很容易地增加或者減少 Docker。

## 搭建微服務架構

Docker 輕量級的特點使得它很適合用於部署、維護、組合微服務。

# 五、鏡像與容器

鏡像是一種靜態的結構，可以看成面向對象裡面的類，而容器是鏡像的一個實例。

鏡像包含著容器運行時所需要的代碼以及其它組件，它是一種分層結構，每一層都是隻讀的（read-only layers）。構建鏡像時，會一層一層構建，前一層是後一層的基礎。鏡像的這種分層存儲結構很適合鏡像的複用以及定製。

構建容器時，通過在鏡像的基礎上添加一個可寫層（writable layer），用來保存著容器運行過程中的修改。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/docker-filesystems-busyboxrw.png"/> </div><br>

# 參考資料

- [DOCKER 101: INTRODUCTION TO DOCKER WEBINAR RECAP](https://blog.docker.com/2017/08/docker-101-introduction-docker-webinar-recap/)
- [Docker 入門教程](http://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)
- [Docker container vs Virtual machine](http://www.bogotobogo.com/DevOps/Docker/Docker_Container_vs_Virtual_Machine.php)
- [How to Create Docker Container using Dockerfile](https://linoxide.com/linux-how-to/dockerfile-create-docker-container/)
- [理解 Docker（2）：Docker 鏡像](http://www.cnblogs.com/sammyliu/p/5877964.html)
- [為什麼要使用 Docker？](https://yeasy.gitbooks.io/docker_practice/introduction/why.html)
- [What is Docker](https://www.docker.com/what-docker)
- [持續集成是什麼？](http://www.ruanyifeng.com/blog/2015/09/continuous-integration.html)





# 微信公眾號


更多精彩內容將發佈在微信公眾號 CyC2018 上，你也可以在公眾號後臺和我交流學習和求職相關的問題。另外，公眾號提供了該項目的 PDF 等離線閱讀版本，後臺回覆 "下載" 即可領取。公眾號也提供了一份技術面試複習大綱，不僅系統整理了面試知識點，而且標註了各個知識點的重要程度，從而幫你理清多而雜的面試知識點，後臺回覆 "大綱" 即可領取。我基本是按照這個大綱來進行復習的，對我拿到了 BAT 頭條等 Offer 起到很大的幫助。你們完全可以和我一樣根據大綱上列的知識點來進行復習，就不用看很多不重要的內容，也可以知道哪些內容很重要從而多安排一些複習時間。


<br><div align="center"><img width="320px" src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/other/公眾號海報6.png"></img></div>
