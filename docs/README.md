# fearless<sup>233</sup>'s blog

> The harder you work, the more luck you have.

> ⭐️ 推荐文章：[个人博客搭建](/blog/个人博客搭建.md)

<!-- tabs:start -->

  #### **English**

  Hello!

  #### **French**

  Bonjour!

  #### **Italian**

  Ciao!

<!-- tabs:end -->

> 点击切换主题

  <div class="theme-preview">
    <a data-theme="vue">vue.css</a>
    <a data-theme="dark">dark.css</a>
    <a data-theme="buble">buble.css</a>
    <a data-theme="simple">simple.css</a>
    <a data-theme="dolphin">dolphin.css</a>
    <a data-theme="pure">pure.css</a>
  </div>

  <style>
    .theme-preview a {
      padding-right: 10px;
    }

    .theme-preview a:hover {
      cursor: pointer;
      text-decoration: underline;
    }
  </style>

  <script>
    let preview = Docsify.dom.find('.theme-preview');
    let themes = Docsify.dom.findAll('[name="theme"]');

    preview.onclick = function (e) {
      let title = e.target.getAttribute('data-theme');
      
      themes.forEach(function (theme) {
        theme.disabled = theme.title !== title
      });
    };
  </script>
