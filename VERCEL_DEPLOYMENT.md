# Vercel 部署指南

本项目已经改造为支持 Vercel Serverless 部署，同时保持原有的服务器部署方式。

## 部署到 Vercel

### 方式一：通过 Vercel CLI（推荐）

1. 安装 Vercel CLI：
```bash
npm install -g vercel
```

2. 登录 Vercel：
```bash
vercel login
```

3. 部署项目：
```bash
vercel
```

首次部署时会询问一些配置问题，按照提示操作即可。

4. 生产环境部署：
```bash
vercel --prod
```

### 方式二：通过 Vercel Dashboard

1. 访问 [Vercel Dashboard](https://vercel.com/dashboard)
2. 点击 "New Project"
3. 导入你的 Git 仓库
4. Vercel 会自动检测配置并部署

## 环境变量配置

在 Vercel Dashboard 的项目设置中配置以下环境变量（如果需要）：

- `NODE_ENV`: production
- 其他项目所需的环境变量

## API 端点

部署后，所有 API 端点保持不变：

- `GET /` - 服务信息
- `POST /v1/images/generations` - 图像生成
- `POST /v1/images/compositions` - 图像合成
- `POST /v1/videos/generations` - 视频生成
- `POST /v1/chat/completions` - 聊天补全
- `GET /v1/models` - 模型列表
- `GET /ping` - 健康检查

## 调用方式

部署到 Vercel 后，调用方式完全相同，只需将域名替换为 Vercel 提供的域名：

```bash
# 原来的调用方式（服务器）
curl -X POST http://your-server:3000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model": "jimeng", "messages": [{"role": "user", "content": "生成一张图片"}]}'

# Vercel 部署后的调用方式
curl -X POST https://your-project.vercel.app/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model": "jimeng", "messages": [{"role": "user", "content": "生成一张图片"}]}'
```

## 本地开发

本地开发仍然使用原来的方式：

```bash
# 安装依赖
npm install

# 开发模式
npm run dev

# 构建
npm run build

# 启动服务
npm start
```

## 注意事项

1. **函数超时限制**：Vercel 免费版的函数执行时间限制为 10 秒，Pro 版为 60 秒。如果你的 API 需要更长的执行时间，请升级到 Pro 版本或考虑使用其他部署方式。

2. **冷启动**：Serverless 函数在一段时间不使用后会进入休眠状态，下次调用时会有冷启动延迟（通常 1-3 秒）。

3. **文件系统**：Vercel Serverless 函数的文件系统是只读的（除了 /tmp 目录）。如果你的应用需要写入文件，请使用外部存储服务。

4. **环境变量**：确保在 Vercel Dashboard 中配置所有必要的环境变量。

5. **日志查看**：可以在 Vercel Dashboard 的 "Deployments" 页面查看函数日志。

## 项目结构变化

为了支持 Vercel 部署，添加了以下文件：

- `api/index.ts` - Vercel Serverless 函数入口
- `src/lib/vercel-adapter.ts` - Koa 到 Vercel 的适配器
- `src/lib/configs/vercel-config.ts` - Vercel 环境配置
- `.vercelignore` - Vercel 部署忽略文件
- `vercel.json` - Vercel 配置文件（已更新）

原有的服务器部署方式完全保留，不受影响。

## 故障排查

### 部署失败

1. 检查 `vercel.json` 配置是否正确
2. 确保所有依赖都在 `package.json` 中声明
3. 查看 Vercel 部署日志获取详细错误信息

### API 调用失败

1. 检查 Vercel Dashboard 中的函数日志
2. 确认环境变量配置正确
3. 检查请求路径和参数是否正确

### 性能问题

1. 考虑使用 Vercel Pro 版本以获得更好的性能
2. 优化代码以减少冷启动时间
3. 使用 Vercel Edge Functions（如果适用）

## 支持

如有问题，请查看：
- [Vercel 文档](https://vercel.com/docs)
- [项目 GitHub Issues](https://github.com/your-repo/jimeng-api/issues)
