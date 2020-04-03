# Eleectron
## electron-vue的安装
1. 安装vue-cli
```
npm install -g @vue/cli	或者	yarn global add @vue/cli
或者	npm install -g @vue/cli-init
```
2. 安装electron-vue
```
vue init simulatedgreg/electron-vue my-project
cd my-project
npm install
npm run dev
```
3. 在npm run dev时遇到的问题

```
可能会遇见Html Webpack Plugin:
		ReferenceError: process is not defind
解决方案： 
	修改	.electron-vue下面的 webpack.main.config.js 和 webpack.render.config.js, 修改的地方一样，如下
```
```javascript
new HtmlWebpackPlugin({
    filename: 'index.html',
    template: path.resolve(__dirname, '../src/index.ejs'),
    templateParameters(compilation, assets, options) {
        return {
            compilation: compilation,
            webpack: compilation.getStats().toJson(),
            webpackConfig: compilation.options,
            htmlWebpackPlugin: {
                files: assets,
                options: options
            },
            process,
        };
    },
    minify: {
        collapseWhitespace: true,
        removeAttributeQuotes: true,
        removeComments: true
    },
    nodeModules: process.env.NODE_ENV !== 'production'
        ? path.resolve(__dirname, '../node_modules')
        : false
}),
```

