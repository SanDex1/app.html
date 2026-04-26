<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Админ-панель</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
            background: #f5f5f5;
            padding: 20px;
        }

        .container {
            max-width: 600px;
            margin: 0 auto;
        }

        .header {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 30px 20px;
            border-radius: 12px;
            margin-bottom: 30px;
            text-align: center;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
        }

        .header h1 {
            font-size: 28px;
            margin-bottom: 10px;
        }

        .header p {
            font-size: 14px;
            opacity: 0.9;
        }

        .card {
            background: white;
            border-radius: 12px;
            padding: 20px;
            margin-bottom: 15px;
            box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
        }

        .card h2 {
            font-size: 18px;
            margin-bottom: 10px;
            color: #333;
            display: flex;
            align-items: center;
        }

        .card h2 span {
            font-size: 24px;
            margin-right: 10px;
        }

        .stat {
            font-size: 14px;
            color: #666;
            margin: 8px 0;
            display: flex;
            justify-content: space-between;
        }

        .stat-value {
            font-weight: bold;
            color: #667eea;
        }

        .button-group {
            display: flex;
            gap: 10px;
            margin-top: 20px;
        }

        button {
            flex: 1;
            padding: 12px 20px;
            border: none;
            border-radius: 8px;
            font-size: 14px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .btn-primary {
            background: #667eea;
            color: white;
        }

        .btn-primary:active {
            transform: scale(0.98);
            opacity: 0.9;
        }

        .btn-secondary {
            background: #e0e0e0;
            color: #333;
        }

        .btn-secondary:active {
            transform: scale(0.98);
            opacity: 0.9;
        }

        .user-info {
            background: #f9f9f9;
            padding: 15px;
            border-radius: 8px;
            margin-bottom: 20px;
            border-left: 4px solid #667eea;
        }

        .user-info p {
            font-size: 13px;
            color: #666;
            margin: 5px 0;
        }

        .user-info strong {
            color: #333;
        }

        .loader {
            display: inline-block;
            width: 20px;
            height: 20px;
            border: 3px solid #f3f3f3;
            border-top: 3px solid #667eea;
            border-radius: 50%;
            animation: spin 1s linear infinite;
            margin-right: 10px;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        .message {
            padding: 12px;
            border-radius: 8px;
            margin-bottom: 15px;
            display: none;
        }

        .message.show {
            display: block;
        }

        .message.success {
            background: #d4edda;
            color: #155724;
            border: 1px solid #c3e6cb;
        }

        .message.error {
            background: #f8d7da;
            color: #721c24;
            border: 1px solid #f5c6cb;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>📊 Админ-панель</h1>
            <p>Управление приложением</p>
        </div>

        <div id="message" class="message"></div>

        <div class="user-info" id="userInfo">
            <p><strong>Пользователь:</strong> <span id="userName">Загрузка...</span></p>
            <p><strong>ID:</strong> <span id="userId">-</span></p>
        </div>

        <div class="card">
            <h2><span>👥</span>Статистика пользователей</h2>
            <div class="stat">
                <span>Всего пользователей:</span>
                <span class="stat-value" id="totalUsers">1,234</span>
            </div>
            <div class="stat">
                <span>Активных сегодня:</span>
                <span class="stat-value" id="activeToday">456</span>
            </div>
            <div class="stat">
                <span>Новых за неделю:</span>
                <span class="stat-value" id="newWeek">89</span>
            </div>
        </div>

        <div class="card">
            <h2><span>⚙️</span>Действия</h2>
            <div class="button-group">
                <button class="btn-primary" onclick="refreshData()">🔄 Обновить</button>
                <button class="btn-secondary" onclick="closeApp()">✖️ Закрыть</button>
            </div>
        </div>

        <div class="card">
            <h2><span>📝</span>Логи</h2>
            <p style="font-size: 13px; color: #999; margin: 10px 0;">
                ✓ Приложение загружено успешно<br>
                ✓ Данные пользователя получены<br>
                ✓ Готово к работе
            </p>
        </div>
    </div>

    <script>
        // Инициализируем Telegram Web App
        const tg = window.Telegram.WebApp;

        // Настраиваем внешний вид
        tg.expand();
        tg.MainButton.text = "Готово";
        tg.MainButton.show();
        tg.MainButton.onClick(() => {
            closeApp();
        });

        // Получаем данные пользователя
        function initializeApp() {
            const userData = tg.initData;
            
            if (tg.initDataUnsafe && tg.initDataUnsafe.user) {
                const user = tg.initDataUnsafe.user;
                document.getElementById('userName').textContent = 
                    user.first_name + (user.last_name ? ' ' + user.last_name : '');
                document.getElementById('userId').textContent = user.id;
            }

            // Симулируем загрузку данных
            loadStats();
        }

        // Загружаем статистику
        function loadStats() {
            // Симуляция загрузки данных
            setTimeout(() => {
                showMessage('✓ Данные загружены успешно', 'success');
            }, 500);
        }

        // Функция для отображения сообщений
        function showMessage(text, type = 'success') {
            const msgDiv = document.getElementById('message');
            msgDiv.textContent = text;
            msgDiv.className = 'message show ' + type;
            
            setTimeout(() => {
                msgDiv.classList.remove('show');
            }, 3000);
        }

        // Функция обновления данных
        function refreshData() {
            const btn = event.target;
            btn.disabled = true;
            btn.innerHTML = '<span class="loader"></span>Обновляю...';
            
            setTimeout(() => {
                btn.disabled = false;
                btn.innerHTML = '🔄 Обновить';
                showMessage('✓ Данные обновлены', 'success');
            }, 1000);
        }

        // Функция закрытия приложения
        function closeApp() {
            tg.close();
        }

        // Инициализируем приложение при загрузке
        window.addEventListener('load', initializeApp);

        // Обработчик кнопки назад (Android)
        tg.onEvent('backButtonClicked', closeApp);
    </script>
</body>
</html>
