<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>食品热量分析器</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            background: white;
            border-radius: 20px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.1);
            overflow: hidden;
        }

        .header {
            background: linear-gradient(135deg, #ff6b6b, #ee5a24);
            color: white;
            padding: 30px;
            text-align: center;
        }

        .header h1 {
            font-size: 2.5em;
            margin-bottom: 10px;
        }

        .header p {
            font-size: 1.1em;
            opacity: 0.9;
        }

        .main-content {
            padding: 40px;
        }

        .upload-section {
            background: #f8f9fa;
            border: 3px dashed #dee2e6;
            border-radius: 15px;
            padding: 40px;
            text-align: center;
            margin-bottom: 30px;
            transition: all 0.3s ease;
        }

        .upload-section:hover {
            border-color: #007bff;
            background: #e3f2fd;
        }

        .upload-section.dragover {
            border-color: #28a745;
            background: #d4edda;
        }

        .upload-icon {
            font-size: 4em;
            color: #6c757d;
            margin-bottom: 20px;
        }

        .file-input {
            display: none;
        }

        .upload-btn {
            background: linear-gradient(135deg, #007bff, #0056b3);
            color: white;
            padding: 15px 30px;
            border: none;
            border-radius: 25px;
            font-size: 1.1em;
            cursor: pointer;
            transition: all 0.3s ease;
            margin: 10px;
        }

        .upload-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 10px 20px rgba(0,123,255,0.3);
        }

        .image-container {
            position: relative;
            margin: 30px 0;
            text-align: center;
        }

        .preview-image {
            max-width: 100%;
            max-height: 500px;
            border-radius: 15px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.2);
        }

        .controls {
            display: flex;
            gap: 20px;
            justify-content: center;
            margin: 30px 0;
            flex-wrap: wrap;
        }

        .control-btn {
            background: linear-gradient(135deg, #28a745, #20c997);
            color: white;
            padding: 15px 25px;
            border: none;
            border-radius: 25px;
            font-size: 1em;
            cursor: pointer;
            transition: all 0.3s ease;
            min-width: 180px;
        }

        .control-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 10px 20px rgba(40,167,69,0.3);
        }

        .control-btn:disabled {
            background: #6c757d;
            cursor: not-allowed;
            transform: none;
            box-shadow: none;
        }

        .results {
            margin-top: 30px;
        }

        .result-card {
            background: #f8f9fa;
            border-radius: 15px;
            padding: 25px;
            margin: 20px 0;
            border-left: 5px solid #007bff;
        }

        .result-title {
            font-size: 1.3em;
            font-weight: bold;
            color: #495057;
            margin-bottom: 15px;
        }

        .calorie-display {
            font-size: 2em;
            font-weight: bold;
            color: #dc3545;
            text-align: center;
            margin: 20px 0;
        }

        .loading {
            display: none;
            text-align: center;
            margin: 20px 0;
        }

        .spinner {
            border: 4px solid #f3f3f3;
            border-top: 4px solid #007bff;
            border-radius: 50%;
            width: 40px;
            height: 40px;
            animation: spin 1s linear infinite;
            margin: 0 auto 10px;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        .error {
            background: #f8d7da;
            color: #721c24;
            padding: 15px;
            border-radius: 10px;
            margin: 20px 0;
            border: 1px solid #f5c6cb;
        }

        .food-list {
            list-style: none;
            padding: 0;
        }

        .food-item {
            background: white;
            margin: 10px 0;
            padding: 15px;
            border-radius: 10px;
            border-left: 4px solid #28a745;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }

        .food-item.high-calorie {
            border-left-color: #dc3545;
            background: #fff5f5;
        }

        @media (max-width: 768px) {
            .main-content {
                padding: 20px;
            }
            
            .controls {
                flex-direction: column;
                align-items: center;
            }
            
            .control-btn {
                width: 100%;
                max-width: 300px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🍎 食品热量分析器</h1>
            <p>上传食品图片，智能识别高热量食品并计算总热量</p>
        </div>
        
        <div class="main-content">
            <div class="upload-section" id="uploadSection">
                <div class="upload-icon">📷</div>
                <h3>上传食品图片</h3>
                <p>支持 JPG、PNG、GIF 格式，最大 10MB</p>
                <input type="file" id="fileInput" class="file-input" accept="image/*">
                <button class="upload-btn" onclick="document.getElementById('fileInput').click()">选择图片</button>
                <p style="margin-top: 10px; color: #6c757d;">或拖拽图片到此区域</p>
            </div>
            
            <div class="image-container" id="imageContainer" style="display: none;">
                <img id="previewImage" class="preview-image" alt="预览图片">
            </div>
            
            <div class="controls">
                <button class="control-btn" id="highlightBtn" onclick="highlightHighCalorieFoods()" disabled>
                    🔴 标记高热量食品
                </button>
                <button class="control-btn" id="calculateBtn" onclick="calculateTotalCalories()" disabled>
                    🧮 计算总热量
                </button>
            </div>
            
            <div class="loading" id="loading">
                <div class="spinner"></div>
                <p>AI正在分析图片，请稍候...</p>
            </div>
            
            <div class="results" id="results"></div>
        </div>
    </div>

    <script>
        let currentImageBase64 = null;
        let currentImageUrl = null;
        
        // API配置
        const API_CONFIG = {
            url: 'https://ark.cn-beijing.volces.com/api/v3/chat/completions',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': 'Bearer a23cfa2f-9da1-46c8-8a89-604be0b32eb1'
            },
            model: 'doubao-seed-1-6-flash-250715'
        };
        
        // 文件上传处理
        document.getElementById('fileInput').addEventListener('change', handleFileSelect);
        
        // 拖拽上传
        const uploadSection = document.getElementById('uploadSection');
        uploadSection.addEventListener('dragover', handleDragOver);
        uploadSection.addEventListener('dragleave', handleDragLeave);
        uploadSection.addEventListener('drop', handleDrop);
        
        function handleDragOver(e) {
            e.preventDefault();
            uploadSection.classList.add('dragover');
        }
        
        function handleDragLeave(e) {
            e.preventDefault();
            uploadSection.classList.remove('dragover');
        }
        
        function handleDrop(e) {
            e.preventDefault();
            uploadSection.classList.remove('dragover');
            const files = e.dataTransfer.files;
            if (files.length > 0) {
                handleFile(files[0]);
            }
        }
        
        function handleFileSelect(e) {
            const file = e.target.files[0];
            if (file) {
                handleFile(file);
            }
        }
        
        function handleFile(file) {
            if (!file.type.startsWith('image/')) {
                showError('请选择有效的图片文件');
                return;
            }
            
            if (file.size > 10 * 1024 * 1024) {
                showError('图片大小不能超过10MB');
                return;
            }
            
            const reader = new FileReader();
            reader.onload = function(e) {
                currentImageBase64 = e.target.result;
                displayImage(e.target.result);
                enableControls();
            };
            reader.readAsDataURL(file);
        }
        
        function displayImage(src) {
            const imageContainer = document.getElementById('imageContainer');
            const previewImage = document.getElementById('previewImage');
            
            previewImage.src = src;
            imageContainer.style.display = 'block';
            
            // 清除之前的结果
            document.getElementById('results').innerHTML = '';
        }
        
        function enableControls() {
            document.getElementById('highlightBtn').disabled = false;
            document.getElementById('calculateBtn').disabled = false;
        }
        
        function showLoading() {
            document.getElementById('loading').style.display = 'block';
        }
        
        function hideLoading() {
            document.getElementById('loading').style.display = 'none';
        }
        
        function showError(message) {
            const results = document.getElementById('results');
            results.innerHTML = `<div class="error">❌ ${message}</div>`;
        }
        
        async function callAPI(prompt) {
            try {
                const response = await fetch(API_CONFIG.url, {
                    method: 'POST',
                    headers: API_CONFIG.headers,
                    body: JSON.stringify({
                        model: API_CONFIG.model,
                        messages: [{
                            content: [
                                {
                                    image_url: {
                                        url: currentImageBase64
                                    },
                                    type: "image_url"
                                },
                                {
                                    text: prompt,
                                    type: "text"
                                }
                            ],
                            role: "user"
                        }]
                    })
                });
                
                if (!response.ok) {
                    throw new Error(`API请求失败: ${response.status}`);
                }
                
                const data = await response.json();
                return data.choices[0].message.content;
            } catch (error) {
                console.error('API调用错误:', error);
                throw error;
            }
        }
        
        async function highlightHighCalorieFoods() {
            if (!currentImageBase64) {
                showError('请先上传图片');
                return;
            }
            
            showLoading();
            
            try {
                const prompt = `请分析这张图片中的食品，识别出所有高热量的食品（每100克超过300卡路里的食品）。请以JSON格式返回结果，包含以下信息：
{
  "high_calorie_foods": [
    {
      "name": "食品名称",
      "calories_per_100g": "每100克热量",
      "reason": "为什么是高热量食品的原因"
    }
  ],
  "low_calorie_foods": [
    {
      "name": "食品名称",
      "calories_per_100g": "每100克热量"
    }
  ]
}`;
                
                const result = await callAPI(prompt);
                displayHighCalorieResults(result);
            } catch (error) {
                showError('分析失败，请重试');
            } finally {
                hideLoading();
            }
        }
        
        async function calculateTotalCalories() {
            if (!currentImageBase64) {
                showError('请先上传图片');
                return;
            }
            
            showLoading();
            
            try {
                const prompt = `请分析这张图片中的所有食品，估算每种食品的重量和热量，然后计算总热量。请以JSON格式返回结果：
{
  "foods": [
    {
      "name": "食品名称",
      "estimated_weight": "估算重量（克）",
      "calories_per_100g": "每100克热量",
      "total_calories": "该食品总热量"
    }
  ],
  "total_calories": "所有食品的总热量",
  "analysis": "分析说明"
}`;
                
                const result = await callAPI(prompt);
                displayCalorieResults(result);
            } catch (error) {
                showError('计算失败，请重试');
            } finally {
                hideLoading();
            }
        }
        
        function displayHighCalorieResults(result) {
            try {
                const data = JSON.parse(result);
                let html = '<div class="result-card">';
                html += '<div class="result-title">🔴 高热量食品标记结果</div>';
                
                if (data.high_calorie_foods && data.high_calorie_foods.length > 0) {
                    html += '<h4 style="color: #dc3545; margin: 15px 0;">⚠️ 高热量食品：</h4>';
                    html += '<ul class="food-list">';
                    data.high_calorie_foods.forEach(food => {
                        html += `<li class="food-item high-calorie">`;
                        html += `<strong>${food.name}</strong> - ${food.calories_per_100g}卡/100克<br>`;
                        html += `<small style="color: #666;">${food.reason}</small>`;
                        html += `</li>`;
                    });
                    html += '</ul>';
                } else {
                    html += '<p style="color: #28a745;">✅ 未发现高热量食品</p>';
                }
                
                if (data.low_calorie_foods && data.low_calorie_foods.length > 0) {
                    html += '<h4 style="color: #28a745; margin: 15px 0;">✅ 低热量食品：</h4>';
                    html += '<ul class="food-list">';
                    data.low_calorie_foods.forEach(food => {
                        html += `<li class="food-item">`;
                        html += `<strong>${food.name}</strong> - ${food.calories_per_100g}卡/100克`;
                        html += `</li>`;
                    });
                    html += '</ul>';
                }
                
                html += '</div>';
                document.getElementById('results').innerHTML = html;
            } catch (error) {
                showError('结果解析失败，请重试');
            }
        }
        
        function displayCalorieResults(result) {
            try {
                const data = JSON.parse(result);
                let html = '<div class="result-card">';
                html += '<div class="result-title">🧮 热量计算结果</div>';
                
                if (data.total_calories) {
                    html += `<div class="calorie-display">${data.total_calories} 卡路里</div>`;
                }
                
                if (data.foods && data.foods.length > 0) {
                    html += '<h4 style="margin: 15px 0;">📋 食品详情：</h4>';
                    html += '<ul class="food-list">';
                    data.foods.forEach(food => {
                        html += `<li class="food-item">`;
                        html += `<strong>${food.name}</strong><br>`;
                        html += `重量：${food.estimated_weight}克 | `;
                        html += `热量密度：${food.calories_per_100g}卡/100克<br>`;
                        html += `<span style="color: #dc3545; font-weight: bold;">总热量：${food.total_calories}卡</span>`;
                        html += `</li>`;
                    });
                    html += '</ul>';
                }
                
                if (data.analysis) {
                    html += `<div style="margin-top: 20px; padding: 15px; background: #e3f2fd; border-radius: 10px;">`;
                    html += `<strong>📝 分析说明：</strong><br>${data.analysis}`;
                    html += `</div>`;
                }
                
                html += '</div>';
                document.getElementById('results').innerHTML = html;
            } catch (error) {
                showError('结果解析失败，请重试');
            }
        }
    </script>
</body>
</html>
