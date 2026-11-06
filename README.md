# Profitum-app<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Profitum Chisinau</title>
    <!-- Подключаем Tailwind CSS для красивого дизайна -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Подключаем библиотеку Supabase -->
    <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
    <!-- Подключаем Telegram WebApp API -->
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
</head>
<body class="bg-gray-50 text-gray-800 antialiased min-h-screen flex flex-col">

    <!-- Шапка приложения -->
    <header class="bg-white shadow-sm px-4 py-3 flex items-center justify-between">
        <div class="flex items-center space-x-2">
            <div class="w-8 h-8 bg-emerald-600 rounded-lg flex items-center justify-center text-white font-bold">💎</div>
            <div>
                <h1 class="font-bold text-sm leading-tight">PROFITUM</h1>
                <p class="text-[10px] text-gray-500">CHISINAU (CLOUD)</p>
            </div>
        </div>
        <div class="flex items-center space-x-2">
            <span class="text-xs bg-gray-100 px-2 py-1 rounded">🇷🇺 RU</span>
            <button class="bg-emerald-600 text-white text-xs px-3 py-1.5 rounded-lg font-medium">+ Создать</button>
        </div>
    </header>

    <!-- Основной контент -->
    <main class="flex-1 p-4 max-w-lg mx-auto w-full">
        <!-- Блок задач -->
        <div id="tasks-container" class="space-y-3">
            <div class="text-center py-10 text-gray-400 text-sm">Загрузка заданий...</div>
        </div>
    </main>

    <!-- Скрипт подключения и логики -->
    <script>
        // --- НАСТРОЙКА SUPABASE ---
        const SUPABASE_URL = "https://xudwyqmpucmqgjjpbgll.supabase.co";
        const SUPABASE_ANON_KEY = "Sb_publishable_dxJStXW9jVQ4XUZE_J5Umw_jNr2Vw8h";

        const supabaseClient = supabase.createClient(SUPABASE_URL, SUPABASE_ANON_KEY);

        const tg = window.Telegram?.WebApp;
        if (tg) { tg.expand(); }

        let tasksList = [];

        // Функция рендеринга задач на экране
        function renderTasks(tasks) {
            const container = document.getElementById('tasks-container');
            if (!tasks || tasks.length === 0) {
                container.innerHTML = '<div class="text-center py-10 text-gray-400 text-sm">Нет активных заданий</div>';
                return;
            }

            container.innerHTML = tasks.map(task => `
                <div class="bg-white p-4 rounded-xl shadow-sm border border-gray-100">
                    <div class="flex justify-between items-start mb-2">
                        <h3 class="font-semibold text-sm">${task.title}</h3>
                        <span class="text-emerald-600 font-bold text-sm">${task.price} MDL</span>
                    </div>
                    <p class="text-xs text-gray-500 mb-2">${task.desc || ''}</p>
                    <div class="flex justify-between items-center text-[11px] text-gray-400">
                        <span>📍 ${task.district || 'Кишинев'}</span>
                        <span>👤 ${task.client || 'Клиент'}</span>
                    </div>
                </div>
            `).join('');
        }

        // Безопасная загрузка задач из Supabase
        async function fetchTasks() {
            try {
                const { data, error } = await supabaseClient
                    .from('tasks')
                    .select('*');

                if (error) {
                    console.error("Ошибка Supabase:", error);
                    renderTasks([]);
                    return;
                }

                tasksList = data || [];
                renderTasks(tasksList);
            } catch (err) {
                console.error("Сетевая ошибка:", err);
                renderTasks([]);
            }
        }

        // Запуск загрузки при старте
        fetchTasks();
    </script>
</body>
</html>
