<!-- GFM-TOC -->
* [一、概述](#一概述)
* [二、數據類型](#二數據類型)
    * [STRING](#string)
    * [LIST](#list)
    * [SET](#set)
    * [HASH](#hash)
    * [ZSET](#zset)
* [三、數據結構](#三數據結構)
    * [字典](#字典)
    * [跳躍表](#跳躍表)
* [四、使用場景](#四使用場景)
    * [計數器](#計數器)
    * [緩存](#緩存)
    * [查找表](#查找表)
    * [消息隊列](#消息隊列)
    * [會話緩存](#會話緩存)
    * [分佈式鎖實現](#分佈式鎖實現)
    * [其它](#其它)
* [五、Redis 與 Memcached](#五redis-與-memcached)
    * [數據類型](#數據類型)
    * [數據持久化](#數據持久化)
    * [分佈式](#分佈式)
    * [內存管理機制](#內存管理機制)
* [六、鍵的過期時間](#六鍵的過期時間)
* [七、數據淘汰策略](#七數據淘汰策略)
* [八、持久化](#八持久化)
    * [RDB 持久化](#rdb-持久化)
    * [AOF 持久化](#aof-持久化)
* [九、事務](#九事務)
* [十、事件](#十事件)
    * [文件事件](#文件事件)
    * [時間事件](#時間事件)
    * [事件的調度與執行](#事件的調度與執行)
* [十一、複製](#十一複製)
    * [連接過程](#連接過程)
    * [主從鏈](#主從鏈)
* [十二、Sentinel](#十二sentinel)
* [十三、分片](#十三分片)
* [十四、一個簡單的論壇系統分析](#十四一個簡單的論壇系統分析)
    * [文章信息](#文章信息)
    * [點贊功能](#點贊功能)
    * [對文章進行排序](#對文章進行排序)
* [參考資料](#參考資料)
<!-- GFM-TOC -->


# 一、概述

Redis 是速度非常快的非關係型（NoSQL）內存鍵值數據庫，可以存儲鍵和五種不同類型的值之間的映射。

鍵的類型只能為字符串，值支持五種數據類型：字符串、列表、集合、散列表、有序集合。

Redis 支持很多特性，例如將內存中的數據持久化到硬盤中，使用複製來擴展讀性能，使用分片來擴展寫性能。

# 二、數據類型

| 數據類型 | 可以存儲的值 | 操作 |
| :--: | :--: | :--: |
| STRING | 字符串、整數或者浮點數 | 對整個字符串或者字符串的其中一部分執行操作</br> 對整數和浮點數執行自增或者自減操作 |
| LIST | 列表 | 從兩端壓入或者彈出元素 </br> 對單個或者多個元素進行修剪，</br> 只保留一個範圍內的元素 |
| SET | 無序集合 | 添加、獲取、移除單個元素</br> 檢查一個元素是否存在於集合中</br> 計算交集、並集、差集</br> 從集合裡面隨機獲取元素 |
| HASH | 包含鍵值對的無序散列表 | 添加、獲取、移除單個鍵值對</br> 獲取所有鍵值對</br> 檢查某個鍵是否存在|
| ZSET | 有序集合 | 添加、獲取、刪除元素</br> 根據分值範圍或者成員來獲取元素</br> 計算一個鍵的排名 |

> [What Redis data structures look like](https://redislabs.com/ebook/part-1-getting-started/chapter-1-getting-to-know-redis/1-2-what-redis-data-structures-look-like/)

## STRING

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/6019b2db-bc3e-4408-b6d8-96025f4481d6.png" width="400"/> </div><br>

```html
> set hello world
OK
> get hello
"world"
> del hello
(integer) 1
> get hello
(nil)
```

## LIST

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/fb327611-7e2b-4f2f-9f5b-38592d408f07.png" width="400"/> </div><br>

```html
> rpush list-key item
(integer) 1
> rpush list-key item2
(integer) 2
> rpush list-key item
(integer) 3

> lrange list-key 0 -1
1) "item"
2) "item2"
3) "item"

> lindex list-key 1
"item2"

> lpop list-key
"item"

> lrange list-key 0 -1
1) "item2"
2) "item"
```

## SET

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/cd5fbcff-3f35-43a6-8ffa-082a93ce0f0e.png" width="400"/> </div><br>

```html
> sadd set-key item
(integer) 1
> sadd set-key item2
(integer) 1
> sadd set-key item3
(integer) 1
> sadd set-key item
(integer) 0

> smembers set-key
1) "item"
2) "item2"
3) "item3"

> sismember set-key item4
(integer) 0
> sismember set-key item
(integer) 1

> srem set-key item2
(integer) 1
> srem set-key item2
(integer) 0

> smembers set-key
1) "item"
2) "item3"
```

## HASH

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/7bd202a7-93d4-4f3a-a878-af68ae25539a.png" width="400"/> </div><br>

```html
> hset hash-key sub-key1 value1
(integer) 1
> hset hash-key sub-key2 value2
(integer) 1
> hset hash-key sub-key1 value1
(integer) 0

> hgetall hash-key
1) "sub-key1"
2) "value1"
3) "sub-key2"
4) "value2"

> hdel hash-key sub-key2
(integer) 1
> hdel hash-key sub-key2
(integer) 0

> hget hash-key sub-key1
"value1"

> hgetall hash-key
1) "sub-key1"
2) "value1"
```

## ZSET

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/1202b2d6-9469-4251-bd47-ca6034fb6116.png" width="400"/> </div><br>

```html
> zadd zset-key 728 member1
(integer) 1
> zadd zset-key 982 member0
(integer) 1
> zadd zset-key 982 member0
(integer) 0

> zrange zset-key 0 -1 withscores
1) "member1"
2) "728"
3) "member0"
4) "982"

> zrangebyscore zset-key 0 800 withscores
1) "member1"
2) "728"

> zrem zset-key member1
(integer) 1
> zrem zset-key member1
(integer) 0

> zrange zset-key 0 -1 withscores
1) "member0"
2) "982"
```

# 三、數據結構

## 字典

dictht 是一個散列表結構，使用拉鍊法解決哈希衝突。

```c
/* This is our hash table structure. Every dictionary has two of this as we
 * implement incremental rehashing, for the old to the new table. */
typedef struct dictht {
    dictEntry **table;
    unsigned long size;
    unsigned long sizemask;
    unsigned long used;
} dictht;
```

```c
typedef struct dictEntry {
    void *key;
    union {
        void *val;
        uint64_t u64;
        int64_t s64;
        double d;
    } v;
    struct dictEntry *next;
} dictEntry;
```

Redis 的字典 dict 中包含兩個哈希表 dictht，這是為了方便進行 rehash 操作。在擴容時，將其中一個 dictht 上的鍵值對 rehash 到另一個 dictht 上面，完成之後釋放空間並交換兩個 dictht 的角色。

```c
typedef struct dict {
    dictType *type;
    void *privdata;
    dictht ht[2];
    long rehashidx; /* rehashing not in progress if rehashidx == -1 */
    unsigned long iterators; /* number of iterators currently running */
} dict;
```

rehash 操作不是一次性完成，而是採用漸進方式，這是為了避免一次性執行過多的 rehash 操作給服務器帶來過大的負擔。

漸進式 rehash 通過記錄 dict 的 rehashidx 完成，它從 0 開始，然後每執行一次 rehash 都會遞增。例如在一次 rehash 中，要把 dict[0] rehash 到 dict[1]，這一次會把 dict[0] 上 table[rehashidx] 的鍵值對 rehash 到 dict[1] 上，dict[0] 的 table[rehashidx] 指向 null，並令 rehashidx++。

在 rehash 期間，每次對字典執行添加、刪除、查找或者更新操作時，都會執行一次漸進式 rehash。

採用漸進式 rehash 會導致字典中的數據分散在兩個 dictht 上，因此對字典的查找操作也需要到對應的 dictht 去執行。

```c
/* Performs N steps of incremental rehashing. Returns 1 if there are still
 * keys to move from the old to the new hash table, otherwise 0 is returned.
 *
 * Note that a rehashing step consists in moving a bucket (that may have more
 * than one key as we use chaining) from the old to the new hash table, however
 * since part of the hash table may be composed of empty spaces, it is not
 * guaranteed that this function will rehash even a single bucket, since it
 * will visit at max N*10 empty buckets in total, otherwise the amount of
 * work it does would be unbound and the function may block for a long time. */
int dictRehash(dict *d, int n) {
    int empty_visits = n * 10; /* Max number of empty buckets to visit. */
    if (!dictIsRehashing(d)) return 0;

    while (n-- && d->ht[0].used != 0) {
        dictEntry *de, *nextde;

        /* Note that rehashidx can't overflow as we are sure there are more
         * elements because ht[0].used != 0 */
        assert(d->ht[0].size > (unsigned long) d->rehashidx);
        while (d->ht[0].table[d->rehashidx] == NULL) {
            d->rehashidx++;
            if (--empty_visits == 0) return 1;
        }
        de = d->ht[0].table[d->rehashidx];
        /* Move all the keys in this bucket from the old to the new hash HT */
        while (de) {
            uint64_t h;

            nextde = de->next;
            /* Get the index in the new hash table */
            h = dictHashKey(d, de->key) & d->ht[1].sizemask;
            de->next = d->ht[1].table[h];
            d->ht[1].table[h] = de;
            d->ht[0].used--;
            d->ht[1].used++;
            de = nextde;
        }
        d->ht[0].table[d->rehashidx] = NULL;
        d->rehashidx++;
    }

    /* Check if we already rehashed the whole table... */
    if (d->ht[0].used == 0) {
        zfree(d->ht[0].table);
        d->ht[0] = d->ht[1];
        _dictReset(&d->ht[1]);
        d->rehashidx = -1;
        return 0;
    }

    /* More to rehash... */
    return 1;
}
```

## 跳躍表

是有序集合的底層實現之一。

跳躍表是基於多指針有序鏈表實現的，可以看成多個有序鏈表。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/beba612e-dc5b-4fc2-869d-0b23408ac90a.png" width="600px"/> </div><br>

在查找時，從上層指針開始查找，找到對應的區間之後再到下一層去查找。下圖演示了查找 22 的過程。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/0ea37ee2-c224-4c79-b895-e131c6805c40.png" width="600px"/> </div><br>

與紅黑樹等平衡樹相比，跳躍表具有以下優點：

- 插入速度非常快速，因為不需要進行旋轉等操作來維護平衡性；
- 更容易實現；
- 支持無鎖操作。

# 四、使用場景

## 計數器

可以對 String 進行自增自減運算，從而實現計數器功能。

Redis 這種內存型數據庫的讀寫性能非常高，很適合存儲頻繁讀寫的計數量。

## 緩存

將熱點數據放到內存中，設置內存的最大使用量以及淘汰策略來保證緩存的命中率。

## 查找表

例如 DNS 記錄就很適合使用 Redis 進行存儲。

查找表和緩存類似，也是利用了 Redis 快速的查找特性。但是查找表的內容不能失效，而緩存的內容可以失效，因為緩存不作為可靠的數據來源。

## 消息隊列

List 是一個雙向鏈表，可以通過 lpush 和 rpop 寫入和讀取消息

不過最好使用 Kafka、RabbitMQ 等消息中間件。

## 會話緩存

可以使用 Redis 來統一存儲多臺應用服務器的會話信息。

當應用服務器不再存儲用戶的會話信息，也就不再具有狀態，一個用戶可以請求任意一個應用服務器，從而更容易實現高可用性以及可伸縮性。

## 分佈式鎖實現

在分佈式場景下，無法使用單機環境下的鎖來對多個節點上的進程進行同步。

可以使用 Redis 自帶的 SETNX 命令實現分佈式鎖，除此之外，還可以使用官方提供的 RedLock 分佈式鎖實現。

## 其它

Set 可以實現交集、並集等操作，從而實現共同好友等功能。

ZSet 可以實現有序性操作，從而實現排行榜等功能。

# 五、Redis 與 Memcached

兩者都是非關係型內存鍵值數據庫，主要有以下不同：

## 數據類型

Memcached 僅支持字符串類型，而 Redis 支持五種不同的數據類型，可以更靈活地解決問題。

## 數據持久化

Redis 支持兩種持久化策略：RDB 快照和 AOF 日誌，而 Memcached 不支持持久化。

## 分佈式

Memcached 不支持分佈式，只能通過在客戶端使用一致性哈希來實現分佈式存儲，這種方式在存儲和查詢時都需要先在客戶端計算一次數據所在的節點。

Redis Cluster 實現了分佈式的支持。

## 內存管理機制

- 在 Redis 中，並不是所有數據都一直存儲在內存中，可以將一些很久沒用的 value 交換到磁盤，而 Memcached 的數據則會一直在內存中。

- Memcached 將內存分割成特定長度的塊來存儲數據，以完全解決內存碎片的問題。但是這種方式會使得內存的利用率不高，例如塊的大小為 128 bytes，只存儲 100 bytes 的數據，那麼剩下的 28 bytes 就浪費掉了。

# 六、鍵的過期時間

Redis 可以為每個鍵設置過期時間，當鍵過期時，會自動刪除該鍵。

對於散列表這種容器，只能為整個鍵設置過期時間（整個散列表），而不能為鍵裡面的單個元素設置過期時間。

# 七、數據淘汰策略

可以設置內存最大使用量，當內存使用量超出時，會施行數據淘汰策略。

Redis 具體有 6 種淘汰策略：

| 策略 | 描述 |
| :--: | :--: |
| volatile-lru | 從已設置過期時間的數據集中挑選最近最少使用的數據淘汰 |
| volatile-ttl | 從已設置過期時間的數據集中挑選將要過期的數據淘汰 |
|volatile-random | 從已設置過期時間的數據集中任意選擇數據淘汰 |
| allkeys-lru | 從所有數據集中挑選最近最少使用的數據淘汰 |
| allkeys-random | 從所有數據集中任意選擇數據進行淘汰 |
| noeviction | 禁止驅逐數據 |

作為內存數據庫，出於對性能和內存消耗的考慮，Redis 的淘汰算法實際實現上並非針對所有 key，而是抽樣一小部分並且從中選出被淘汰的 key。

使用 Redis 緩存數據時，為了提高緩存命中率，需要保證緩存數據都是熱點數據。可以將內存最大使用量設置為熱點數據佔用的內存量，然後啟用 allkeys-lru 淘汰策略，將最近最少使用的數據淘汰。

Redis 4.0 引入了 volatile-lfu 和 allkeys-lfu 淘汰策略，LFU 策略通過統計訪問頻率，將訪問頻率最少的鍵值對淘汰。

# 八、持久化

Redis 是內存型數據庫，為了保證數據在斷電後不會丟失，需要將內存中的數據持久化到硬盤上。

## RDB 持久化

將某個時間點的所有數據都存放到硬盤上。

可以將快照複製到其它服務器從而創建具有相同數據的服務器副本。

如果系統發生故障，將會丟失最後一次創建快照之後的數據。

如果數據量很大，保存快照的時間會很長。

## AOF 持久化

將寫命令添加到 AOF 文件（Append Only File）的末尾。

使用 AOF 持久化需要設置同步選項，從而確保寫命令同步到磁盤文件上的時機。這是因為對文件進行寫入並不會馬上將內容同步到磁盤上，而是先存儲到緩衝區，然後由操作系統決定什麼時候同步到磁盤。有以下同步選項：

| 選項 | 同步頻率 |
| :--: | :--: |
| always | 每個寫命令都同步 |
| everysec | 每秒同步一次 |
| no | 讓操作系統來決定何時同步 |

- always 選項會嚴重減低服務器的性能；
- everysec 選項比較合適，可以保證系統崩潰時只會丟失一秒左右的數據，並且 Redis 每秒執行一次同步對服務器性能幾乎沒有任何影響；
- no 選項並不能給服務器性能帶來多大的提升，而且也會增加系統崩潰時數據丟失的數量。

隨著服務器寫請求的增多，AOF 文件會越來越大。Redis 提供了一種將 AOF 重寫的特性，能夠去除 AOF 文件中的冗餘寫命令。

# 九、事務

一個事務包含了多個命令，服務器在執行事務期間，不會改去執行其它客戶端的命令請求。

事務中的多個命令被一次性發送給服務器，而不是一條一條發送，這種方式被稱為流水線，它可以減少客戶端與服務器之間的網絡通信次數從而提升性能。

Redis 最簡單的事務實現方式是使用 MULTI 和 EXEC 命令將事務操作包圍起來。

# 十、事件

Redis 服務器是一個事件驅動程序。

## 文件事件

服務器通過套接字與客戶端或者其它服務器進行通信，文件事件就是對套接字操作的抽象。

Redis 基於 Reactor 模式開發了自己的網絡事件處理器，使用 I/O 多路複用程序來同時監聽多個套接字，並將到達的事件傳送給文件事件分派器，分派器會根據套接字產生的事件類型調用相應的事件處理器。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/9ea86eb5-000a-4281-b948-7b567bd6f1d8.png" width=""/> </div><br>

## 時間事件

服務器有一些操作需要在給定的時間點執行，時間事件是對這類定時操作的抽象。

時間事件又分為：

- 定時事件：是讓一段程序在指定的時間之內執行一次；
- 週期性事件：是讓一段程序每隔指定時間就執行一次。

Redis 將所有時間事件都放在一個無序鏈表中，通過遍歷整個鏈表查找出已到達的時間事件，並調用相應的事件處理器。

## 事件的調度與執行

服務器需要不斷監聽文件事件的套接字才能得到待處理的文件事件，但是不能一直監聽，否則時間事件無法在規定的時間內執行，因此監聽時間應該根據距離現在最近的時間事件來決定。

事件調度與執行由 aeProcessEvents 函數負責，偽代碼如下：

```python
def aeProcessEvents():
    # 獲取到達時間離當前時間最接近的時間事件
    time_event = aeSearchNearestTimer()
    # 計算最接近的時間事件距離到達還有多少毫秒
    remaind_ms = time_event.when - unix_ts_now()
    # 如果事件已到達，那麼 remaind_ms 的值可能為負數，將它設為 0
    if remaind_ms < 0:
        remaind_ms = 0
    # 根據 remaind_ms 的值，創建 timeval
    timeval = create_timeval_with_ms(remaind_ms)
    # 阻塞並等待文件事件產生，最大阻塞時間由傳入的 timeval 決定
    aeApiPoll(timeval)
    # 處理所有已產生的文件事件
    procesFileEvents()
    # 處理所有已到達的時間事件
    processTimeEvents()
```

將 aeProcessEvents 函數置於一個循環裡面，加上初始化和清理函數，就構成了 Redis 服務器的主函數，偽代碼如下：

```python
def main():
    # 初始化服務器
    init_server()
    # 一直處理事件，直到服務器關閉為止
    while server_is_not_shutdown():
        aeProcessEvents()
    # 服務器關閉，執行清理操作
    clean_server()
```

從事件處理的角度來看，服務器運行流程如下：

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/c0a9fa91-da2e-4892-8c9f-80206a6f7047.png" width="350"/> </div><br>

# 十一、複製

通過使用 slaveof host port 命令來讓一個服務器成為另一個服務器的從服務器。

一個從服務器只能有一個主服務器，並且不支持主主複製。

## 連接過程

1. 主服務器創建快照文件，發送給從服務器，並在發送期間使用緩衝區記錄執行的寫命令。快照文件發送完畢之後，開始向從服務器發送存儲在緩衝區中的寫命令；

2. 從服務器丟棄所有舊數據，載入主服務器發來的快照文件，之後從服務器開始接受主服務器發來的寫命令；

3. 主服務器每執行一次寫命令，就向從服務器發送相同的寫命令。

## 主從鏈

隨著負載不斷上升，主服務器可能無法很快地更新所有從服務器，或者重新連接和重新同步從服務器將導致系統超載。為了解決這個問題，可以創建一箇中間層來分擔主服務器的複製工作。中間層的服務器是最上層服務器的從服務器，又是最下層服務器的主服務器。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/395a9e83-b1a1-4a1d-b170-d081e7bb5bab.png" width="600"/> </div><br>

# 十二、Sentinel

Sentinel（哨兵）可以監聽集群中的服務器，並在主服務器進入下線狀態時，自動從從服務器中選舉出新的主服務器。

# 十三、分片

分片是將數據劃分為多個部分的方法，可以將數據存儲到多臺機器裡面，這種方法在解決某些問題時可以獲得線性級別的性能提升。

假設有 4 個 Redis 實例 R0，R1，R2，R3，還有很多表示用戶的鍵 user:1，user:2，... ，有不同的方式來選擇一個指定的鍵存儲在哪個實例中。

- 最簡單的方式是範圍分片，例如用戶 id 從 0\~1000 的存儲到實例 R0 中，用戶 id 從 1001\~2000 的存儲到實例 R1 中，等等。但是這樣需要維護一張映射範圍表，維護操作代價很高。
- 還有一種方式是哈希分片，使用 CRC32 哈希函數將鍵轉換為一個數字，再對實例數量求模就能知道應該存儲的實例。

根據執行分片的位置，可以分為三種分片方式：

- 客戶端分片：客戶端使用一致性哈希等算法決定鍵應當分佈到哪個節點。
- 代理分片：將客戶端請求發送到代理上，由代理轉發請求到正確的節點上。
- 服務器分片：Redis Cluster。

# 十四、一個簡單的論壇系統分析

該論壇系統功能如下：

- 可以發佈文章；
- 可以對文章進行點贊；
- 在首頁可以按文章的發佈時間或者文章的點贊數進行排序顯示。

## 文章信息

文章包括標題、作者、贊數等信息，在關係型數據庫中很容易構建一張表來存儲這些信息，在 Redis 中可以使用 HASH 來存儲每種信息以及其對應的值的映射。

Redis 沒有關係型數據庫中的表這一概念來將同種類型的數據存放在一起，而是使用命名空間的方式來實現這一功能。鍵名的前面部分存儲命名空間，後面部分的內容存儲 ID，通常使用 : 來進行分隔。例如下面的 HASH 的鍵名為 article:92617，其中 article 為命名空間，ID 為 92617。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/7c54de21-e2ff-402e-bc42-4037de1c1592.png" width="400"/> </div><br>

## 點贊功能

當有用戶為一篇文章點贊時，除了要對該文章的 votes 字段進行加 1 操作，還必須記錄該用戶已經對該文章進行了點贊，防止用戶點贊次數超過 1。可以建立文章的已投票用戶集合來進行記錄。

為了節約內存，規定一篇文章發佈滿一週之後，就不能再對它進行投票，而文章的已投票集合也會被刪除，可以為文章的已投票集合設置一個一週的過期時間就能實現這個規定。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/485fdf34-ccf8-4185-97c6-17374ee719a0.png" width="400"/> </div><br>

## 對文章進行排序

為了按發佈時間和點贊數進行排序，可以建立一個文章發佈時間的有序集合和一個文章點贊數的有序集合。（下圖中的 score 就是這裡所說的點贊數；下面所示的有序集合分值並不直接是時間和點贊數，而是根據時間和點贊數間接計算出來的）

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/f7d170a3-e446-4a64-ac2d-cb95028f81a8.png" width="800"/> </div><br>

# 參考資料

- Carlson J L. Redis in Action[J]. Media.johnwiley.com.au, 2013.
- [黃健宏. Redis 設計與實現 [M]. 機械工業出版社, 2014.](http://redisbook.com/index.html)
- [REDIS IN ACTION](https://redislabs.com/ebook/foreword/)
- [Skip Lists: Done Right](http://ticki.github.io/blog/skip-lists-done-right/)
- [論述 Redis 和 Memcached 的差異](http://www.cnblogs.com/loveincode/p/7411911.html)
- [Redis 3.0 中文版- 分片](http://wiki.jikexueyuan.com/project/redis-guide)
- [Redis 應用場景](http://www.scienjus.com/redis-use-case/)
- [Using Redis as an LRU cache](https://redis.io/topics/lru-cache)




# 微信公眾號


更多精彩內容將發佈在微信公眾號 CyC2018 上，你也可以在公眾號後臺和我交流學習和求職相關的問題。另外，公眾號提供了該項目的 PDF 等離線閱讀版本，後臺回覆 "下載" 即可領取。公眾號也提供了一份技術面試複習大綱，不僅系統整理了面試知識點，而且標註了各個知識點的重要程度，從而幫你理清多而雜的面試知識點，後臺回覆 "大綱" 即可領取。我基本是按照這個大綱來進行復習的，對我拿到了 BAT 頭條等 Offer 起到很大的幫助。你們完全可以和我一樣根據大綱上列的知識點來進行復習，就不用看很多不重要的內容，也可以知道哪些內容很重要從而多安排一些複習時間。


<br><div align="center"><img width="320px" src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/other/公眾號海報6.png"></img></div>
