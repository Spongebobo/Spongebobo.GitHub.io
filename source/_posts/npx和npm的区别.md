---
title: npm 和 npx的区别
tags:
  - npm
  - npx
categories:
  - npm
keywords: "npm"
date: 2020-11-27 16:30:40
type: artitalk
top_img: https://42f2671d685f51e10fc6-b9fcecea3e50b3b59bdc28dead054ebc.ssl.cf5.rackcdn.com/illustrations/finance_0bdk.svg
cover: https://42f2671d685f51e10fc6-b9fcecea3e50b3b59bdc28dead054ebc.ssl.cf5.rackcdn.com/illustrations/finance_0bdk.svg
---

# npm 和 npx

- [npm 和 npx](#npm-和-npx)
  - [npm](#npm)
    - [1. npm install](#1-npm-install)
    - [2. npm install -g](#2-npm-install--g)
  - [npx](#npx)
    - [1. 调用本地项目的模块，本地 bin 寻址](#1-调用本地项目的模块本地-bin-寻址)
    - [2. 全局命令，避免全局安装模块](#2-全局命令避免全局安装模块)

<!-- TOC -->

## npm

- 定义

  - npm 是一个包管理工具，node package installer commander tool。

> ### 前情提要
>
> 下文举例操作，都是在 macos 环境下。

### 1. npm install

- 我们以 typescript 为例。

- 首先执行 `npm i typescript`

- typescript 会安装在当前文件夹下的 node_modules 中，且会在 node_modules/.bin 文件夹下生成两个文件：tsc tsserver。

- **为什么会在 node_modules/.bin 文件夹下生成这两个文件？**

  - 这是由于 typescript 包的 package.json 文件中声明了 bin 属性，在 bin 属性下配置了 tsc 和 tsserver 执行命令。

  - 所以当我们在安装使用这个包的时候，会在本地 node_modules/.bin 中自动生成软链接。如图 1 所示。

  ![图1](https://haimian-bucket.oss-cn-hangzhou.aliyuncs.com/uPic/typescript.png)

  - 我们可以通过 node 执行 node_modules/.bin/tsc 这个文件。我们发现是能正常执行的。如图 2 所示。

  ![图2](https://haimian-bucket.oss-cn-hangzhou.aliyuncs.com/uPic/tsc-v.png)

### 2. npm install -g

- 我们还是以 typescript 为例。

- 首先执行 `npm i -g typescript`

- typescript 会安装在 /usr/local/lib/node_modules 中。**且会生成一个 /usr/local/bin/tsc 文件。**

  > **插个题外话：**
  >
  > - npm -g 安装的全局包放在了 /usr/local/lib/node_modules
  > - yarn 使用 global 命令安装的全局包放在 ~/.config/yarn/global
  > - Homebrew 安装的软件，安装的路径都在 /usr/local/Cellar

  - **为什么能在全局使用 tsc 命令？**

    - 原因就是：**全局安装后，这个包的 bin 执行命令会被注册到全局，你就可以直接执行这个命令。**

  - **那大家再往深思考一下，这个过程是怎么样的？**

    - 首先是因为 typescript 这个包中的 package.json 文件声明了 bin 属性，其中包含了 tsc 命令。
    - 通过 `npm i -g typescript` 安装后，会生成一个 /usr/local/bin/tsc 文件，全局注册了 tsc 这个命令。
    - 当使用 tsc 指令时，系统会去 \$PATH 里的路径去查找有没有 `tsc` 这个命令。没有则会提示 `command not found`。

- 通过 **which** 指令 我们可以查找 `tsc` 在 \$PATH 中的路径。如图 3 所示，tsc 存在于 usr/local/bin 文件夹下。

  ![图3](https://haimian-bucket.oss-cn-hangzhou.aliyuncs.com/uPic/globalts.png)

  - **什么是 which？**

    - 一个 linux 指令。

    - which 命令的作用是：在 \$PATH 变量指定的路径中，搜索某个命令的位置，并且返回第一个搜索结果。
    - 也就是说，使用 which 命令，就可以查找某个命令是否存在，以及执行的到底是哪一个位置的命令。

- **什么是 \$PATH**

  - PATH 是 linux 中的其中一个环境变量。

  - PATH 是指系统会去哪些目录中寻找可执行程序的一个环境变量。

  - linux 中变量调用的时候会在变量名前加一个 \$ 符号。

  - 在 shell 中 \$PATH 是取得 PATH 变量的值。

  - 例如：你执行 `tsc`，如果不设置 PATH 这个环境变量，那么就会出现 `command not found`。有了 PATH 这个变量，系统会优先去这个变量的值里指定的目录去找 `tsc`，如果都找不到，才会告诉你 `command not found`。

  - 我们打印一下 \$PATH 的输出。可以发现有 7 个目录存在指令。

  ![图4](https://haimian-bucket.oss-cn-hangzhou.aliyuncs.com/uPic/echo.png)

      1. /Users/xxxx/.nvm/versions/node/v12.18.0/bin
      2. /usr/local/bin
      3. /usr/bin
      4. /bin
      5. /usr/sbin
      6. /sbin
      7. /Library/Apple/usr/bin

  - 总结一下过程就是：

  1. 执行 `npm i -g typescript`

  2. 会生成一个 /usr/local/bin/tsc 文件，全局注册了 tsc 这个命令。

  3. 以我的 mac 为例，在 shell 中输入 tsc，系统会优先去这 7 个目录下寻找。

  4. 如果都找不到，才会告诉你 `command not found`。

---

## npx

- 定义

  - npx 是一个工具，它是 npm v5.2.0 引入的一条命令（npx），是 npm 的一个包执行器。

- 原理

  - npx 的原理很简单，就是运行的时候，会到本地项目的 node_modules/.bin 路径和环境变量 \$PATH 里面，检查命令是否存在。

- 使用场景：

  ### 1. 调用本地项目的模块，本地 bin 寻址

  - 假设我们未在全局安装 typescript，仅仅是在本地文件夹中安装。

    ```shell
    $npm i -D typescript
    ```

  - 这个时候你直接使用 `tsc -v` 命令，因为你不是全局安装这个命令，那么肯定会报 `command not found`。

  - 你可以这样使用：

    ```shell
    $node ./node_modules/.bin/tsc -v
    ```

  - 还可以这样用：

    - 把 `tsc` 指令写入到 npm scripts 后，会自动寻址 node_modules/.bin
    - 在 package.json 中 加入 `scripts: { "xxx": "tsc" }`
    - 然后执行 npm xxx 就会自动执行 scripts 中 xxx 的命令。

    ```shell
    npm xxx -v
    # 等价于
    tsc -v
    # 此时的tsc 会自动去 node_modules/.bin 寻址
    ```

  - **以上两种方法都比较麻烦**。

  - **npx 就是想解决这个问题，npx 运行的时候会到 node_modules/.bin 路径和环境变量\$PATH 里面，检查命令是否存在。**

  ```shell
  $npx tsc -v
  ```

  ### 2. 全局命令，避免全局安装模块

  - 我们用 cra 来举例。

  ```shell
  npx create-react-app my-react-app
  # 等价于
  npm i -g create-react-app
  create-react-app my-react-app
  ```

  - npx 会下载 create-react-app 包到一个临时目录，使用完之后会删除。每一次执行都会重新下载。
  - 利用这个特性，我们可以使用不同版本的 node。某些场景下，这个方法用来切换 node 版本，要比 nvm 方便一些。

    ```shell
    npx node@10.16.0 -v
    # v10.16.0
    ```

  - 如何让 npx 强制使用本地模块

    - 使用 **--no-install** 参数

    ```shell
    npx --no-install create-react-appmy-react-app
    ```

  - 如何让 npx 强制使用远程模块

    - 使用 **--ignore-existing** 参数

    ```shell
    npx --ignore-existing create-react-app my-react-app
    ```
