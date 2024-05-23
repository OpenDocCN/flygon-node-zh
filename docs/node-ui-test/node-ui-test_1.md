# 第一章：使用 Zombie.js 入门

> "Zombie.js 是一个轻量级的框架，用于在模拟环境中测试客户端 JavaScript 代码。无需浏览器。"

这个定义来自*Zombie.js*文档，网址为[`zombie.labnotes.org`](http://zombie.labnotes.org)

为您的 Web 应用程序自动化测试对于拥有高质量的产品至关重要，但正确执行可能是一种痛苦的经历。这就是为什么大多数时候项目的这一部分从未得到实施。开发人员要么限制自己只测试底层业务逻辑和控制流，要么，如果他们真的想测试用户界面，必须采用复杂的设置，以某种方式连接到真实的浏览器并使用远程脚本对其进行命令。

Zombie.js 为这种情景提供了一个快速简便的替代方案，使您可以仅通过使用 JavaScript 轻松快速地为您的 Web 应用程序创建自动化测试。

本章涵盖的主题有：

+   软件测试的简要历史

+   理解服务器端 DOM

+   Zombie.js 的内部工作原理

在本章结束时，您应该了解 Zombie.js 的工作原理以及可以使用它进行测试的应用程序类型。

# 软件和用户界面测试的简要历史

软件测试是收集有关某种产品或服务质量的信息的必要活动。在传统的软件开发周期中，这项活动被委托给一个唯一工作是在软件中找问题的团队。如果正在向国内终端用户销售通用产品，或者公司正在购买许可的操作系统，则需要进行这种类型的测试。

在大多数定制软件中，测试团队负责手动测试软件，但通常客户必须进行验收测试，以确保软件的行为符合预期。

每当这些团队中的某人在软件中发现新问题时，开发团队就必须修复软件并将其重新放入测试循环中。这意味着每次发现错误时，交付最终版本的软件所需的成本和时间都会增加。此外，问题在开发过程的后期被发现，将会对产品的最终成本产生更大的影响。

此外，软件交付方式在过去几年发生了变化；网络使我们能够轻松交付软件及其升级，缩短了新功能开发和投入使用之间的时间。但一旦交付了产品的第一个版本并有一些客户在使用，你可能会面临一个困境；较少的更新可能意味着产品很快就会过时。另一方面，对软件进行许多更改增加了出现问题的可能性，使您的软件变得有缺陷，这可能会让客户流失。

关于如何缓解交付有缺陷产品的风险并增加按时交付新功能的机会，以及整体产品达到一定的质量标准，有许多版本和迭代的开发过程，但是所有参与软件构建的人都必须同意，越早发现错误越好。

这意味着您应该尽早发现问题，最好是在开发周期中。不幸的是，每次软件更改时都通过手工完全测试软件将会很昂贵。解决方案是自动化测试，以最大化测试覆盖率（应用程序代码的百分比和可能的输入变化）并最小化运行每个测试所需的时间。如果您的测试只需几秒钟就能运行，您就可以负担得起每次对代码库进行单个更改时运行测试。

## 进入自动化时代

测试自动化已经存在了一些年头，甚至在 Web 出现之前就有了。一旦**图形用户界面**（**GUI**）开始变得流行，允许你录制、构建和运行自动化测试的工具开始出现。由于有许多语言和 GUI 库用于构建应用程序，许多涵盖其中一些的工具开始出现。通常它们允许你录制一个测试会话，然后可以自动重现。在这个会话中，你可以自动化指针点击事物（按钮、复选框、窗口上的位置等），选择值（例如从选择框中选择），输入键盘操作并测试结果。

所有这些工具操作起来都相当复杂，而且最糟糕的是，大多数都是特定技术的。

但是，如果你正在构建一个使用 HTML 和 JavaScript 的基于 Web 的应用程序，你有更好的选择。其中最著名的可能是 Selenium，它允许你录制、更改和运行针对所有主要浏览器的测试脚本。

你可以使用 Selenium 来运行测试，但是你至少需要一个浏览器让 Selenium 附加到其中，以便加载和运行测试。如果你尽可能多地使用浏览器来运行测试，你将能够保证你的应用在所有浏览器上都能正确运行。但是由于 Selenium 插入到浏览器并控制它，在尽可能多的浏览器上运行相当复杂的应用的所有测试可能需要一些时间，而你最不希望的就是尽可能少地运行测试。

## 单元测试与集成测试

通常，你可以将自动化测试分为两类，即单元测试和集成测试。

+   **单元测试**：这些测试是选择应用程序的一个小子集（例如一个类或特定对象）并测试该类或对象向应用程序的其余部分提供的接口。通过这种方式，你可以隔离一个特定的组件，并确保它的行为符合预期，以便应用程序中的其他组件可以安全地使用它。

+   **集成测试**：这些测试是将单独的组件组合在一起并作为一个工作组进行测试。在这些测试中，你与用户界面进行交互和操作，用户界面反过来与应用程序的基础块进行交互。你使用 Zombie.js 进行的测试属于这一类。

## Zombie.js 是什么

Zombie.js 允许你在没有真实网络浏览器的情况下运行这些测试。相反，它使用一个模拟的浏览器，在其中存储 HTML 代码并运行你可能在 HTML 页面中有的 JavaScript。这意味着不需要显示 HTML 页面，节省了本来会被渲染的宝贵时间。

然后你可以使用 Zombie.js 来模拟浏览器加载页面，并且一旦页面加载完成，执行某些操作并观察结果。你可以使用 JavaScript 来完成所有这些，而不需要在客户端代码和测试脚本之间切换语言。

# 理解服务器端的 DOM

Zombie.js 运行在 Node.js（[`nodejs.org`](http://nodejs.org)）之上，这是一个可以轻松使用 JavaScript 构建网络服务器的平台。它运行在谷歌快速的 V8 JavaScript 引擎之上，这也是谷歌 Chrome 浏览器的动力来源。

### 注意

在撰写本文时，V8 实现了 JavaScript ECMA 3 标准和部分 ECMA 5 标准。并非所有浏览器都平等地实现了所有版本的 JavaScript 标准的所有功能。这意味着即使你的测试在 Zombie.js 中通过了，也不意味着它们会在所有目标浏览器中通过。

在 Node.js 之上，有一个名为 JSDOM 的第三方模块（[`npmjs.org/package/jsdom`](https://npmjs.org/package/jsdom)），它允许你解析 HTML 文档并在该文档的表示之上使用 API；这使你能够查询和操作它。提供的 API 是标准的**文档对象模型**（**DOM**）。

所有浏览器都实现了 DOM 标准的一个子集，这是由**万维网联盟**（**W3C**）内的一个工作组作为一组推荐来规定的。它们有三个推荐级别。JSDOM 实现了所有三个。

Web 应用程序直接或间接（通过使用诸如 jQuery 之类的工具）使用浏览器提供的 DOM API 来查询和操作文档，从而使您能够创建具有复杂行为的浏览器应用程序。这意味着通过使用 JSDOM，您自动支持大多数现代浏览器支持的任何 JavaScript 库。

### 提示

**下载示例代码**

您可以从您在[`www.packtpub.com`](http://www.packtpub.com)的帐户中下载您购买的所有 Packt 图书的示例代码文件。如果您在其他地方购买了这本书，您可以访问[`www.packtpub.com/`](http://www.packtpub.com/)support 并注册，以便将文件直接发送到您的电子邮件。

## Zombie.js 是您的无头浏览器

在 Node.js 和 JSDOM 之上是 Zombie.js。Zombie.js 提供类似浏览器的功能和一个可用于测试的 API。例如，Zombie.js 的典型用法是打开浏览器，请求加载某个 URL，填写表单上的一些值并提交，然后查询生成的文档，看看是否有成功消息。

为了更具体，这里是一个简单的 Zombie.js 测试代码的示例：

```js
browser.visit('http://localhost:8080/form', function() {
  browser
    .fill('Name', 'Pedro Teixeira')
    .select('Born', '1975')
    .check('Agree with terms and conditions')
    .pressButton('Submit', function() {
      assert.equal(browser.location.pathname, '/success');
      assert.equal(browser.text('#message'),
        'Thank you for submitting this form!');
    });
});
```

在这里，您正在典型地使用 Zombie.js：加载包含表单的 HTML 页面；填写并提交该表单；然后验证结果是否成功。

### 注意

Zombie.js 不仅可以用于测试您的 Web 应用程序，还可以用于需要像浏览器一样行为的应用程序，例如 HTML 抓取器、爬虫和各种 HTML 机器人。

如果您要使用 Zombie.js 进行任何这些活动，请做一个良好的网络公民，并在道德上使用它。

# 摘要

创建自动化测试是任何软件应用程序开发过程的重要部分。在使用 HTML、JavaScript 和 CSS 创建 Web 应用程序时，您可以使用 Zombie.js 创建一组测试；这些测试加载、查询、操作并为任何给定的网页提供输入。

鉴于 Zombie.js 模拟了浏览器，并且不依赖于 HTML 页面的实际渲染，因此测试运行速度比如果您使用真实浏览器进行测试要快得多。因此，您可以在对应用程序进行任何小的更改时运行这些测试。

Zombie.js 在 Node.js 之上运行，使用 JSDOM 在任何 HTML 文档之上提供 DOM API，并使用简单的 API 模拟类似浏览器的功能，您可以使用 JavaScript 创建您的测试。