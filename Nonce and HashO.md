

---

### 1️⃣ Hash 與 Nonce 的差別

|特性|Nonce|Hash|
|---|---|---|
|是否需要伺服器生成？|✅ 需要，每個請求生成不同的隨機值|❌ 不需要，如果內容固定且事先知道 hash|
|適用於 inline 內容？|✅ 是|✅ 是，必須完全匹配|
|適用於動態內容？|✅ 可以|❌ 不行，內容必須固定|
|安全性|高，每次請求不同|高，但僅限固定內容|

---

### 2️⃣ Hash 的使用方式（前端已知即可，不需修改伺服器）

假設你的 inline `<script>` 內容固定為：

```js
console.log('Hello world');
```

1. 計算這段內容的 SHA-256 hash（可以用線上工具或本地命令）
    
2. 在 HTML `<meta>` 標籤中加入 CSP，指定這個 hash：
    

```html
<meta http-equiv="Content-Security-Policy" content="
  default-src 'self'; 
  script-src 'self' 'sha256-<計算好的 hash 值>';
">
<script>
  console.log('Hello world');  <!-- 必須與 hash 完全相同 -->
</script>
```

✅ 特點：

- 無需伺服器生成或修改程式碼
    
- 只允許與 hash 完全匹配的 inline script 執行
    

---

### 3️⃣ 注意事項

- 如果你沒有任何 inline CSS 或 JS，而只是使用外部資源（如 Bootstrap、jQuery），**根本不需要 nonce 或 hash**，只要在 CSP 裡 whitelist 對應 URL 即可，例如：
    

```http
Content-Security-Policy:
  default-src 'self';
  style-src 'self' https://maxcdn.bootstrapcdn.com;
  script-src 'self' https://code.jquery.com;
  font-src 'self' https://fonts.gstatic.com;
```

- 這樣就安全又簡單。
    

---

好的，下面給你一個 **使用 nonce 的完整範例（中文說明）**，適合 Node.js/Express 環境，展示如何讓 inline `<script>` 或 `<style>` 被 CSP 允許。

---

### 1️⃣ Node.js / Express 範例

```js
import express from 'express';
import crypto from 'crypto';

const app = express();

app.get('/', (req, res) => {
  // 生成隨機 nonce，每次請求都不同
  const nonce = crypto.randomBytes(16).toString('base64');

  // 設定 CSP 標頭，指定 nonce
  res.setHeader(
    "Content-Security-Policy",
    `default-src 'self'; style-src 'self' 'nonce-${nonce}'; script-src 'self' 'nonce-${nonce}'`
  );

  // 回傳 HTML，inline style 和 script 都加上 nonce
  res.send(`
    <html>
      <head>
        <title>Nonce 範例</title>
        <style nonce="${nonce}">
          body { background-color: #f0f0f0; font-family: Arial; }
          h1 { color: #333; }
        </style>
      </head>
      <body>
        <h1>使用 nonce 的 CSP 範例</h1>
        <script nonce="${nonce}">
          console.log('這個 script 被允許執行，因為 nonce 正確');
          alert('Hello world!');
        </script>
      </body>
    </html>
  `);
});

app.listen(3000, () => {
  console.log('伺服器已啟動，請訪問 http://localhost:3000');
});
```

---

### 2️⃣ 說明

1. **Nonce 是什麼？**
    
    - 一個隨機字串，每次頁面請求生成不同的值。
        
    - CSP 標頭和 inline 標籤都必須使用同一個 nonce。
        
2. **CSP 標頭解釋**
    
    ```bash
    default-src 'self';          // 預設只允許自家域名
    style-src 'self' 'nonce-XXX'; // 允許自家域名 + 有 nonce 的 inline style
    script-src 'self' 'nonce-XXX'; // 允許自家域名 + 有 nonce 的 inline script
    ```
    
3. **優點**
    
    - 阻擋惡意注入的 inline script/style
        
    - 每次請求 nonce 不同，安全性高
        

---

