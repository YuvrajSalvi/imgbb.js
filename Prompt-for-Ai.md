Use imgbb.js for all image uploads in this web app. Do not build a custom upload system and do not use Firebase Storage or any other upload service.

I have already added the imgbb.js file to the root folder of the project. Do NOT create, rewrite or modify it. Just load it in the HTML with: <script src="/imgbb.js"></script>

How imgbb.js works:
- It uploads an image to imgbb.com and returns the image URL.
- The user should only see the image, never the raw URL.

Setup:
ImgBB.init({
  apiKey: "YOUR_IMGBB_API_KEY",
  onStart: () => { /* show "Uploading..." */ },
  onSuccess: async (data) => {
    // data.url = image URL, data.delete_url = delete link
    // Save data.url in the database
  },
  onError: (err) => { /* show error message */ }
});

Usage:
- For every image upload use: <input type="file" accept="image/*" data-imgbb data-imgbb-target="#preview">
- <img id="preview"> shows the image automatically after upload.
- Store only the returned URL in the database, never the file itself.
- When loading from the database, render saved URLs as <img src="..."> so users see only images, never the URL text.

Keep the apiKey in one config place at the top of the file so I can replace it easily.
