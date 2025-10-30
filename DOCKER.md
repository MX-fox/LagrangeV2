# Lagrange.Milky Docker 部署指南

本文档介绍如何使用Docker部署Lagrange.Milky应用程序。

## 快速开始

### 使用预构建镜像

从GitHub Container Registry拉取最新镜像：

```bash
docker pull ghcr.io/lagrangedev/lagrange-milky:latest
```

运行容器：

```bash
docker run -d \
  --name lagrange-milky \
  -p 8080:8080 \
  -v $(pwd)/config:/app/config \
  -v $(pwd)/logs:/app/logs \
  ghcr.io/lagrangedev/lagrange-milky:latest
```

### 使用Docker Compose

1. 克隆仓库：
```bash
git clone https://github.com/LagrangeDev/LagrangeV2.git
cd LagrangeV2
```

2. 启动服务：
```bash
docker-compose up -d
```

3. 查看日志：
```bash
docker-compose logs -f lagrange-milky
```

## 本地构建

如果你想从源代码构建Docker镜像：

```bash
# 构建镜像
docker build -f Lagrange.Milky/Dockerfile -t lagrange-milky .

# 运行容器
docker run -d \
  --name lagrange-milky \
  -p 8080:8080 \
  -v $(pwd)/config:/app/config \
  -v $(pwd)/logs:/app/logs \
  lagrange-milky
```

## 配置

### 环境变量

- `TZ`: 时区设置，默认为 `Asia/Shanghai`
- `Logging__LogLevel__Default`: 日志级别，可选值：`Trace`, `Debug`, `Information`, `Warning`, `Error`, `Critical`

### 卷挂载

- `/app/config`: 配置文件目录
- `/app/logs`: 日志文件目录

### 端口

- `8080`: HTTP API端口（如果应用程序提供HTTP服务）

## 多架构支持

Docker镜像支持以下架构：
- `linux/amd64` (x86_64)
- `linux/arm64` (ARM64)

Docker会自动选择适合你系统架构的镜像。

## 故障排除

### 查看容器日志
```bash
docker logs lagrange-milky
```

### 进入容器调试
```bash
docker exec -it lagrange-milky /bin/bash
```

### 检查容器状态
```bash
docker ps -a
```

## 更新

### 更新预构建镜像
```bash
docker pull ghcr.io/lagrangedev/lagrange-milky:latest
docker-compose down
docker-compose up -d
```

### 清理旧镜像
```bash
docker image prune -f
```

## 安全注意事项

1. 容器以非root用户运行，提高安全性
2. 建议使用具体的版本标签而不是`latest`标签用于生产环境
3. 定期更新镜像以获取安全补丁
4. 适当配置防火墙规则限制端口访问

## 性能优化

1. 镜像使用了.NET AOT编译，启动速度更快，内存占用更少
2. 使用了distroless基础镜像，减小镜像大小
3. 启用了Docker构建缓存，加快构建速度

## 支持

如果遇到问题，请：
1. 检查容器日志
2. 确认配置文件正确
3. 在GitHub仓库提交Issue