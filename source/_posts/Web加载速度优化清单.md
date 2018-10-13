---
title: Web加载速度优化清单
abbrlink: 5891d813
date: 2018-09-01 15:57:23
description: 网页加载速度是衡量一个网页好坏的重要标准，网页遗弃率随网页加载时间的增加而增加
tags:
  - web技术
  - web加速
  - web性能
cover: /img/blur-fast-hurry-312848.jpg
---

网页加载速度是衡量一个网页好坏的重要标准，网页遗弃率随网页加载时间的增加而增加。据说近一半的用户希望网页加载时间不超过2s，超过3s一般就放弃该网页。时间就是生命，干等着，谁愿意平白无故地+1s呀，所以今天来整理下具体如何加快网页

### HTML
- **压缩 HTML:** HTML代码压缩，将注释、空格和新行从生产文件中删除。

    *为什么：*
    > 删除所有不必要的空格、注释和中断行将减少HTML的大小，加快网站的页面加载时间，并显著减少用户的下载时间。

    *怎么做：*
    > 大多数框架都有插件用来压缩网页的体积。你可以使用一组可以自动完成工作的NPM模块。

    * 🛠 [HTML minifier | Minify Code](http://minifycode.com/html-minifier/)
    * 📖 [Experimenting with HTML minifier — Perfection Kills](http://perfectionkills.com/experimenting-with-html-minifier/#use_short_doctype)

- **删除不必要的注释：** 确保从您的网页中删除注释。

    *为什么：*
    > 注释对用户来说是没有用的，应该从生产环境文件中删除。可能需要保留注释的一种情况是：保留远端代码库（keep the origin for a library）。

    *怎么做：*
    > 大多数情况下，可以使用HTML minify插件删除注释。

 * 🛠 [remove-html-comments - npm](https://www.npmjs.com/package/remove-html-comments)

- **删除不必要的属性：** 像 `type="text/javascript"` or `type="text/css"` 这样的属性应该被移除。

    ```html
    <!-- Before HTML5 -->
    <script type="text/javascript">
        // Javascript code
    </script>

    <!-- Today -->
    <script>
        // Javascript code
    </script>
    ```

    *为什么*
    > 类型属性不是必需的，因为HTML5把text/css和text/javascript作为默认值。没用的代码应在网站或应用程序中删除，因为它们会使网页体积增大。

    *怎么做：*
    > ⁃ 确保所有link和script标记都没有任何type属性。

    * 📖 [The Script Tag | CSS-Tricks](https://css-tricks.com/the-script-tag/)
   
- **在JavaScript引用之前引用CSS标记：**  确保在使用JavaScript代码之前加载CSS。

    *为什么：*
    > 在引用JavaScript之前引用CSS可以实现更好地并行下载，从而加快浏览器的渲染速度。

    *怎么做：*
    >确保 head 中的 link 和 style 始终位于 script 之前。


- **最小化iframe的数量：**  仅在没有任何其他技术可行性时才使用iframe。尽量避免使用iframe。

- **DNS预取：**  一次 DNS 查询时间大概在60-120ms之间或者更长，提前解析网页中可能的网络连接域名
    ```html
     <link rel="dns-prefetch" href="http://example.com/">
    ```
### CSS

-  **压缩:**  所有CSS文件都需要被压缩，从生产文件中删除注释，空格和空行。

    *为什么：*
    > 缩小CSS文件后，内容加载速度更快，并且将更少的数据发送到客户端，所以在生产中缩小CSS文件是非常重要，这对用户是有益的，就像任何企业想要降低带宽成本和降低资源。

    *怎么做：*
    > 使用工具在构建或部署之前自动压缩文件。

    * 🛠 [cssnano: 基于PostCSS生态系统的模块化压缩工具。](https://cssnano.co/)
    * 🛠 [@neutrinojs/style-minify - npm](https://www.npmjs.com/package/@neutrinojs/style-minify)

-  **Concatenation:**  CSS文件合并（对于HTTP/2效果不是很大）。

    ```html

    <!-- 不推荐 -->
    <link rel="stylesheet" href="foo.css"/>
    <link rel="stylesheet" href="bar.css"/>

    <!-- 推荐 -->
    <link rel="stylesheet" href="foobar.css"/>
    ```

    *为什么：*
    > 如果你还在使用HTTP/1，那么你就需要合并你的文件。不过在使用HTTP/2的情况下不用这样（效果待测试）。

    *怎么做：*
    > 在构建或部署之前使用在线工具或者其他插件来合并文件。
    > 当然，要确保合并文件后项目可以正常运行。

    * 📖 [HTTP: 优化应用程序交付 - 高性能浏览器网络 (O'Reilly)](https://hpbn.co/optimizing-application-delivery/#optimizing-for-http2)
    * 📖 [HTTP/2时代的性能最佳实践](https://deliciousbrains.com/performance-best-practices-http2/)

-  **非阻塞：**  CSS文件需要非阻塞引入，以防止DOM花费更多时间才能渲染完成。

    ```html
    <link rel="preload" href="global.min.css" as="style" onload="this.rel='stylesheet'">
    <noscript><link rel="stylesheet" href="global.min.css"></noscript>
    ```

    *为什么：*
    > CSS文件可以阻止页面加载并延迟页面呈现。使用`preload`实际上可以在浏览器开始显示页面内容之前加载CSS文件。

    *怎么做：*
    > 需要添加`rel`属性并赋值`preload`，并在`<link>`元素上添加`as=“style”`。

    * 📖 [loadCSS by filament group](https://github.com/filamentgroup/loadCSS)
    * 📖 [使用loadCSS预加载CSS的示例](https://gist.github.com/thedaviddias/c24763b82b9991e53928e66a0bafc9bf)
    * 📖 [使用rel =“preload”预加载内容](https://developer.mozilla.org/en-US/docs/Web/HTML/Preloading_content)
    * 📖 [Preload: What Is It Good For? — Smashing Magazine](https://www.smashingmagazine.com/2016/02/preload-what-is-it-good-for/)

-  **CSS类(class)的长度:** class的长度会对HTML和CSS文件产生（轻微）影响。

    *为什么：*
    > 甚至性能影响也是有争议的，项目的命名策略会对样式表的可维护性有重大影响。如果使用BEM，在某些情况下可能会写出比所需要的类名更长的字符。重要的是要明智的选择名字和命名空间。

    *怎么做：*
    > 可能有些人更关注类名的长度，但是网站按组件进行划分的话可以帮助减少类名的数量和长度。

    * 🛠 [long vs short class · jsPerf](https://jsperf.com/long-vs-short-class)

-  **不用的CSS:**  删除未使用的CSS选择器。

    *为什么：*
    > 删除未使用的CSS选择器可以减小文件的大小，提高资源的加载速度。

    *怎么做：*
    > ⚠️ 检查要使用的CSS框架是否已包含reset/normalize代码，可能不需要用到reset/normalize文件中的内容。

    * 🛠 [UnCSS Online](https://uncss-online.com/)
    * 🛠 [PurifyCSS](https://github.com/purifycss/purifycss)
    * 🛠 [PurgeCSS](https://github.com/FullHuman/purgecss)
    * 🛠 [Chrome DevTools Coverage](https://developers.google.com/web/updates/2017/04/devtools-release-notes#coverage)

###  JavaScript

-  **JS 压缩:**  所有JavaScript文件都要被压缩，生产环境中删除注释、空格和空行（在HTTP/2仍然有效果）。

    *为什么：*
    > 删除所有不必要的空格、注释和空行将减少JavaScript文件的大小，并加快网站的页面加载时间，提升用户体验。

    *怎么做：*
    > 建议使用下面的工具在构建或部署之前自动缩小文件。

    * 📖 [uglify-js - npm](https://www.npmjs.com/package/uglify-js)
    * 📖 [Short read: How is HTTP/2 different? Should we still minify and concatenate?](https://scaleyourcode.com/blog/article/28)


*  **非阻塞JavaScript：**  使用defer属性或使用async来异步加载JavaScript文件。

    ```html
    <!-- Defer Attribute -->
    <script defer src="foo.js">

    <!-- Async Attribute -->
    <script async src="foo.js">
    ```

    *为什么：*
    > JavaScript阻止HTML文档的正常解析，因此当解析器到达script标记时（特别是在<head>内），它会停止解析并且执行脚本。如果您的脚本位于页面顶部，则强烈建议添加`async`和`defer`，但如果在</body>标记之前加载，没有太大影响。但是，使用这些属性来避免性能问题是一种很好的做法。

    *怎么做：*
    > 添加`async`（如果脚本不依赖于其他脚本）或`defer`（如果脚本依赖或依赖于异步脚本）作为script脚本标记的属性。
    > 如果有小脚本，可以在异步脚本上方使用内联脚本。

    * 📖 [Remove Render-Blocking JavaScript](https://developers.google.com/speed/docs/insights/BlockingJS)


 -  **使用 tree shaking 技术减少 js 大小:**  通过构建工具分析 JavaScript 代码并移除生产环境中用不到的 js 模块或方法

    * 📖 [
Reduce JavaScript Payloads with Tree Shaking](https://developers.google.com/web/fundamentals/performance/optimizing-javascript/tree-shaking/)

-  **使用 code splitting 分包加载 js:**  通过分包加载，减少首次加载所需时间
    
    *怎么做：*
    > **Vendor splitting** 根据库文件拆分模块，例如 React 或 lodash 单独打包成一个文件
    > **Entry point splitting** 根据入口拆分模块，例如通过多页应用入口或者单页应用路由进行拆分
    > **Dynamic splitting** 根据动态加载拆分模块，使用动态加载语法 ```import()``` ，实现模块按需加载

    * 📖 [Reduce JavaScript Payloads with Tree Shaking](https://developers.google.com/web/fundamentals/performance/optimizing-javascript/code-splitting/) 

###  图片资源

 * 📖 [Image Bytes in 2018](https://httparchive.org/reports/page-weight#bytesImg)

*  **图像优化:**  在保证压缩后的图片符合产品要求的情况下将图像进行优化。

    *为什么：*
    > 优化的图像在浏览器中加载速度更快，消耗的数据更少。

    *怎么做：*
    > 尽可能尝试使用CSS3效果（而不是用小图像替代）
    > 尽可能使用字体图片
    > 使用 SVG
    > 使用编译工具并指定85以下的级别压缩。

    * 📖 [Image Optimization | Web Fundamentals | Google Developers](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/image-optimization)
    * 🛠 [TinyJPG – Compress JPEG images intelligently](https://tinyjpg.com/)
    * 🛠 [Kraken.io - Online Image Optimizer](https://kraken.io/web-interface)
    * 🛠 [Compressor.io - optimize and compress JPEG photos and PNG images](https://compressor.io/compress)
    * 🛠 [Cloudinary - Image Analysis Tool](https://webspeedtest.cloudinary.com/)


*  **图像格式：**  适当选择图像格式。

    *为什么：*
    > 确保图片不会减慢网站速度
        
    *怎么做：*
    > 使用[Lighthouse](https://developers.google.com/web/tools/lighthouse/)识别哪些图像可以使用下一代图片格式（如JPEG 2000m JPEG XR或WebP）。
    > 比较不同的格式，有时使用PNG8比PNG16好，有时候不是。

    * 📖 [Serve Images in Next-Gen Formats  |  Tools for Web Developers  |  Google Developers](https://developers.google.com/web/tools/lighthouse/audits/webp)
    * 📖 [What Is the Right Image Format for Your Website? — SitePoint](https://www.sitepoint.com/what-is-the-right-image-format-for-your-website/)
     * 📖 [PNG8 - The Clear Winner — SitePoint](https://www.sitepoint.com/png8-the-clear-winner/)
     * 📖 [8-bit vs 16-bit - What Color Depth You Should Use And Why It Matters - DIY Photography](https://www.diyphotography.net/8-bit-vs-16-bit-color-depth-use-matters/)

-  **使用矢量图像 VS 栅格/位图：**  可以的话，推荐使用矢量图像而不是位图图像。

    *为什么：*
    > 矢量图像（SVG）往往比图像小，具有响应性和完美缩放功能。而且这些图像可以通过CSS进行动画和修改操作。

*  **图像尺寸：**  如果已知最终渲染图像大小，请在<img>上设置宽度和高度属性。

    *为什么：*
    > 如果设置了高度和宽度，则在加载页面时会保留图像所需的空间。如果没有这些属性，浏览器就不知道图像的大小，也无法为其保留适当的空间，导致页面布局在加载期间发生变化。

*  **避免使用Base64图像：**  你可以将微小图像转换为base64，但实际上并不是最佳实践。

    * 📖 [Base64 Encoding & Performance, Part 1 and 2 by Harry Roberts](https://csswizardry.com/2017/02/base64-encoding-and-performance/)
    * 📖 [A closer look at Base64 image performance – The Page Not Found Blog](http://www.andygup.net/a-closer-look-at-base64-image-performance/)
    * 📖 [When to base64 encode images (and when not to) | David Calhoun](https://www.davidbcalhoun.com/2011/when-to-base64-encode-images-and-when-not-to/)
   * 📖 [Base64 encoding images for faster pages | Performance and seo factors](https://varvy.com/pagespeed/base64-images.html)

*  **懒加载：**  图像懒加载（始终提供noscript作为后备方案）。

    *为什么：*
    > 它能改善当前页面的响应时间，避免加载一些用户可能不需要或不必要的图像。

    *怎么做：*
    > 使用[Lighthouse](https://developers.google.com/web/tools/lighthouse/)可以识别屏幕外的图像数量。
    > 使用懒加载图像的JavaScript插件。

    * 🛠 [verlok/lazyload: Github](https://github.com/verlok/lazyload)
    * 📖 [Lazy Loading Images and Video  |  Web Fundamentals  |  Google Developers](https://developers.google.com/web/fundamentals/performance/lazy-loading-guidance/images-and-video/)
    * 📖 [5 Brilliant Ways to Lazy Load Images For Faster Page Loads - Dynamic Drive Blog](http://blog.dynamicdrive.com/5-brilliant-ways-to-lazy-load-images-for-faster-page-loads/)

*  **响应式图像：**  确保提供接近设备显示尺寸的图像。

    *为什么：*
    > 小型设备不需要比视口大的图像。建议在不同尺寸上使用一个图像的多个版本。

    *怎么做：*
    > 为不同的设备设置不同大小的图像。
    > 使用srcset和picture为每个图像提供多种变体（variants）。

     * 📖 [Responsive images - Learn web development | MDN](https://developer.mozilla.org/en-US/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images)

###  服务部署

-  **页面大小 < 1500 KB:**  (理想情况 < 500 KB) 尽可能减少页面和资源的大小。

    *为什么：*
    > 理想情况下，应该尝试让页面大小<500 KB，但Web页面大小中位数大约为1500 KB（即使在移动设备上）。根据你的目标用户、连接速度、设备，尽可能减少页面大小以尽可能获得最佳用户体验非常重要。

    *怎么做：*
    > 前端性能清单中的所有规则将帮助你尽可能地减少资源和代码。

    * 📖 [Page Weight](https://httparchive.org/reports/page-weight#bytesTotal)
    * 🛠 [What Does My Site Cost?](https://whatdoesmysitecost.com/)


*  **Cookie 大小:**  如果您使用cookie，请确保每个cookie不超过4096字节，并且一个域名下不超过20个cookie。

    *为什么：*
    > cookie存在于HTTP头中，在Web服务器和浏览器之间交换。保持cookie的大小尽可能低是非常重要的，以尽量减少对用户响应时间的影响。

    *怎么做：*
    > 消除不必要的cookie
    
    * 📖 [Cookie specification: RFC 6265](https://tools.ietf.org/html/rfc6265)
    * 📖 [Cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies)
    * 🛠 [Browser Cookie Limits](http://browsercookielimits.squawky.net/)
    * 📖 [Website Performance: Cookies Don't Taste So Good - Monitis Blog](http://www.monitis.com/blog/website-performance-cookies-dont-taste-so-good/)
    * 📖 [Google's Web Performance Best Practices #3: Minimize Request Overhead - GlobalDots Blog](https://www.globaldots.com/googles-web-performance-best-practices-3-minimize-request-overhead/)

-  **最小化HTTP请求：**  始终确保所请求的每个文件对网站或应用程序至关重要，尽可能减少http请求。

-  **使用CDN提供静态文件：**  使用CDN可以更快地在全球范围内获取到你的静态文件。

 * 📖 [10 Tips to Optimize CDN Performance - CDN Planet](https://www.cdnplanet.com/blog/10-tips-optimize-cdn-performance/)
 * 📖 [HTTP Caching  |  Web Fundamentals  |  Google Developers](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching)

-  **正确设置HTTP缓存标头：**  合理设置HTTP缓存标头来减少http请求次数。

-  **启用GZIP压缩** 

 * 📖 [Check GZIP compression](https://checkgzipcompression.com/)

-  **分域存放资源：**  由于浏览器同一域名并行下载数有限，利用多域名主机存放静态资源，增加并行下载数，缩短资源加载时间

-  **减少页面重定向**  

 
 以上清单仅为节选，来源 https://github.com/w3cay/Front-End-Performance-Checklist