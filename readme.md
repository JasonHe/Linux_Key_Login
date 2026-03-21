# 🔑 SSH 密钥认证一键配置脚本

本仓库包含一个简单而强大的 Shell 脚本 (`key.sh`)，用于自动完成 Linux 服务器的 SSH 安全加固配置。它会将服务器配置为**仅允许密钥登录**，并**禁用不安全的密码登录**方式。

这是保障新服务器安全最重要的初始步骤之一。

## 功能

-   **自动化配置**: 自动将您的 SSH 公钥添加到目标用户的 `authorized_keys` 文件中。
-   **安全加固**: 修改 SSH 守护进程的配置文件 (`sshd_config`) 以实现：
    -   确保公钥认证 (`PubkeyAuthentication`) 已启用。
    -   禁用密码认证 (`PasswordAuthentication`)。
    -   禁用挑战-响应认证 (`ChallengeResponseAuthentication`)。
-   **跨发行版兼容**: 智能地在不同系统（如使用 `systemd` 的 Ubuntu/Debian/CentOS 7+ 或使用旧式 `init.d` 的系统）上重启 SSH 服务。
-   **安全且可重复执行**: 脚本会检查公钥是否已存在以避免重复添加，并为 `.ssh` 目录和相关文件设置正确的权限。

## 🚀 一键执行 (推荐方法)

这个命令会引导您完成所有操作：下载脚本、授予权限，并自动打开编辑器让您粘贴公钥。

**请在您的目标服务器上复制并运行以下指令：**

bash
curl -L -o key.sh https://raw.githubusercontent.com/JasonHe/LinuxKeyLogin/main/key.sh && chmod +x key.sh && echo -e "\n\033[1;32m脚本 'key.sh' 已下载成功。\033[0m" && echo -e "\033[1;33m下一步：请在即将打开的编辑器中，将您的 SSH 公钥粘贴到 PUBKEY 变量中。\033[0m" && read -p "准备好后，请按 Enter 键继续..." && nano key.sh && echo -e "\n\033[1;32m编辑完成！现在请运行 'sudo ./key.sh' 来执行脚本。\033[0m"

**发生了什么？**
1.  `curl`: 从 GitHub 下载最新的 `key.sh` 脚本。
2.  `chmod`: 授予脚本执行权限。
3.  `echo` & `read`: 显示清晰的提示信息，并等待您按回车。
4.  `nano key.sh`: 自动打开 `nano` 编辑器，让您可以方便地编辑文件、粘贴公钥。
5.  编辑保存后，它会提示您运行最终的执行命令 `sudo ./key.sh`。

> **提示**: 此命令默认使用 `nano` 编辑器。如果您的系统没有安装 `nano`，可以先通过 `sudo apt update && sudo apt install nano` (Debian/Ubuntu) 或 `sudo yum install nano` (CentOS/RHEL) 来安装，或者将命令中的 `nano` 替换为您熟悉的编辑器（如 `vim`）。


## ⚠️ 重要：配置公钥

无论使用哪种方式，最终目的都是修改脚本中的 `PUBKEY` 变量。

1.  用文本编辑器打开 `key.sh` 脚本。
2.  找到 `PUBKEY` 这个变量。
3.  **用您自己的公钥**，替换掉引号内的**整段**占位符公钥字符串。

bash
# --- 配置区 ---
# 重要：这是将要被添加的公钥。
# 将下面的整段字符串替换为您自己的公钥。
readonly PUBKEY="ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICtm9SaEUaLDqC0gsDrRYQOV+eFtGi+Beu4Fl+3Agff9 your-user@your-machine"

**如何获取我的公钥？**
在您自己的电脑（本地电脑，不是服务器）上，运行以下命令之一，它会显示您的公钥，然后复制其输出内容即可。

bash
# 如果您使用 ed25519 密钥 (推荐)
cat ~/.ssh/ided25519.pub

# 如果您使用 RSA 密钥
cat ~/.ssh/idrsa.pub


## 🚨 黄金法则：断开前先测试！

此脚本会禁用密码认证。如果您的 SSH 密钥因任何原因配置错误，**您将会被服务器锁定在外面，无法登录**。

**请严格遵循以下步骤：**

1.  脚本执行成功后，**不要关闭当前的终端窗口**。
2.  打开一个**全新的**终端窗口。
3.  在新窗口中尝试通过 SSH 登录您的服务器。
    bash
    ssh 用户名@你的服务器IP
    4.  此时应该不需要输入密码就能直接登录。
5.  **只有在新连接确认可以成功登录之后**，您才能安全地关闭原来的终端窗口。

## 免责声明

本脚本按“原样”提供。在您的系统上（尤其是使用 `sudo`）运行任何脚本之前，请务必了解其功能。对于因使用本脚本而可能导致的任何问题（例如被锁定在服务器外），作者不承担任何责任。

## 许可证

本项目采用 [MIT 许可证](LICENSE) 授权。
