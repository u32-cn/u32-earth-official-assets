# u32-earth-official-assets

u32-earth 官方资源库：按「系列 / 资源类型 / 资源」三级组织 3D Tiles、地形、GeoJSON、图标等资源，并提供逐级索引，供 Cesium 三维地球加载。

## 快速使用

```text
GET https://u32-earth-official-assets.u32.cn/assets/index.json
→ 按 index 字段逐级下钻到 assets/default/<类型>/index.json
→ 纯文件资源：按 path + entry 拼 URL
   https://u32-earth-official-assets.u32.cn/assets/<系列>/<类型>/<资源>/<entry>
→ 影像/地形条目：直接取条目里的对象属性（source 等）当属性用
```

本地可直接双击 `docs/index.html` 打开浏览页（索引数据已内嵌，不依赖网络）。

## 特性

- **三级索引**：系列 → 资源类型 → 资源，每级目录各存一份 `index.json`，不合并成总表
- 覆盖 3D Tiles / 影像 / 地形 / GeoJSON / 图标 / 特效 / 视频，并记录不托管在本库的在线影像与地形源
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

- `files` / `bytes` 从磁盘实算，**不含任何 `index.json` 文件本身**；只有库内实存资源才写这两个字段，在线源不写。
- `entry` 为该资源的约定入口文件（如 `tileset.json`、`layer.json`），裸文件与无入口的目录省略。
- 第 3 级条目分两类形态：
  - **纯文件资源**（`3dtiles` / `effects` / `geojson` / `gltfs` / `icons` / `misc` / `videos`）：`name` / `path` / `entry` / `files` / `bytes`
  - **对象配置资源**（`imageries` / `terrains`）：`name` / `description` / `entry` / `files` / `bytes`，外加 `czmImageryJson` 或 `czmTerrainJson`

## 路径规则

地址字段只有一个判断依据：**以 `http` 开头 = 绝对地址，数据不在本库；否则 = 资源在本库内的相对路径**（相对该 `index.json` 所在目录解析）。

| 形态 | 地址字段 | 示例 |
|---|---|---|
| 纯文件资源 | `path`（+ `entry`） | `path:"pngs/"`、`path:"world.jpg"` |
| 影像 / 地形 | `czmImageryJson.source.url` / `czmTerrainJson.source.url` | `"taiwan/"`（库内）、`"https://{s}.tianditu.gov.cn/..."`（在线） |

例外只有两类，都不参与上述判断：`${czm-path}` / `${CzmEarth3DAssets}` 这类由代码解析的模板变量，以及 `ion://1` 这类协议地址。

## 影像 / 地形配置

`imageries` 与 `terrains` 两个类型同时是「资源清单」和「对象配置清单」。条目里的 `czmImageryJson` / `czmTerrainJson` 就是该对象的 JSON，**内部字段与对象序列化结果完全一致，不增不减**：

| 属性 | 内部字段 | 来源 |
|---|---|---|
| `czmImageryJson` | `show` / `source` / `zIndex` / `config` | `u32-earth/src/sceneSerialization/entries/CzmImagery.serialization.ts` 的 `toJson` |
| `czmTerrainJson` | `show` / `source` / `zIndex` | `CzmTerrain` 的 `show` / `source` / `zIndex` 属性（`provider` 为运行时对象，不序列化） |

改这里 = 改配置，不需要改代码库、不需要重新发版。典型在线源（天地图、高德、ArcGIS、Cesium ion、Google）只记录 URL 与参数，数据不在本库。

## 资源概览

| 资源类型 | 资源数 | 文件数 | 体积 |
|---|---:|---:|---:|
| 3dtiles | 2 | 321 | 24,788,903 B |
| effects | 4 | 5 | 431,620 B |
| geojson | 1 | 4 | 15,970,978 B |
| gltfs | 1 | 1 | 261,536 B |
| icons | 2 | 8 | 14,853 B |
| imageries | 14 | 0 | 0 B |
| misc | 1 | 1 | 30,684 B |
| terrains | 6 | 3,216 | 48,179,522 B |
| videos | 1 | 1 | 2,805,071 B |
| **合计** | **32** | **3,557** | **92,483,167 B（88.2 MB）** |

## 索引格式

第 1 级 `docs/assets/index.json`：

```json
{"schema":1,"series":[{"name":"default","path":"default/","index":"default/index.json","types":9,"files":3557,"bytes":92483167}]}
```

第 2 级 `docs/assets/default/index.json`：

```json
{"schema":1,"updated":"2026-11-27","counting":"...","files":3557,"bytes":92483167,
 "types":[{"name":"3dtiles","path":"3dtiles/","index":"3dtiles/index.json","resources":2,"files":321,"bytes":24788903}]}
```

第 3 级 `docs/assets/default/3dtiles/index.json`（纯文件资源）：

```json
{"name":"dayanta_3dtiles_20241202","path":"dayanta_3dtiles_20241202/","entry":"tileset.json",
 "files":316,"bytes":12958165}
```

第 3 级 `docs/assets/default/imageries/index.json`（在线源，绝对地址）：

```json
{"name":"tianditu_geo","description":"天地图-地理坐标系",
 "czmImageryJson":{"show":true,
   "source":{"type":"UrlTemplateImageryProvider","url":"https://{s}.tianditu.gov.cn/img_c/wmts?...&tk=<tk>",
     "tilingScheme":{"type":"GeographicTilingScheme"},"maximumLevel":17},
   "zIndex":0,"config":null}}
```

第 3 级 `docs/assets/default/terrains/index.json`（库内资源，相对路径）：

```json
{"name":"taiwan","description":"台湾地形","entry":"layer.json","files":3216,"bytes":48179522,
 "czmTerrainJson":{"show":true,"zIndex":0,
   "source":{"type":"CesiumTerrainProvider","url":"taiwan/","requestVertexNormals":false,
     "requestWaterMask":false,"requestMetadata":false}}}
```

整个库不含域名，切换分发域名时索引无需改动。

## 站点

- `/` —— 资源浏览页，三级逐级展开（单文件 HTML，数据内嵌）
- `/assets/index.json` —— 第 1 级索引
- `/assets/default/index.json` —— 第 2 级索引
- `/assets/default/<类型>/index.json` —— 第 3 级索引

## License

MIT，详见 [LICENSE](LICENSE)。各资源的来源与许可字段见对应第 3 级索引。
