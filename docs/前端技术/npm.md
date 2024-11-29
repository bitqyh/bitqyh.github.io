# Node和npm

## 安装 

**安装包直接安装**

1. 配置npm国内镜像**

   为加速npm下载依赖，可以为npm配置国内镜像，在终端执行以下命令为npm配置阿里云镜像。

   ```bash
   npm config set registry https://registry.npmmirror.com
   ```

   若想取消上述配置，可在终端执行以下命令删除镜像，删除后将恢复默认配置。

   ```bash
   npm config delete registry
   ```



## 前端项目启动

### 安装所需依赖

```bash
npm install
npm i 
```

### 项目启动

```bash
npm run dev
```

### 项目打包

```bash
npm run build
```

**打包完成后提取dist文件夹即可**