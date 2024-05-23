# 第二章：MongoDB、Mongoose 和 REST API – 第一部分

在本章中，您将学习如何将您的 Node 应用程序连接到您在本地计算机上运行的 MongoDB 数据库。这意味着我们将能够在我们的 Node 应用程序内部发出数据库命令，执行诸如插入、更新、删除或读取数据等操作。如果我们要制作 Todo REST API，这将是至关重要的。当有人访问我们的 API 端点时，我们希望操作数据库，无论是读取所有的 Todos 还是添加一个新的。然而，在我们做任何这些之前，我们必须先学习基础知识。

# 连接到 MongoDB 并写入数据

要从 Node.js 内部连接到我们的 MongoDB 数据库，我们将使用 MongoDB 团队创建的一个 npm 模块。它被称为 node-mongodb-native，但它包括了你需要连接和与数据库交互的所有功能。要找到它，我们将谷歌搜索`node-mongodb-native`：

![](img/d2d25ad4-75ae-45ac-ba0e-26abbda50968.png)

GitHub 仓库，应该是第一个链接，是我们想要的——node-mongodb-native 仓库——如果我们向下滚动，我们可以看一下一些重要的链接：

![](img/c02996e6-da4d-4ffa-8612-721523ea48e4.png)

首先是文档，还有我们的 api-docs；当我们开始探索这个库内部的功能时，这些将是至关重要的。如果我们在这个页面上继续向下滚动，我们会发现大量关于如何入门的示例。我们将在本章中讨论很多这些内容，但我想让你知道你可以在哪里找到其他资源，因为 mongodb-native 库有很多功能。有整个课程专门致力于 MongoDB，甚至都没有涵盖这个库内置的所有功能。

我们将专注于 Node.js 应用程序所需的 MongoDB 的重要和常见子集。要开始，让我们打开上图中显示的文档。当你进入文档页面时，你必须选择你的版本。我们将使用 3.0 版本的驱动程序，有两个重要的链接：

+   **参考链接：** 这包括类似指南的文章，入门指南和其他各种参考资料。

+   **API 链接：** 这包括您在使用该库时可用的每个单独方法的详细信息。当我们开始创建我们的 Node Todo API 时，我们将在此链接上探索一些方法。

不过，现在我们可以开始为这个项目创建一个新目录，然后我们将安装 MongoDB 库并连接到我们正在运行的数据库。我假设您在本章的所有部分中都已经运行了您的数据库。我在我的终端中的一个单独标签页中运行它。

如果您使用的是 Windows，请参考 Windows 安装部分的说明来启动您的数据库，如果您忘记了。如果您使用的是 Linux 或 macOS 操作系统，请使用我已经提到的说明，并且不要忘记也包括`dbpath`参数，这对于启动 MongoDB 服务器至关重要。

# 为项目创建一个目录

首先，我要在桌面上为 Node API 创建一个新文件夹。我将使用`mkdir`创建一个名为`node-todo-api`的新文件夹。然后，我可以使用`cd`进入该目录，`cd node-todo-api`。从这里，我们将运行`npm init`，这将创建我们的`package.json`文件，并允许我们安装我们的 MongoDB 库。再次，我们将使用回车键跳过所有选项，使用每个默认值：

![](img/08418c55-f4af-4d52-8f3c-239701eb5c9f.png)

一旦我们到达结尾，我们可以确认我们的选择，现在我们的`package.json`文件已经创建。接下来我们要做的是在 Atom 中打开这个目录。它在桌面上，`node-todo-api`。接下来，在项目的根目录中，我们将创建一个新文件夹，我将称这个文件夹为`playground`。在这个文件夹里，我们将存储各种脚本。它们不会是与 Todo API 相关的脚本；它们将是与 MongoDB 相关的脚本，所以我希望将它们放在文件夹中，但我不一定希望它们成为应用的一部分。我们将像以前一样使用`playground`文件夹。

在`playground`文件夹中，让我们继续创建一个新文件，我们将称这个文件为`mongodb-connect.js`。在这个文件中，我们将通过加载库并连接到数据库来开始。现在，为了做到这一点，我们必须安装库。从终端，我们可以运行`npm install`来完成这项工作。新的库名称是`mongodb`；全部小写，没有连字符。然后，我们将继续指定版本，以确保我们都使用相同的功能，`@3.0.2`。这是写作时的最新版本。在版本号之后，我将使用`--save`标志。这将把它保存为常规依赖项，它已经是：

[PRE0]

我们需要这个来运行 Todo API 应用程序。

# 将`mongodb-connect`文件连接到数据库。

现在安装了 MongoDB，我们可以将其移动到我们的`mongodb-connect`文件并开始连接到数据库。我们需要做的第一件事是从我们刚刚安装的库中提取一些东西，那就是`mongodb`库。我们要找的是一个叫做`MongoClient`的构造函数。`MongoClient`构造函数允许您连接到 Mongo 服务器并发出命令来操作数据库。让我们继续创建一个名为`MongoClient`的常量。我们将把它设置为`require`，并且我们将要求我们刚刚安装的库`mongodb`。从那个库中，我们将取出`MongoClient`：

[PRE1]

现在`MongoClient`已经就位，我们可以调用`MongoClient.connect`来连接到数据库。这是一个方法，它接受两个参数：

+   第一个参数是一个字符串，这将是您的数据库所在的 URL。现在在生产示例中，这可能是 Amazon Web Services URL 或 Heroku URL。在我们的情况下，它将是本地主机 URL。我们稍后会谈论这个。

+   第二个参数将是一个回调函数。回调函数将在连接成功或失败后触发，然后我们可以适当地处理事情。如果连接失败，我们将打印一条消息并停止程序。如果成功，我们可以开始操作数据库。

# 将字符串添加为第一个参数

对于我们的第一个参数，我们将从`mongodb://`开始。当我们连接到 MongoDB 数据库时，我们要使用像这样的 mongodb 协议：

[PRE2]

接下来，它将在本地主机上，因为我们在本地机器上运行它，并且我们已经探索了端口：`27017`。在端口之后，我们需要使用`/`来指定我们要连接的数据库。现在，在上一章中，我们使用了测试数据库。这是 MongoDB 给你的默认数据库，但我们可以继续创建一个新的。在`/`之后，我将称数据库为`TodoApp`，就像这样：

[PRE3]

# 将回调函数添加为第二个参数

接下来，我们可以继续提供回调函数。我将使用 ES6 箭头（`=>`）函数，并且我们将通过两个参数。第一个将是一个错误参数。这可能存在，也可能不存在；就像我们过去看到的那样，如果实际发生了错误，它就会存在；否则就不会存在。第二个参数将是`client`对象。这是我们可以用来发出读写数据命令的对象：

[PRE4]

# mongodb-connect 中的错误处理

现在，在写入任何数据之前，我将继续处理可能出现的任何错误。我将使用一个`if`语句来做到这一点。如果有错误，我们将在控制台上打印一条消息，让查看日志的人知道我们无法连接到数据库服务器，`console.log`，然后在引号内放上类似`Unable to connect to MongoDB server`的内容。在`if`语句之后，我们可以继续记录一个成功的消息，类似于`console.log`。然后，在引号内，我们将使用`Connected to MongoDB server`：

[PRE5]

现在，当你处理这样的错误时，即使错误块运行，成功代码也会运行。我们要做的是在`console.log('Unable to connect to MongoDB server');`行之前添加一个`return`语句。

这个`return`语句并没有做什么花哨的事情。我们所做的只是使用它来阻止函数的其余部分执行。一旦从函数返回，程序就会停止，这意味着如果发生错误，消息将被记录，函数将停止，我们将永远看不到这条`Connected to MongoDB server`消息：

[PRE6]

使用`return`关键字的替代方法是添加一个`else`子句，并将我们的成功代码放在`else`子句中，但这是不必要的。我们可以只使用我更喜欢的`return`语法。

现在，在运行这个文件之前，我还想做一件事。在我们的回调函数的最底部，我们将在 db 上调用一个方法。它叫做`client.close`：

[PRE7]

这关闭了与 MongoDB 服务器的连接。现在我们已经有了这个设置，我们实际上可以保存`mongodb-connect`文件并在终端内运行它。它现在还没有做太多事情，但它确实会工作。

# 在终端中运行文件

在终端中，我们可以使用`node playground`作为目录运行文件，文件本身是`mongodb-connect.js`：

[PRE8]

当我们运行这个文件时，我们会得到`Connected to MongoDB server`打印到屏幕上：

![](img/9407d5a3-011f-4cbe-90eb-9c4a31b55d58.png)

如果我们进入我们拥有 MongoDB 服务器的选项卡，我们可以看到我们有一个新的连接：连接已接受。正如你在下面的截图中所看到的，该连接已关闭，这太棒了：

![](img/6f1192b4-66da-47e3-bde5-7f094bbc762f.png)

使用 Mongo 库，我们能够连接，打印一条消息，并从服务器断开连接。

现在，你可能已经注意到我们在 Atom 中的`MongoClient.connect`行中更改了数据库名称，但我们实际上并没有做任何事情来创建它。在 MongoDB 中，与其他数据库程序不同，你不需要在开始使用之前创建数据库。如果我想启动一个新的数据库，我只需给它一个名称，比如`Users`。

现在我有一个`Users`数据库，我可以连接到它并对其进行操作。没有必要先创建该数据库。我将继续将数据库名称更改回`TodoApp`。如果我们进入 Robomongo 程序并连接到我们的本地数据库，你还会看到我们唯一拥有的数据库是`test`。`TodoApp`数据库甚至从未被创建过，即使我们连接到它。Mongo 不会创建数据库，直到我们开始向其中添加数据。我们现在可以继续做到这一点。

# 向数据库添加数据

在 Atom 中，在我们调用`db.close`之前，我们将向集合中插入一条新记录。这将是 Todo 应用程序。在这个应用程序中，我们将有两个集合：

+   一个`Todos`集合

+   一个`Users`集合

我们可以继续通过调用`db.collection`向`Todos`集合添加一些数据。`db.collection`方法以要插入的集合的字符串名称作为其唯一参数。现在，就像实际数据库本身一样，您不需要首先创建此集合。您只需给它一个名称，比如`Todos`，然后可以开始插入。无需运行任何命令来创建它：

[PRE9]

接下来，我们将使用集合中可用的一个方法`insertOne`。`insertOne`方法允许您将新文档插入到集合中。它需要两个参数：

+   第一个将是一个对象。这将存储我们希望在文档中拥有的各种键值对。

+   第二个将是一个回调函数。当事情失败或顺利进行时，将触发此回调函数。

您将获得一个错误参数，可能存在，也可能不存在，您还将获得结果参数，如果一切顺利，将会提供：

[PRE10]

在错误回调函数本身内部，我们可以添加一些代码来处理错误，然后我们将添加一些代码来在成功添加时将对象打印到屏幕上。首先，让我们添加一个错误处理程序。就像我们之前做的那样，我们将检查错误参数是否存在。如果存在，那么我们将简单地使用`return`关键字打印一条消息，以阻止函数继续执行。接下来，我们可以使用`console.log`打印`无法插入 todo`。我将传递给`console.log`的第二个参数将是实际的`err`对象本身，这样如果有人查看日志，他们可以看到出了什么问题：

[PRE11]

在我们的`if`语句旁边，我们可以添加我们的成功代码。在这种情况下，我们要做的只是将一些内容漂亮地打印到`console.log`屏幕上，然后我将调用`JSON.stringify`，我们将继续传入`result.ops`。`ops`属性将存储所有插入的文档。在这种情况下，我们使用了`insertOne`，所以它只会是我们的一个文档。然后，我可以添加另外两个参数，对于筛选函数是`undefined`，对于缩进是`2`：

[PRE12]

有了这个，我们现在可以继续执行我们的文件，看看会发生什么。在终端中，我将运行以下命令：

[PRE13]

当我执行命令时，我们会收到成功消息：`已连接到 MongoDB 服务器`。然后，我们会得到一个插入的文档数组：

![](img/bc451fb0-aae9-404f-9d73-cb56589083ef.png)

现在正如我所提到的，在这种情况下，我们只插入了一个文档，如前面的屏幕截图所示。我们有`text`属性，由我们创建；我们有`completed`属性，由我们创建；我们有`_id`属性，由 Mongo 自动添加。`_id`属性将是接下来部分的主题。我们将深入讨论它是什么，为什么存在以及为什么它很棒。

目前，我们将继续注意它是一个唯一标识符。这是一个仅分配给此文档的 ID。这就是使用 Node.js 将文档插入到您的 MongoDB 数据库中所需的全部内容。我们可以在 Robomongo 中查看此文档。我将右键单击连接，然后单击刷新：

![](img/3618889d-f9e2-4b28-9548-d34f9e2f0315.png)

这显示了我们全新的`TodoApp`数据库。如果我们打开它，我们会得到我们的`Collections`列表。然后我们可以进入`Collections`，查看文档，我们得到了什么？我们得到了我们的一个 Todo 项目。如果我们展开它，我们可以看到我们有我们的 _id，我们有我们的文本属性，我们有我们的完成布尔值：

![](img/952d1c7e-fe2e-40e3-a249-8ac9ca3e4b68.png)

在这种情况下，Todo 未完成，因此`completed`值为`false`。现在，我希望您将一个新记录添加到集合中。这将是本节的挑战。

# 向集合中添加新记录

在 Atom 中，我希望您从`db.collection`一直到回调的底部，将代码注释掉。然后，我们将继续添加一些内容。在`db.close()`之前，您将输入`Insert new doc into the Users collection`。这个文档将有一些属性。我希望您给它一个`name`属性；将其设置为您的名字。然后，我们将给它一个`age`属性，最后但并非最不重要的是我们可以给它一个`location`字符串。我希望您使用`insertOne`插入该文档。您需要将新的集合名称传递给集合方法。然后，再往下，您将添加一些错误处理代码，并将操作打印到屏幕上。重新运行文件后，您应该能够在终端中查看您的记录，并且应该能够刷新。在 Robomongo 中，您应该看到新的 Users 集合，并且应该看到您指定的用户的名称、年龄和位置。

希望您能够成功将一个新文档插入到 Users 集合中。为了完成这个任务，您需要调用`db.collection`，这样我们就可以访问我们想要插入的集合，这种情况下是`Users`：

[PRE14]

接下来，我们需要调用一个方法来操作`Users`集合。我们想要插入一个新文档，所以我们将使用`insertOne`，就像我们在上一小节中所做的那样。我们将把两个参数传递给`insertOne`。第一个是要插入的文档。我们将给它一个`name`属性；我将把它设置为`Andrew`。然后，我们可以设置`age`等于`25`。最后，我们将`location`设置为我的当前位置，`Philadelphia`：

[PRE15]

我们要传入的下一个参数是我们的回调函数，它将在错误对象和结果一起被调用。在回调函数内部，我们将首先处理错误。如果有错误，我们将继续将其记录到屏幕上。我将返回`console.log`，然后我们可以放置消息：`Unable to insert user`。然后，我将添加错误参数作为`console.log`的第二个参数。接下来，我们可以添加我们的成功案例代码。如果一切顺利，我将使用`console.log`将`result.ops`打印到屏幕上。这将显示我们插入的所有记录：

[PRE16]

现在我们可以继续使用*向上*箭头键和*回车*键在终端内重新运行文件：

![](img/1efe9b45-0509-4625-9bcf-cf85ef2e1968.png)

我们得到了我们插入的文档数组，只有一个。`name`、`age`和`location`属性都来自我们，`_id`属性来自 MongoDB。

接下来，我希望您验证它是否确实被插入到 Robomongo 中。通常，当您添加一个新的集合或新的数据库时，您可以右键单击连接本身，单击刷新，然后您应该能够看到添加的所有内容：

![](img/029bbfea-6826-4ece-ac25-f10f834c5966.png)

如前面的屏幕截图所示，我们有我们的 Users 集合。我可以查看 Users 的文档。我们得到了一个文档，其中名称设置为 Andrew，年龄设置为 25，位置设置为 Philadelphia。有了这个，我们现在完成了。我们已经能够使用 Node.js 连接到我们的 MongoDB 数据库，还学会了如何使用这个 mongo-native 库插入文档。在下一节中，我们将深入研究 ObjectIds，探讨它们究竟是什么，以及它们为什么有用。

# ObjectId

现在您已经将一些文档插入到 MongoDB 集合中，我想花一点时间谈谈 MongoDB 中的`_id`属性，因为它与您可能已经使用过的其他数据库系统（如 Postgres 或 MySQL）中的 ID 有些不同。

# MongoDB 中的 _id 属性

为了开始我们对`_id`属性的讨论，让我们继续重新运行`mongodb-connect`文件。这将向 Users 集合中插入一个新的文档，就像我们在`db.collection`行中定义的那样。我将通过在节点中运行文件来做到这一点。它在`playground`文件夹中，文件本身叫做`mongodb-connect.js`：

[PRE17]

我将运行命令，然后我们将打印出插入的文档：

![](img/dc75c923-aecb-4897-9cde-e6938420c025.png)

正如我们过去所看到的，我们得到了我们的三个属性，以及 Mongo 添加的一个属性。

关于这个的第一件事是，它不是一个自动递增的整数，就像对于 Postgres 或 MySQL 一样，第一条记录的 ID 是 1，第二条记录的 ID 是 2。Mongo 不使用这种方法。Mongo 被设计为非常容易扩展。扩展意味着你可以添加更多的数据库服务器来处理额外的负载。

想象一下，你有一个每天大约有 200 个用户的 Web 应用程序，你当前的服务器已经准备好处理这个流量。然后，你被某个新闻媒体选中，有 1 万人涌入你的网站。使用 MongoDB，很容易启动新的数据库服务器来处理额外的负载。当我们使用随机生成的 ID 时，我们不需要不断地与其他数据库服务器通信，以检查最高递增值是多少。是 7 吗？是 17 吗？这并不重要；我们只是简单地生成一个新的随机 ObjectId，并将其用于文档的唯一标识符。

现在，ObjectId 本身由几个不同的部分组成。它是一个 12 字节的值。前四个字节是时间戳；我们稍后会谈论这个。这意味着我们在数据中有一个内置的时间戳，指的是 ID 创建时刻的时间。这意味着在我们的文档中，我们不需要有一个`createdAt`字段；它已经编码在 ID 中了。

接下来的三个字节是机器标识符。这意味着如果两台计算机生成 ObjectId，它们的机器 ID 将是不同的，这将确保 ID 是唯一的。接下来，我们有两个字节，进程 ID，这只是另一种创建唯一标识符的方式。最后，我们有一个 3 字节的计数器。这类似于 MySQL 会做的。这只是 ID 的 3 个字节。正如我们已经提到的，我们有一个时间戳，它将是唯一的；一个机器标识符；一个进程 ID；最后，只是一个随机值。这就是 ObjectId 的组成部分。

ObjectId 是`_id`的默认值。如果没有提供任何内容，你确实可以对该属性做任何你喜欢的事情。例如，在`mongodb-connect`文件中，我可以指定一个`_id`属性。我将给它一个值，所以让我们用`123`；在末尾加上逗号；这是完全合法的：

[PRE18]

我们可以保存文件，并使用*上*箭头键和*回车*键重新运行脚本：

![](img/dfbb1afb-2704-4061-b756-9cdefb890c9e.png)

我们得到了我们的记录，其中`_id`属性是`123`。`ObjectId`是 MongoDB 创建 ID 的默认方式，但你可以为 ID 创建做任何你喜欢的事情。在 Robomongo 中，我们可以刷新我们的 Users 集合，然后得到我们的文档：

![](img/201195b7-8371-478d-a7a1-988e81213e14.png)

我们有我们在上一节中创建的一个，以及我们刚刚创建的两个，都有一个唯一的标识符。这就是为什么唯一的 ID 非常重要。在这个例子中，我们有三个属性：名称、年龄和位置，它们对所有记录都是相同的。这是一个合理的做法。想象两个人需要做同样的事情，比如买东西。仅仅那个字符串是不够唯一标识一个 Todo 的。另一方面，ObjectId 是唯一的，这就是我们将用来将诸如 Todos 之类的事物与诸如`Users`之类的事物关联起来的东西。

接下来，我想看一下我们在代码中可以做的一些事情。正如我之前提到的，时间戳被嵌入在这里，我们实际上可以将其提取出来。在 Atom 中，我们要做的是移除`_id`属性。时间戳只有在使用`ObjectId`时才可用。然后，在我们的回调函数中，我们可以继续将时间戳打印到屏幕上。

[PRE19]

如果你记得，`result.ops`是一个包含所有插入的文档的数组。我们只插入一个，所以我将访问数组中的第一个项目，然后我们将访问`_id`属性。这将正如你所想的那样：

[PRE20]

如果我们保存文件并从终端重新运行脚本，我们只会得到`ObjectId`打印到屏幕上：

![](img/288d7028-2bd9-4e7e-8fc1-fab1a9e60a0c.png)

现在，我们可以在`_id`属性上调用一个方法。

# 调用`.getTimestamp`函数

我们要调用的是`.getTimestamp`。`getTimestamp`是一个函数，但它不需要任何参数。它只是返回 ObjectId 创建的时间戳：

[PRE21]

现在，如果我们继续重新运行我们的程序，我们会得到一个时间戳：

![](img/7d48f1c5-d083-463a-859e-3c4a91749924.png)

在前面的截图中，我可以看到 ObjectId 是在 2016 年 2 月 16 日 08:41 Z 创建的，所以这个时间戳确实是正确的。这是一个绝妙的方法，可以准确地确定文档是何时创建的。

现在，我们不必依赖 MongoDB 来创建我们的 ObjectIds。在 MongoDB 库中，他们实际上给了我们一个可以随时运行的函数来创建一个 ObjectId。暂时，让我们继续注释掉我们插入的调用。

在文件的顶部，我们将改变我们的导入语句，加载 MongoDB 的新内容，并且我们将使用 ES6 的对象解构来实现这一点。在我们实际使用它之前，让我们花一点时间来谈谈它。

# 使用对象解构 ES6

对象解构允许你从对象中提取属性以创建变量。这意味着如果我们有一个名为`user`的对象，并且它等于一个具有`name`属性设置为`andrew`和一个年龄属性设置为`25`的对象，如下面的代码所示：

[PRE22]

我们可以很容易地将其中一个提取到一个变量中。比如说，我们想要获取名字并创建一个`name`变量。要在 ES6 中使用对象解构，我们将创建一个变量，然后将其包裹在花括号中。我们将提供我们想要提取的名字；这也将是变量名。然后，我们将把它设置为我们想要解构的对象。在这种情况下，那就是`user`对象：

[PRE23]

我们已经成功解构了`user`对象，取出了`name`属性，创建了一个新的`name`变量，并将其设置为任何值。这意味着我可以使用`console.log`语句将`name`打印到屏幕上：

[PRE24]

我将重新运行脚本，我们得到`andrew`，这正是你所期望的，因为这是`name`属性的值：

![](img/724333b0-a6d8-4e53-b09d-2266f7b30fdc.png)

ES6 解构是从对象的属性中创建新变量的一种绝妙方式。我将继续删除这个例子，并且在代码顶部，我们将改变我们的`require`语句，以便使用解构。

在添加任何新内容之前，让我们继续并将 MongoClient 语句切换到解构；然后，我们将担心抓取那个新东西，让我们能够创建 ObjectIds。我将复制并粘贴该行，并注释掉旧的，这样我们就可以参考它。

[PRE25]

我们要做的是在`require`之后删除我们的`.MongoClient`调用。没有必要去掉那个属性，因为我们将使用解构代替。这意味着在这里我们可以使用解构，这需要我们添加花括号，并且我们可以从 MongoDB 库中取出任何属性。

[PRE26]

在这种情况下，我们唯一拥有的属性是`MongoClient`。这创建了一个名为`MongoClient`的变量，将其设置为`require('mongodb')`的`MongoClient`属性，这正是我们在之前的`require`语句中所做的。

# 创建 objectID 的新实例

现在我们有了一些解构，我们可以很容易地从 MongoDB 中取出更多的东西。我们可以添加一个逗号并指定我们想要取出的其他东西。在这种情况下，我们将取出大写的`ObjectID`。

[PRE27]

这个`ObjectID`构造函数让我们可以随时创建新的 ObjectIds。我们可以随心所欲地使用它们。即使我们不使用 MongoDB 作为我们的数据库，创建和使用 ObjectIds 来唯一标识事物也是有价值的。接下来，我们可以通过首先创建一个变量来创建一个新的 ObjectId。我会称它为`obj`，并将其设置为`new ObjectID`，将其作为一个函数调用：

[PRE28]

使用`new`关键字，我们可以创建`ObjectID`的一个新实例。接下来，我们可以使用`console.log(obj)`将其记录到屏幕上。这是一个普通的 ObjectId：

[PRE29]

如果我们从终端重新运行文件，我们会得到你期望的结果：

![](img/f429516c-25b3-4fae-8689-052b7c4c1ceb.png)

我们得到了一个看起来像 ObjectId 的东西。如果我再次运行它，我们会得到一个新的；它们都是唯一的：

![](img/b2b1feb5-0446-44b9-b276-e57ae9a9279e.png)

使用这种技术，我们可以在任何地方都使用 ObjectIds。我们甚至可以生成我们自己的 ObjectIds，将它们设置为我们文档的`_id`属性，尽管我发现让 MongoDB 为我们处理这些繁重的工作要容易得多。我将继续删除以下两行，因为我们实际上不会在脚本中使用这段代码：

[PRE30]

我们已经了解了一些关于 ObjectIds 的知识，它们是什么，以及它们为什么有用。在接下来的章节中，我们将看看我们可以如何与 MongoDB 一起工作的其他方式。我们将学习如何读取、删除和更新我们的文档。

# 获取数据

现在你知道如何向数据库插入数据了，让我们继续讨论如何从中获取数据。我们将在 Todo API 中使用这种技术。人们会想要填充一个他们需要的所有 Todo 项目的列表，并且他们可能想要获取有关单个 Todo 项目的详细信息。所有这些都需要我们能够查询 MongoDB 数据库。

# 在 Robomongo 文件中获取 todos

现在，我们将基于`mongodb-connect`创建一个新文件。在这个新文件中，我们将从数据库中获取记录，而不是插入记录。我将创建一个副本，将这个新文件称为`mongodb-find`，因为`find`是我们将用来查询数据库的方法。接下来，我们可以开始删除当前插入记录的所有注释掉的代码。让我们开始尝试从我们的 Todos 集合中获取所有的 Todos。现在，如果我转到 Robomongo 并打开`Todos`集合，我们只有一条记录：

![](img/c2f51586-1c3b-4789-bdef-5628eaa6ce3a.png)

为了使这个查询更有趣一些，我们将继续添加第二个。在 Robomongo 窗口中，我可以点击插入文档。Robomongo 可以删除、插入、更新和读取所有的文档，这使它成为一个很棒的调试工具。我们可以随时添加一个新的文档，其中`text`属性等于`Walk the dog`，我们还可以附加一个`completed`值。我将`completed`设置为`false`：

[PRE31]

现在，默认情况下，我们不会提供`_id`属性。这将让 MongoDB 自动生成那个 ObjectId，而在这里我们有我们的两个 Todos：

![](img/98401436-3619-4b3e-ade6-1924c766d1fe.png)

有了这个，让我们继续在 Atom 中运行我们的第一个查询。

# find 方法

在 Atom 中，我们要做的是访问集合，就像我们在`mongodb-connect`文件中使用`db.collection`一样，将集合名称作为字符串传递。这个集合将是`Todos`集合。现在，我们将继续使用集合上可用的一个叫做`find`的方法。默认情况下，我们可以不带参数地调用`find`：

[PRE32]

这意味着我们没有提供查询，所以我们没有说我们想要获取所有已完成或未完成的`Todos`。我们只是说我们想获取所有`Todos`：无论其值如何，一切。现在，调用 find 只是第一步。`find`返回一个 MongoDB 游标，而这个游标并不是实际的文档本身。可能有几千个，那将非常低效。它实际上是指向这些文档的指针，并且游标有大量的方法。我们可以使用这些方法来获取我们的文档。

我们将要使用的最常见的游标方法之一是`.toArray.`它确切地做了你认为它会做的事情。我们不再有游标，而是有一个文档的数组。这意味着我们有一个对象的数组。它们有 ID 属性，文本属性和完成属性。这个`toArray`方法恰好得到了我们想要的东西，也就是文档。`toArray`返回一个 promise。这意味着我们可以添加一个`then`调用，我们可以添加我们的回调，当一切顺利时，我们可以做一些像将这些文档打印到屏幕上的事情。

[PRE33]

我们将得到文档作为第一个和唯一的参数，我们还可以添加一个错误处理程序。我们将传递一个错误参数，我们可以简单地打印一些像`console.log(无法获取 todos)`的东西到屏幕上；作为第二个参数，我们将传递`err`对象：

[PRE34]

现在，对于成功的情况，我们要做的是将文档打印到屏幕上。我将继续使用`console.log`来打印一条小消息，`Todos`，然后我将再次调用`console.log`。这次，我们将使用`JSON.stringify`技术。我将传递文档，`undefined`作为我们的过滤函数和`2`作为我们的间距。

[PRE35]

我们现在有一个能够获取文档，将其转换为数组并将其打印到屏幕上的脚本。现在，暂时地，我将注释掉`db.close`方法。目前，那会干扰我们之前的代码。我们的最终代码将如下所示：

[PRE36]

保存文件并从终端运行它。在终端中，我将继续运行我们的脚本。显然，由于我们用 Robomongo 连接到了数据库，它正在某个地方运行；它正在另一个标签页中运行。在另一个标签页中，我可以运行脚本。我们将通过`node`运行它；它在`playground`文件夹中，文件本身叫做`mongodb-find.js`：

[PRE37]

当我执行这个文件时，我们将得到我们的结果：

![](img/3c47a41b-466f-4f2a-8554-d765a66781ee.png)

我们有我们的`Todos`数组和我们的两个文档。我们有我们的`_id`，我们的`text`属性和我们的`completed`布尔值。现在，我们有一种在 Node.js 中查询我们的数据的方法。现在，这是一个非常基本的查询。我们获取`Todos`数组中的所有内容，无论它是否具有某些值。

# 编写一个查询以获取特定值

为了基于某些值进行查询，让我们继续切换我们的`Todos`。目前，它们两个的`completed`值都等于`false`。让我们继续将`Walk the dog`的完成值更改为`true`，这样我们就可以尝试只查询未完成的项目。在 Robomongo 中，我将右键单击文档，然后单击编辑文档，然后我们可以编辑值。我将把`completed`值从`false`更改为`true`，然后我可以保存记录：

![](img/b3502cf4-287e-423b-bd7e-91e512b9dc29.png)

在终端内，我可以重新运行脚本来证明它已经改变。我将通过运行*control* + *C*关闭脚本，然后可以重新运行它：

![](img/b94e60a9-afce-4a1a-b615-bc1a35291560.png)

如前面的屏幕截图所示，我们有两个`Todos`，一个`completed`值为`false`，另一个`completed`值为`true`。默认情况下，待办事项应用程序可能只会显示您尚未完成的`Todos`集合。您已经完成的待办事项，比如`Walk the dog`，可能会被隐藏，尽管如果您点击了一个按钮，比如显示所有待办事项，它们可能是可访问的。让我们继续编写一个查询，只获取`completed`状态设置为`false`的`Todos`集合。

# 编写一个查询以获取已完成的待办事项

为了完成这个目标，在 Atom 中，我们将更改调用 find 的方式。我们不再传递`0`个参数，而是传递`1`个参数。这就是我们所谓的查询。我们可以开始指定我们想要查询`Todos`集合的方式。例如，也许我们只想查询`completed`值等于`false`的`Todos`。我们只需设置键值对来按值查询，如下所示：

[PRE38]

如果我在终端中关闭脚本后重新运行我们的脚本，我们只会得到我们的一个待办事项：

![](img/2c632bdf-9012-4b71-acf5-fdccbb9420ad.png)

我们的项目有一个`text`等于`Something to do`。它的`completed`状态为`false`，所以它显示出来。我们的另一个待办事项，`Walk the dog`的`text`属性没有显示出来，因为它已经完成。它不匹配查询，所以 MongoDB 不会返回它。当我们开始根据已完成的值、文本属性或 ID 查询我们的文档时，这将会很有用。让我们花点时间来看看我们如何可以通过 ID 查询我们的`Todos`中的一个。

# 按 ID 查询待办事项

我们需要做的第一件事是从我们的查询对象中删除所有内容；我们不再想要按`completed`值查询。相反，我们将按`_id`属性查询。

现在，为了说明这一点，我将从终端获取`completed`值为`false`的待办事项的 ID。我将使用*command* + *C*进行复制。如果您使用的是 Windows 或 Linux，您可能需要在突出显示 ID 后右键单击，并单击复制文本。现在我已经将文本放入剪贴板，我可以转到查询本身。现在，如果我们尝试像这样添加 ID：

[PRE39]

它不会按预期工作，因为我们在 ID 属性中拥有的不是一个字符串。它是一个 ObjectId，这意味着我们需要使用之前导入的`ObjectID`构造函数来为查询创建一个 ObjectId。

为了说明这将如何发生，我将继续缩进我们的对象。这将使它更容易阅读和编辑。

[PRE40]

现在，我要删除字符串并调用`new ObjectID`。`new ObjectID`构造函数确实需要一个参数：ID，在这种情况下，我们将其存储为字符串。这将按预期工作。

[PRE41]

我们在这里所做的是查询`Todos`集合，寻找任何具有与我们拥有的 ID 相等的`_id`属性的记录。现在，我可以保存这个文件，通过重新运行脚本来刷新一下，我们将得到完全相同的待办事项：

![](img/c40589de-95cc-48bc-bcfd-25d7c72ee229.png)

我可以继续将其更改为`Walk the dog`的待办事项，通过复制字符串值，将其粘贴到 ObjectID 构造函数中，并重新运行脚本。当我这样做时，我得到了`Walk the dog`的待办事项，因为那是我查询的 ObjectId。

现在，以这种方式查询是我们将使用 find 的方式之一，但除了`toArray`之外，我们的光标上还有其他方法可用。我们可以通过转到原生驱动程序的文档来探索其他方法。在 Chrome 中，打开 MongoDB 文档-这些是我在上一章中向您展示如何访问的文档-在左侧，我们有光标部分。

如果您点击，我们可以查看光标上可用的所有方法的列表：

![](img/fd2e42a1-88bf-4fc0-a885-e024cf5e2cc4.png)

这是从 find 返回的内容。在列表的最底部，我们有我们的`toArray`方法。我们现在要看的是称为 count 的方法。从以前的内容，您可以继续并点击 count；它将带您到文档；原生驱动程序的文档实际上非常好。这里有您可以提供的所有参数的完整列表。其中一些是可选的，一些是必需的，通常有一个真实世界的例子。接下来，我们可以确切地找出如何使用`count`。

# 实现计数方法

现在，我们将继续在 Atom 中实现`count`。我要做的是将当前查询复制到剪贴板，然后将其注释掉。我将用一个调用`count`替换我们对`toArray`的调用。让我们继续删除我们传递给 find 的查询。我们要做的是计算`Todos`集合中的所有 Todos。我们将不再调用`toArray`，而是调用 count。

[PRE42]

正如您在 count 的示例中看到的那样，他们这样调用 count：调用 count，传递一个回调函数，该函数在出现错误或实际计数时调用。您还可以将 promise 作为访问数据的一种方式，这正是我们使用`toArray`的方式。在我们的情况下，我们将使用 promise 而不是传递回调函数。我们已经设置好了 promise。我们需要做的就是将`docs`更改为`count`，然后我们将删除打印 docs 到屏幕的`console.log`调用者。在我们打印 Todos 之后，我们将打印`Todos count`，并传入值。

[PRE43]

这不是一个模板字符串，但我将继续并用一个替换它，用`` ` ``替换引号。现在，我可以传入`count`。

[PRE44]

Now that we have this in place, we have a way to count up all of the Todos in the `Todos` collection. Inside the Terminal, I'm going to go ahead and shut down our previous script and rerun it:

![](img/9fd9155b-32b9-4c22-9771-82a526d89065.png)

We get `Todos count` too, which is correct. The cursor that we have, a call to find, returns everything in the Todos collection. If you count all of that up, you're going to get those two Todo items.

Once again, these are `count` and `toArray`; they're just a subset of all of the awesome methods you have available to you. We will be using other methods, whether it be the MongoDB native driver or, as you'll see later, the library Mongoose, but for now let's go ahead and do a challenge, given what you know.

# Querying users collection

To get started, let's head into Robomongo, open up the Users collection, and take a look at all the documents we have inside of there. We currently have five. If you don't have the exact same number or yours are a little different, that's fine. I'm going to highlight them, right-click them, and click Expand Recursively. This is going to show me all of the key-value pairs for each document:

![](img/389ebd7e-0161-4c1b-a31d-bab563cc5621.png)

Currently, aside from the ID, they're all identical. The name's Andrew, the age is 25, and the location is Philadelphia. I'm going to tweak the name property for two of them. I'm going to right-click the first document, and change the name to something like `Jen`. Then, I'll go ahead and do the same thing for the second document. I'm going to edit that document and change the name from `Andrew` to `Mike`. Now I have one document with a name of `Jen`, one with `Mike`, and three with `Andrew`.

We're going to query our users, looking for all of the users with the name equal to the name that you provided in the script. In this case, I'm going to try to query for all documents in the `Users` collection where the name is `Andrew`. Then, I'm going to print them into the screen, and I will expect to get three back. The two with the names `Jen` and `Mike` should not show up.

The first thing we need to do is fetch from the collection. This is going to be the `Users` collection as opposed to the `Todos` collection we've used in this chapter. In the `db.collection`, we're looking for the `Users` collection and now we're going to go ahead and call `find`, passing in our query. We want a query, fetching all documents where the `name` is equal to the string `Andrew`.

[PRE45]

This is going to return the cursor. In order to actually get the documents, we have to call `toArray`. We now have a promise; we can attach a `then` call onto `toArray` to do something with the `docs`. The documents are going to come back as the first argument in our success handler, and right inside of the function itself we can print the docs to the screen. I'm going to go ahead and use `console.log(JSON.stringify())`, passing in our three classic arguments: the object itself, `docs`, `undefined`, and `2` for formatting:

[PRE46]

With this in place, we have now done. We have a query, and it should work. We can test it by running it from the Terminal. Inside the Terminal, I'm going to go ahead and shut down the previous connection and rerun the script:

![](img/4fd344a9-de63-45ff-a7b0-fd5353c06e3b.png)

When I do this, I get my three documents back. All of them have a `name` equal to `Andrew`, which is correct because of the query we set up. Notice the documents with a name equal to `Mike` or `Jen` are nowhere to be found.

We now know how to insert and query data from the database. Up next, we're going to take a look at how we can remove and update documents.

# Setting up the repo

Before we go any further, I do want to add version control to this project. In this section, we're going to create a new repo locally, make a new GitHub repository, and push our code to that GitHub repository. If you're already familiar with Git or GitHub, you can go ahead and do that on your own; you don't need to go through this section. If you're new to Git and it doesn't make sense just yet, that's also fine. Simply follow along, and we'll go through the whole process.

This section is going to be really simple; nothing MongoDB- related here. To get started, I am going to go ahead and initialize a new Git repository from the Terminal by using `git init`. This is going to initialize a new repository, and I can always run `git status` like this to take a look at the files that are untracked:

![](img/8c2070ea-2d27-43e3-b609-f3a4948be5bc.png)

Here we have our `playground` folder, which we want to add under version control, and we have `package.json`. We also have `node_modules`. We do not want to track this directory. This contains all of our npm libraries. To ignore `node_modules`, in Atom we're going to make the `.gitignore` file in the root of our project. If you remember, this lets you specify files and folders that you want to leave out of your version control. I'm going to create a new file called `.gitignore`. In order to ignore the `node_modules` directory, all we have to do is type it exactly as it's shown here:

[PRE47]

I'm going to save the file and rerun `git status` from the Terminal. We get the `.gitignore` folder showing up, and the `node_modules` folder is nowhere in sight:

![](img/ce67bd02-10cd-4cf0-b6ba-6754e2b5a4aa.png)

The next thing we're going to do is make our first commit, using two commands. First up, I'm going to use `git add .` to add everything to the next commit. Then, I can make the commit using `git commit` with the `-m` flag. A good message for this commit would be `Init commit`:

[PRE48]

Now before we go, I do want to make a GitHub repository and get this code up there. This is going to require me to open up the browser and go to [github.com](http://www.github.com). Once you're logged in we can make a new repo. I'm going to make a new repo and give it a name:

![](img/e42875c4-0a3d-41cb-8ee9-f150f3c02dcf.png)

I'm going to go with `node-course-2-todo-api`. You can name yours something else if you wish. I'm going to go with this one to keep the course files organized. Now I can go ahead and create this repository, and as you may recall, GitHub actually gives us a few helpful commands:

![](img/77b896c2-0987-4c37-a6d7-7fe02ea00204.png)

In this case, we're pushing an existing repository from the command line. We already went through the steps of initializing the repository, adding our files and making our first commit. That means I can take the following two lines, copy them, head over to the Terminal, and paste them in:

[PRE49]

You might need to do these one at a time, depending on your operating system. On the Mac, when I try to paste in multiple commands it's going to run all but the last, and then I just have to hit enter to run the last one. Take a moment to knock that out for your operating system. You might need to run it as one command, or you might be able to paste it all in and hit *enter*. Either way, what we have here is our code pushed up to GitHub. I can prove that it's pushed up by refreshing the repository page:

![](img/1bd89ea4-3f8b-4cb0-9ff0-194e62d0d9e0.png)

Right there we have all of our source code, the `.gitignore` file, `package.json`, and we have our `playground` directory with our MongoDB scripts.

That's it for this section. We'll explore how to delete data from a MongoDB collection in the next section.

# Deleting documents

In this section, you're going to learn how to delete documents from your MongoDB collections. Before we get into that, in order to explore the methods that let us delete multiple documents or just one, we want to create a few more Todos. Currently, the `Todos` collection only has two items, and we're going to need a few more in order to play around with all these methods involving deletion.

Now, I do have two. I'm going to go ahead and create a third by right-clicking and then going to Insert Document.... We'll make a new document with a `text` property equal to something like `Eat lunch`, and we'll set `completed` equal to `false`:

[PRE50]

Now before we save this, I am going to copy it to the clipboard. We're going to create a few duplicate Todos so we can see how we can delete items based off of specific criteria. In this case, we're going to be deleting multiple Todos with the same text value. I'm going to copy that to the clipboard, click Save, and then I'll create two more with the exact same structure. Now we have three Todos that are identical except for the ID, and we have two that have unique text properties:

![](img/8fc55286-909e-408d-8951-e315c454287f.png)

Let's go ahead and move into Atom and start writing some code.

# Exploring methods to delete data

I'm going to duplicate the `mongodb-find` file, creating a brand-new file called `mongodb-delete.js`. In here, we'll explore the methods for deleting data. I'm also going to remove all of the queries that we set up in the previous section. I am going to keep the `db.close` method commented out, as once again we don't want to close the connection just yet; it's going to interfere with these statements we're about to write.

Now, there are three methods that we'll be using in order to remove data.

*   The first one is going to be `deleteMany`. The `deleteMany` method will let us target many documents and remove them.
*   We'll also be using `deleteOne`, which targets one document and removes it.
*   And finally, we'll be using `findOneAndDelete`. The `findOneAndDelete` method lets you remove an individual item and it also returns those values. Imagine I want to delete a Todo. I delete the Todo, but I also get the Todo object back so I can tell the user exactly which one got deleted. This is a really useful method.

# The deleteMany method

Now, we're going to start off with `deleteMany`, and we're going to target those duplicates we just created. The goal in this section, is to delete every single Todo inside of the Todos collection that has a `text` property equal to `Eat lunch`. Currently, there are three out of five that fit that criteria.

In Atom, we can go ahead and kick things off by doing `db.collection`. This is going to let us target our Todos collection. Now, we can go ahead and use the collection method `deleteMany`, passing in the arguments. In this case, the only argument we need is our object, and this object is just like the object we passed to find. With this, we can target our Todos. In this case, we're going to delete every Todo where the `text` equals `Eat lunch`.

[PRE51]

We didn't use any punctuation in RoboMongo, so we're also going to avoid punctuation over in Atom; it needs to be exactly the same.

Now that we have this in place, we could go ahead and tack on a `then` call to do something when it either succeeds or fails. For now, we'll just add a success case. We are going to get a result argument passed back to the callback, and we can print that to the `console.log(result)` screen, and we'll take a look at exactly what is in this result object a bit later.

[PRE52]

With this in place, we now have a script that deletes all Todos where the text value is `Eat lunch`. Let's go ahead and run it, and see exactly what happens. In the Terminal, I'm going to run this file. It's in the `playground` folder, and we just called it `mongodb-delete.js`:

[PRE53]

Now when I run it, we get a lot of output:

![](img/7744c074-3a75-4b6b-8dc0-2d5f890da471.png)

A really important piece of output, the only important piece actually, is up at the very top. If you scroll to the top, what you're going to see is this `result` object. We get `ok` set to `1`, which means things did go as expected, and we get `n` set to `3`. `n` is the number of records that were deleted. In this case, we had three Todos that match that criteria, so three Todos were deleted. This is how you can target and delete many Todos.

# The deleteOne Method

Now, aside from `deleteMany`, we have `deleteOne`, and `deleteOne` works exactly the same as `deleteMany`, only it deletes the first item it sees that matches the criteria and then it stops.

To illustrate exactly how this works, we're going to create two items inside of our collection. If I give things a refresh, you will see that we now only have two documents:

![](img/9a571ad9-8625-46c8-813d-118afc431db6.png)

These are the ones we started with. I'm going to insert documents again using the same data that's already in my clipboard. This time we'll just make two document, two that are identical.

# The deleteOne method

The goal here is to use `deleteOne` to delete the document where the text equals `Eat lunch`, but since we're using `deleteOne` and not `deleteMany`, one of these should stay around and one of them should go away.

Back inside of Atom, we can go ahead and get started by calling `db.collection` with the collection name we want to target. In this case it's `Todos` again, and we're going to use `deleteOne`. The `deleteOne` method takes that same criteria. We're going to target documents where `text` equals `Eat lunch`.

This time though, instead of deleting multiple documents we're just going to delete the one, and we are still going to get that same exact result. To prove it, I'll just print to the screen like we did previously with `console.log(result)`:

[PRE54]

With this in place, we can now rerun our script and see what happens. In the Terminal, I'm going to shut down our current connection and rerun it:

![](img/30033a3b-581d-4d26-9540-f20270b17f0a.png)

We get a similar-looking object, a bunch of junk we don't really care about, but once again if we scroll to the top we have a `result` object, where `ok` is `1` and the number of deleted documents is also `1`. Even though multiple documents did pass this criteria it only deleted the first one, and we can prove that by going over to Robomongo, right-clicking up above, and viewing the documents again. This time around, we have three Todos.

We do still have one of the Todos with the `Eat lunch` text:

![](img/d13d8b43-2602-4ce3-9343-ca45bc01c592.png)

And now that we know how to use these two methods, I want to take a look at my favorite method. This is `findOneAndDelete`.

# The findOneAndDelete method

Most of the time, when I'm deleting a document, I only have the ID. This means that I don't exactly know what the text is or the completed status, and that can be really useful depending on your user interface. For example, if I delete a Todo, maybe I want to show that next, saying *You deleted the Todo that says Eat lunch*, with a little undo button in case they didn't mean to take that action. Getting the data back as well as deleting it can be really useful.

In order to explore `findOneAndDelete`, we're going to once again target the Todo where the `text` equals `Eat lunch`. I'm going to go ahead and comment out `deleteOne`, and next we can get started by accessing the appropriate collection. The method is called `findOneAndDelete`. The `findOneAndDelete` method takes a very similar set of arguments. The only thing we need to pass in is the query. This is going to be identical to the ones we have in the previous screenshot. This time though, let's go ahead and target Todos that had a `completed` value set to `false`.

Now there are two Todos that fit this query, but once again we're using a `findOne` method, which means it's only going to target the first one it sees, the one with a `text` property of `Something to do`. Back in Atom, we can get this done by targeting Todos where `completed` equals `false`. Now, instead of getting back a result object with an `ok` property and an `n` property, the `findOneAndDelete` method actually gets that document back. This means we can tack on a `then` call, we can get our result, and we can print it to the screen once again with `console.log(result)`:

[PRE55]

Now that we have this in place, let's test things out over in the Terminal. In the Terminal, I'm going to shut down the script and start it up again:

![](img/c21e6884-fda6-41aa-aa79-316cdfcf9085.png)

We get a few different things in our result object. We do get an `ok` set to `1`, letting us know things went as planned. We have a `lastErrorObject`; we'll talk about that in just a second; and we have our `value` object. This is the actual document we deleted. This is why the `findOneAndDelete` method is super handy. It gets that document back as well as deleting it.

Now in this particular case, the `lastErrorObject`, once again just has our `n` property, and we can see the number of Todos that were deleted. There is other information that could potentially be in `lastErrorObject`, but that's only going to happen when we use other methods, so we'll look at that when the time comes. For now, when you delete a Todo, we just get the number back.

With this in place, we now have three different ways we can target our MongoDB documents and remove them.

# Using the deleteMany and findOneAndDelete methods

We're going to go ahead and go over a quick challenge to test your skills. Inside of Robomongo, we can look at the data we have in the `Users` collection. I'm going to open it up, highlight all the data, and expand it recursively so we can view it:

![](img/93f0f8e8-ab68-4b27-a89e-83c04cd39268.png)

We have the name Jen; we have Mike; we have Andrew, Andrew and Andrew. This is perfect data. Yours might look a little different, but the goal is to use two methods. First up, look for any duplicates, anything that has a name set to the name of another document. In this case, I have three documents where the name is Andrew. What I want to do is use `deleteMany` to target all of these documents and remove them. I also want to use `findOneAndDelete` to delete another document; it doesn't matter which one. And I want you to delete it by ID.

In the end, both statements should show their effect over inside of Robomongo. When I'm done, I'm hoping to see these three documents deleted. They all have the name Andrew, and I'm hoping to see the document where the name Mike is deleted, because I'm going to target this one by ID in my `findOneAndDelete` method call.

First up, I'm going to write my scripts, one for deleting users where the name is `Andrew` and one for deleting the document with the ID. In order to grab the ID, I am going to go ahead and edit it and simply grab the text inside of quotes, and then I can cancel the update and move into Atom.

# Removing duplicate documents

First up, we're going to go ahead and try to remove the duplicate users, and I'm going to do this by using `db.collection`. We're going to target the `Users` collection, and in this particular case, we're going to be using the `deleteMany` method. Here, we're going to try to delete all of the users where the `name` property equals `Andrew`.

[PRE56]

Now I could tack on a then call to check for success or errors, or I could just leave it like this, which is what I'm going to do. If you use a callback or the promise then method, that is perfectly fine. As long as the deletion happens, you're good to go.

# Targeting the documents using ID

Next up, I'm going to write the other statement. We're going to target the `Users` collection once again. Now, we're going to go ahead and use the `findOneAndDelete` method. In this particular case, I am going to be deleting the Todo where the `_id` equals the ObjectId I have copied to the clipboard, which means I need to create a `new ObjectID`, and I also need to go ahead and pass in the value from the clipboard inside of quotes.

[PRE57]

Either single or double would work. Make sure the capitalization of `ObjectID` is identical to what you have defined, otherwise this creation will not happen.

Now that we have the ID created and passed in as the `_id` property, we can go ahead and tack on a `then` callback. Since I'm using `findOneAndDelete`, I am going to print that document to the screen. Right here I'll get my argument, `results`, and I'm going to print it to the screen using our pretty- printing method,`console.log(JSON.stringify())`, passing in those three arguments, the `results`, `undefined`, and the spacing, which I'm going to use as `2`.

[PRE58]

With this in place, we are now ready to go.

# Running the findOneAndDelete and deleteMany statements

Let's go ahead and comment out `findOneAndDelete` first. We'll run the `deleteMany` statement. Over in the Terminal, I can shut down the current connection, start it up again, and if we go over to Robomongo, we should see that those three documents were deleted. I'm going to right-click on `Users` and view the documents:

![](img/f0a3f3dc-cabf-4503-bc29-7152fe580144.png)

We just get the two documents back. Anything where the name was `Andrew` is now removed, which means our statement worked as expected, and this is fantastic.

Next up, we can run our `findOneAndDelete` statement. In this case, we're expecting that that one document, the one where the `name` equals `Mike`, gets removed. I'm going to go ahead and make sure I save the file. Once I do, I can move into the Terminal and rerun the script. This time around, we get the document back where the `name` is `Mike`. We did target the correct one, and it does appear that one item was deleted:

![](img/b3fb5a61-5cdd-45ea-85fc-52ebca362ae0.png)

I can always go ahead and verify this by refreshing the collection inside of Robomongo:

![](img/84ba63a2-1cd9-48ff-90df-833865eb2282.png)

I get my collection with just one document inside of it. We are now done. We know how to delete documents from our MongoDB collections; we can delete multiple documents; we can target just one, or we can target one and get its value back.

# Making commit for the deleting documents methods

Before we go, let's go ahead and make a commit, pushing it up to GitHub. In the Terminal, I can shut down the script and I can run `git status` to see what files we have untracked. Here, we have our `mongodb-delete` file. I can add it using `git add .` and then I can commit, using `git commit` with the `-m` flag. Here, I can go ahead and provide a commit message, which is going to be `Add delete script`:

[PRE59]

I'm going to make that commit and I am going to push it up to GitHub using `git push`, which will default to the origin remote. When you only have one remote, the first one is going to be called origin. This is the default name, just like master is the default branch. With this in place, we are now done. Our code is up on GitHub. The topic of the next section is updating, which is where you're going to learn how to update documents inside of a collection.

# Updating data

You know how to insert, delete, and fetch documents out of MongoDB. In this section, you're going to learn how to update documents in your MongoDB collections. To kick things off, as usual, we're going to duplicate the last script we wrote, and we'll update it for this section.

I'm going to duplicate the `mongodb-delete` file, renaming it to `mongodb-update.js`, and this is where we'll write our update statements. I'm also going to delete all of the statements we wrote, which is the deleted data. Now that we have this in place, we can explore the one method we'll be looking at in this section. This one is called `findOneAndUpdate`. It's kind of similar to `findOneAndDelete`. It lets us update an item and get the new document back. So if I update a Todo, set it as `completed` equal to `true`, I will get that document back in the response. Now in order to get started, we're going to be updating one of the items that we have inside of our Todos collection. If I view the documents, we currently have two. The goal here is going to be to update the second item, the one where `text` equals `Eat lunch`. We're going to try to set the `completed` value to `true`, which would be a pretty common action.

If I check off a Todo item, we want to toggle that completed Boolean value. Back inside of Atom, we're going to kick things off by accessing the appropriate collection. That'll be `db.collection`. The collection name is `Todos`, and the method we'll be using is `findOneAndUpdate`. Now, `findOneAndUpdate` is going to take the most arguments we've used so far, so let's go ahead and look up the documentation for it for future reference.

Over inside of Chrome, we currently have the Cursor tab open. This is where we have the `count` method defined. If we scroll next the Cursor tab, we have our other tabs. The one we're looking for is `Collection`. Now, inside of the Collection section, we have our typedefs and our methods. We're looking at methods here, so if I scroll down, we should be able to find `findOneAndUpdate` and click it. Now, `findOneAndUpdate` takes quite a few arguments. The first one is the `filter`. The `update` argument lets us target the document we want to update. Maybe we have the text, or most likely we have the ID of the document. Next up is the actual updates we want to make. We don't want to update the ID, we just want to filter by ID. In this case, the updates are going to be updating the completed Boolean. Then we have some options, which we are going to define. We'll use just one of them. We also have our `callback`. We're going to leave off the callback as we've been doing so so far, in favor of promises. As you can see on the documentation page, it returns a promise if no callback is passed in, and that's exactly what we expect. Let's go ahead and start filling out the appropriate arguments for `findOneAndUpdate`, kicking things off with the `filter`. What I'm going to do is filter by ID. In Robomongo, I can grab the ID of this document. I'm going to edit it and copy the ID to the clipboard. Now, in Atom, we can start querying the first object, `filter`. We're only looking for documents where the `_id` equals `new ObjectID` with the value that we copied to the clipboard. This is all we need for the `filter` argument. Next up is going to be the actual updates we want to apply, and this is not exactly straightforward. What we have to do here is learn about the MongoDB update operators.

We can view a complete list of these operators and exactly what they are by googling `mongodb update operators`. When I do this, we're looking for the [mongodb.com](http://www.mongodb.com) documentation:

![](img/6f97eb20-78fa-402b-a775-9cfc27409e28.png)

Now this documentation is specific to MongoDB, which means it's going to work with all of the drivers. In this case, it is going to work with our Node.js driver. If we scroll down further, we can look at all of the update operators we have access to. The most important, and the one we're going to get started with, is the `$set` operator. This lets us set a field's value inside of our update, which is exactly what we want to do. There's other operators, like increment. This one, `$inc`, lets you increment a field's value, like the age field in our `Users` collection. Although these are super useful, we're going to get started with `$set`. In order to use one of these operators, what we need to do is type it out, `$set`, and then set it equal to an object. In this object, these are the things that we're actually going to be setting. For example, we want to set `completed` equal to `true`. If we tried to put `completed` equal to `true` at the root of the object like this, it would not work as expected. We have to use these update operators, which means we need this. Now that we have our updates in place using the set update operator, we can go ahead and provide our third and final argument. If you head over to the documentation for `findOneAndUpdate`, we can take a look at the `options` real quick. The one we care about is `returnOriginal`.

The `returnOriginal` method is defaulted to `true`, which means that it returns the original document, not the updated one, and we don't want that. When we update a document, we want to get back that updated document. What we're going to do is set `returnOriginal` to `false`, and that's going to happen in our third and final argument. This one is also going to be an object, `returnOriginal`, which is going to be setting equal to `false`.

With this in place, we are done. We can tack on a `then` call to do something with the results. I'll get my result back and I can simply print it to the screen, and we can take a look at exactly what comes back:

[PRE60]

Now, let's go ahead and run this from the Terminal. I'm going to save my file inside the Terminal. We're going to be running `node`. The file is in the `playground` folder, and we will call it `mongodb-update.js`. I'm going to run the following script:

[PRE61]

We get back the value prop, just like we did when we used `findOneAndDelete`, and this has our document with the completed value set to true, which is the brand-new value we just set, which is fantastic:

![](img/6ea4eeaf-5eb6-4035-be14-aa56c0efb3c1.png)

If we head over to Robomongo, we can confirm that the value was indeed updated. We can see this in the old document, where the value is false. I'm going to open up a new view for Todos:

![](img/936011e3-b25f-4701-ac2d-f15393c3216f.png)

We have Eat lunch, with a completed value of true. Now that we have this in place, we know how to insert, delete, update, and read documents from our MongoDB collections. To wrap this section up, I want to give you a quick challenge. Over inside of the `Users` collection, you should have a document. It should have a name. It's probably not `Jen`; it's probably something that you set. What I want you to do is update this name to your name. Now if it's already your name, that's fine; you can change it to something else. I also want you to use `$inc`, the increment operator that we talked about, to increment this by 1\. Now I'm not going to tell you exactly how increment works. What I want you to do is head over to the docs, click on the `operator`, and then scroll down to see the examples. There's examples for each operator. It's going to become really useful for you to learn how to read documentation. Now, documentation for libraries is not always going to be the same; everyone does it a little differently; but once you learn how to read the docs for one library, it gets a lot easier to read the docs for others, and I can only teach so much in this course. The real goal of this course is to get you writing your own code, doing your own research, and looking up your own documentation, so your goal once again is to update this document, setting the name to something other than what it's currently set to, and incrementing the age by 1.

To kick things off, I'm going to grab the ID of the document in Robomongo, since this is the document I want to update. I'll copy the ID to the clipboard, and now we can focus on writing that statement in Atom. First up, we'll update the name, since we already know how to do that. In Atom, I'm going to go ahead and duplicate the statement:

[PRE62]

I'll copy it and paste it. Back inside of Atom, we can start swapping things out. First up, we're going to swap out the old ID for the new one, and we're going to change what we passed to set. Instead of updating `completed`, we want to update `name`. I'm going to set the `name` equal to something other than `Jen`. I'm going to go ahead and use my name, `Andrew`. Now, we are going to keep `returnOriginal` set to `false`. We want to get the new document back, not the original. Now, the other thing that we need to do is increment the age. This is going to be done via the increment operator, which you should have explored using the documentation over inside of Chrome. If you click on `$inc`, it's going to bring you to the `$inc` part of the documentation, and if you scroll down, you should be able to see an example. Right here, we have an example of what it looks like to increment:

![](img/ea8c2677-365e-4533-80bb-e53952b498a4.png)

We set `$inc` just like we set `set`. Then, inside of the object, we specify the things we want to increment, and the degree to which we want to increment them. It could be `-2`, or in our case, it would be positive, `1`. In Atom, we can implement this, as shown in the following code:

[PRE63]

I'll set `$inc` equal to an object, and in there, we'll increment the `age` by `1`. With this in place, we are now done. Before I run this file, I am going to comment out to the other call to `findOneAndUpdate`, just leaving the new one. I also need to swap out the collection. We're no longer updating the Todos collection; we're updating the `Users` collection. Now, we are good to go. We're setting the `name` equal to `Andrew` and we're incrementing the `age` by `1`, which means that we would expect the age in Robomongo to be 26 instead of 25\. Let's go ahead and run this by restarting the script over in the Terminal:

![](img/92d5c78a-7d25-4e19-aad4-155256400d0a.png)

We can see our new document, where the name is indeed `Andrew` and the age is indeed `26`, and this is fantastic. Now that you know how to use the increment operator, you can also go off and learn all of the other operators you have available to you inside of your update calls. I can double-check that everything worked as expected in Robomongo. I'm going to go ahead and refresh the `Users` collection:

![](img/4bce35a0-d75f-4431-8e26-93dbc12f7e02.png)

We have our updated document right here. Well, let's wrap this section up by committing our changes. In the Terminal, I'm going to run `git status` so we can view all of the changes to the repository:

![](img/a0f74cdc-051d-4c3b-9a02-3539a35a2383.png)

Here, we just have one untracked file, our `mongodb-update` script. I'm going to use `git add .` to add that to the next commit, and then I'll use `git commit` to actually make the commit. I am going to provide the `-m` argument for `message` so we can specify a message, which is going to be `Add update script`:

[PRE64]

And now we can run the commit command and push it up to GitHub, so our code is backed up on our GitHub repository:

[PRE65]

更新完成后，我们现在已经掌握了所有基本的 CRUD（创建、读取、更新和删除）操作。接下来，我们将讨论一个叫做 Mongoose 的东西，我们将在 Todo API 中使用它。

# 总结

在本章中，我们从连接到 MongoDB 并写入数据开始。然后，我们继续了解了在 MongoDB 上下文中的`id`属性。在学习更多关于获取数据之后，我们探索了在文档中删除数据的不同方法。

在下一章中，我们将继续与 Mongoose、MongoDB 和 REST API 进行更多的操作。
