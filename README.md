<div align="center">

> **⚠️ 本仓库包含本人2026届毕业设计源码。为符合学术规范，答辩结束前仅当面对面试官开放。**

# 🎵 B1ueMusic 云原生湖仓一体音乐推荐系统 🎧

![Flink](https://img.shields.io/badge/Apache%20Flink-1.20.2-orange)
![Java](https://img.shields.io/badge/Java-11-blue)
![Fluss](https://img.shields.io/badge/Fluss-0.8.0-green)
![Paimon](https://img.shields.io/badge/Paimon-1.2.0-yellow)
![StarRocks](https://img.shields.io/badge/StarRocks-3.x-red)
![Spark](https://img.shields.io/badge/Apache%20Spark-3.5.6-purple)
![LanceDB](https://img.shields.io/badge/LanceDB-0.5.0-blueviolet)

基于云原生湖仓一体架构的智能音乐推荐平台 🚀

</div>

---

## 📖 项目简介

B1ueMusic 是一个基于湖仓一体架构的智能音乐推荐平台，集成了实时数据处理、用户画像计算、AI向量化和向量检索等核心能力。系统采用分层架构，实现了从数据采集、实时处理到智能推荐的全链路能力。

**核心特性** ✨

- 🔥 **实时处理**：基于 Flink 实现毫秒级数据流处理
- 📊 **分层架构**：ODS/DWD/DWS/DIM 标准数仓分层
- 🚀 **Fluss 存储**：使用 Fluss 作为主要实时存储（替代 HBase/Redis）
- 💾 **湖仓一体**：集成 Paimon 实现湖仓一体化
- ☁️ **云原生架构**：纯云原生设计，部署于阿里云 ACS 平台
- 🔀 **存算分离**：所有组件（Fluss/Paimon/StarRocks）均采用存算分离架构，计算与存储完全解耦
- 📦 **统一存储**：基于阿里云 OSS 实现统一数据存储，所有组件共享 OSS 数据湖
- 🔄 **CDC 支持**：PostgreSQL 实时变更数据捕获
- 🎯 **维度建模**：完善的维度建模和事实表设计
- 👤 **用户画像**：实时+离线双重画像特征计算体系
- 🧠 **AI 向量化**：基于 Embedding 模型的用户/歌曲向量化存储
- 🔍 **向量检索**：LanceDB 向量数据库支持高效相似度搜索
- 🌍 **弹性部署**：支持 K8s 容器化部署，自动扩缩容

---

## 🏗️ 系统架构
<img width="1351" height="742" alt="image" src="https://github.com/user-attachments/assets/f1976b65-16f3-4534-a4ab-9a7469dc0f32" />


---

## 📂 项目结构

```
B1ueMusic2.5/
├── Project_Common/          # 公共模块
│   └── src/main/java/
│       ├── base/           # 基础类（BaseApp, BaseAppV2）
│       ├── common/         # 公共常量配置（Common.java）
│       └── util/           # 工具类（FlinkSourceUtil, FlinkSinkUtil, DateFormatUtil, PaimonUtil）
│
├── ODS/                    # 原始数据层（Operational Data Store）
│   └── src/main/
│       ├── java/fluss/
│       │   ├── ods_content_play_log.java      # 播放日志实时入湖
│       │   ├── ods_content_collect_log.java   # 收藏日志实时入湖
│       │   ├── ods_content_comment_log.java   # 评论日志实时入湖
│       │   ├── ods_content_follow_log.java    # 关注日志实时入湖
│       │   └── ods_user_login_log.java       # 登录日志实时入湖
│       └── sql/spark/
│           ├── ods_trade_album_order_di.sql      # 专辑订单离线入湖
│           ├── ods_trade_cancel_order_di.sql     # 取消订单离线入湖
│           └── ods_trade_vip_order_di.sql        # VIP订单离线入湖
│
├── DWD/                    # 明细数据层（Data Warehouse Detail）
│   └── src/main/
│       ├── java/fluss/
│       │   ├── dwd_content_play_rt.java      # 播放交互事实表ETL
│       │   ├── dwd_content_collect_rt.java   # 收藏交互事实表ETL
│       │   ├── dwd_content_follow_rt.java    # 关注交互事实表ETL
│       │   └── dwd_user_login_rt.java        # 登录交互事实表ETL
│       └── sql/sparksql/
│           ├── dwd_concet_song_play_di.sql        # 歌曲播放明细表
│           ├── dwd_trade_album_order_di.sql       # 专辑订单明细表
│           ├── dwd_trade_cancel_order_di.sql      # 取消订单明细表
│           └── dwd_trade_vip_order_di.sql         # VIP订单明细表
│
├── DWS/                    # 汇总数据层（Data Warehouse Service）
│   └── src/main/
│       ├── java/app/
│       │   ├── dws_user_profile_rt.java          # 用户画像实时特征计算
│       │   ├── dws_traffic_pop_song.java         # 热门歌曲统计
│       │   ├── dws_traffic_pop_province.java     # 热门省份统计
│       │   └── dws_traffic_singer_follow.java    # 歌手关注统计
│       ├── python/lancedb/
│       │   ├── dws_user_profile_vector_di.py     # 用户画像向量化
│       │   └── dws_song_profile_vector_di.py     # 歌曲画像向量化
│       └── sql/sparksql/
│           ├── dws_profile_di.sql                 # 用户画像表
│           ├── dws_traffic_user_1d_di.sql         # 用户流量统计日表
│           ├── dws_sales_revenue_1d_di.sql        # 销售营收统计日表
│           ├── dws_trade_revenue_di.sql           # 交易营收统计
│           └── dws_user_profile_offline_update.sql # 用户画像离线更新
│
├── DIM/                    # 维度层（Dimension）
│   └── src/main/java/
│       ├── Fluss/
│       │   └── dim_app_fluss.java               # 维度数据（Fluss）
│       └── Paimon/
│           └── dim_app.java                     # 维度数据（Paimon + CDC）
│
├── INIT/                   # 初始化模块
│   └── src/main/
│       ├── ddl/
│       │   ├── fluss/fluss_ddl.java             # Fluss 表结构初始化
│       │   ├── paimon/paimon_ddl.sql            # Paimon 离线表DDL定义
│       │   └── pg/pg_ddl.sql                    # PostgreSQL 表结构初始化
│       └── yaml/
│           ├── flink.yaml                       # Flink 配置
│           ├── fluss.yaml                       # Fluss 配置
│           ├── kafka.yaml                       # Kafka 配置
│           ├── starrocks.yaml                   # StarRocks 配置
│           ├── spark.yaml                       # Spark 配置
│           └── postgre.yaml                     # PostgreSQL 配置
│
└── pom.xml                 # Maven 父工程配置
```

---

## 🔧 技术栈

| 技术 | 版本 | 用途 |
|------|------|------|
| **Apache Flink** | 1.20.2 | 流处理核心引擎 |
| **Apache Spark** | 3.5.6 | 离线批处理引擎 |
| **Java** | 11 | 开发语言 |
| **Python** | 3.* | 向量化处理脚本 |
| **Fluss** | 0.8.0-incubating | 实时存储系统 |
| **Paimon** | 1.2.0 | 湖仓存储 |
| **StarRocks** | 3.x | OLAP 分析引擎 |
| **LanceDB** | 0.5.0 | 向量数据库 |
| **BAAI/bge-m3** | - | Embedding 模型（1024维） |
| **Kafka** | 3.x | 消息队列 |
| **PostgreSQL** | - | 业务数据库 & CDC 源 |
| **阿里云 OSS** | - | 对象存储（支持 S3 协议） |
| **Maven** | 3.x | 项目管理 |
| **Kubernetes** | 1.32.7-aliyun.1 | 容器编排（阿里云 ACS） |

---

## 📊 数据分层说明

### ODS 层（原始数据层）

**职责**：保持与源数据一致，不做任何处理，直接入湖

**实时表**（Fluss 存储）📝：
- `ods_content_play_log` - 播放日志
- `ods_content_collect_log` - 收藏日志
- `ods_content_comment_log` - 评论日志
- `ods_content_follow_log` - 关注日志
- `ods_user_login_log` - 登录日志
- `ods_dirty` - 脏数据表

**离线表**（Spark SQL）📝：
- `ods_trade_album_order_di` - 专辑订单
- `ods_trade_cancel_order_di` - 取消订单
- `ods_trade_vip_order_di` - VIP订单

**特性**：
- ✅ 自动分区（按天）
- ✅ 脏数据隔离处理
- ✅ JSON 解析容错
- ✅ Fluss 数据湖同步

---

### DWD 层（明细数据层）

**职责**：对 ODS 层数据进行清洗、规范化、维度关联

**实时表**（Fluss 存储）📝：
- `dwd_content_play_rt` - 播放事实表（关联用户、歌曲、歌手维度）
- `dwd_content_collect_rt` - 收藏事实表
- `dwd_content_follow_rt` - 关注事实表
- `dwd_user_login_rt` - 登录事实表

**离线表**（Paimon 存储）📝：
- `dwd_concet_song_play_di` - 歌曲播放明细表
  - 分区字段：etl_load_date
  - 主键：(user_id, start_ts)
  - 分桶：user_id（2个桶）
  - 存储格式：Parquet
- `dwd_trade_album_order_di` - 专辑订单明细表
  - 分区字段：etl_load_date
  - 主键：order_id
  - 分桶：order_id（2个桶）
  - 存储格式：Parquet
- `dwd_trade_cancel_order_di` - 取消订单明细表
  - 分区字段：etl_load_date
  - 主键：order_cancel_id
  - 分桶：order_cancel_id（2个桶）
  - 存储格式：Parquet
- `dwd_trade_vip_order_di` - VIP订单明细表
  - 分区字段：etl_load_date
  - 主键：order_id
  - 分桶：order_id（2个桶）
  - 存储格式：Parquet

**存储特点**：
- ✅ 动态分区（按 etl_load_date）
- ✅ 分桶优化（2个桶）
- ✅ Parquet 列式存储
- ✅ 主键约束（NOT ENFORCED）

**维度关联**：
```sql
-- 示例：播放事实表关联维度
SELECT 
    o.user_id,
    d1.user_name,          -- 用户维度
    d1.gender as user_gender,
    o.song_id,
    d2.song_name,          -- 歌曲维度
    d2.singer_id,
    d3.singer_name,        -- 歌手维度
    d3.country
FROM ods_content_play_log o
LEFT JOIN dim_user FOR SYSTEM_TIME AS OF o.ptime d1
    ON o.user_id = d1.user_id
LEFT JOIN dim_song FOR SYSTEM_TIME AS OF o.ptime d2
    ON o.song_id = d2.song_id
LEFT JOIN dim_singer FOR SYSTEM_TIME AS OF o.ptime d3
    ON CAST(d2.singer_id AS BIGINT) = d3.singer_id
```

---

### DWS 层（汇总数据层）

**职责**：基于 DWD 层进行轻度聚合，生成业务指标

**核心应用** 🚀：

1. **用户画像实时计算**（`dws_user_profile_rt.java`）
   - 使用滑动窗口（10秒步长，20分钟长度）
   - 计算用户歌曲类型偏好比例
   - 记录最近收听的100首歌曲
   - 实时更新最后活跃时间
   - 数据输出到 Fluss 实时存储（`dws_user_profile_rt`）

2. **用户画像离线更新**（`dws_user_profile_offline_update.sql`）
   - 基于近半年历史数据计算
   - 计算歌曲类型偏好占比（JSON格式）
   - 统计常听歌曲列表（Top 20）
   - 计算平均播放时长
   - 分析活跃时间段（凌晨/上午/下午/晚上）
   - 使用 StarRocks 部分更新机制

3. **向量画像生成**（Python脚本）
   - **用户向量化**（`dws_user_profile_vector_di.py`）
     - 流水线架构：StarRocks → Embedding → LanceDB
     - 异步并发向量化（4个并发 worker）
     - 使用 BAAI/bge-m3 模型生成1024维向量
     - 支持几十万级数据处理
     - 存储于 LanceDB（基于 OSS）
   - **歌曲向量化**（`dws_song_profile_vector_di.py`）
     - 基于歌曲元数据向量化
     - 特征：歌名、语言、标签、时长、发行时间等
     - 支持向量相似度检索

4. **流量统计**
   - `dws_traffic_user_1d_di.sql` - 每日用户流量统计
   - `dws_traffic_pop_song.java` - 热门歌曲排行
   - `dws_traffic_pop_province.java` - 热门省份分布
   - `dws_traffic_singer_follow.java` - 歌手关注统计

5. **营收统计**
   - `dws_trade_revenue_di.sql` - 交易营收统计
   - `dws_sales_revenue_1d_di.sql` - 销售营收日统计

6. **用户画像表**（`dws_profile_di.sql`）
   - 存储完整的用户画像数据
   - 包含实时和离线特征

---

### DIM 层（维度层）

**职责**：存储维度数据，提供维度查询服务

**核心维度表** 📚：
- `dim_user` - 用户维度（用户ID、姓名、性别、生日、VIP状态等）
- `dim_song` - 歌曲维度（歌曲ID、名称、类型、时长、歌手ID等）
- `dim_singer` - 歌手维度（歌手ID、姓名、性别、国家、粉丝数等）

**数据来源**：
- PostgreSQL CDC 实时同步（通过 Paimon）
- Fluss 维度表

**CDC 同步流程**：
1. PostgreSQL Debezium CDC 捕获变更
2. Flink 读取 CDC 数据流
3. 转换为 Paimon CdcMultiplexRecord 格式
4. 通过 Paimon 多表 Sink 实时写入

---

## 🎯 推荐算法

### 智能推荐歌单生成流程

本系统采用**向量检索 + 实时特征融合**的混合推荐算法，通过以下步骤为用户生成个性化推荐歌单：

**推荐流程** 🎵：

```
用户请求 (user_id)
       ↓
[1] 从 LanceDB 获取用户长期画像向量
       ↓
[2] 向量相似度检索（与歌曲向量表匹配）
       ↓
获取 500 首候选歌曲
       ↓
[3] 从 Fluss 获取用户实时画像特征
   - 最近收听的 100 首歌曲
   - 实时歌曲类型偏好比例
       ↓
[4] 过滤处理
   - 排除用户最近听过的歌曲
   - 排除用户已收藏的歌曲
       ↓
[5] 加权打分
   - 向量相似度分数（长期兴趣）
   - 实时偏好加权（基于用户当前歌曲类型偏好）
       ↓
[6] 最终排序，输出 Top 10 推荐歌曲
```

**算法特点** ✨：

1. **长期兴趣匹配**：基于用户近半年历史行为生成的长期画像向量，通过 LanceDB 的 ANN（近似最近邻）算法快速检索相似歌曲

2. **实时行为过滤**：结合 Fluss 实时画像中的 `fav_song_ids_rt`（最近100首歌曲），过滤掉用户近期已播放的歌曲，避免重复推荐

3. **实时偏好加权**：根据实时画像中的 `preference_ratio_rt`（歌曲类型偏好比例）对候选歌曲进行二次加权：
   ```python
   # 示例加权逻辑
   实时偏好 = {"流行": "0.4521", "摇滚": "0.3200", "民谣": "0.2279"}
   
   最终分数 = 向量相似度分数 × (1 + 0.3 × 类型偏好权重)
   
   # 如果歌曲类型是"流行"，额外加权 0.3 × 0.4521
   ```

4. **多阶段筛选**：
   - **第一阶段**：向量检索（500首候选）
   - **第二阶段**：实时过滤（排除最近播放）
   - **第三阶段**：加权排序（融合长期+实时偏好）
   - **最终输出**：Top 10 高质量推荐

**技术优势** 🚀：

- 🎯 **精准度高**：结合长期兴趣和实时行为，推荐更符合用户当前需求
- ⚡ **响应快速**：LanceDB 向量检索毫秒级响应，实时画像查询延迟低
- 🔄 **实时更新**：用户画像实时更新，推荐结果动态调整
- 🧠 **语义理解**：基于 Embedding 的向量检索，捕获深层次音乐偏好

**数据流** 📊：

```
LanceDB (长期画像向量)      Fluss (实时画像特征)
        ↓                          ↓
    向量相似度匹配            实时行为+偏好
        ↓                          ↓
    候选歌曲集(500首) ←───────── 加权打分
              ↓
        排序筛选
              ↓
      Top 10 推荐
```

---

## 🎯 核心功能模块

### 1. 实时数据采集 📡

```java
// 示例：播放日志实时入湖
DataStreamSource<String> kafkaSource = env.fromSource(
    FlinkSourceUtil.getKafkaSource("play_log", "g1"),
    WatermarkStrategy.noWatermarks(),
    "kafkaPlaySource"
);

// 数据清洗并写入 Fluss
SingleOutputStreamOperator<RowData> result = kafkaSource
    .process(new PlayProcess(Dirty))
    .sinkTo(FlinkSinkUtil.getFlussSink("b1uemusic", "ods_content_play_log", false));
```

**数据格式示例**：
```json
{
  "common": {
    "song_id": 1006,
    "lead_song_id": "1005"
  },
  "properties": {
    "province": "广东",
    "mac_id": "ac60709dse1185-788163",
    "channel": "APP",
    "ip": "0.0.0.0",
    "user_id": 10058
  },
  "event_type": "play",
  "ts": 1758384000180
}
```

**功能特点**：
- ✅ Kafka 消息实时消费
- ✅ JSON 格式解析与转换
- ✅ 脏数据自动隔离（`ods_dirty` 表）
- ✅ Fluss 实时入湖
- ✅ 自动分区（`etl_load_date`）

---

### 2. 用户画像体系 👤

#### 2.1 实时用户画像（`dws_user_profile_rt.java`）

**计算逻辑**：
- 使用滑动窗口（10秒步长，20分钟长度）统计用户行为
- 计算歌曲类型偏好比例（JSON 格式存储）
- 记录最近收听的100首歌曲
- 实时更新最后活跃时间
- 数据输出到 Fluss 实时存储表

**技术特点**：
- 基于 Flink 的滑动窗口聚合
- 使用自定义聚合器统计类型偏好
- 使用 `FOR SYSTEM_TIME AS OF` 实现维度关联
- 支持 Exactly-Once 语义

**数据示例**：
```json
{
  "user_id": 10058,
  "preference_ratio_rt": {
    "流行": "0.4521",
    "摇滚": "0.3200",
    "民谣": "0.2279"
  },
  "fav_song_ids_rt": [1001, 1002, 1003, ...],
  "last_active_time_rt": "2026-02-22 11:30:00"
}
```

#### 2.2 离线用户画像更新（`dws_user_profile_offline_update.sql`）

**计算逻辑**：
- 基于近半年历史数据（180天）进行统计分析
- 计算歌曲类型偏好占比，输出为 JSON 格式
- 统计用户常听歌曲列表（播放次数 Top 20）
- 计算平均播放时长（分钟）
- 分析用户活跃时间段（凌晨/上午/下午/晚上）
- 使用 StarRocks 的部分更新机制（DELETE + INSERT）

**SQL 逻辑**：
```sql
-- 计算类型偏好
concat('{',
       concat_ws(',',
           collect_list(
               concat('"', song_type, '":', round(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER (PARTITION BY user_id), 2))
           )
       ),
       '}') AS preference_ratio_6m

-- 获取 Top 20 歌曲
slice(collect_list(song_id), 1, 20) AS song_ids_6m

-- 分析活跃时间段
CASE
    WHEN play_hour >= 0 AND play_hour < 6 THEN '凌晨'
    WHEN play_hour >= 6 AND play_hour < 12 THEN '上午'
    WHEN play_hour >= 12 AND play_hour < 18 THEN '下午'
    WHEN play_hour >= 18 AND play_hour < 24 THEN '晚上'
END AS period
```

**数据示例**：
```json
{
  "user_id": 10058,
  "preference_ratio_6m": {"流行":35.2,"摇滚":28.5,"民谣":25.8,"电子":10.5},
  "song_ids_6m": [1001, 1005, 1008, ...],
  "avg_play_duration_6m": 245.3,
  "active_periods_6m": "晚上"
}
```

#### 2.3 向量化画像 🧠

**用户向量化**（`dws_user_profile_vector_di.py`）：
- 流水线架构：StarRocks → Embedding → LanceDB
- 异步并发向量化（4个并发 worker）
- 使用 BAAI/bge-m3 模型生成1024维向量
- 支持几十万级数据处理
- 存储于 LanceDB（基于 OSS）

**画像文本构建**：
```python
def build_profile_text(row: dict) -> str:
    return (
        f"用户 {row.get('user_name') or '未知'}，"
        f"用户ID {row['user_id']}，"
        f"年龄 {row.get('age') or '未知'} 岁，"
        f"性别 {row.get('gender') or '未知'}，"
        f"注册时间 {row.get('register_time') or '未知'}。"
        f"近半年音乐偏好：{pref_str}。"
        f"近半年常听歌曲共 {len(song_ids)} 首。"
        f"近半年平均播放时长 {row.get('avg_play_duration_6m') or 0} 分钟。"
        f"活跃时间段：{row.get('active_periods_6m') or '未知'}。"
    )
```

**向量化流程**：
1. 从 StarRocks 流式读取画像数据（游标方式，避免内存溢出）
2. 将数据分批（每批32条）发送到 Embedding API
3. 异步并发处理，支持重试机制
4. 将向量批量写入 LanceDB（每批5000条）
5. 使用 Upsert 机制更新已有数据

**性能优化**：
- 异步流水线设计，读写分离
- 背压控制（队列最大大小限制）
- 批量写入减少网络开销
- 支持断点续传和错误重试

---

### 3. 维度关联 🔗

**实现方式**：
- 使用 Flink SQL 的 `FOR SYSTEM_TIME AS OF` 语法
- 支持实时维度变更
- 维度数据缓存优化

```sql
SELECT *
FROM ods_content_play_log o
LEFT JOIN dim_user FOR SYSTEM_TIME AS OF o.ptime d1
    ON o.user_id = d1.user_id
LEFT JOIN dim_song FOR SYSTEM_TIME AS OF o.ptime d2
    ON o.song_id = d2.song_id
```

---

### 4. CDC 数据同步 🔄

**PostgreSQL CDC → Paimon**（`dim_app.java`）：

```java
// 1. 从 PostgreSQL 读取 CDC 数据
DataStreamSource<String> pgDimDS = env.fromSource(
    FlinkSourceUtil.getPostgresSource(
        "flink_dim_slot",
        "public.ods_user", "public.ods_singer", "public.ods_song"
    ),
    WatermarkStrategy.noWatermarks(),
    "pg_source"
);

// 2. 转换为 CdcMultiplexRecord
SingleOutputStreamOperator<CdcMultiplexRecord> result = pgDimDS
    .process(new CdcRecordConverter());

// 3. 输出到 Paimon 多表
FlinkCdcMultiTableSink sink = new FlinkCdcMultiTableSink(
    catalogLoader, 1, MemorySize.parse("512mb"), 0.5,
    MemorySize.parse("256mb"), "flink_cdc", false, null
);
sink.sinkFrom(result);
```

**CDC 数据格式**：
```json
{
  "before": {"user_id": 10058, "user_name": "张三"},
  "after": {"user_id": 10058, "user_name": "李四"},
  "source": {"db": "b1uemusic", "table": "ods_user"},
  "op": "u",
  "ts_ms": 1758384000180
}
```

**操作类型**：
- `c` - CREATE（插入）
- `u` - UPDATE（更新）
- `d` - DELETE（删除）

---


## 📖 使用说明

### 配置文件

主要配置在 `Project_Common/src/main/java/common/Common.java` 中：

```java
// Kafka 配置
public static String KafkaBootstrapServers = "my-cluster-kafka-bootstrap.kafka.svc:9092";

// StarRocks 配置
public static String StarRocksJdbcUrl = "jdbc:mysql://sr-cluster-fe-service.starrocks:9030";
public static String StarRocksFeNodes = "sr-cluster-fe-service.starrocks:8030";
public static String StarRocksUsername = "root";
public static String StarRocksPassword = "";

// OSS 配置
public static String OssAccessKeyId = System.getenv("OSS_ACCESS_KEY_ID");
public static String OssAccessKeySecret = System.getenv("OSS_ACCESS_KEY_SECRET");
public static String OssEndpoint = "oss-cn-hongkong.aliyuncs.com";
public static String WarehousePath = "oss://b1ue-osss/paimon/warehouse";
public static String CheckPointPath = "oss://b1ue-osss/Flink_CheckPoint/";

// Fluss 配置
public static String Fluss_BootstrapServers = "fluss-coordinator.fluss:9123";

// PostgreSQL 配置
public static String Pg_host = "postgres.postgresql";
public static int Pg_port = 5432;
public static String Pg_DataBase = "b1uemusic";
```

### 配置 YAML 文件

所有服务配置文件位于 `INIT/src/main/yaml/` 目录：
- `flink.yaml` - Flink 配置
- `fluss.yaml` - Fluss 配置
- `kafka.yaml` - Kafka 配置
- `starrocks.yaml` - StarRocks 配置
- `spark.yaml` - Spark 配置
- `postgre.yaml` - PostgreSQL 配置

---

## 🎓 设计理念

### 1. 分层架构

采用标准数仓分层设计，职责清晰：
- **ODS**：原始数据，不做处理
- **DWD**：明细数据，清洗关联
- **DWS**：汇总数据，轻度聚合
- **DIM**：维度数据，共享服务

### 2. 云原生架构

- 纯云原生设计，部署于阿里云 ACS（Application Computing Service）
- 容器化部署，支持自动扩缩容
- 服务网格（Service Mesh）友好
- 声明式配置与 GitOps

### 3. 存算分离

- 计算与存储完全解耦，独立弹性伸缩
- Flink/Spark 计算集群可按需扩容
- 统一使用阿里云 OSS 作为持久化存储
- 检查点、表数据、日志全部存储于 OSS
- 降低运维成本，提升资源利用率

### 4. 实时优先

- 使用 Fluss 替代传统 HBase/Redis
- 毫秒级数据延迟
- 支持实时查询和分析

### 5. 湖仓一体

- Fluss + Paimon 实现湖仓一体化
- 支持 OLAP 查询（StarRocks）
- 降低数据流转成本

### 6. 容错设计

- Checkpoint 机制保障 Exactly-Once
- 脏数据隔离处理
- 自动重启策略

---

## 🚀 性能优化

### Fluss 配置优化

```sql
-- Bloom Filter 索引
'paimon.file.index.bloom-filter.columns' = 'user_id',
'paimon.file.index.bloom-filter.fpp' = '0.05',

-- 分区策略
'table.auto-partition.enabled' = 'true',
'table.auto-partition.time-unit' = 'day',
'table.auto-partition.num-retention' = '2',
'table.auto-partition.num-precreate' = '5',

-- 数据湖同步
'table.datalake.enabled' = 'true',
'table.datalake.freshness' = '120s'
```

### Flink 配置优化

```java
// 非对齐 Checkpoint（提升吞吐）
checkpointConfig.enableUnalignedCheckpoints();
checkpointConfig.setAlignedCheckpointTimeout(Duration.ofSeconds(60));

// 并行度配置
env.setParallelism(2);
conf.setInteger("taskmanager.numberOfTaskSlots", 16);
```

---

## 🤝 贡献指南

欢迎提交 Issue 和 Pull Request！

1. Fork 本仓库
2. 创建特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 开启 Pull Request

---

## 📝 更新日志

### v2.5 (当前版本)
- ✨ 迁移至 Fluss 存储
- ✨ 集成 Paimon 湖仓
- ✨ 实现用户画像实时计算（滑动窗口聚合）
- ✨ 实现用户画像离线更新（近半年历史分析）
- ✨ 实现用户/歌曲向量化（BAAI/bge-m3模型）
- ✨ 集成 LanceDB 向量数据库
- ✨ 支持向量相似度检索和推荐
- ✨ 完成 DWD 层 Paimon 离线表设计
- ✨ 添加 PostgreSQL CDC 支持
- 🐛 修复数据延迟问题
- 📚 完善文档

---

## 📄 许可证

本项目采用 MIT 许可证 - 详见 [LICENSE](LICENSE) 文件

---

## 👨‍💻 作者

**B1ue** - *项目架构与开发*

---

## 🙏 致谢

- [Apache Flink](https://flink.apache.org/) - 流处理引擎
- [Fluss](https://github.com/apache/incubator-fluss) - 实时存储系统
- [Paimon](https://paimon.apache.org/) - 湖仓存储
- [StarRocks](https://www.starrocks.io/) - OLAP 数据库
- [LanceDB](https://lancedb.com/) - 向量数据库

---

<div align="center">

**如果这个项目对您有帮助，请给个 ⭐️ Star 支持一下！**

Made with ❤️ by B1ue

</div>
