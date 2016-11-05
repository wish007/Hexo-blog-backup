# wish007.github.io

一直就希望有个博客记录自己的学习过程，方便今后查找和回顾。开始想在著名站点直接注册博客，注册了才发现各种限制，自定义的空间太小。

直到发现了 GitHub 这个宝库，继而利用 [GitHub Pages](https://help.github.com/articles/what-is-github-pages/) 托管静态文件，结合 [Hexo](https://hexo.io/) 部署了这个博客。



## 部署过程

- 安装 [Git](https://git-scm.com/downloads)

- 安装 [Node.js](https://nodejs.org/en/download/)

- 安装 Hexo

  ```bash
  $ npm install hexo-cli -g
  $ npm install hexo --save
  ```




## 初始化 Hexo

- **创建 Hexo 开发目录**

  ```bash
  $ hexo init
  $ npm install
  ```

- **安装 Hexo 插件**

  ```bash
  npm install hexo-generator-index --save
  npm install hexo-generator-archive --save
  npm install hexo-generator-category --save
  npm install hexo-generator-tag --save
  npm install hexo-server --save
  npm install hexo-deployer-git --save
  npm install hexo-deployer-heroku --save
  npm install hexo-deployer-rsync --save
  npm install hexo-deployer-openshift --save
  npm install hexo-renderer-marked@0.2 --save
  npm install hexo-renderer-stylus@0.2 --save
  npm install hexo-generator-feed@1 --save
  npm install hexo-generator-sitemap@1 --save
  ```

- **开启本地服务**

  ``` bash
  $ hexo server
  ```

  浏览器登录 http://127.0.0.1:4000/ 即可查看效果

  ​


# 更换主题

- Hexo 官网展示了许多[可选主题](https://hexo.io/themes/)

- 例如挑选了主题：**hexo-theme-next**

  进入 Hexo 文件夹下的 themes 目录，将该主题的 GitHub 仓库下载到 themes 目录

  ```bash
  git clone https://github.com/iissnan/hexo-theme-next
  ```

- 修改 Hexo 配置文件

  ```bash
  theme: hexo-theme-next
  ```



## 部署到 GitHub Pages

- 在 GitHub 新建一个名为`[GitHub账户].github.io`的仓库


- 生成静态文件

  ```bash
  $ hexo generate
  ```

- 将静态文件同步到`[GitHub账户].github.io`仓库

  ```bash
  $ hexo deploy
  ```

- 浏览器登录 https://[GitHub账户].github.io/ 即可查看效果

  我的 GitHub Pages ：[wish007.github.io](https://wish007.github.io/)

  ​



## 备份博客

- 在 GitHub 建了一个仓库 [wish007/blog-backup](https://github.com/wish007/blog-backup) 来备份 Hexo 源文件，将 Hexo 文件夹`push`到仓库

  ```bash
  $ git push origin master
  ```

- 当切换电脑需要继续写博客时，直接把整个仓库`clone`到本地

  ```bash
  $ git clone git@github.com:wish007/blog-backup.git
  ```




## To Do

- 关联个人域名到 GitHub Pages
- 添加访问量统计
- 添加网站图标