# ohos-vim

本项目为 OpenHarmony 平台编译了 vim，并发布预构建包。

## 获取预构建包

前往 [release 页面](https://github.com/Harmonybrew/ohos-vim/releases) 获取。

## 用法

**1\. 在鸿蒙 PC 中使用**

不支持鸿蒙 PC。

**2\. 在鸿蒙开发板中使用**

用 hdc 把它推到设备上，然后以“解压 + 配 PATH” 的方式使用。

示例：
```sh
hdc file send vim-9.2.0150-ohos-arm64.tar.gz /data
hdc shell

# 需要先把根目录挂载为读写，才能创建 /opt 目录。
mount -o remount,rw /
mkdir -p /data/opt
ln -s /data/opt /opt

cd /data
tar -zxf vim-9.2.0150-ohos-arm64.tar.gz -C /opt
export PATH=/opt/vim-9.2.0150-ohos-arm64/bin:$PATH
export TERM=screen-256color

# 现在可以使用 vim 命令了
```

在 hdc shell 环境中使用 vim，需要多一个 `export TERM=screen-256color` 的步骤。因为 hdc 在连接 OpenHarmony 设备时会创建一个伪终端（pseudo-terminal），它的行为并不完全像一个标准的 xterm 或 xterm-256color 终端，而更接近于 screen 或 tmux 这类多路复用器的终端模拟方式。我们必须手动设置一个有效的 TERM 类型，hdc 发送的键码和 terminfo 定义的键码才能匹配，才不会出现按键错位。

**3\. 在 [鸿蒙容器](https://github.com/hqzing/docker-mini-openharmony) 中使用**

在容器中用 curl 下载这个软件包，然后以“解压 + 配 PATH” 的方式使用。

示例：
```sh
cd /opt
curl -fLO https://github.com/Harmonybrew/ohos-vim/releases/download/9.2.0150/vim-9.2.0150-ohos-arm64.tar.gz
tar -zxf vim-9.2.0150-ohos-arm64.tar.gz
export PATH=/opt/vim-9.2.0150-ohos-arm64/bin:$PATH

# 现在可以使用 vim 命令了
```

## 从源码构建

**1\. 手动构建**

这个项目使用本地编译（native compilation，也可以叫本机编译或原生编译）的做法来编译鸿蒙版 vim，而不是交叉编译。

需要在 [鸿蒙容器](https://github.com/hqzing/dockerharmony) 中运行项目里的 build.sh，以实现 vim 的本地编译。

示例：
```sh
git clone https://github.com/Harmonybrew/ohos-vim.git
cd ohos-vim
docker run \
  --rm \
  -it \
  -v "$PWD":/workdir \
  -w /workdir \
  ghcr.io/hqzing/dockerharmony:latest \
  ./build.sh
```

**2\. 使用流水线构建**

如果你熟悉 GitHub Actions，你可以直接复用项目内的工作流配置，使用 GitHub 的流水线来完成构建。

这种情况下，你使用的是 GitHub 提供的构建机，不需要自己准备构建环境。

只需要这么做，你就可以进行你的个人构建：
1. Fork 本项目，生成个人仓
2. 在个人仓的“Actions”菜单里面启用工作流
3. 在个人仓提交代码或发版本，触发流水线运行
