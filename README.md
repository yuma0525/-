<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>動画の尺を計算する - カラフル版</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Helvetica Neue', Arial, 'Hiragino Kaku Gothic ProN', sans-serif;
            background: linear-gradient(45deg, #ff6b6b, #4ecdc4, #45b7d1, #f9ca24, #f0932b, #eb4d4b, #6c5ce7);
            background-size: 400% 400%;
            animation: gradientShift 15s ease infinite;
            min-height: 100vh;
            padding: 20px;
        }

        @keyframes gradientShift {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }

        .container {
            max-width: 900px;
            margin: 0 auto;
            background: rgba(255, 255, 255, 0.95);
            border-radius: 25px;
            overflow: hidden;
            box-shadow: 0 25px 50px rgba(0, 0, 0, 0.15);
            backdrop-filter: blur(10px);
        }

        .header {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 30px;
            text-align: center;
            position: relative;
            overflow: hidden;
        }

        .header::before {
            content: '';
            position: absolute;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            background: repeating-linear-gradient(
                45deg,
                transparent,
                transparent 10px,
                rgba(255, 255, 255, 0.1) 10px,
                rgba(255, 255, 255, 0.1) 20px
            );
            animation: headerPattern 20s linear infinite;
        }

        @keyframes headerPattern {
            0% { transform: translate(-50%, -50%) rotate(0deg); }
            100% { transform: translate(-50%, -50%) rotate(360deg); }
        }

        .header h1 {
            font-size: 2.5em;
            font-weight: 700;
            position: relative;
            z-index: 1;
            text-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
        }

        .input-section {
            padding: 30px;
            background: linear-gradient(135deg, #ff9a9e 0%, #fecfef 50%, #fecfef 100%);
        }

        .input-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 20px;
            margin-bottom: 25px;
        }

        .input-card {
            background: white;
            border-radius: 15px;
            padding: 20px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
            border: 3px solid transparent;
            background-clip: padding-box;
            transition: all 0.3s ease;
        }

        .input-card:nth-child(1) {
            border-image: linear-gradient(45deg, #ff6b6b, #4ecdc4) 1;
        }

        .input-card:nth-child(2) {
            border-image: linear-gradient(45deg, #45b7d1, #f9ca24) 1;
        }

        .input-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 15px 40px rgba(0, 0, 0, 0.2);
        }

        .input-card label {
            display: block;
            font-weight: 700;
            margin-bottom: 10px;
            font-size: 1.1em;
            background: linear-gradient(45deg, #667eea, #764ba2);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }

        .input-card input {
            width: 100%;
            padding: 15px;
            border: 2px solid #e1e8ff;
            border-radius: 10px;
            font-size: 16px;
            transition: all 0.3s ease;
            background: rgba(255, 255, 255, 0.9);
        }

        .input-card:nth-child(1) input:focus {
            border-color: #ff6b6b;
            box-shadow: 0 0 0 3px rgba(255, 107, 107, 0.2);
            outline: none;
        }

        .input-card:nth-child(2) input:focus {
            border-color: #4ecdc4;
            box-shadow: 0 0 0 3px rgba(78, 205, 196, 0.2);
            outline: none;
        }

        .hint-card {
            background: linear-gradient(135deg, #a8edea 0%, #fed6e3 100%);
            border-radius: 15px;
            padding: 20px;
            margin: 20px 0;
            border-left: 5px solid #45b7d1;
            animation: pulse 2s ease-in-out infinite alternate;
        }

        @keyframes pulse {
            from { transform: scale(1); }
            to { transform: scale(1.02); }
        }

        .button-container {
            display: flex;
            gap: 15px;
            justify-content: center;
        }

        .btn {
            padding: 15px 30px;
            border: none;
            border-radius: 50px;
            font-size: 16px;
            font-weight: 700;
            cursor: pointer;
            transition: all 0.3s ease;
            text-transform: uppercase;
            letter-spacing: 1px;
            position: relative;
            overflow: hidden;
        }

        .btn-calculate {
            background: linear-gradient(45deg, #ff6b6b, #4ecdc4);
            color: white;
            box-shadow: 0 10px 30px rgba(255, 107, 107, 0.3);
        }

        .btn-calculate:hover {
            transform: translateY(-3px);
            box-shadow: 0 15px 40px rgba(255, 107, 107, 0.4);
        }

        .btn-reset {
            background: linear-gradient(45deg, #45b7d1, #f9ca24);
            color: white;
            box-shadow: 0 10px 30px rgba(69, 183, 209, 0.3);
        }

        .btn-reset:hover {
            transform: translateY(-3px);
            box-shadow: 0 15px 40px rgba(69, 183, 209, 0.4);
        }

        .results-section {
            padding: 30px;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
        }

        .results-title {
            text-align: center;
            font-size: 1.8em;
            font-weight: 700;
            margin-bottom: 25px;
            text-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
        }

        .results-table {
            width: 100%;
            border-collapse: collapse;
            background: rgba(255, 255, 255, 0.95);
            border-radius: 15px;
            overflow: hidden;
            box-shadow: 0 15px 35px rgba(0, 0, 0, 0.1);
            margin-bottom: 30px;
        }

        .results-table th {
            background: linear-gradient(45deg, #ff6b6b, #4ecdc4);
            color: white;
            padding: 15px 10px;
            text-align: center;
            font-weight: 700;
            font-size: 14px;
        }

        .results-table td {
            padding: 12px 10px;
            text-align: center;
            border-bottom: 1px solid #f0f0f0;
            color: #333;
            font-weight: 500;
        }

        .corner-cell {
            font-weight: 700;
            font-size: 16px;
        }

        .corner-1 { color: #ff6b6b; }
        .corner-2 { color: #4ecdc4; }
        .corner-3 { color: #45b7d1; }
        .corner-4 { color: #f9ca24; }
        .corner-5 { color: #f0932b; }
        .corner-6 { color: #eb4d4b; }
        .corner-7 { color: #6c5ce7; }
        .corner-8 { color: #a29bfe; }

        .chart-container {
            background: rgba(255, 255, 255, 0.95);
            border-radius: 15px;
            padding: 25px;
            box-shadow: 0 15px 35px rgba(0, 0, 0, 0.1);
        }

        .chart-title {
            text-align: center;
            font-size: 1.4em;
            font-weight: 700;
            margin-bottom: 20px;
            color: #333;
        }

        .chart-bars {
            display: flex;
            align-items: end;
            height: 200px;
            gap: 10px;
            padding: 20px 0;
            justify-content: center;
        }

        .chart-bar {
            display: flex;
            flex-direction: column;
            justify-content: end;
            align-items: center;
            color: white;
            font-weight: bold;
            padding: 10px 8px;
            border-radius: 10px 10px 0 0;
            min-width: 60px;
            flex: 1;
            max-width: 80px;
            transition: all 0.3s ease;
            position: relative;
            overflow: hidden;
        }

        .chart-bar:hover {
            transform: scale(1.05);
            z-index: 10;
        }

        .chart-bar::before {
            content: '';
            position: absolute;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            background: linear-gradient(45deg, rgba(255,255,255,0.1), transparent, rgba(255,255,255,0.1));
            animation: shine 3s ease-in-out infinite;
        }

        @keyframes shine {
            0% { transform: translateX(-100%); }
            100% { transform: translateX(100%); }
        }

        .bar-1 { background: linear-gradient(to top, #ff6b6b, #ff8787); }
        .bar-2 { background: linear-gradient(to top, #4ecdc4, #6bcf7f); }
        .bar-3 { background: linear-gradient(to top, #45b7d1, #96c93f); }
        .bar-4 { background: linear-gradient(to top, #f9ca24, #f0932b); }
        .bar-5 { background: linear-gradient(to top, #f0932b, #eb4d4b); }
        .bar-6 { background: linear-gradient(to top, #eb4d4b, #6c5ce7); }
        .bar-7 { background: linear-gradient(to top, #6c5ce7, #a29bfe); }
        .bar-8 { background: linear-gradient(to top, #a29bfe, #fd79a8); }

        .error-message {
            background: linear-gradient(135deg, #ff6b6b, #ff8787);
            color: white;
            padding: 20px;
            border-radius: 15px;
            margin: 20px 30px;
            text-align: center;
            font-weight: 600;
            box-shadow: 0 10px 30px rgba(255, 107, 107, 0.3);
        }

        @media (max-width: 768px) {
            .input-grid {
                grid-template-columns: 1fr;
            }
            
            .button-container {
                flex-direction: column;
            }
            
            .header h1 {
                font-size: 2em;
            }
            
            .chart-bars {
                height: 150px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🎬 動画の尺を計算する 🌈</h1>
        </div>

        <div class="input-section">
            <div class="input-grid">
                <div class="input-card">
                    <label for="totalDuration">📺 総尺（秒）</label>
                    <input type="number" id="totalDuration" placeholder="例：3000" min="1">
                </div>
                
                <div class="input-card">
                    <label for="endTimes">⏰ 終了タイム（HHMM形式）</label>
                    <input type="text" id="endTimes" placeholder="例：0800,1200,1800">
                </div>
            </div>
            
            <div class="hint-card">
                <strong>🎯 使い方のヒント:</strong><br>
                • 総尺は秒数で入力してください（例：50分 = 3000秒）<br>
                • 終了タイムは24時間形式で、カンマで区切って入力（例：08:00 → 0800）<br>
                • 複数のコーナーがある場合は「0800,1200,1800」のように入力
            </div>
            
            <div class="button-container">
                <button class="btn btn-calculate" onclick="calculateDuration()">🚀 計算する</button>
                <button class="btn btn-reset" onclick="resetForm()">🔄 リセット</button>
            </div>
        </div>

        <div id="error-container"></div>

        <div id="results" style="display: none;">
            <div class="results-section">
                <div class="results-title">📊 計算結果</div>
                
                <table class="results-table">
                    <thead>
                        <tr>
                            <th>🎯 コーナー</th>
                            <th>⏰ 終了タイム</th>
                            <th>📏 尺（秒）</th>
                            <th>🕐 時間表示</th>
                            <th>📊 割合</th>
                        </tr>
                    </thead>
                    <tbody id="results-tbody">
                    </tbody>
                </table>
                
                <div class="chart-container">
                    <div class="chart-title">🌈 コーナー別尺の視覚化</div>
                    <div id="chart-bars" class="chart-bars"></div>
                </div>
            </div>
        </div>
    </div>

    <script>
        const colors = [
            'corner-1', 'corner-2', 'corner-3', 'corner-4',
            'corner-5', 'corner-6', 'corner-7', 'corner-8'
        ];
        
        const barColors = [
            'bar-1', 'bar-2', 'bar-3', 'bar-4',
            'bar-5', 'bar-6', 'bar-7', 'bar-8'
        ];

        function calculateDuration() {
            const totalDuration = parseInt(document.getElementById('totalDuration').value);
            const endTimesStr = document.getElementById('endTimes').value;
            const errorContainer = document.getElementById('error-container');
            
            // エラーメッセージをクリア
            errorContainer.innerHTML = '';
            
            // 入力検証
            if (!totalDuration || totalDuration <= 0) {
                showError('🚨 総尺を正しく入力してください（1以上の数字）');
                return;
            }
            
            if (!endTimesStr.trim()) {
                showError('🚨 終了タイムを入力してください');
                return;
            }
            
            // 終了タイムの解析
            const endTimes = endTimesStr.split(',').map(time => time.trim());
            
            // 形式チェック
            for (let time of endTimes) {
                if (!/^\d{4}$/.test(time)) {
                    showError(`🚨 終了タイム "${time}" の形式が正しくありません（HHMM形式で入力してください）`);
                    return;
                }
                
                const hours = parseInt(time.substring(0, 2));
                const minutes = parseInt(time.substring(2, 4));
                
                if (hours >= 24 || minutes >= 60) {
                    showError(`🚨 終了タイム "${time}" が無効です（24時間以内で入力してください）`);
                    return;
                }
            }
            
            // 秒に変換してソート
            const endTimesInSeconds = endTimes.map(time => {
                const hours = parseInt(time.substring(0, 2));
                const minutes = parseInt(time.substring(2, 4));
                return hours * 3600 + minutes * 60;
            }).sort((a, b) => a - b);
            
            // 各コーナーの尺を計算
            const results = [];
            let previousTime = 0;
            
            endTimesInSeconds.forEach((endTime, index) => {
                const duration = endTime - previousTime;
                results.push({
                    corner: index + 1,
                    endTime: endTimes[endTimesInSeconds.indexOf(endTime)],
                    duration: duration,
                    timeDisplay: formatTime(duration),
                    percentage: ((duration / totalDuration) * 100).toFixed(1)
                });
                previousTime = endTime;
            });
            
            // 最後のコーナー
            const lastDuration = totalDuration - previousTime;
            if (lastDuration > 0) {
                results.push({
                    corner: results.length + 1,
                    endTime: '🏁 終了',
                    duration: lastDuration,
                    timeDisplay: formatTime(lastDuration),
                    percentage: ((lastDuration / totalDuration) * 100).toFixed(1)
                });
            }
            
            displayResults(results, totalDuration);
        }
        
        function showError(message) {
            const errorContainer = document.getElementById('error-container');
            errorContainer.innerHTML = `<div class="error-message">${message}</div>`;
            document.getElementById('results').style.display = 'none';
        }
        
        function displayResults(results, totalDuration) {
            const tbody = document.getElementById('results-tbody');
            const chartBars = document.getElementById('chart-bars');
            
            // テーブル更新
            tbody.innerHTML = '';
            results.forEach((result, index) => {
                const row = tbody.insertRow();
                const cornerCell = row.insertCell(0);
                cornerCell.textContent = `コーナー${result.corner}`;
                cornerCell.className = `corner-cell ${colors[index % colors.length]}`;
                
                row.insertCell(1).textContent = result.endTime;
                row.insertCell(2).textContent = result.duration;
                row.insertCell(3).textContent = result.timeDisplay;
                row.insertCell(4).textContent = result.percentage + '%';
            });
            
            // チャート更新
            chartBars.innerHTML = '';
            const maxDuration = Math.max(...results.map(r => r.duration));
            
            results.forEach((result, index) => {
                const bar = document.createElement('div');
                bar.className = `chart-bar ${barColors[index % barColors.length]}`;
                const height = (result.duration / maxDuration) * 160;
                bar.style.height = height + 'px';
                bar.innerHTML = `
                    <div style="font-size: 12px; font-weight: bold; position: relative; z-index: 2;">
                        ${result.duration}s
                    </div>
                    <div style="font-size: 10px; position: relative; z-index: 2;">
                        C${result.corner}
                    </div>
                `;
                chartBars.appendChild(bar);
            });
            
            document.getElementById('results').style.display = 'block';
            
            // スムーズスクロール
            document.getElementById('results').scrollIntoView({
                behavior: 'smooth'
            });
        }
        
        function formatTime(seconds) {
            const hours = Math.floor(seconds / 3600);
            const minutes = Math.floor((seconds % 3600) / 60);
            const secs = seconds % 60;
            
            if (hours > 0) {
                return `${hours}時間${minutes}分${secs}秒`;
            } else if (minutes > 0) {
                return `${minutes}分${secs}秒`;
            } else {
                return `${secs}秒`;
            }
        }
        
        function resetForm() {
            document.getElementById('totalDuration').value = '';
            document.getElementById('endTimes').value = '';
            document.getElementById('error-container').innerHTML = '';
            document.getElementById('results').style.display = 'none';
        }
    </script>
</body>
</html>
