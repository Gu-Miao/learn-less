# 简单环境搭建

确保你的电脑上安装了 Node.js。

1. 创建一个工作目录

```bash
mkdir learn-less
npm init -y
```

2. 安装 less 编译器

```bash
npm i less nodemon -D
```

3. 在根目录下创建 index.less 测试文件

4. 修改 npm script

```json
{
  "scripts": {
    "start": "nodemon --watch index.less --exec lessc index.less index.css"
  }
}
```

这个命令的意思是，监听 index.less 文件的变化，每当它的内容变化，就将其编译为 index.css。

运行命令后，你就可以同时打开 index.less 和 index.css，修改 Less 文件的同时也能查看 CSS 输出的情况。
