```shell
mamba run -n vln-docs mkdocs serve -a 0.0.0.0:8000
```

---

## 下次更新可直接使用的提示词

你是一名资深的 DevOps 与技术文档工程师。请在当前仓库中直接更新 MkDocs 文档，而不是只给建议。

项目背景：
- 项目：Vision-Language Navigation (VLN) 文档
- 环境：WSL Ubuntu
- 文档框架：MkDocs Material
- 已有导航结构：
	- Quick Start
		- Dependencies: docs/quick_start/dependencies.md
		- Installation Steps: docs/quick_start/installation.md
		- Data Download: docs/quick_start/data.md
		- Agent Integration: docs/quick_start/integration.md
	- API Document
		- Human State Queries: docs/api/human_state.md
		- Dynamic Scene Updates: docs/api/scene_updates.md
		- Collision Checks: docs/api/collision_checks.md

请先读取以下原始素材（如果存在）：
- env.md
- agent.md
- api.md

更新规则：
1. 将 env.md 内容映射到 Quick Start 的前三页（依赖、安装、数据）。
2. 将 agent.md 作为 env 与 api 的桥接内容，主要写入 Agent Integration 页。
3. 将 api.md 按主题拆分写入三个 API 页面。
4. 保留现有页面标题，不破坏已有导航结构。
5. 纯英文。
6. 如发现配置与页面不一致，直接修复 mkdocs.yml。
7. 更新后执行文档构建验证：
	 - mamba run -n vln-docs mkdocs build --strict

输出要求：
- 直接完成文件修改。
- 最后给出：
	- 实际改了哪些文件
	- 构建是否成功
	- 若有 warning，说明是否影响使用

如果这次我会附带新的素材，请优先以新素材覆盖旧内容，并保持文档结构稳定。
