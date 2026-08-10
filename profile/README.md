<div align="center">
<pre>
   _                    _       
  /_\   __ _  ___ _ __ | |_ ___ 
 //_\\ / _` |/ _ \ '_ \| __/ __|
/  _  \ (_| |  __/ | | | |_\__ \
\_/ \_/\__, |\___|_| |_|\__|___/
       |___/                    
  /\  /\___ _ __ _ __   ___  ___ ___ 
 / /_/ / _ \ '__| '_ \ / _ \/ __/ __|
/ __  /  __/ |  | | | |  __/\__ \__ \
\/ /_/ \___|_|  |_| |_|\___||___/___/
</pre>
</div>

三件套 ACP 工具链：**浏览器控制台**（herness-console）↔ **本地节点**（herness-node）↔ **中心中继**（herness-relay），基于 [Agent Client Protocol](https://agentclientprotocol.com/)，把 Grok Build（后续支持更多 Agent）的能力带到任何设备的浏览器，无需 APP，你的数据全在本地节点存储，浏览器和中心中继仅作展示和转发。

## 架构

```
herness-console ──HTTP/SSE──▶ herness-node ──stdio──▶ grok agent
herness-console ──WS+HTTP──▶ herness-relay ──QUIC/WS──▶ herness-node × N ──stdio──▶ grok agent
```

## 仓库

| 仓库 | 职责 | 技术栈 |
|------|------|--------|
| [herness-console](https://github.com/AgentsHerness/herness-console) | 浏览器端操作台：ACP 流式对话、工具卡片、权限审批、斜杠命令 | Vite + React + Tailwind |
| [herness-node](https://github.com/AgentsHerness/herness-node) | 本地 Host：拉起 `grok agent stdio`，把 ACP 会话暴露给 console / relay | Go |
| [herness-relay](https://github.com/AgentsHerness/herness-relay) | 中心中继：多 Host 配对、注册与发现、事件聚合、请求转发，NAT 友好（QUIC/WS） | Go |

## License

MIT —— 各仓库均含 LICENSE 文件。
