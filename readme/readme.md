<!-- PATH=/opt/homebrew/opt/node@24/bin:$PATH node -v
PATH=/opt/homebrew/opt/node@24/bin:$PATH npm -v -->


toolName: search_by_regex
            
status: success
          
query: FIRECRAWL_API_URL|self-hosted|自托管|docker
          
**自托管用 Docker 启动**
- 构建镜像（项目根目录）：
```bash
docker build -t firecrawl-mcp-server .
```
- 以 HTTP 流式模式运行（绑定到 `0.0.0.0:3000`，指向你的自托管 Firecrawl API）：
```bash
docker run --rm \
  -e FIRECRAWL_API_URL=https://127.0.0.1:3002 \
  -e HTTP_STREAMABLE_SERVER=true \
  -e HOST=0.0.0.0 \
  -e PORT=3000 \
  -p 3001:3000 \
  firecrawl-mcp-server
```
- 说明：
  - `FIRECRAWL_API_URL` 指向你的自托管 Firecrawl 服务的基础地址（例如 `https://firecrawl.your-domain.com` 或 `http://your-firecrawl:3002`）。
  - `HTTP_STREAMABLE_SERVER=true` 切到 HTTP 流式模式，适合容器运行。
  - `HOST=0.0.0.0` 让容器内服务对外可访问（默认是 `localhost`）。
  - `PORT` 默认 3000，可自行调整。

**若自托管 API 需要鉴权（单一兜底）**
- 给容器再加上密钥（你的自托管服务若开启鉴权才需要）：
```bash
docker run --rm \
  -e FIRECRAWL_API_URL=https://firecrawl.your-domain.com \
  -e FIRECRAWL_API_KEY=fc-你的自托管API键 \
  -e HTTP_STREAMABLE_SERVER=true \
  -e HOST=0.0.0.0 \
  -e PORT=3000 \
  -p 3000:3000 \
  firecrawl-mcp-server
```

**代码依据**
- 自托管 API 配置读取：`/Users/yangcailu/chengtay_code/firecrawl-mcp-server/src/index.ts:124`
- API Key 可选（自托管时只要提供 `FIRECRAWL_API_URL` 就能启动）：`/Users/yangcailu/chengtay_code/firecrawl-mcp-server/src/index.ts:104`
- HTTP 模式与端口/主机：`/Users/yangcailu/chengtay_code/firecrawl-mcp-server/src/index.ts:627-647`
- 镜像使用 Node 22（避免 EBADENGINE）：`/Users/yangcailu/chengtay_code/firecrawl-mcp-server/Dockerfile:3,21`

**验证**
- 看到初始化日志如“Firecrawl MCP Server initialized successfully”且无 `EBADENGINE`。
- 主机访问 `http://localhost:3000`（客户端通过 MCP HTTP 流式接口连接）。

需要我帮你把 `FIRECRAWL_API_URL` 填成你当前自托管地址并检查启动日志吗？