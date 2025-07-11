---
id: development  
title: 如何将 HertzBeat 运行编译?    
sidebar_label: 运行编译
---

## 让 HertzBeat 运行起来

> 让 HertzBeat 的代码在您的开发工具上运行起来，并且能够断点调试。
> 此为前后端分离项目，本地代码启动需将后端 [manager](https://github.com/apache/hertzbeat/tree/master/hertzbeat-manager) 和前端 [web-app](https://github.com/apache/hertzbeat/tree/master/web-app) 分别启动生效。

### 后端启动

1. 需要 `maven3+`, `java17` 和 `lombok` 环境

2. (可选)修改配置文件配置信息-`manager/src/main/resources/application.yml`

3. 在项目根目录运行编译: `mvn clean install -DskipTests`

4. 在 `jvm` 加入参数 `--add-opens=java.base/java.nio=org.apache.arrow.memory.core,ALL-UNNAMED`

5. 启动`springboot manager`服务 `manager/src/main/java/org/apache/hertzbeat/hertzbeat-manager/Manager.java`

### 前端启动

1. 需要 `nodejs yarn` 环境, 版本要求 `Node.js >= 18`

2. 进入 `web-app` 目录: `cd web-app`

3. 安装yarn: `npm install -g yarn`

4. 在前端工程目录 `web-app` 下执行: `yarn install` 或者 `yarn install --registry=https://registry.npmmirror.com`

5. 待本地后端启动后，在web-app目录下启动本地前端 `yarn start`

6. 浏览器访问 localhost:4200 即可开始，默认账号密码 admin/hertzbeat

## 生成二进制包

> 需要 `maven3+`, `java17`, `node` 和 `yarn` 环境.

### 前端打包

1. 需要 `Node Yarn` 环境, 版本要求 `Node.js >= 18`

2. 切换到 `web-app` 目录: `cd web-app`

3. 安装 yarn: `npm install -g yarn`

4. 安装本项目依赖: `yarn install` 或 `yarn install --registry=https://registry.npmmirror.com`

5. 打包: `yarn package`

### 后端打包

1. 需要 `maven3+`, `java17` 环境

2. 在项目根目录运行: `mvn clean package -Prelease`

HertzBeat 包将生成为 `dist/hertzbeat-{version}.tar.gz`

### 采样器打包

1. 需要 `maven3+`, `java17` 环境

2. 在项目根目录运行: `mvn clean install`

3. 切换到 `hertzbeat-collector` 目录: `cd hertzbeat-collector`

4. 在 `hertzbeat-collector` 目录下执行: `mvn clean package -Pcluster`

HertzBeat 采样器包将生成为 `dist/hertzbeat-collector-{version}.tar.gz`

## 后端CI

### Maven Surefire 插件测试失败排查指南
1. 在构建配置中添加测试报告上传步骤（适用于GitHub Actions）
   ```yaml
      # .github/workflows/backend-build-test.yml
      # 其他构建步骤...
      - name: Build with Maven
        run: mvnd clean -B package -Prelease -Dmaven.test.skip=false --file pom.xml

      # 测试报告上传步骤
      - name: Upload test reports
        if: failure()  # 仅在测试失败时执行
        uses: actions/upload-artifact@v4
        with:
          name: test-reports-${{ github.run_id }}  # 动态生成唯一报告名称
          path: |
            **/target/surefire-reports  # 递归收集所有单元测试报告
            **/target/failsafe-reports  # 递归收集所有集成测试报告
2. 获取测试报告文件步骤
    1. 测试失败后，在GitHub Actions页面找到对应的运行记录
    2. 在"Artifacts"部分找到生成的测试报告文件
    3. 点击下载按钮获取报告压缩包
    4. 解压后查看具体的测试失败详情
  ![测试报告下载示例](https://github.com/user-attachments/assets/bac50f93-5e13-4295-84ed-444a9146ee28)

