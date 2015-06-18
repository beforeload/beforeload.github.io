# 移动性能分析

> 内容简介
1. 针对高延迟的移动网络
2. 传输低于一秒渲染体验
3. FAQ

页面速度监控对一个移动网络的页面进行分析，可以看出是否按照我们推荐的，在低于一秒的时间里作出页面渲染。调查研究表明，任何延迟超过一秒都会导致用户中断连惯的思想，造成糟糕的用户体验。我们的目标是无论任何类型的网络或者设备，都能提供最优质的体验，让用户保持对页面的关注。

我们的预算只有一秒钟的时间，这很难满足。幸运的是，整个页面渲染的时间并不一定要在预算以内，相反，我们在一秒以内提供和渲染首屏（ATF）内容，让用户尽可能早地开始交互。然后，当用户正在理解首屏内容时，在后台逐步接收页面的其他部分。

### 适配高延迟的移动网络

相对于其他网络，在移动设备上满足一秒的首屏标准会遇到一些特殊的挑战。用户可能通过 2G, 3G或者4G 不同的网络进入你的网站。网络延迟也明显的高于有线连接，在1000毫秒的预算中，消费的一个重要的部分是渲染首屏内容：

1. 对于 3G 网络，200-300ms 线路来回时间（roundtrip time, RTT）
2. 对于 4G 网络，50-100ms 线路来回时间（roundtrip time, RTT）

3G在全球范围内是主导的网络类型，而4G的部署正在进行中，你应该还在期待大多数用户通过3G网络进入你的页面。因此，我们必须假定每个网络请求平均耗费200毫秒。

牢记这一点，我们来逆向思考。如果我们观察一个浏览器和服务器之间典型的一系列的连接，预算的600毫秒已经被用在网络开销上：DNS查询解决域名对应的IP地址，一个线路来回执行TCP握手，最后一个网络来回发送HTTP请求。留给我们的只有400ms！

### 传输低于一秒渲染体验

减去网络延迟，在我们的预算中，仅仅只剩下400毫秒，而需要做的事情还有很多：服务端必须有返回，客户端的应用代码必须执行，浏览器必须布局和渲染内容。考虑到这一点，下面的标准应该会帮助我们不超过预算。

(1) 服务端必须返回（<200毫秒）

服务器响应时间指的是服务器返回初始化的html，分块的网络传输时间。因为我们只有少的可怜的时间，
所以服务器响应时间要保持在一个极低的水平，理想情况在200毫秒以内，越少越完美！

(2) 重定向的次数应该最小化

额外的 HTTP 重定向会增加一或两个额外的网络线路来回（当需要一个额外的DNS查询时，为两个），
导致在3G网络情况下产生额外的几百毫秒的延迟。由此，我们强烈支持站长最小化重定向次数，最理想的是完全消除重定向 —— 这一点对于HTML文档尤为重要（尽可能的避免 "m 点“ 的重定向）。

(3) 首屏渲染的线路来回次数应该最小化

由于 TCP 如何评估一个连接的容量（例如TCP 慢启动），一个新的TCP连接不能立即使用所有客户端和服务器之间可用的带宽。因此，在一个新的连接的第一次线路来回，服务器最多传输10个TCP包（最多14KB)，然后在它增长阻塞的窗口和传输更多数据之前必须等待客户端接收数据。

由于TCP的这种行为，优化你的内容这很重要，可以最小化展现首屏页面的必要的数据传输造成的线路返回次数。理想情况下，首屏内容应该满足低于14KB，这允许浏览器在仅仅一次线路返回后绘制页面。
值得注意的是这10个包（IW10）的限制是TCP标准最新更新的：为了应用这些改变，你应该确保你的服务器更新到最新的版本。否则这个限制可能是3到4个包。

(4) 首屏内容避免外部阻塞的JavaScript和CSS

Before a browser can render a page to the user, it has to parse the page. If it encounters a non-async or blocking external script during parsing, it has to stop and download that resource. Each time it does that, it is adding a network round trip, which will delay the time to first render of the page.

As a result, the JavaScript and CSS needed to render the above the fold region should be inlined, and JavaScript or CSS needed to add additional functionality to the page should be loaded after the ATF content has been delivered.

(5) 给浏览器布局和渲染预留时间（200毫秒）

The process of parsing the HTML, CSS, and executing JavaScript takes time and client resources! Depending on the speed of the mobile device, and the complexity of the page, this process can take hundreds of milliseconds. Our recommendation is to reserve 200 milliseconds for browser overhead.

(6) 优化 JavaScript 执行和渲染时间

Complicated scripts and inefficient code can take tens and hundreds of milliseconds to execute - use built-in developer tools in the browser to profile and optimize your code. For a great introduction, take a look at our interactive course for Chrome Developer Tools.

Note: The above is also not the complete list of all possible optimizations - it is a list of top mobile criteria to deliver a sub one second rendering time - and all other web performance best practices should be applied. Check out PageSpeed Insights for further advice and recommendations.

For a deep-dive on the above mobile criteria, also check out
Web Fundamentals: Critical Rendering Path Optimizing the Critical Rendering Path for Instant Mobile Websites (slides, video).
Instant Mobile Websites: Techniques and Best Practices (slides, video)


### FAQ

4G 网络会对上面的移动标准造成怎样的影响？

Lower roundtrip latencies are one of the key improvements in 4G networks. This will help enormously, by reducing the total network overhead time, which is currently over 50% of our one second budget on 3G networks. However, 3G is the dominant network type around the world, and will be for years to come - you have to optimize pages with 3G users in mind.

我正在使用一个 JavaScript 库，例如 jQuery，有没有什么建议？

Many JavaScript libraries, such as JQuery, are used to enhance the page to add additional interactivity, animations, and other effects. However, many of these behaviors can be safely added after the ATF content is rendered. Investigate moving the execution and loading of such JavaScript until after the page is loaded.

我正在使用一个 JavaScript 框架来构建页面，有没有什么建议？

If the content of the page is constructed by client-side JavaScript, then you should investigate inlining the relevant JavaScript modules to avoid extra network roundtrips. Similarly, leveraging server-side rendering can significantly improve first page load performance: render JS templates on the server, inline the results into HTML, and then use client-side templating once the application is loaded.

SPDY和HTTP 2.0会带来怎样的帮助？

SPDY and HTTP 2.0 both aim to reduce latency of page loads by making more efficient use of the underlying TCP connection (multiplexing, header compression, prioritization). Further, server push can further help improve performance by eliminating extra network latency. We encourage you to investigate adding SPDY support on your servers, and switching to HTTP 2.0 once the standard is ready.

我该怎么在页面上定义标准的CSS?

In Chrome Developer Tools, open the Audits panel, and run a Web Page Performance report, in the generated report, look for Remove unused CSS rules. Or use any other third party tool, or script, to identify which CSS selectors are applied on each page.

这些最佳体验可以自动化吗？

Absolutely. There are many commercial and open-source web performance optimization (WPO) products which can help you meet some or all of the criteria above. For open-source solutions, take a look at the PageSpeed optimization tools.

我该怎样调整服务器来满足这些标准？

First, ensure that your server is running an up-to-date version of the operating system. In order to benefit from the increased initial TCP congestion window size (IW10), you will need Linux kernel 2.6.39+. For other operating systems, consult the documentation. 

To optimize server response time, instrument your code, or use an application monitoring solution to identify your bottleneck - e.g., scripting runtime, database calls, RPC requests, rendering, etc. The goal is to render the HTML response within 200 milliseconds.

内容安全政策如何？

If you are using CSP, then you may need to update your default policy. 
First, inline CSS attributes on HTML elements(e.g., < p style=...>) should be avoided where possible, as they often lead to unnecessary code duplication, and are blocked by default with CSP (disabled via “unsafe inline” on “style-src”). If CSP is enabled, it will block all inline script tags by default. If you have inline JavaScript, then you will need to update the CSP policy to either use script hashes or nonces or use “unsafe-inline” to enable all inline scripts to execute. If you have inline styles, then you will need to update the CSP policy to either use style hashes or nonces or use "unsafe-inline" to enable all inline style blocks to be processed.

Have more questions? Please feel free to ask and provide feedback in our discussion group at pagespeed-insights-discuss.


### 名词解释

1. Round-trip time(RTT) 是客户端发送请求的时间到服务器通过网络发送响应的时间间隔，不包括必要的数据传输时间。