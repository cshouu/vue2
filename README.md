## 对一些前端功能点的测试
nodejs 14.20.0、@vue/cli 5.0.8、vue 2

### 展示pdf
1. 基于pdfjs-dist
2. babal.config.js 需要添加配置：
    ```javascript
    plugins: ["@babel/plugin-transform-private-methods"]
    ```