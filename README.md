# Momotan Storage Explorer

Website local storage management tool / 网站本地存储管理工具

单文件、零依赖、纯本地的**同源存储检查器**。所有数据只读写当前 origin 的浏览器存储。

v3.1.0 release 2026/10/23

Example / 示例: https://momotan.cc/storage.html

## 能力

| 能力 | localStorage | sessionStorage | IndexedDB | CacheStorage |
|---|---|---|---|---|
| 查看 | ✓ | ✓ | ✓ | ✓ |
| 编辑/新增 | ✓ | ✓ | ✓ | ✗ |
| 删除单项 | ✓ | ✓ | ✓（真正删除整个数据库） | ✓ |
| 清空全部 | ✓ | ✓ | ✓（清空数据、保留结构） | ✓ |
| 导出 JSON | ✓ | ✓ | ✓（**不是完整备份**） | ✗ |
| 导入 JSON | ✓ | ✓ | ✓（覆盖前自动备份） | ✗ |

## 已知限制

- 只能查看/操作**当前 origin** 的存储。
- CacheStorage 为只读，且仅在**安全上下文**（HTTPS 或 localhost）下可用。
- IndexedDB 编辑器每个对象存储最多加载前 **1000 条**记录（超出部分在界面上有提示；结构变更时会先读取全量记录合并，不会丢失）。
- IndexedDB **导出不含二进制记录**，Date/Map/Set 等 JSON 无法无损表达的值也会被跳过并计数——**导出不是完整备份**。
- IndexedDB 编辑器中：Date/数组/二进制类型的**键只读**（保存时保留原始键对象）；含上述复杂类型的**值只读**（保存时保留原值）；复合键路径用 JSON 数组表示（如 `["a","b"]`）。
- 记录值为 JSON 格式编辑；输入普通文本会按字符串保存，形如 JSON 但语法错误会报错而不是静默降级。
- `indexedDB.databases()` 在旧版 WebKit/Safari 上不可用（此时无法枚举数据库列表）。
- 页面含内联 `<script>`/`<style>`，严格 CSP 的站点需要相应放行。
- 若站点的 Service Worker 缓存了本页面，可能一直看到旧版本。

## 安全提示

- 导出的 JSON 可能包含 **token、会话等敏感信息**，请勿随意分享。
- **一般情况下不推荐把本页面公开暴露在生产路径**，至少加 `noindex`，最好加访问保护。
- 不要导入来源不可信的 JSON 文件（导入覆盖同名 IndexedDB 数据库前会自动下载一次当前备份）。

## 部署建议

- 作为静态文件直接放到站点任意路径即可。
- 建议响应头 `Cache-Control: no-store`，避免被站点 SW 或 CDN 缓存旧版本。

## 浏览器支持

- 推荐最新版 Chrome / Edge / Firefox。
- Safari：旧版本不支持 `indexedDB.databases()` 枚举；CacheStorage 需要安全上下文。

## 开发

- 单文件、无构建，可以直接编辑 `storage.html`。
- 纯函数测试页：`storage-test.html`（需通过 HTTP 访问，与 storage.html 同目录；file:// 协议下因同源限制无法运行）。
- 测试页通过隐藏 iframe 加载 storage.html 并直接调用其中的 `storageManager` 纯函数，覆盖 XSS 转义、IndexedDB 键序列化、写计划校验等测试。

## 页面截图

<img width="1598" height="1177" alt="image" src="https://github.com/user-attachments/assets/03ea672c-df65-4320-b84d-86d4fbf384e2" />
<img width="1598" height="1177" alt="image" src="https://github.com/user-attachments/assets/0870025c-23da-4ef7-ae29-2ee7e01c24b5" />
<img width="1598" height="1177" alt="image" src="https://github.com/user-attachments/assets/77100817-026f-47b2-9918-8072e44fd15f" />
<img width="1599" height="1177" alt="image" src="https://github.com/user-attachments/assets/02e54915-3821-4447-afb1-baa470a8911b" />
<img width="1598" height="1177" alt="image" src="https://github.com/user-attachments/assets/30d9374b-fd56-4229-a5d7-c091b909e523" />
<img width="3699" height="2345" alt="b4ef1782136f914948df35649d7c8c1d" src="https://github.com/user-attachments/assets/77d2511b-7cdc-46ce-af29-e752659bebdf" />
<img width="3734" height="2347" alt="e03be8de73c7735ed3c5cefc07cb5bb0" src="https://github.com/user-attachments/assets/c6fbf4cb-729b-45ea-a662-03498202b4cd" />
