<div align="center">
<pre>
   _                    _       
  /_\   __ _  ___ _ __ | |_ ___ 
 //_\\ / _` |/ _ \ '_ \| __/ __|
/  _  \ (_| |  __/ | | | |_\__ \
\_/ \_/\__, |\___|_| |_|\__|___/
       |___/                    
  /\  /\__ _ _ __ _ __   ___  ___ ___ 
 / /_/ / _` | '__| '_ \ / _ \/ __/ __|
/ __  / (_| | |  | | | |  __/\__ \__ \
\/ /_/ \__,_|_|  |_| |_|\___||___/___/
</pre>
</div>

**AgentsHarness** 的愿景：让所有 Agent 支持**远程接入与相互调用**。第一个 subproject 是 **Capri**（摩羯座）——基于 [Agent Client Protocol](https://agentclientprotocol.com/) 的三件套工具链：**浏览器操作台**（capri-fe）↔ **本地节点**（capri-host）↔ **中心中继**（capri-hub），把 Grok Build（后续支持更多 Agent）的能力带到任何设备的浏览器，无需 APP。你的数据全在本地节点存储，浏览器和中心中继仅作展示和转发。

## 架构

```
capri-fe ──HTTP/SSE──▶ capri-host ──stdio──▶ grok agent
capri-fe ──WS+HTTP──▶ capri-hub ──QUIC/WS──▶ capri-host × N ──stdio──▶ grok agent
```

Host 主动出站连接 Hub（适配 NAT），优先 QUIC（UDP 8788），失败自动回退 WebSocket；事件带单调 `seq`，Hub 缓冲每 Host 最近 4000 条，断线/丢帧靠补拉最终收敛。

## 仓库

| 仓库 | 职责 | 技术栈 |
|------|------|--------|
| [capri-fe](https://github.com/AgentsHarness/capri-fe) | 浏览器端操作台：ACP 流式对话、工具卡片、权限审批、斜杠命令，Local / Hub 多 Host 模式切换 | Vite + React + TypeScript + Tailwind CSS v4 + Zustand |
| [capri-host](https://github.com/AgentsHarness/capri-host) | 本地 Host：拉起 `grok agent stdio`，把 ACP 会话暴露给 capri-fe / capri-hub；单端口同时提供 API 与内嵌前端 | Go |
| [capri-hub](https://github.com/AgentsHarness/capri-hub) | 中心中继：多 Host 配对、注册与发现、事件聚合、请求转发，NAT 友好（QUIC/WS 双通道） | Go |

## 快速开始

```bash
# 本地单机：前端直连 capri-host，无需 Hub
git clone https://github.com/AgentsHarness/capri-host && cd capri-host
go run ./cmd/acp-host                          # :8765，内置 Web 界面
# 另开终端
git clone https://github.com/AgentsHarness/capri-fe && cd capri-fe
npm install && npm run dev                     # http://localhost:5173

# 多 Host / 跨机器：加一个 Hub（生产务必设置 FE_TOKEN）
git clone https://github.com/AgentsHarness/capri-hub && cd capri-hub
FE_TOKEN=dev-secret go run ./cmd/acp-hub       # :8787，日志打印配对码
cd ../capri-host && HUB_URL=http://<hub>:8787 HUB_PAIR_CODE=<code> go run ./cmd/acp-host
```

## License

MIT —— 各仓库均含 LICENSE 文件。
