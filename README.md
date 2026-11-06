# 直笛守護者

國小直笛課堂遊戲（麥克風音高偵測、冒險地圖、班級排行榜）。

- `index.html`：遊戲本體
- `netlify/functions/sync.mjs`：雲端同步（Netlify Functions + Netlify Blobs，不需要另外申請資料庫）
- `netlify.toml`、`package.json`：Netlify 自動部署設定

Netlify 連結這個倉庫後，Build command 留空、Publish directory 填 `.` 即可。
