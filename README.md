# SimpleDistributed-Spider
闲来更新一下github。
教大家，如何简单的几个parser. 就可以去做一些分布式的爬虫任务，爬取全站。

### 实用场景 
- 爬取全站，我们首要要知道层级递进关系

    - 比如我们要爬取，赶集网全国所有的二手车信息。我们首先爬到每个城市的链接并缓存起来，然后从缓存的城市链接里去——> 爬去剖析出所有的页面链接并缓存——>
   然后从每个页面中读取单个信息的详情页链接并缓存  ——>然后就是解析详情页
 
    -  这个思路具有普遍性，我去年就用这个方法 爬过很多的大型网站，房天下全国租房信息，58全国的贷款信息。QQ游戏论坛留言等等，，


### 反爬
- 如果要爬取 大量的信息，势必会遭遇一些反爬虫的阻击。一般能通过更换ip的方法来跳过。

- 里我们再*download.py*中的**Downloader**中 留了一个 result_testing（对返回的结果进行检测，判断是否遭遇了反爬） 和change_proxy（获取新ip) 的接口。
可以根据自己的任务 去继承Downloader 改写这两个方法。

### 健壮性
- *downloader.py*中的Downloader类。 内置了 爬取重试次数和爬取间隔的设置
- * multiprocess-keeper.py* 监控进程，可以做到。进程挂掉后自启。
### 排程
- 我们在myspider里。将每一次的分层 都用一个类来表达。（直接继承 NodeProcess 就好了）.写好自己的parser 。
- 然后实例化第一个类 StartNode . ，在根据排程的顺序写一个列表spider_list 就Ok了
- schedule.py Schedules 中的run_spider方法 已经做好了排程
- 如果想要实现 增量爬取 ，StartNode 需要实现 set_initiate_data 方法。 init_data 是一个范围的概念，在parse中需要用到这个
范围来规范爬虫。
比如根据页码 或者 url中的递增标识的id范围 增量爬取时 那inint_data 可能就类似range(100,200) ，根据时间增量爬取时，
init_data 也可以为时间戳范围或者干脆时date 字符串的元组（2018-01-01，2019-01-01）。  


### 布署和运行
- 首先在主服务器上安装redis ，用于做缓存队列使用
- 将整个文件夹放到要部署的服务器上
- 直接运行xx_spider.py  如果想同时运行多个的话  可以运行 multiprocess-keeper.py

### 代理ip
- 等我的下篇 ，我会传一个代理ip池搭建的方法。然后轻松愉快的通过rest 调取最新被确认过可以用的ip
--- 
 大家只要写 每个分层的parse类 就可以了 即自己编辑my_spider.py就好了。 
