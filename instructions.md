🔌 LoLOS App API — Документация для разработчиков
 
Привет, креатор! 🎨 Ты создаёшь своё приложение для LoLAllApps, и оно запускается внутри LoLOS в изолированном окне (iframe). Чтобы твоё приложение могло сохранять данные (прогресс, настройки, статистику), показывать уведомления, получать информацию о пользователе и закрываться — есть специальный API.
 
Всё просто: одна глобальная переменная window.LoLOS — и куча возможностей. 🚀 
 
📦 Что можно делать
Функция Что делает Пример
💾 LoLOS.appData.save(data) Сохранить данные приложения Прогресс, настройки, статистика
📥 LoLOS.appData.load() Загрузить сохранённые данные Восстановить прогресс при запуске
🗑 LoLOS.appData.clear() Удалить все сохранённые данные Кнопка «Сбросить прогресс»
💬 LoLOS.showToast(text) Показать уведомление внизу экрана «Сохранено!»
👤 LoLOS.getUser() Получить информацию о юзере Ключ, ник, имя
🎨 LoLOS.getTheme() Узнать тему (тёмная/светлая) Адаптация цветов приложения
❌ LoLOS.close() Закрыть приложение Кнопка «Выход»
🚀 LoLOS.openApp(appId) Открыть другое приложение из LoLOS Переход в «Заметки»

 
Все функции асинхронные — возвращают Promise. Значит можно использовать await. ✨ 
 
💾 ХРАНИЛИЩЕ ДАННЫХ
 
📌 Что это
 
У каждого приложения есть личное хранилище — как «сохранёнка» в игре. 🎮 Туда можно положить любые данные:
• Прогресс игры 🎯
• Настройки 🎛
• Статистику 📊
• Пользовательские файлы 📁
• Черновики 📝 
Лимит — 500 МБ. Хватит с головой. 😎
 
Хранилище — только твоё. Другие приложения не могут туда залезть. 🔒
 
🎯 Куда попадают данные
1. Ты вызываешь LoLOS.appData.save({...})
2. LoLOS сохраняет это в IndexedDB — локальное хранилище браузера
3. Если юзер удалит приложение и выберет «💾 Сохранить данные» — данные останутся
4. При переустановке — данные восстановятся через LoLOS.appData.load() 
Юзер видит это в LoLAllApps → 💾 Хранилище. Там он может удалить данные вручную. 
 
💾 LoLOS.appData.save(data)
 
Что: сохранить данные приложения.
 
Параметры:
• data — любой объект (число, строка, массив, объект) 
Возвращает: Promise<boolean> — true если успешно
 
Пример:
// 💾 Сохранить прогресс игры
await LoLOS.appData.save({
    level: 8,
    score: 1500,
    achievements: ['first_win', 'combo_master'],
    settings: {
        sound: true,
        difficulty: 'normal'
    },
    lastPlayed: Date.now()
});

LoLOS.showToast('✅ Прогресс сохранён!');
 
Можно сохранить что угодно:
// 📝 Массив
await LoLOS.appData.save(['яблоко', 'банан', 'вишня']);

// 🔢 Просто число
await LoLOS.appData.save(42);

// 📦 Вложенный объект
await LoLOS.appData.save({
    user: { name: 'Герой', hp: 100 },
    inventory: [
        { item: 'меч', damage: 15 },
        { item: 'щит', defense: 10 }
    ]
});
 
Совет: сохраняй после важных событий, а не каждый кадр. Иначе браузер загрузится. 😅 
 
📥 LoLOS.appData.load()
 
Что: загрузить сохранённые данные.
 
Возвращает: Promise<data | null> — что сохранил, или null если ничего нет
 
Пример:
// 📥 Загрузить при запуске
const data = await LoLOS.appData.load();

if (data) {
    console.log('🎮 Добро пожаловать обратно!');
    console.log('Уровень:', data.level);
    console.log('Очки:', data.score);
    // ... восстановить состояние игры
} else {
    console.log('👋 Первый запуск!');
    // ... начать с нуля
}
 
С паттерном «первый запуск»:
const saved = await LoLOS.appData.load();

const state = saved || {
    level: 1,
    score: 0,
    achievements: []
};

// Работаем с state...
 
 
🗑 LoLOS.appData.clear()
 
Что: удалить все сохранённые данные приложения.
 
Возвращает: Promise<boolean>
 
Пример:
// 🗑 Кнопка «Сбросить прогресс»
if (confirm('🗑 Сбросить весь прогресс?')) {
    await LoLOS.appData.clear();
    LoLOS.showToast('🔄 Прогресс сброшен');
    location.reload();
}
 
⚠️ Осторожно! Это безвозвратно. Данные нельзя восстановить. 
 
💬 УВЕДОМЛЕНИЯ
 
💬 LoLOS.showToast(text)
 
Что: показать всплывающее уведомление внизу экрана (в стиле LoLOS).
 
Параметры:
• text — строка с текстом 
Возвращает: Promise<boolean>
 
Пример:
LoLOS.showToast('💾 Сохранено!');
LoLOS.showToast('🎉 Новый рекорд: 5000!');
LoLOS.showToast('⚠️ Мало здоровья');
LoLOS.showToast('🎁 Получен предмет: Меч героя');
 
Полезно для:
• ✅ Обратной связи после действий
• ⚠️ Предупреждений
• 🎁 Уведомлений о наградах
• 📢 Подсказок игроку 
Уведомление показывается 2 секунды и исчезает. 
 
👤 ИНФОРМАЦИЯ О ЮЗЕРЕ
 
👤 LoLOS.getUser()
 
Что: получить данные о текущем юзере LoLOS.
 
Возвращает: Promise<Object>:
{
    key: "XXXX-XXXX-XXXX-XXXX",   // 🔑 ключ юзера (для идентификации)
    name: "Пользователь",          // 📛 имя
    nickname: "СуперИгрок"         // ✨ ник из LoL Cloud (может быть null)
}
 
Пример:
const user = await LoLOS.getUser();
console.log('👤 Игрок:', user.nickname || user.name);
console.log('🔑 ID:', user.key);

// Персональное приветствие
document.getElementById('greeting').textContent =
    `Привет, ${user.nickname || user.name}! 👋`;
 
Используй для:
• 👋 Персональных приветствий
• 🏆 Таблиц лидеров (сохранять user.key)
• 💬 Чатов между юзерами (по ключу)
• 🎁 Личных наград 
⚠️ Не показывай ключ юзера в интерфейсе — он секретный. 
 
🎨 ТЕМА
 
🎨 LoLOS.getTheme()
 
Что: узнать текущую тему LoLOS.
 
Возвращает: Promise<string> — "dark" или "light"
 
Пример:
const theme = await LoLOS.getTheme();

if (theme === 'light') {
    document.body.style.background = '#f0f0f0';
    document.body.style.color = '#333';
} else {
    document.body.style.background = '#1a1a3e';
    document.body.style.color = '#fff';
}
 
Зачем: чтобы твоё приложение выглядело уместно — светлое на светлой теме, тёмное на тёмной. 😎 
 
❌ ЗАКРЫТИЕ
 
❌ LoLOS.close()
 
Что: закрыть приложение и вернуться на рабочий стол LoLOS.
 
Возвращает: Promise<boolean>
 
Пример:
// Кнопка выхода
document.getElementById('exitBtn').onclick = () => {
    LoLOS.close();
};
 
Когда использовать:
• 🔙 Своя кнопка «Назад»
• ✅ После завершения важного действия
• 🚪 Кнопка «Выход» в меню 
Есть альтернатива: юзер всегда может нажать ✕ в правом верхнем углу — это работает автоматически. 
 
🚀 ОТКРЫТИЕ ДРУГИХ ПРИЛОЖЕНИЙ
 
🚀 LoLOS.openApp(appId)
 
Что: открыть другое приложение из LoLAllApps.
 
Параметры:
• appId — ID приложения (не название!) 
Возвращает: Promise<boolean>
 
Пример:
// Открыть другое приложение (по ID)
await LoLOS.openApp('ABCDEF123456');
 
⚠️ Проблема: ты не знаешь ID других приложений заранее. Эта функция — для будущего, когда появятся «связки» между приложениями.
 
Пока что — используй только для своих приложений, если знаешь ID. 
 
🎯 ПОЛНЫЙ ПРИМЕР — ИГРА «ЗМЕЙКА»
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Змейка</title>
    <style>
        body {
            margin: 0;
            font-family: sans-serif;
            background: #1a1a3e;
            color: white;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
        }
        #score { font-size: 2rem; margin: 20px; }
        canvas { border: 2px solid #7b5ea7; border-radius: 12px; }
        button {
            margin: 20px;
            padding: 12px 24px;
            font-size: 1rem;
            border-radius: 12px;
            border: none;
            background: #7b5ea7;
            color: white;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <h1>🐍 Змейка</h1>
    <div id="score">Очки: 0</div>
    <canvas id="game" width="400" height="400"></canvas>
    <button id="reset">🔄 Сбросить прогресс</button>

    <script>
    // 🎮 Состояние игры
    let state = {
        score: 0,
        highScore: 0,
        games: 0
    };

    // 🎨 Применяем тему LoLOS
    LoLOS.getTheme().then(theme => {
        if (theme === 'light') {
            document.body.style.background = '#f0f0f0';
            document.body.style.color = '#333';
        }
    });

    // 👋 Персональное приветствие
    LoLOS.getUser().then(user => {
        document.querySelector('h1').textContent =
            `🐍 Змейка для ${user.nickname || user.name}`;
    });

    // 📥 Загрузка прогресса при старте
    (async () => {
        const saved = await LoLOS.appData.load();
        if (saved) {
            state = { ...state, ...saved };
            document.getElementById('score').textContent =
                `Очки: ${state.score} · Рекорд: ${state.highScore} 🏆`;
            LoLOS.showToast(`🎮 С возвращением! Рекорд: ${state.highScore}`);
        } else {
            LoLOS.showToast('👋 Первая игра!');
        }
    })();

    // ... логика игры ...

    function gameOver() {
        state.games++;
        if (state.score > state.highScore) {
            state.highScore = state.score;
            LoLOS.showToast('🏆 Новый рекорд!');
        }
        // 💾 Сохраняем прогресс
        LoLOS.appData.save(state);
    }

    // 🗑 Кнопка сброса
    document.getElementById('reset').onclick = async () => {
        if (!confirm('🗑 Сбросить весь прогресс?')) return;
        await LoLOS.appData.clear();
        state = { score: 0, highScore: 0, games: 0 };
        LoLOS.showToast('🔄 Прогресс сброшен');
        location.reload();
    };
    </script>
</body>
</html>
 
 
🎁 БОНУС — рецепты
 
🏆 Таблица рекордов (внутри приложения)
// Сохранить список рекордов
const records = [
    { name: 'Игрок 1', score: 5000 },
    { name: 'Игрок 2', score: 4200 }
];
await LoLOS.appData.save({ records });
 
💾 Автосохранение каждые 30 секунд
setInterval(async () => {
    await LoLOS.appData.save(state);
    console.log('💾 Автосохранение');
}, 30000);
 
🎯 Синхронизация с юзером
const user = await LoLOS.getUser();
const saved = await LoLOS.appData.load();

// Данные привязаны к юзеру
if (saved && saved.userKey === user.key) {
    // это тот же юзер
    restoreState(saved);
}
 
🎨 Адаптация под тему
const theme = await LoLOS.getTheme();
const styles = theme === 'dark'
    ? { bg: '#1a1a3e', text: '#fff' }
    : { bg: '#f0f0f0', text: '#333' };

document.body.style.background = styles.bg;
document.body.style.color = styles.text;
 
 
⚠️ ВАЖНОЕ ПРО ПРАВИЛА
 
Твоё приложение работает в песочнице (iframe). Значит:
 
❌ Нельзя:
• Обращаться к parent.document — заблокировано
• Читать localStorage родителя — заблокировано
• Использовать document.cookie — заблокировано
• Загружать внешние <script src="..."> — не рекомендуется
• Обращаться к Firestore напрямую — используй API 
✅ Можно:
• Использовать API LoLOS.* 🎯
• Работать с <canvas>, видео, аудио 🎨
• Использовать localStorage внутри своего iframe 💾
• Загружать картинки через <img> 🖼 
Если в коде найдут eval(), Function(), parent.document — приложение не пройдёт модерацию. 🚫 
 
📏 РЕКОМЕНДАЦИИ
 
💾 Размер данных
• Сохраняй разумно — не пихай 100 МБ картинок в appData
• Используй JSON-сериализуемые объекты — числа, строки, массивы, объекты
• Нельзя сохранять: функции, DOM-элементы, классы, File 
⚡ Скорость
• Сохраняй после важных событий (конец уровня, победа)
• Не сохраняй в цикле анимации (60 раз в секунду — плохо)
• Загружай один раз при старте, потом держи в переменной 
🎨 Внешний вид
• Следуй теме — светлая/тёмная через getTheme()
• Используй эмодзи — приложение с ними выглядит дружелюбнее 😊
• Не делай белый фон на тёмной теме — будет резко 
 
🚨 ЧАСТЫЕ ОШИБКИ
 
❌ «LoLOS is not defined»
 
Причина: ты открыл приложение вне LoLOS (например, напрямую в браузере).
 
Решение: приложение работает только внутри LoLOS через LoLAllApps. 📱
 
❌ «parent.postMessage is not allowed»
 
Причина: ты используешь запрещённые API — parent.document, localStorage.parent.
 
Решение: используй только LoLOS.*. 🔒
 
❌ Данные не сохраняются
 
Причина: ты забыл await:
// ❌ Неправильно
LoLOS.appData.save(data);

// ✅ Правильно
await LoLOS.appData.save(data);
 
❌ Приложение не проходит модерацию
 
Причина: в коде есть eval(), Function(), <script src="..."> или parent.document.
 
Решение: удали запрещённые паттерны. 🚫 
 
🎉 ВСЁ
 
Ты готов создавать приложения! 🚀
