### 常用命令

```bash
npm install -g hexo-cli
npm update -g hexo-cli
npm install hexo-deployer-git --save  # 安装git部署插件

hexo init  # 初始化
hexo n [layout] ""   # 新建文件  layout-布局方式
hexo cl  # cl==clean 清楚缓存
hexo publish 
hexo generate  # 生成静态网页
hexo server  # 启动服务并预览
hexo deploy  # 部署

hexo d -g # 生成并部署
hexo s -g #生成并预览
```

layout布局方式有三种
- post  创建的是markdown文件
- page  创建的是网站的菜单项
- drad  草稿布局

在_config.yml文件中添加如下
```bash
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
-  type:
+  type: git
+  repository: https://github.com/username/username.github.io.git
+  branch: master
```