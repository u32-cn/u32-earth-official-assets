# u32-earth-official-assets

u32-earth 官方资源库：普通目录树组织 3D Tiles、地形、GeoJSON、图标等资源；目录发现走标准清单 `.u32-dir.json`（u32-dir-listing），资源语义走资源自述 `.u32-earth-assets.json`（u32-earth-assets），供 u32-earth 三维地球加载。

## 快速使用

```text
GET https://u32-earth-official-assets.u32.cn/assets/.u32-dir.json
→ 逐级取子目录的 .u32-dir.json 下钻
→ 子目录有 .u32-earth-assets.json = u32-earth 资源（entries 给出挂接对象的类型与配置）
→ 其余目录 = 普通导航目录；清单里的文件 = 普通文件资源
```

格式规范全文：u32-earth `docs/dev/data-formats/asset-library.md`（方案转正后位置）。

浏览页 `docs/index.html` 渲染目录树（数据来自 `.u32-dir.json`，需经静态服务访问，`file://` 双击会被浏览器拦 fetch）。

## 特性

- **无索引文件**：没有 index.json，目录即结构；清单与自述都是点开头约定文件，不与资源文件混
- 覆盖 3D Tiles / 影像 / 地形 / GeoJSON / 图标 / 特效 / 视频，并记录不托管在本库的在线影像与地形源
- 站点零 CDN、零构建、无后端；资源按原路径静态发布

## 目录与文件规则

```text
docs/assets/
├── .u32-dir.json                        # 静态目录清单（u32-ls gen-dir-listing 生成）
└── default/                             # 普通导航目录
    ├── .u32-dir.json
    ├── 3dtiles/
    │   ├── .u32-dir.json
    │   └── dayanta_3dtiles_20241202/    # 资源目录
    │       ├── .u32-dir.json
    │       ├── .u32-earth-assets.json   # 资源自述（本资源挂接的对象）
    │       └── tileset.json …
    └── imageries/
        ├── .u32-dir.json
        └── tianditu_geo/                # 配置型资源目录（无实体数据）
            ├── .u32-dir.json
            └── .u32-earth-assets.json
```

- `.u32-dir.json`：`u32-ls gen-dir-listing docs/assets --deep` 生成/更新（覆盖保留 created）；**新增资源后必须重新执行**
- `.u32-earth-assets.json`：手工维护，单一事实源；**只针对地形 / 影像 / 3D Tiles**（glb、geojson 等普通文件资源不需要，靠扩展名判型）；改动不要求重新生成清单（清单只列文件名）

## 资源自述要点（`.u32-earth-assets.json`）

- 信封：`magic: "u32-earth-assets"`、`version: 1`，`data.entries` 为挂接对象数组，每条 `{ name, description, type, objJson }`
- `type` = u32-earth 场景对象类型名；`objJson` = 该对象序列化 JSON，**与场景序列化结果字段完全一致，不增不减**
- `objJson` 内地址填相对路径（相对本文件所在目录）或在线源绝对地址，如 `{z}/{x}/{y}.png`、`tileset.json`

## 资源概览

| 目录 | 内容 |
|---|---|
| 3dtiles | 大雁塔、Revit 学校 2 个 3D Tiles 资源（CzmTileset，有自述文件指 tileset.json 入口） |
| geojson | 天地图 GS 审图号省市县 3 份 GeoJSON 文件（CzmGeoJson，按扩展名判型） |
| gltfs | running_jerry.glb 模型文件 |
| imageries | 10 个在线/程序化影像源（CzmImagery 配置型） |
| terrains | 台湾地形（库内实体）+ 平球/ion/ArcGIS/Google 4 个配置型（CzmTerrain） |
| effects / icons / misc / videos | 普通文件资源（gif/png/jpg/mp4 等，靠 dir-listing 发现） |

## 站点

- `/` —— 目录结构浏览页（单文件 HTML，fetch 各级 `.u32-dir.json`）
- `/assets/.u32-dir.json` —— 根目录清单，逐级下钻入口

## License

MIT，详见 [LICENSE](LICENSE)。各资源的来源与许可信息见对应资源 `.u32-earth-assets.json` 的 meta/entry 字段。
