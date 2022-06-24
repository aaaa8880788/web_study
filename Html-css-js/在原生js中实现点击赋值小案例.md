# 在原生js中实现点击赋值小案例

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>

  <body>
    <h1 class="title">我爱你啊~桓桓</h1>
    <button id="btn">点击复制</button>
    <script>
      function copy(data, callback) {
        //创建input
        const input = window.document.createElement("input");
        //赋值并设置不可见
        input.setAttribute("value", data);
        input.setAttribute("display", "none");
        document.body.appendChild(input);
        input.select();
        let err = undefined;
        //捕获异常，如果浏览器不支持则抛出异常。
        try {
          const status = window.document.execCommand("copy");
          if (status) {
            callback(err, data);
            return;
          }
          callback(new Error("复制失败"), data);
        } catch (error) {
          err = error;
          callback(err, data);
        } finally {
          window.document.body.removeChild(input);
        }
      }
      const btn = document.getElementById("btn");
      const title = document.querySelector(".title");
      btn.addEventListener("click", () => {
        copy(title.innerHTML, (err, data) => {
          if (!err) {
            alert("已复制");
          } else {
            console.log(err.message);
          }
        });
      });
    </script>
  </body>
</html>

```

