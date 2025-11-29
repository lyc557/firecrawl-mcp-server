本地模式：
{
     "mcpServers": {
       "firecrawl-mcp": {
         "command": "npx",
         "args": ["-y", "firecrawl-mcp"],
         "env": {
           "FIRECRAWL_API_KEY": "YOUR-API-KEY"
         }
       }
     }
}

http server模式


工具概览

- firecrawl_scrape ：单页内容抓取，支持多种输出格式与可选高级参数。适用于已确定目标页面的场景。定义见 src/index.ts:262-286 ，执行见 src/index.ts:292-309 。
- firecrawl_map ：站点映射，发现站内可索引的 URL 列表（更可控的“发现”能力）。定义见 src/index.ts:313-331 ，参数见 src/index.ts:332-339 ，执行见 src/index.ts:350-356 。
- firecrawl_search ：联网搜索，支持搜索算子与可选对结果做轻量抓取。定义见 src/index.ts:360-420 ，参数见 src/index.ts:422-432 ，执行见 src/index.ts:441-446 。
- firecrawl_crawl ：启动站点级异步爬取任务，批量提取多个页面内容。定义见 src/index.ts:450-479 ，参数见 src/index.ts:480-509 ，执行见 src/index.ts:515-520 。
- firecrawl_check_crawl_status ：查询爬取任务状态与进度。定义见 src/index.ts:524-538 ，执行见 src/index.ts:545-547 。
- firecrawl_extract ：按给定 schema 从多个 URL 提取结构化数据，支持 LLM 抽取与可选外链/搜索。定义见 src/index.ts:551-588 ，参数见 src/index.ts:589-596 ，执行见 src/index.ts:615-617 。
典型用途与参数

- firecrawl_scrape
  - 关键词： url 、 formats （如 markdown ）、 onlyMainContent 、 waitFor 、 includeTags 、 excludeTags 、 skipTlsVerification
  - 返回：按 formats 输出的页面内容
- firecrawl_map
  - 关键词： url 、 search 、 sitemap （ include/skip/only ）、 includeSubdomains 、 limit 、 ignoreQueryParameters
  - 返回：发现到的 URL 列表
- firecrawl_search
  - 关键词： query 、 limit 、 sources （ web/images/news ）、 lang 、 country 、 scrapeOptions （可选对命中的结果做轻量抓取）
  - 返回：搜索结果数组（必要时附带抓取内容）
- firecrawl_crawl
  - 关键词： url （支持路径前缀）、 maxDiscoveryDepth 、 limit 、 sitemap 、 allowExternalLinks 、 allowSubdomains 、 deduplicateSimilarURLs 、 ignoreQueryParameters 、 scrapeOptions
  - 返回：操作 ID（用 firecrawl_check_crawl_status 查询进度与结果）
- firecrawl_check_crawl_status
  - 关键词： id （爬取任务 ID）
  - 返回：任务状态、进度与结果摘要
- firecrawl_extract
  - 关键词： urls 、 prompt 、 schema 、 allowExternalLinks 、 enableWebSearch 、 includeSubdomains
  - 返回：按 schema 定义的结构化数据
选择建议

- 已知具体页面 → 用 firecrawl_scrape
- 先发现站内页面再挑选 → 用 firecrawl_map ，后续对选中的 URL 用 scrape 或 extract
- 跨站点、开放问题 → 用 firecrawl_search 找到结果，再对命中的页面用 scrape/extract
- 需要较全面的站点覆盖 → 用 firecrawl_crawl 启动任务，再用 firecrawl_check_crawl_status 追踪
- 需要结构化信息（价格、名称、属性等） → 用 firecrawl_extract 并提供 schema