+++
title = '如何写一篇blog'
date = 2024-07-19T14:19:51+08:00
draft = false
+++  
1. 首先在博客目录下，输入`hugo new posts/<name>.md`    
之后便会自动在content/posts目录下创建一个md文件  
`Content "/home/ayu/Ayu's Blog/content/posts/如何写一篇blog.md" created`  
2. 进入文件夹，使用vim进行编辑。(或者使用其他的Markdown编辑器)  
因为已经安装了Markdown预览插件，所以可以输入`:MarkdownPreview`或使用`,m`启用markdown进行预览。  
3. 写你想写的内容
4. 目前该文章是草稿模式，保存后回到博客文件夹下，输入`hugo server -D`进行预览，这样预览会看到所有文章，包括草稿。
5. 效果无误后，回到文件夹内，变更开头`draft = false`，取消草稿模式。  
6. 使用`hugo server`查看最终效果。
7. 效果无误，部署到github
    ```
    git add .
    git commit -m "update"
    git push origin main
    ```
    Git Actions会自动部署  
8. 访问github pages查看效果
