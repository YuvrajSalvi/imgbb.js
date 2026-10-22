# imgbb.js

Single-file JavaScript helper: upload images to [imgbb.com](https://imgbb.com), show them on your page automatically (the user never sees the URL), and save the URL in your database.

## Install
Copy `imgbb.js` to your website root.

```html
<script src="/imgbb.js"></script>
<script>
  ImgBB.init({
    apiKey: "YOUR_IMGBB_API_KEY",   // get it at https://api.imgbb.com
    saveEndpoint: "/api/images"      // your backend that stores the URL
  });
</script>

<input type="file" data-imgbb data-imgbb-field="avatar" data-imgbb-target="#preview">
<img id="preview">

<!-- On page load, shows the saved image from your database -->
<img data-imgbb-show="avatar">
```

## Backend contract (any language)
- `POST /api/images` with JSON `{ field, url, delete_url, id }` -> save in DB.
- `GET /api/images?field=avatar` -> return `{ "url": "https://i.ibb.co/..." }`.

Example (Node + Express + SQLite):
```js
const express = require("express"), Database = require("better-sqlite3");
const db = new Database("images.db");
db.exec("CREATE TABLE IF NOT EXISTS images(field TEXT PRIMARY KEY, url TEXT, delete_url TEXT)");
const app = express(); app.use(express.json());
app.post("/api/images", (req, res) => {
  const { field, url, delete_url } = req.body;
  db.prepare("REPLACE INTO images VALUES(?,?,?)").run(field, url, delete_url);
  res.json({ ok: true });
});
app.get("/api/images", (req, res) => {
  const row = db.prepare("SELECT url FROM images WHERE field=?").get(req.query.field);
  res.json(row || {});
});
app.use(express.static(".")).listen(3000);
```
(In production, key the rows by logged-in user too.)

## Options
| Option | Description |
|---|---|
| `apiKey` | ImgBB key (public in browser) |
| `uploadEndpoint` | Your own proxy to ImgBB; hides the key (recommended in production) |
| `saveEndpoint` | Your DB endpoint |
| `expiration` | Seconds, 0 = never |
| `headers` | Extra headers for saveEndpoint (auth/CSRF) |
| `onStart / onSuccess / onError` | Callbacks |

Events on the input: `imgbb:success`, `imgbb:error`. Manual API: `ImgBB.upload(file)`, `ImgBB.save(field, data)`.

## Security note
A browser-side `apiKey` can be seen by anyone. For public sites use `uploadEndpoint` with a small server proxy that adds your key.

## License
MIT
