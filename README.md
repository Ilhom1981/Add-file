<!DOCTYPE html>
<html lang="uz">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Turkona</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            background-color: #f0f0f0;
        }
        .post {
            background: white;
            padding: 10px;
            margin: 10px auto;
            width: 80%;
            border-radius: 10px;
        }
        input, button {
            padding: 10px;
            margin: 5px;
            width: 80%;
        }
        img {
            max-width: 100%;
            border-radius: 5px;
        }
        .comments p {
            background: #ddd;
            padding: 5px;
            border-radius: 5px;
            margin: 5px 0;
        }
    </style>
</head>
<body>
    <h1>Turkona</h1>
    <h2>Янги пост қўшиш</h2>
    <input type="text" id="postText" placeholder="Постингизни ёзинг...">
    <input type="file" id="postImage">
    <button onclick="addPost()">Пост жойлаш</button>

    <h3>Постлар</h3>
    <div id="posts"></div>

    <script>
        function addPost() {
            let lastPostTime = localStorage.getItem("lastPostTime");
            let now = new Date().getTime();

            if (lastPostTime && now - lastPostTime < 86400000) {
                alert("Сиз 24 соатда фақат бир марта пост жойлай оласиз!");
                return;
            }

            let text = document.getElementById("postText").value;
            let image = document.getElementById("postImage").files[0];
            let posts = localStorage.getItem("posts") ? JSON.parse(localStorage.getItem("posts")) : [];

            if (text || image) {
                let reader = new FileReader();
                reader.onload = function (e) {
                    let newPost = { text: text, img: image ? e.target.result : null, comments: [] };
                    posts.push(newPost);
                    localStorage.setItem("posts", JSON.stringify(posts));
                    localStorage.setItem("lastPostTime", now);
                    displayPosts();
                };
                
                if (image) {
                    reader.readAsDataURL(image);
                } else {
                    let newPost = { text: text, img: null, comments: [] };
                    posts.push(newPost);
                    localStorage.setItem("posts", JSON.stringify(posts));
                    localStorage.setItem("lastPostTime", now);
                    displayPosts();
                }
            }
        }

        function displayPosts() {
            let posts = localStorage.getItem("posts") ? JSON.parse(localStorage.getItem("posts")) : [];
            document.getElementById("posts").innerHTML = posts.map((p, index) => 
                `<div class="post">
                    <p>${p.text}</p>
                    ${p.img ? `<img src="${p.img}" width="200">` : ""}
                    <input type="text" placeholder="Изоҳ қўшинг" onkeypress="addComment(event, ${index}, this)">
                    <div class="comments">${p.comments ? p.comments.map(c => `<p>${c}</p>`).join("") : ""}</div>
                </div>`
            ).join("");
        }

        function addComment(event, index, input) {
            if (event.key === "Enter") {
                let comment = input.value;
                let posts = JSON.parse(localStorage.getItem("posts"));

                if (!posts[index].comments) {
                    posts[index].comments = [];
                }

                posts[index].comments.push(comment);
                localStorage.setItem("posts", JSON.stringify(posts));
                displayPosts();
            }
        }

        displayPosts();
    </script>
</body>
</html>
