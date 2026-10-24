# Momotan Storage Explorer

Website local storage management tool / 网站本地存储管理工具

单文件、零依赖、纯本地的**同源存储检查器**。所有数据只读写当前 origin 的浏览器存储。

开源地址：https://github.com/kvetc/Momotan-Storage-Explorer

许可证：MIT license

当前版本：v3.2.2 release 2026/10/24

Example / 示例: https://momotan.cc/storage.html

## 能力

| 能力 | localStorage | sessionStorage | IndexedDB | CacheStorage |
|---|---|---|---|---|
| 查看 | ✓ | ✓ | ✓ | ✓ |
| 编辑/新增 | ✓ | ✓ | ✓ | ✗ |
| 删除单项 | ✓ | ✓ | ✓（删除整个数据库） | ✓ |
| 清空全部 | ✓ | ✓ | ✓（清空数据、保留结构） | ✓ |
| 导出 JSON | ✓ | ✓ | ✓（**不是完整备份**） | ✗ |
| 导入 JSON | ✓ | ✓ | ✓（覆盖前自动备份） | ✗ |

## 已知限制

- 只能查看/操作**当前 origin** 的存储。
- CacheStorage 为只读，且仅在**安全上下文**（HTTPS 或 localhost）下可用。
- IndexedDB 编辑器每个对象存储最多加载前 **1000 条**记录（超出部分在界面上有提示；结构变更时会先读取全量记录合并，不会丢失）。
- 导出/结构变更合并会读取全量记录，超大库可能较慢或内存占用高。
- IndexedDB **导出不含二进制记录**，Date/Map/Set 等 JSON 无法无损表达的值也会被跳过并计数——**导出不是完整备份**。导入覆盖前的**自动备份使用同一导出格式，同样受限**，请勿将其当作完整备份。
- IndexedDB 编辑器中：Date/数组/二进制类型的**键只读**（保存时保留原始键对象）；含上述复杂类型的**值只读**（保存时保留原值）；复合键路径用 JSON 数组表示（如 `["a","b"]`）。
- 索引以结构化方式编辑（名称 / 键路径 / 唯一 unique / 多值 multiEntry）；重建对象存储时完整保留索引语义。
- 记录值为 JSON 格式编辑；除 `{` / `[` 等结构化 JSON 起始形式外的普通文本会按字符串保存，形如 JSON 但语法错误会报错而不是静默降级（纯文本字符串也可用英文双引号包裹后按 JSON 字符串保存）。
- `indexedDB.databases()` 在旧版 WebKit/Safari 上不可用（此时无法枚举数据库列表）。
- 页面含内联 `<script>`/`<style>`，严格 CSP 的站点需要相应放行。
- 若站点的 Service Worker 缓存了本页面，可能一直看到旧版本。
- 部分浏览器在**没有任何页面内交互**时连续按返回，浏览器的安全策略会静默跳过压入的历史条目，保存提示弹窗可能被绕过。本工具会在页面离开（`pagehide`）时会自动把未保存的被编辑内容保存为**草稿**，下次打开时提示恢复。若草稿超出 localStorage 配额导致无法写入，下次打开时会提示草稿未能保存。

## 安全提示

- 导出的 JSON 可能包含 **token、会话等敏感信息**，请勿随意分享。
- 草稿会把未保存内容（包括 sessionStorage / IndexedDB 的编辑）暂存到 localStorage，可能包含 token 等敏感信息；恢复或丢弃后会清除草稿。
- **一般情况下不推荐把本页面公开暴露在生产路径**：至少加 `noindex`，最好加访问保护。
- 不要导入来源不可信的 JSON 文件（导入覆盖同名 IndexedDB 数据库前会自动下载一次当前备份，但该备份同样不含二进制与复杂类型记录）。

## 部署建议

- 作为静态文件直接放到站点任意路径即可。
- 建议响应头 `Cache-Control: no-store`，避免被站点 SW 或 CDN 缓存旧版本。
- **不要部署 `storage-test.html`**：它会把 storage.html 加载进 iframe 并直接访问其中的 `storageManager`，仅供本地开发使用。

## 浏览器支持

- 推荐最新版 Chrome / Edge / Firefox。
- Safari：旧版本不支持 `indexedDB.databases()` 枚举；CacheStorage 需要安全上下文。

## 开发

- 单文件、无构建，可以直接编辑 `storage.html`。
- 自动化测试页：`storage-test.html`（需通过 HTTP 访问，与 storage.html 同目录；file:// 协议下因同源限制无法运行；**不要部署到生产路径**）。
- 测试页通过隐藏 iframe 加载 storage.html 并直接调用其中的 `storageManager`，覆盖 XSS 转义、IndexedDB 键序列化、写计划校验、升级事务回滚、导入事务失败、草稿恢复与生命周期等数据安全高发区。

## 页面截图

<img width="1663" height="1163" alt="image" src="https://github.com/user-attachments/assets/584ba517-6cfd-4a2a-82a5-f13ad1b4eed4" />
<img width="1663" height="1166" alt="image" src="https://github.com/user-attachments/assets/a1b2d87c-70cb-4287-861d-aa6b1348ea7e" />
<img width="1663" height="1165" alt="image" src="https://github.com/user-attachments/assets/46a64447-54ae-4a07-95ca-08372934c5ef" />
<img width="1663" height="1164" alt="image" src="https://github.com/user-attachments/assets/8ab4f338-b213-4acd-9ee0-8677acab2b99" />
<img width="1663" height="1165" alt="image" src="https://github.com/user-attachments/assets/26ec79b9-f009-4c82-839a-00315f634a7a" />
<img width="3732" height="2345" alt="0e53321f163f199a640c03d3adc17950" src="https://github.com/user-attachments/assets/487b4af2-4e93-49a9-9668-925b70458760" />
<img width="3732" height="2345" alt="24e882fb0c348db2ca881b94bcf4d188" src="https://github.com/user-attachments/assets/e5174bde-6cf4-4aff-9445-f7166bb8701f" />
