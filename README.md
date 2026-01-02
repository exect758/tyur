<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>密码保护代码生成器</title>
    <style>
        body { font-family: Arial, sans-serif; max-width: 800px; margin: 0 auto; padding: 20px; }
        .container { background: #f9f9f9; padding: 20px; border-radius: 5px; }
        input[type="password"] { padding: 8px; width: 200px; margin: 10px 0; }
        button { padding: 10px 20px; background: #007bff; color: white; border: none; cursor: pointer; }
        button:hover { background: #0056b3; }
        pre { background: #2d2d2d; color: #f8f8f2; padding: 15px; border-radius: 5px; overflow-x: auto; }
        .note { color: #666; font-size: 14px; margin-top: 10px; }
    </style>
</head>
<body>
    <div class="container">
        <h1>🔐 密码保护代码生成器</h1>
        <p>输入密码，生成对应的前端密码保护代码</p>
        
        <div>
            <label><strong>设置密码：</strong></label><br>
            <input type="password" id="newPassword" placeholder="输入新密码">
            <button onclick="generateCode()">生成代码</button>
        </div>
        
        <div id="result" style="display:none; margin-top:30px;">
            <h3>✅ 生成的代码：</h3>
            <p class="note">复制以下代码到你需要保护的HTML文件最顶部：</p>
            <pre id="newCode"></pre>
            <button onclick="copyCode()">📋 复制代码</button>
            <p class="note"><strong>使用方法：</strong>将代码放在 &lt;body&gt; 标签之后的第一行</p>
        </div>
        
        <div style="margin-top: 40px; padding: 15px; background: #e9f7fe; border-left: 4px solid #2196F3;">
            <h3>📖 使用说明</h3>
            <ol>
                <li>在此页面输入你想要设置的密码</li>
                <li>点击"生成代码"按钮</li>
                <li>复制右侧生成的代码</li>
                <li>粘贴到需要密码保护的HTML文件最顶部</li>
                <li>将HTML文件上传到GitHub Pages或Netlify</li>
            </ol>
            <p><strong>⚠️ 安全提示：</strong>此方法仅适用于保护非敏感内容，重要数据请使用后端验证。</p>
        </div>
    </div>
    
    <script>
    async function sha256(message) {
        const encoder = new TextEncoder();
        const data = encoder.encode(message);
        const hashBuffer = await crypto.subtle.digest('SHA-256', data);
        const hashArray = Array.from(new Uint8Array(hashBuffer));
        return hashArray.map(b => b.toString(16).padStart(2, '0')).join('');
    }
    
    async function generateCode() {
        const newPassword = document.getElementById('newPassword').value;
        if (!newPassword) {
            alert("请输入密码");
            return;
        }
        
        const newHash = await sha256(newPassword);
        const newCode = `<script>
// 密码保护脚本
const storedHash = "${newHash}";

async function checkPassword() {
    const input = prompt("🔒 请输入密码访问此页面：");
    if (!input) {
        document.body.innerHTML = "<h1 style=\\"text-align:center;margin-top:100px;\\">⛔ 访问被取消</h1>";
        return;
    }
    
    const encoder = new TextEncoder();
    const data = encoder.encode(input);
    const hashBuffer = await crypto.subtle.digest('SHA-256', data);
    const hashArray = Array.from(new Uint8Array(hashBuffer));
    const inputHash = hashArray.map(b => b.toString(16).padStart(2, '0')).join('');
    
    if (inputHash !== storedHash) {
        document.body.innerHTML = "<h1 style=\\"text-align:center;margin-top:100px;color:red;\\">❌ 密码错误，拒绝访问</h1>";
    }
}

// 页面加载时检查密码
window.addEventListener('DOMContentLoaded', checkPassword);
<\/script>`;
        
        document.getElementById('newCode').textContent = newCode;
        document.getElementById('result').style.display = 'block';
        
        // 自动滚动到结果区域
        document.getElementById('result').scrollIntoView({ behavior: 'smooth' });
    }
    
    function copyCode() {
        const code = document.getElementById('newCode').textContent;
        navigator.clipboard.writeText(code).then(() => {
            alert("✅ 代码已复制到剪贴板！");
        }).catch(err => {
            alert("❌ 复制失败，请手动选择代码复制");
        });
    }
    </script>
</body>
</html>
用于编程设置密码
