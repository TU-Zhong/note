html代码
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>FindById</title>
</head>
<body>

<p id="hhh">你好</p>

<p id="test">你好</p>

<div id="test2">
    你好啊
    <ul>
        <li>你好</li>
        <li>你好</li>
        <li>你好</li>
        <li>你好</li>
    </ul>
</div>

<button>按下</button>
<script src="../Test/FindByID.js" type="module"></script>
</body>
</html>
```
Javascript代码
```javascript
import DOMPurify from "dompurify";

function OClick(){
    const userInput = '<img src="x" alt="aaa" onerror="alert(1)">';
    const cleanHTML = DOMPurify.sanitize(userInput); // 输出: <img src="x">

    const P0=document.getElementById("hhh");
    P0.innerHTML = cleanHTML;


    const P = document.getElementById("test");
    P.innerHTML = "<strong>hello xtu!</strong>"

    const P2 = document.getElementById("test2");
    P2.innerHTML =`<ul>
    <li>你好</li>
    <li>你好</li>
    <li>你好</li>
    <li>你好</li>
    <li>鸟</li>
</ul>
    `
}

addEventListener("click", OClick);
```
[[报错2]]
但是浏览器报错
1. `Uncaught SyntaxError: Cannot use import statement outside a module`
	[[Uncaught SyntaxError：Cannot use import statement outside a module]]
2. `Uncaught TypeError: Failed to resolve module specifier "dompurify". Relative references must start with either "/", "./", or "../".`
	[[报错2]]
<mark style="background: #FFF3A3A6;">在浏览器中使用 ES6 模块时，直接通过 `import DOMPurify from "dompurify"` 导入 npm 包会失败，因为浏览器无法解析非相对路径的模块。</mark>
<mark style="background: #FFF3A3A6;">在 JavaScript 中遇到 `Uncaught SyntaxError: Cannot use import statement outside a module` 错误，是因为你使用了 ES6 的模块导入语法（`import`），但当前环境**未启用模块支持**。以下是具体原因和解决方案：</mark>