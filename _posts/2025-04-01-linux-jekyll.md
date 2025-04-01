---
layout: post        # 文章布局
title: "linux-jekyll搭建"
date: 2025-04-01   # 日期必须和文件名一致
---
# 在 Linux 上配置 Jekyll 需要安装 Ruby、Jekyll 和相关依赖

## **1. 安装 Ruby 和必要依赖**

```shell
sudo apt update
sudo apt install ruby ruby-dev build-essential zlib1g-dev
```

## **2. 配置 Gem 安装路径（避免使用 `sudo`）**

```shell
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

**📌 注意**：如果你用的是 `zsh`，请替换 `~/.bashrc` 为 `~/.zshrc`

## **3. 安装 Jekyll 和 Bundler**

```shell
jekyll -v  # 应该输出 Jekyll 版本（如 4.3.2）
bundle -v  # 应该输出 Bundler 版本
```

## **4. 创建并运行 Jekyll 博客**

#### **① 新建博客项目**

```shell
jekyll new myblog  # 创建一个新博客目录
cd myblog         # 进入项目目录
```

#### **② 启动本地服务器**

```shell
bundle exec jekyll serve
```

- 默认访问地址：`http://localhost:4000`
- 如果要用 `--livereload`（修改后自动刷新）：

```shell
bundle exec jekyll serve --livereload
```

## **5. **进阶配置

#### **📂 使用自定义主题**

1. 在 `_config.yml` 中指定主题：

   yaml

   ```
   theme: minima  # 示例（minima 是默认主题）
   ```

2. 安装主题：

   bash

   ```
   bundle add minima
   bundle install
   ```

#### **🚀 部署到 GitHub Pages**

1. 在 GitHub 上创建仓库 `用户名.github.io`。

2. 推送代码：

   bash

   ```
   git init
   git add .
   git commit -m "Initial commit"
   git remote add origin https://github.com/用户名/用户名.github.io.git
   git push -u origin main
   ```

3. 稍等几分钟，访问 `https://用户名.github.io` 即可。

   ---

### 完整解决步骤：

#### 1. 首先检查 Ruby 和 Bundler 版本

bash

复制

```
ruby -v  # 应该 ≥ 2.4.0
bundler -v  # 应该 ≥ 2.0
```

#### 2. 如果提示 "bundler: 未找到命令"

bash

复制

```
gem install bundler
```

#### 3. 确保在项目目录

bash

复制

```
cd /home/myf/myblog
```

#### 4. 检查 Gemfile 是否包含 minima 主题

bash

复制

```
cat Gemfile
```

正常应该包含：

ruby

复制

```
gem "minima", "~> 2.5"
```

#### 5. 尝试带详细日志的安装

bash

复制

```
bundle install --verbose
```

#### 6. 常见错误解决方案

**如果出现权限错误**：

bash

复制

```
bundle config set --local path 'vendor/bundle'
bundle install
```

**如果网络问题**（特别是国内用户）：

bash

复制

```
# 更换RubyGems镜像源
bundle config mirror.https://rubygems.org https://gems.ruby-china.com
rm Gemfile.lock
bundle install
```

**如果提示 Webrick 缺失**（Ruby 3.0+ 需要）：

bash

复制

```
bundle add webrick
```

#### 7. 强制清理并重试

bash

复制

```
rm -rf vendor/bundle
rm Gemfile.lock
bundle install
```

#### 8. 验证安装成功

bash

复制

```
bundle exec jekyll serve
```

要在 Windows 主机 (192.168.1.5) 访问 Ubuntu 虚拟机 (192.168.1.41) 上运行的 Jekyll 服务，请按照以下步骤操作：

### 1. 确保 Jekyll 服务正确启动

在 Ubuntu 虚拟机中运行：

bash

复制

```shell
bundle exec jekyll serve --host 0.0.0.0
```

关键参数说明：

- `--host 0.0.0.0` 允许所有网络接口访问（默认只允许本地）
- 服务默认运行在 `4000` 端口

### 2. 检查 Ubuntu 防火墙

bash

复制

```
sudo ufw status
```

如果防火墙开启，需要放行 4000 端口：

bash

复制

```
sudo ufw allow 4000
```

### 3. 在 Windows 主机访问

直接在浏览器输入：

复制

```
http://192.168.1.41:4000
```

### 4. 常见问题排查

#### 如果无法访问：

1. **测试网络连通性**（在 Windows CMD 中）：

   cmd

   复制

   ```
   ping 192.168.1.41
   ```

2. **检查端口监听**（在 Ubuntu 中）：

   bash

   复制

   ```
   netstat -tulnp | grep 4000
   ```

   应该显示类似：

   复制

   ```shell
   tcp    0   0 0.0.0.0:4000    0.0.0.0:*    LISTEN    [PID]/ruby
   ```

### **1. 确认虚拟机网络模式**

在虚拟机软件中确保选择 **桥接模式**：

- **VMware**：`虚拟机设置 → 网络适配器 → 桥接模式`
- **VirtualBox**：`设置 → 网络 → 连接方式：桥接网卡`

------

### **2. 检查 Ubuntu 的 IP 地址**

在 Ubuntu 终端运行：

bash

复制

```
ip a
```

确认 `eth0` 或 `ens33` 网卡的 IP 是 `192.168.1.x` 段（和 Windows 同网段），例如：

复制

```shell
inet 192.168.1.41/24
```

如果 IP 不是 `192.168.1.41`，可能需要：

bash

复制

```shell
sudo dhclient -r  # 释放IP
sudo dhclient     # 重新获取IP
```

------

### **3. 启动 Jekyll 并允许外部访问**

在 Ubuntu 项目目录下运行：

bash

复制

```shell
bundle exec jekyll serve --host 0.0.0.0 --port 4000
```

关键参数：

- `--host 0.0.0.0`：允许所有网络访问
- `--port 4000`：指定端口（可改为其他端口）

------

### **4. 开放 Ubuntu 防火墙（如有启用）**

bash

复制

```shell
sudo ufw allow 4000  # 允许4000端口
sudo ufw status     # 检查规则
```