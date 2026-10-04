# WxPilot 功能包（CDN 分发）

本仓库只存放**加密的功能 DEX**，供 WxPilot 客户端从 CDN 下载。

## 为什么可以公开

因为文件是**加密的**（AES-256-GCM）：

- 密钥编译在客户端的 native so 里（分段混淆 + 反调试）
- 下载了密文也没用 —— 攻击者仍需逆向 so 提取密钥
- 授权由服务端把关（未授权连 URL 都拿不到）

所以公开反而更好：**CDN 可缓存**，所有用户共享，服务器带宽占用 ≈ 0。

## 文件说明

| 文件 | 说明 |
|---|---|
| `chat.enc` | 聊天功能包（消息复读、消息撤回等） |

## 如何更新

在源码仓库（私有）里：

```bash
cd /path/to/wxpilot

# 1. 编译 + 加密
cargo xtask remote-features build chat
cargo xtask remote-features publish chat

# 2. 同步到本仓库并推送
bash tools/publish-cdn.sh
```

## 客户端如何下载

```
客户端 → WxPilot 服务器（验 token，返回 URL，343 字节）
       → 本仓库的 raw CDN（下载密文）
       → native 解密 → DEX
```

URL 形如：

```
https://raw.githubusercontent.com/huihuangc34/wxpilot-features/main/chat.enc
```

## 完整性校验

服务端会在响应里返回**密文**的 sha256（`dex_sha256`），
客户端下载后先比对再解密。

---

⚠️ **本仓库不含任何源码**，只有加密产物。
