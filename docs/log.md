## 从0开始开发React组件库

#### 准备工作

+ 项目结构

```
.
├── dist                 //打包输出目录
├── examples    //demo示例代码                
├── lib                  //发布包入口                 
├── log.md
├── node_modules
├── package.json
├── package-lock.json
├── README.md
├── src               //组件源代码
├── webpack.config.js      //webpack配置
└── yarn.lock
└── .babelrc   //babel配置
└── .eslintrc   //eslint配置
└── .gitignore    //git忽略文件
└── .npmignore   //npm忽略文件

```

+ 安装npm工具包

````json
//package.json
{
  "name": "sliver-react-components-lib",
  "version": "1.0.5",
  "description": "react components lib",
  "main": "lib/index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "webpack-dev-server --mode development",
    "build": "webpack --mode production",
    "transpile": "babel src -d dist ",
    "compile": "npx babel src --out-dir lib"
  },
  "author": "sliver",
  "license": "ISC",
  "peerDependencies": {
    "react": "^16.13.1",
    "react-dom": "^16.13.1"
  },
  "dependencies": {
    "@babel/core": "^7.9.0",
    "react": "^16.13.1",
    "react-dom": "^16.13.1",
    "styled-components": "^5.1.0"
  },
  "devDependencies": {
    "@babel/cli": "^7.8.4",
    "@babel/preset-env": "^7.9.5",
    "@babel/preset-react": "^7.9.4",
    "babel-cli": "^6.26.0",
    "babel-loader": "^8.1.0",
    "babel-preset-env": "^1.7.0",
    "babel-preset-react": "^6.24.1",
    "css-loader": "^3.5.2",
    "html-webpack-plugin": "^4.2.0",
    "style-loader": "^1.1.4",
    "webpack": "^4.42.1",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3"
  }
}

````
`react`和`react-dom`必备，`styled-components`用于写`css in js`

注意：新版的`babel`都会带有`@`标记，安装最新版的就行,老版的`babel-cli`,`babel-preset-env`,`babel-preset-env`不需要安装，可能会有冲突，具体详见[babel](https://babeljs.io/docs/en/presets)

+ 配置工具

`webpack`配置

````js

/*** webpack.config.js ***/
const path = require('path');
const HtmlWebpackPlugin = require("html-webpack-plugin");
//该插件用于生成index.html包含打包后的js文件
const htmlWebpackPlugin = new HtmlWebpackPlugin({
 template: path.join(__dirname, "examples/src/index.html"),
 filename: "./index.html"
});
module.exports = {
 entry: path.join(__dirname, "examples/src/index.js"),
 module: {
   rules: [{
     test: /\.(js|jsx)$/,
   use: "babel-loader",
   exclude: /node_modules/
 },{
   test: /\.css$/,
   use: ["style-loader", "css-loader"]
 }]
},
 plugins: [htmlWebpackPlugin],
 resolve: {
   extensions: [".js", ".jsx"]
 },
 devServer: {
   port: 3001
}};
````

`babel`配置:
````json
//.babelrc
{
    "presets": ["@babel/preset-env","@babel/preset-react"]
}

````

+ npm-scripts

1. `"start": "webpack-dev-server --mode development --open"`
：自动开启浏览器，开启热更新
2. `"build": "webpack --mode production"`:打包项目代码
3. `"compile": "npx babel src --out-dir lib --copy-files"`: 编译组件源码输出到`lib`文件夹(**注意`--copy-files`参数用来拷贝一些静态文件，因为babel-cli只处理js文件，css文件直接拷贝即可**),
运行这个命令后，会生成lib文件夹，这是用户可以直接导入使用的。此时需要在`package.json`中更改`main`：
````json
//package.json
{
    //....
    "main": "lib/index.js"
}

````

#### 开发组件

组件源码放在`src`文件夹下

#### 组件发布
1. 先使用`babel-cli`编译
````
npm run compile
````
2. 登录npm

如果没用npm账号，先注册[npm](https://www.npmjs.com/)

````
npm login
````
输入用户名和密码

3. 发布

````
npm publish
````


注意：每次更新发布的时候，必须把版本号更新。

发布遇到403错误，是由于使用了淘宝源，切换到npmjs源就好

````
npm config set registry http://registry.npmjs.org/
````

4. Demo页

发布在线 demo 可以直接用 Github Pages 来帮助我们托管，通过 webpack 构建生产环境版本，然后发到 Github 上去即可

````
yarn add gh-pages --dev
````

添加npm-scripts：

````
//packag.json
{
  "scripts":{
    "gh-pages":"gh-pages -d dist"
  }
}

````

打包代码,并发布在线demo

````
yarn build
yarn gh-pages
````

在线demo：`https://justforfunmy.github.io/React-Components-Lib/`

justforfunmy 是我的GitHub用户名，React-Components-Lib是仓库名


#### 开发过程中的问题

+ css Module 配置问题

[CSS Modules 用法教程](http://www.ruanyifeng.com/blog/2016/06/css_modules.html)

````js
//webpack.config.js
{
   test: /\.css$/,
   use: ["style-loader", "css-loader?modules"]
}
````

+ 多入口打包

````js
module.exports = {
  entry: {
    //配置多个入口
    'bundle':path.join(__dirname, "examples/src/index.js"),
    'index':path.join(__dirname, "src/index.js"),
  },
  //出口的filename中的name参数根据入口的key值确定
  output:{
    path:path.resolve(__dirname,'dist'),
    filename:'[name].js'
  }
}
````

#### 开发日记

##### 0422

+ 显示代码组件

`pre`,`code`

+ 计算tabpane的宽度(undone)

##### 0423

+ 代码规范

````
yarn add eslint --dev
npx eslint --init
````
[使用ESLint+Prettier来统一前端代码风格](https://segmentfault.com/a/1190000015315545)


[参考链接](https://blog.csdn.net/sinat_17775997/article/details/86690152)