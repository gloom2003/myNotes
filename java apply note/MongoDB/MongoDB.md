# MongoDB的使用



## 1）为什么要用MongoDB



~~~
请基于Java程序员的角度进行说明，MongoDB是什么，为什么要使用它（使用MySQL和Redis不行吗），应用场景是？（举一个例子）
~~~



好的，这个问题非常好。对于一个Java程序员来说，理解 MongoDB 的最佳方式就是把它和你已经非常熟悉的 MySQL 和 Redis 进行对比。

我会从你的视角出发，用你熟悉的概念来解释。

---

### 一、MongoDB 是什么？

想象一下你正在用 Java 写代码：

*   **MySQL 就像是 `List<Map<String, Object>>`**：每一行数据是一个 `Map`，这个 `Map` 的 `key` (列名) 是固定的，类型也是固定的 (`CREATE TABLE` 时就定义好了)。如果你想存一个复杂对象，比如一个 `User` 对象，它里面还包含一个 `List<Address>` 地址列表，你就必须把 `User` 拆开存到 `user` 表，把 `Address` 拆开存到 `address` 表，还需要一个外键 `user_id` 来关联它们。

*   **MongoDB 就像是 `List<YourCustomObject>`**：它直接存储你的整个 Java 对象。那个包含地址列表的 `User` 对象，可以被完整地、原封不动地存进去，就像把它序列化成一个 JSON 字符串然后存起来一样。

**所以，从根本上说：**

**MongoDB 是一个“文档型”数据库**。这里的“文档”不是指 Word 文档，而是指一个类似于 JSON 格式的数据单元。它天然就是用来存储复杂、嵌套的、半结构化的数据对象的。

*   **数据库 (Database)**：和 MySQL 的概念一样。
*   **集合 (Collection)**：大致相当于 MySQL 的**表 (Table)**。
*   **文档 (Document)**：大致相当于 MySQL 的**行 (Row)**，但功能强大得多。一个文档就是一个完整的 JSON 对象。



### 二、为什么要使用它？（MySQL + Redis 不行吗？）

你问到了问题的关键。`MySQL + Redis` 是一个非常成熟和强大的组合，解决了绝大多数问题。但在某些场景下，这个组合会显得“笨拙”，而这正是 MongoDB 发光发热的地方。

我们来分析一下 `MySQL + Redis` 组合的“痛点”：

1.  **对象关系阻抗不匹配 (Object-Relational Impedance Mismatch)**
    *   **你的痛点**：在 Java 代码里，你操作的是对象（`User`、`Order`）。但为了存进 MySQL，你必须通过 ORM 框架（如 MyBatis、Hibernate）把对象“拍扁”成一张或多张二维表。当你要取数据时，又需要通过复杂的 `JOIN` 查询或者多次查询，再把数据组装回一个完整的对象。这个转换过程既损耗性能，又增加了开发心智负担。
    *   **MongoDB 的优势**：**所见即所得**。你的对象在代码里长什么样，存到数据库里就长什么样。没有中间转换的损耗。你需要一个完整的 `User` 对象？一条查询就拿到了它所有的数据，包括他所有的地址、订单历史等等，因为它们本来就存在一个“文档”里。

2.  **“灵活”的业务需求 vs “僵化”的表结构**
    *   **你的痛点**：产品经理今天说要给用户加个“昵称”字段，明天说要给商品加一个“标签”数组。在 MySQL 中，这意味着频繁的 `ALTER TABLE` 操作。在生产环境对一个百万、千万级的大表执行 `ALTER TABLE`，可能导致锁表、服务长时间不可用，是一件非常危险和痛苦的事情。
    *   **MongoDB 的优势**：**无模式 (Schema-Free)**。一个集合里的文档，它们的结构可以不一样。你想给新的用户文档加个 `nickname` 字段？直接加就行了。旧的文档没有这个字段，完全没关系。这使得应用迭代非常快，特别适合敏捷开发和需求快速变化的业务。

3.  **天生的横向扩展能力**
    *   **你的痛点**：当单台 MySQL 扛不住压力时，要做分库分表。无论是自己实现还是用 Sharding-JDBC 这样的中间件，都非常复杂，对业务有侵入性，后期维护成本很高。
    *   **MongoDB 的优势**：它在设计之初就考虑了分布式。它的“分片 (Sharding)”功能是内置的、自动的。当数据量和并发量上来后，加几台机器进去，MongoDB 可以自动地把数据和负载均衡到整个集群，对应用层几乎是透明的。

**那么，Redis 的角色呢？**

Redis 是一个基于内存的**键值数据库**，它的核心优势是 **“快”**。它通常用来做**缓存**（缓存 MySQL 的查询结果）、分布式锁、计数器等。它的数据结构相对简单（string, list, hash, set），不适合存储和查询复杂的业务数据。

所以，它们三者的关系是：

*   **MySQL**：关系型数据的**最终可靠存储**。强调事务、数据一致性。
*   **Redis**：热点数据的**高速缓存**。强调性能、速度。
*   **MongoDB**：复杂业务对象的**主要存储**。强调开发效率、灵活性和扩展性。

**结论：** 不是 `MySQL + Redis` 不行，而是在某些场景下，用 MongoDB 会让你的**开发更爽、迭代更快、扩展更容易**。

### 三、应用场景（举例说明）

基于以上优点，MongoDB 特别适合以下场景：

*   **内容管理系统**：比如博客、新闻网站。
*   **用户中心**：用户资料、配置、社交网络关系等。
*   **物联网 (IoT)**：海量设备上报的数据，结构可能经常变化。
*   **游戏**：玩家的装备、状态、日志等。
*   **电商平台**：商品目录，每个商品的属性千差万别。

---

#### **场景举例：一个典型的博客文章页面**

一个文章页面需要展示：文章标题、内容、作者信息、多个标签、多条评论。

**如果用 MySQL 设计：**
你需要至少4张表：

1.  `posts` (id, title, content, author_id)
2.  `authors` (id, name, bio)
3.  `tags` (id, tag_name)
4.  `post_tags` (post_id, tag_id)  <- 关联表
5.  `comments` (id, content, author_name, post_id)

当你需要加载一篇文章页面时，你的后端代码可能需要：
1.  查询 `posts` 表拿到文章主体。
2.  `LEFT JOIN` `authors` 表获取作者信息。
3.  `LEFT JOIN` `post_tags` 再 `LEFT JOIN` `tags` 获取所有标签。
4.  再发起一个 `SELECT` 查询 `comments` 表获取所有评论。
这是一个非常典型的、需要多次查询或复杂 JOIN 的场景。

**如果用 MongoDB 设计：**
你只需要一个 `posts` **集合 (Collection)**。每篇文章是一个 **文档 (Document)**，它的结构看起来就像这样：

```json
{
  "_id": "post_123",
  "title": "A Guide to MongoDB for Java Developers",
  "content": "MongoDB is a document-oriented database...",
  "author": {
    "name": "Gloom",
    "bio": "A passionate Java developer."
  },
  "tags": [
    "mongodb",
    "java",
    "database",
    "nosql"
  ],
  "comments": [
    {
      "user": "Alice",
      "text": "Great article, very clear!",
      "timestamp": "2023-10-27T10:00:00Z"
    },
    {
      "user": "Bob",
      "text": "This really helped me understand the difference.",
      "timestamp": "2023-10-27T11:30:00Z"
    }
  ],
  "publish_date": "2023-10-27"
}
```

**看到了吗？**

所有渲染页面所需的数据，都**聚合**在了一个文档里。你只需要一条简单的查询 `db.posts.findOne({_id: "post_123"})`，就能获取到全部信息。

这对你这个Java程序员意味着什么？
你后端代码里，可以直接定义一个 `Post` 类，它的字段和上面这个 JSON 结构完全对应。从数据库拿到数据，直接就反序列化成一个 `Post` 对象，没有任何胶水代码，非常清爽。这就是 MongoDB 强大的地方。



## 2）使用mongoDB实现crud操作

~~~
那请你以这个典型的博客文章页面为例子，使用mongoDB实现crud操作，让我看看有多方便
~~~



好的，没问题！

为了让你直观地感受到这种便利性，我将使用 Java 语言和 MongoDB 的官方 Java 驱动程序来演示对上面那个博客文章例子的完整 CRUD (Create, Read, Update, Delete) 操作。

在看代码前，请先想象一下如果用 `JDBC` 或 `MyBatis` 来实现，你需要写的 SQL 语句以及处理结果集 `ResultSet` 的代码，对比之下，你会发现 MongoDB 的代码是多么的直观和面向对象。

---

### 准备工作：添加 Maven 依赖

首先，我们需要在项目的 `pom.xml` 中加入 MongoDB 的驱动包。

```xml
<dependencies>
    <!-- MongoDB Java Driver -->
    <dependency>
        <groupId>org.mongodb</groupId>
        <artifactId>mongodb-driver-sync</artifactId>
        <version>4.11.1</version> <!-- 使用一个较新的稳定版本 -->
    </dependency>

    <!-- 用于方便地将Java对象和BSON文档转换，但为了演示核心API，我下面的例子会手动构建，让你看得更清楚 -->
    <!--
    <dependency>
        <groupId>org.mongodb</groupId>
        <artifactId>mongodb-driver-pojo</artifactId>
        <version>4.11.1</version>
    </dependency>
    -->

    <!-- SLF4J for logging -->
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>2.0.7</version>
    </dependency>
</dependencies>
```

---

### 核心演示代码

下面的代码在一个 `main` 方法里完整地展示了 C-R-U-D 的全过程。

```java
import com.mongodb.client.*;
import com.mongodb.client.model.Filters;
import com.mongodb.client.model.Updates;
import com.mongodb.client.result.DeleteResult;
import com.mongodb.client.result.InsertOneResult;
import com.mongodb.client.result.UpdateResult;
import org.bson.Document;
import org.bson.types.ObjectId;

import java.util.Arrays;
import java.util.Date;
import java.util.List;

public class BlogDemo {

    public static void main(String[] args) {
        // --- 1. 连接到 MongoDB ---
        // 假设 MongoDB 运行在本地默认端口 27017
        // connection string 格式: "mongodb://[username:password@]host1[:port1][,...]"
        String uri = "mongodb://localhost:27017";
        try (MongoClient mongoClient = MongoClients.create(uri)) {

            // 获取数据库 "blog"。如果不存在，MongoDB 会在第一次插入数据时自动创建它。
            MongoDatabase database = mongoClient.getDatabase("blog");

            // 获取集合 "posts"。同样，如果不存在，会自动创建。
            // 这就像获取一个 Table 的引用，但你不需要提前 CREATE TABLE。
            MongoCollection<Document> collection = database.getCollection("posts");

            // 为了演示，我们先清空集合
            collection.drop();
            System.out.println("--- 集合已清空，开始演示 ---");


            // --- 2. CREATE (创建) ---
            System.out.println("\n--- 正在创建新文章... ---");
            Document newPost = new Document("_id", new ObjectId())
                    .append("title", "MongoDB入门指南 (Java版)")
                    .append("content", "本文将向Java开发者展示如何使用MongoDB...")
                    .append("author", new Document("name", "Gloom").append("bio", "一个热情的Java开发者"))
                    .append("tags", Arrays.asList("mongodb", "java", "database", "nosql"))
                    .append("comments", Arrays.asList()) // 刚发布的文章，评论为空
                    .append("publish_date", new Date());

            // 插入文档，就像执行 INSERT INTO
            InsertOneResult insertResult = collection.insertOne(newPost);
            System.out.println("文章创建成功！ID: " + insertResult.getInsertedId().asObjectId().getValue());
            ObjectId postId = insertResult.getInsertedId().asObjectId().getValue();


            // --- 3. READ (读取) ---
            System.out.println("\n--- 正在读取文章... ---");
            // 按标题查找。Filters 工具类提供了各种查询条件，非常方便
            Document foundPost = collection.find(Filters.eq("title", "MongoDB入门指南 (Java版)")).first();
            if (foundPost != null) {
                // .toJson() 可以漂亮地打印出整个文档结构
                System.out.println("按标题找到了文章:\n" + foundPost.toJson());
            }


            // --- 4. UPDATE (更新) ---
            // 场景A: 增加一条新评论。这是MongoDB最强大的地方之一！
            System.out.println("\n--- 正在添加评论... ---");
            Document newComment = new Document("user", "Alice")
                    .append("text", "文章写得太好了，非常清晰！")
                    .append("timestamp", new Date());

            // 使用 $push 操作符向 "comments" 数组中添加一个元素
            // 这比在SQL里操作一个单独的 comments 表要简单得多！
            UpdateResult updateResult = collection.updateOne(
                    Filters.eq("_id", postId), // 查询条件：找到我们要更新的那篇文章
                    Updates.push("comments", newComment) // 更新操作：向 comments 数组 push 一个新元素
            );
            System.out.println("评论添加完成，" + updateResult.getModifiedCount() + " 个文档被更新。");

            // 场景B: 修改文章标题
            System.out.println("\n--- 正在修改标题... ---");
            collection.updateOne(
                    Filters.eq("_id", postId),
                    Updates.set("title", "MongoDB快速上手指南 (Java程序员专属)") // 使用 $set 修改字段值
            );
            System.out.println("标题修改完成。");

            // 读取更新后的文档，看看效果
            Document updatedPost = collection.find(Filters.eq("_id", postId)).first();
            System.out.println("更新后的文章:\n" + updatedPost.toJson());


            // --- 5. DELETE (删除) ---
            System.out.println("\n--- 正在删除文章... ---");
            DeleteResult deleteResult = collection.deleteOne(Filters.eq("_id", postId));
            System.out.println(deleteResult.getDeletedCount() + " 个文档被删除。");

            // 验证一下是否真的删掉了
            long count = collection.countDocuments(Filters.eq("_id", postId));
            System.out.println("删除后再次查询，还剩下 " + count + " 个文档。");
        }
    }
}
```

### 代码解读与对比

1.  **直观的对象操作**：
    *   你创建的是一个 `Document` 对象（可以看作一个 `LinkedHashMap`），然后用 `.append()` 方法来构建它。这完全是 Java 的编程思维，而不是 SQL 的语句思维。
    *   嵌套对象（比如 `author`）和数组（比如 `tags`, `comments`）都是原生支持的，直接创建相应的 `Document` 或 `List` 放进去就行。

2.  **无需 `JOIN` 的关联查询**：
    *   在读取文章时，我们一次查询就拿到了文章的所有信息，包括作者、标签、评论。因为它们本身就存储在一起。根本不需要 `JOIN` 这个概念，避免了复杂的 SQL 和多次数据库交互。

3.  **极其灵活的更新**：
    *   给文章添加评论的操作是整个例子的亮点。在 MySQL 中，你需要 `INSERT INTO comments ...`。而在 MongoDB 中，你只是对 `posts` 集合做了一次 `updateOne` 操作，使用 `$push` 操作符原子性地把新评论添加到了文档内部的数组里。这不仅代码简单，性能也通常更好。
    *   修改标题用了 `$set`，同样非常直观。

4.  **无模式 (Schema-Free) 的便利**：
    *   在整个过程中，我们没有写一行类似 `CREATE TABLE` 的代码。`database` 和 `collection` 都是在第一次使用时按需创建的。如果你想给下一篇文章增加一个 `views` 字段，直接在 `Document` 中 `.append("views", 0)` 就行了，已有的文档不受任何影响。

**总结一下**：对于 Java 程序员来说，使用 MongoDB 就像是在操作一个持久化的、支持高效查询的、超大号的 `List<Map<String, Object>>`。它把大部分和数据库打交道的逻辑，都转换成了你最熟悉的**面向对象编程**，极大地提升了开发效率和幸福感，尤其是在处理复杂或多变的业务数据时。



## 命令行操作

参考：https://juejin.cn/post/6844904150635921422
