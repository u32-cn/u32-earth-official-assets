# u32-earth-official-assets

u32-earth 官方资源库：按「系列 / 资源类型 / 资源」三级组织 3D Tiles、地形、GeoJSON、图标等资源，并提供逐级索引，供 Cesium 三维地球加载。

## 快速使用

```text
GET https://u32-earth-official-assets.u32.cn/assets/index.json
→ 按 index 字段逐级下钻到 assets/default/<类型>/index.json
→ 按 entry 拼资源 URL：
   https://u32-earth-official-assets.u32.cn/assets/<系列>/<类型>/<资源>/<entry>
```

本地可直接双击 `docs/index.html` 打开浏览页（索引数据已内嵌，不依赖网络）。

## 特性

- **三级索引**：系列 → 资源类型 → 资源，每级目录各存一份 `index.json`，不合并成总表
- 覆盖 3D Tiles / 地形 / GeoJSON / 图标 / 特效 / 视频
- 站点零 CDN、零构建、无后端；资源按原路径静态发布

## 层级规则

```text
docs/assets/
├── index.json                 # 第1级：系列清单
└── default/
    ├── index.json             # 第2级：资源类型清单
    └── 3dtiles/
        └── index.json         # 第3级：该类型下的资源清单
```

- `files` / `bytes` 从磁盘实算，**不含任何 `index.json` 文件本身**。
- `kind` 为 `file` 的条目直接位于类型目录下，`path` 为空、`entry` 指向该文件。
- `entry` 为空字符串表示该目录没有约定入口文件。
- `source` 无法确证的写 `"未标注"`；仓库整体许可见 [LICENSE](LICENSE)。

## 资源概览

| 资源类型 | 资源数 | 文件数 | 体积 |
|---|---:|---:|---:|
| 3dtiles | 2 | 321 | 24,788,903 B |
| effects | 4 | 5 | 431,620 B |
| geojson | 1 | 4 | 15,970,978 B |
| gltfs | 1 | 1 | 261,536 B |
| icons | 2 | 8 | 14,853 B |
| misc | 1 | 1 | 30,684 B |
| terrains | 1 | 3,216 | 48,179,522 B |
| videos | 1 | 1 | 2,805,071 B |
| **合计** | **13** | **3,557** | **92,483,167 B（88.2 MB）** |

## 索引格式

第 1 级 `docs/assets/index.json`：

```json
{"schema":1,"series":[{"name":"default","path":"default/","index":"default/index.json","types":8,"files":3557,"bytes":92483167}]}
```

第 2 级 `docs/assets/default/index.json`：

```json
{"schema":1,"series":"default","types":[{"name":"3dtiles","path":"3dtiles/","index":"3dtiles/index.json","resources":2,"files":321,"bytes":24788903}]}
```

第 3 级 `docs/assets/default/3dtiles/index.json`：

```json
{"schema":1,"series":"default","type":"3dtiles","resources":[{"name":"dayanta_3dtiles_20241202","kind":"dir","path":"dayanta_3dtiles_20241202/","entry":"tileset.json","files":316,"bytes":12958165,"source":"未标注","license":"MIT"}]}
```

所有路径均为相对路径，不含域名；切换分发域名时索引无需改动。

## 站点

- `/` —— 资源浏览页，三级逐级展开（单文件 HTML，数据内嵌）
- `/assets/index.json` —— 第 1 级索引
- `/assets/default/index.json` —— 第 2 级索引
- `/assets/default/<类型>/index.json` —— 第 3 级索引

## License

MIT，详见 [LICENSE](LICENSE)。各资源的来源与许可字段见对应第 3 级索引。
