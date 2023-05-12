## 抖音极简版

### 项目设计要求

#### 基础功能项

视频Feed流、视频投稿、个人主页

- 视频Feed流：支持所有用户刷抖音，视频按投稿时间倒序推出
- 视频投稿：支持登录用户自己拍视频投稿

- 个人主页：支持查看用户基本信息和投稿列表，注册用户流程简化

#### 方向要求

社交方向

- 关系列表：
  - 登录用户可以关注其他用户，能够在个人主页查看本人的关注数和粉丝数，查看关注列表和粉丝列表
- 消息
  - 登录用户在消息页展示已关注的用户列表，点击用户头像进入聊天页后可以发送消息

### 项目技术概览



### 项目说明

#### 项目目录（草图

douyin/
├── config/
│   └── config.go           # 项目配置文件
├── controller/
│   ├── user_controller.go  # 用户相关接口控制器
│   └── video_controller.go # 视频相关接口控制器
├── dao/
│   ├── user_dao.go         # 用户相关数据访问对象
│   └── video_dao.go        # 视频相关数据访问对象
├── entity/
│   ├── user.go             # 用户实体类
│   └── video.go            # 视频实体类
├── middleware/
│   ├── auth_middleware.go  # 认证中间件
│   └── logger_middleware.go# 日志中间件
├── router/
│   └── router.go           # 项目路由配置
├── service/
│   ├── user_service.go     # 用户相关服务逻辑
│   └── video_service.go    # 视频相关服务逻辑
├── util/
│   └── util.go             # 工具类函数
└── main.go                 # 项目入口文件（备份）

#### 数据库设计

**users表**

| 字段名称     | 类型     | 约束条件                    | 描述         |
| ------------ | -------- | --------------------------- | ------------ |
| id           | INT      | PRIMARY KEY, AUTO_INCREMENT | 用户ID       |
| username     | VARCHAR  | UNIQUE                      | 用户名       |
| password     | VARCHAR  |                             | 用户密码     |
| created_time | DATETIME | DEFAULT CURRENT_TIMESTAMP   | 用户创建时间 |

**videos表**

| 字段名称     | 类型     | 约束条件                    | 描述           |
| ------------ | -------- | --------------------------- | -------------- |
| id           | INT      | PRIMARY KEY, AUTO_INCREMENT | 视频ID         |
| user_id      | INT      | FOREIGN KEY (users.id)      | 视频所属用户ID |
| title        | VARCHAR  |                             | 视频标题       |
| video_url    | VARCHAR  |                             | 视频链接       |
| created_time | DATETIME | DEFAULT CURRENT_TIMESTAMP   | 视频创建时间   |

**relations表**

| 字段名称     | 类型     | 约束条件                    | 描述                             |
| ------------ | -------- | --------------------------- | -------------------------------- |
| id           | INT      | PRIMARY KEY, AUTO_INCREMENT | 关系ID                           |
| from_user    | INT      | FOREIGN KEY (users.id)      | 关系发起用户ID                   |
| to_user      | INT      | FOREIGN KEY (users.id)      | 关系接收用户ID                   |
| relation     | INT      |                             | 关系类型（1：关注，2：取消关注） |
| created_time | DATETIME | DEFAULT CURRENT_TIMESTAMP   | 关系创建时间                     |

**messages表**

| 字段名称     | 类型     | 约束条件                    | 描述         |
| ------------ | -------- | --------------------------- | ------------ |
| id           | INT      | PRIMARY KEY, AUTO_INCREMENT | 消息ID       |
| from_user    | INT      | FOREIGN KEY (users.id)      | 消息发送者ID |
| to_user      | INT      | FOREIGN KEY (users.id)      | 消息接收者ID |
| content      | TEXT     |                             | 消息内容     |
| created_time | DATETIME | DEFAULT CURRENT_TIMESTAMP   | 消息创建时间 |

解释：

1. users表存储用户信息，包括用户ID、用户名、密码和用户创建时间等字段。
2. videos表存储视频信息，包括视频ID、所属用户ID、视频标题、视频链接和视频创建时间等字段。
3. relations表存储用户之间的关系信息，包括关系ID、关系发起用户ID、关系接收用户ID、关系类型（关注或取消关注）和关系创建时间等字段。
4. messages表存储用户之间的消息信息，包括消息ID、消息发送者ID、消息接收者ID、消息内容和消息创建时间等字段。

......表不全，待补充

#### Redis使用

在抖音极简版中，以下模块使用 Redis 进行缓存优化：

- 视频 Feed 流：可以缓存最近的视频列表，以提高读取速度。
- 个人主页：可以缓存用户的基本信息和投稿列表，以提高读取速度。
- 关注列表和粉丝列表：可以缓存关注列表和粉丝列表，以提高读取速度。
- 消息：可以缓存已关注用户的列表，以提高读取速度。

1. 视频 Feed 流：

- 命名空间：feed
- 键名： videoList？（feed:[timestamp]（[timestamp]） 表示该视频列表的生成时间戳）
- 键值：视频列表的 JSON 数据
- 命令：GET、SET、EXPIRE

2. 个人主页：

- 命名空间：user
- 键名：user:[user_id]:info（[user_id] 表示用户的 ID）
- 键值：用户信息的 JSON 数据
- 命令：GET、SET、EXPIRE
- 键名：user:[user_id]:videos（[user_id] 表示用户的 ID）
- 键值：用户发布的视频列表的 JSON 数据
- 命令：GET、SET、EXPIRE

3. 关注列表和粉丝列表：

- 命名空间：relationship
- 键名：relationship:following:[user_id]（[user_id] 表示用户的 ID）
- 键值：该用户关注的用户 ID 列表的 JSON 数据
- 命令：GET、SET、EXPIRE
- 键名：relationship:follower:[user_id]（[user_id] 表示用户的 ID）
- 键值：关注该用户的用户 ID 列表的 JSON 数据
- 命令：GET、SET、EXPIRE

4. 消息：

- 命名空间：message
- 键名：message:[user_id]（[user_id] 表示用户的 ID）
- 键值：已关注用户列表的 JSON 数据
- 命令：GET、SET、EXPIRE



`redis.NewNamespaceHook(namespace)` 创建了一个新的钩子函数
可以使用勾子函数简化代码

筛选热点视频的规则可以有多种，一般是基于视频的热度指标来计算，以下是一些可能的规则：

1. 点赞数和观看数：视频的点赞数和观看数是衡量其热度的重要指标。点赞数和观看数越高的视频，往往是最热门的视频。
2. 评论数：视频的评论数也是一个重要的指标，特别是针对某些有争议性的话题，评论数可能比观看数更重要。
3. 分享数：如果一个视频被大量转发和分享，那么它很可能是一个热门视频。
4. 播放时长：如果用户在看某个视频的时候能够保持很长时间的观看时长，那么这个视频也可以认为是一个热门视频。
5. 用户行为分析：根据用户的历史观看记录，分析出用户最喜欢观看的类型和内容，然后根据这些信息来推荐相应类型的热门视频。

#### 筛选热点视频

1. 点赞数和观看数：视频的点赞数和观看数是衡量其热度的重要指标。点赞数和观看数越高的视频，往往是最热门的视频。

2. 评论数：视频的评论数也是一个重要的指标，特别是针对某些有争议性的话题，评论数可能比观看数更重要。

3. 分享数：如果一个视频被大量转发和分享，那么它很可能是一个热门视频。

4. 播放时长：如果用户在看某个视频的时候能够保持很长时间的观看时长，那么这个视频也可以认为是一个热门视频。

5. 用户行为分析：根据用户的历史观看记录，分析出用户最喜欢观看的类型和内容，然后根据这些信息来推荐相应类型的热门视频。

   **有很多算法和规则可以用来支持筛选热点视频，以下是一些常用的：**

   1. 热门程度排名：通过记录视频的观看数、点赞数、评论数、分享数等指标，计算出每个视频的热度指数，并按照热度指数的大小排名，将排名前面的视频作为热点视频。
   2. 时间衰减模型：通过考虑观看时间对视频热度的影响，采用时间衰减模型来计算视频的热度指数。例如，可以使用指数衰减模型，其中视频的热度指数与观看时间之间的关系是指数级的，随着时间的增加指数逐渐降低。
   3. 协同过滤算法：通过分析用户的行为数据，如历史观看记录、点赞记录等，来预测用户的兴趣爱好，从而推荐相关的热点视频。
   4. 深度学习模型：通过使用深度学习模型，如卷积神经网络、循环神经网络等，来分析视频的特征，如画面内容、音频特征等，从而预测视频的热度

#### Kafka使用

1. 在投稿接口中，将视频信息包装成消息体，发送到 Kafka 集群中的指定主题中。
2. 后续的处理流程中，使用 Kafka 消费者来监听指定主题中的消息。
3. 当有新的视频信息被发送到主题中时，消费者会自动触发回调函数来处理这条消息，包括将视频信息存储到数据库中等操作。

说仔细一点

Sure. 具体来说，使用 Kafka 来异步上传视频的流程可以分为以下几个步骤：

1. 配置 Kafka 环境：包括安装 Kafka，创建 Kafka 主题等。
2. 在投稿接口中，将投稿信息封装成消息，发送到 Kafka 集群中的指定主题。这一步可以使用 Kafka 提供的 Go 客户端库（如 sarama）来实现，代码类似于：

```
goCopy codeproducer, err := sarama.NewSyncProducer([]string{"localhost:9092"}, nil)
if err != nil {
    // 处理错误
}
defer producer.Close()

// 构造消息
msg := &sarama.ProducerMessage{
    Topic: "upload-video", // 指定主题
    Value: sarama.StringEncoder("video info"), // 消息内容
}

// 发送消息
_, _, err = producer.SendMessage(msg)
if err != nil {
    // 处理错误
}
```

1. 在后续的处理流程中，使用 Kafka 消费者来从 Kafka 集群中订阅指定主题，并消费上传的视频信息消息。这一步也可以使用 Kafka 提供的 Go 客户端库来实现，代码类似于：

```
goCopy codeconsumer, err := sarama.NewConsumer([]string{"localhost:9092"}, nil)
if err != nil {
    // 处理错误
}
defer consumer.Close()

partitionConsumer, err := consumer.ConsumePartition("upload-video", 0, sarama.OffsetNewest)
if err != nil {
    // 处理错误
}
defer partitionConsumer.Close()

// 持续消费消息
for {
    select {
    case msg := <-partitionConsumer.Messages():
        // 处理上传视频信息
        fmt.Println("received video info:", msg.Value)
    case err := <-partitionConsumer.Errors():
        // 处理错误
    }
}
```


项目结构示例如下

├── cmd/                  # 入口文件和应用程序配置文件
│   └── main.go
├── internal/             # 应用程序内部代码
│   ├── app/              # 业务逻辑层
│   │   ├── handler/      # 控制器
│   │   ├── kafka/        # Kafka生产者和消费者
│   │   ├── model/        # 数据模型
│   │   ├── service/      # 服务层
│   │   └── util/         # 工具函数
│   ├── config/           # 应用程序配置代码
│   │   ├── config.go
│   │   └── env.go
│   ├── db/               # 数据库代码
│   │   ├── migrate/      # 数据库迁移脚本
│   │   ├── model/        # 数据模型
│   │   ├── repository/   # 数据访问层
│   │   └── db.go
│   └── pkg/              # 共享的库代码
│       └── log/          # 日志库
├── vendor/               # 依赖管理（使用Go Module时不需要）
├── go.mod                # 依赖管理
└── README.md             # 项目说明文档

#### Kitex使用

当引入Kitex时，项目结构可能会变得更加模块化和分层。下面是一个可能的项目结构示例：

```
goCopy code
├── cmd
│   └── server
│       ├── main.go
│       └── config.go
├── config
│   ├── config.go
│   └── kafka.yaml
├── internal
│   ├── app
│   │   ├── handler
│   │   │   ├── controller.go
│   │   │   └── producer.go
│   │   ├── service
│   │   │   ├── video.go
│   │   │   └── kafka.go
│   │   ├── model
│   │   │   └── video.go
│   │   ├── kafka
│   │   │   └── kafka.go
│   │   └── middleware
│   │       └── auth.go
│   ├── pkg
│   │   └── util
│   │       ├── response.go
│   │       ├── snowflake.go
│   │       └── ...
│   └── conf
│       └── config.go
├── vendor
├── go.mod
└── go.sum
```

- `cmd/server`: 主要存放启动项目的代码，包括服务器配置的读取和依赖注入。
- `config`: 存放项目的配置文件，包括 Kafka 配置。
- `internal/app`: 应用程序层，包括控制器和服务等，负责处理请求和业务逻辑。其中，`handler/controller.go` 和 `handler/producer.go` 为控制器，`service/video.go` 和 `service/kafka.go` 为服务。
- `internal/model`: 存放数据模型，这里的 `video.go` 存放了视频的信息。
- `internal/kafka`: 存放 Kafka 相关的代码，这里的 `kafka.go` 存放了创建 Kafka 生产者和消费者的逻辑。
- `internal/middleware`: 存放中间件相关代码，这里的 `auth.go` 实现了用户鉴权的逻辑。
- `internal/pkg`: 存放项目的通用代码，这里的 `util` 包含了一些通用的工具函数和结构体。
- `internal/conf`: 存放应用程序配置相关的代码，这里的 `config.go` 定义了应用程序配置结构体，用于读取和解析配置文件。

当引入 Kitex 时，还需要增加相关的文件，例如：

- `internal/app/handler/kitex_handler.go`：Kitex 的请求处理器实现。
- `internal/app/service/kitex_service.go`：Kitex 的服务实现。
- `internal/pkg/kitexutil/kitex_util.go`：Kitex 的辅助函数，例如对请求参数的解析和对响应参数的封装。

这样的项目结构，将应用程序的不同部分分开存放，便于代码管理和维护。















### 心得汇总

#### 接收器

~~~ go
type A struct {
    // ...
}

func (a *A) TableName() string {
    return "table_a"
}

type B struct {
    // ...
}

func (b B) TableName() string {
    return "table_b"
}

~~~



接收器是一种特殊的参数，它在方法被调用时，接收器所指向的变量的值被复制到方法中的接收器参数中，接着方法就可以使用该接收器参数来操作接收器所指向的变量。

在 Go 语言中，接收器可以是值类型或指针类型。如果接收器是值类型，那么在调用方法时会将接收器的值复制一份，方法中操作的是复制的副本，不会影响原始的接收器变量。而如果接收器是指针类型，则方法中操作的是接收器指向的变量本身，可以修改原始的接收器变量。

在调用GORM的API时，无论是指针类型还是值类型，都是可以的，GORM会自动处理。例如，当你查询A类型的数据时，即便你使用的是&A{}指针类型的实例，也可以正常查询。

这两种写法在实际增删改查数据库时是等效的，都可以正确地映射到相应的数据库表。但是在代码编写过程中，使用指针接收器会更灵活，可以修改接收器所指向的变量的值，而值接收器则不能修改接收器所指向的变量的值。同时，使用指针接收器还可以避免在函数调用时复制结构体数据，提高程序的执行效率。因此，通常建议使用指针接收器来实现方法。

反正最后都会持久化

#### 截图工具

不同的视频截图工具各有优缺点，这里简单列举一下：

1. FFmpeg：是一款功能强大的视频处理工具，可以进行视频截图、剪辑、转码等操作，支持多种视频格式。但是使用复杂，需要命令行操作，对于非专业用户来说可能不太友好。
2. OpenCV：是一款计算机视觉库，可以用来处理图像和视频等，也可以用来进行视频截图。使用OpenCV进行视频截图可以实现更加细致的控制，比如可以选择截取视频的某个区域作为封面。但是需要一定的编程基础。
3. Video Thumbnail Maker：是一款界面简单、易于使用的视频截图软件，可以选择截取视频的某一帧作为封面。但是功能相对较少，不支持批量处理。

综上所述，根据个人需求和使用经验，可以选择适合自己的视频截图工具。

#### 优化方案

1. 更好的个性化推荐算法：抖音的个性化推荐算法已经很出色了，但是可以继续改进。例如，可以进一步根据用户的兴趣、行为和历史记录等信息，提供更精准的推荐，或者更加细致地控制推荐内容的多样性和平衡性，避免用户感到单调或过度重复。
2. 更好的用户体验：尽管抖音已经很注重用户体验，但是总有一些用户会对某些方面有不满意的地方。例如，用户可能需要更方便的界面操作，更快速的视频加载和播放速度，更好的视频质量等等。
3. 更加精细的社交功能：抖音已经具备了一些社交功能，例如关注、点赞、评论、私信等等，但是可以进一步改进。例如，可以增加更多的社交场景，例如多人视频聊天、共享等，或者提供更加精细的社交隐私设置，以保护用户的个人信息和隐私。
4. 更加精准的广告投放：抖音已经成为一个广告投放的重要平台，但是仍然有改进空间。例如，可以进一步提高广告投放的效果和ROI，提供更加精准的广告定位和投放策略，避免过多的广告干扰用户的观看体验。