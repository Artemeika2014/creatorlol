СМОТРИТЕ CODE
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
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
<title>Змейка</title>
<style>
* { margin: 0; padding: 0; box-sizing: border-box; }
body {
    font-family: 'Segoe UI', system-ui, sans-serif;
    background: #0a0a1a;
    color: #fff;
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    overflow: hidden;
    -webkit-tap-highlight-color: transparent;
    user-select: none;
}
.game-wrapper {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 16px;
    padding: 20px;
    width: 100%;
    max-width: 500px;
}
.header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    width: 100%;
    padding: 12px 16px;
    background: rgba(255,255,255,0.05);
    border-radius: 16px;
    border: 1px solid rgba(255,255,255,0.1);
}
.score-box {
    text-align: center;
}
.score-box .label {
    font-size: 0.65rem;
    opacity: 0.5;
    text-transform: uppercase;
    letter-spacing: 1px;
}
.score-box .value {
    font-size: 1.4rem;
    font-weight: 700;
    background: linear-gradient(135deg, #4ade80, #22c55e);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
}
.score-box .value.high {
    background: linear-gradient(135deg, #ffbd2e, #ff9500);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
}
canvas {
    border-radius: 20px;
    background: #0f0f24;
    box-shadow: 0 0 60px rgba(74, 222, 128, 0.15), inset 0 0 60px rgba(74, 222, 128, 0.05);
    border: 1px solid rgba(74, 222, 128, 0.2);
    display: block;
    max-width: 100%;
    height: auto;
    touch-action: none;
}
.controls {
    display: flex;
    gap: 10px;
    width: 100%;
}
.controls button {
    flex: 1;
    padding: 14px;
    border-radius: 14px;
    border: none;
    font-size: 0.95rem;
    font-weight: 600;
    cursor: pointer;
    font-family: inherit;
    transition: all 0.2s;
    background: rgba(74, 222, 128, 0.15);
    color: #4ade80;
    border: 1px solid rgba(74, 222, 128, 0.3);
}
.controls button:hover {
    background: rgba(74, 222, 128, 0.25);
    transform: translateY(-2px);
}
.controls button:active {
    transform: scale(0.96);
}
.controls button.primary {
    background: linear-gradient(135deg, #4ade80, #22c55e);
    color: #0a0a1a;
    border: none;
}
.controls button.primary:hover {
    box-shadow: 0 8px 25px rgba(74, 222, 128, 0.4);
}
.controls button.danger {
    background: rgba(255, 59, 48, 0.15);
    color: #ff6b6b;
    border-color: rgba(255, 59, 48, 0.3);
}
.overlay {
    position: fixed;
    inset: 0;
    background: rgba(10, 10, 26, 0.9);
    backdrop-filter: blur(10px);
    display: none;
    align-items: center;
    justify-content: center;
    padding: 20px;
    z-index: 100;
}
.overlay.active { display: flex; }
.overlay-box {
    background: linear-gradient(135deg, #1a1a3e, #0f0f24);
    border: 1px solid rgba(74, 222, 128, 0.3);
    border-radius: 24px;
    padding: 32px 24px;
    max-width: 360px;
    width: 100%;
    text-align: center;
    box-shadow: 0 0 60px rgba(74, 222, 128, 0.2);
}
.overlay-box .icon {
    font-size: 4rem;
    margin-bottom: 12px;
    line-height: 1;
}
.overlay-box h2 {
    font-size: 1.4rem;
    margin-bottom: 8px;
    background: linear-gradient(135deg, #4ade80, #22c55e);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
}
.overlay-box .sub {
    font-size: 0.9rem;
    opacity: 0.6;
    margin-bottom: 20px;
    line-height: 1.5;
}
.overlay-box .big-score {
    font-size: 3rem;
    font-weight: 800;
    background: linear-gradient(135deg, #ffbd2e, #ff9500);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    margin: 12px 0;
}
.overlay-box .label-small {
    font-size: 0.75rem;
    opacity: 0.5;
    text-transform: uppercase;
    letter-spacing: 2px;
}
.overlay-box button {
    width: 100%;
    padding: 16px;
    border-radius: 14px;
    border: none;
    font-size: 1rem;
    font-weight: 700;
    cursor: pointer;
    font-family: inherit;
    margin-top: 8px;
    background: linear-gradient(135deg, #4ade80, #22c55e);
    color: #0a0a1a;
    transition: all 0.2s;
}
.overlay-box button:hover {
    transform: translateY(-2px);
    box-shadow: 0 10px 30px rgba(74, 222, 128, 0.4);
}
.overlay-box button.secondary {
    background: rgba(255,255,255,0.08);
    color: #fff;
    border: 1px solid rgba(255,255,255,0.15);
}
.new-record {
    color: #ffbd2e;
    font-weight: 700;
    font-size: 1rem;
    margin-bottom: 8px;
    animation: pulse 1s infinite;
}
@keyframes pulse {
    0%, 100% { opacity: 1; transform: scale(1); }
    50% { opacity: 0.7; transform: scale(1.05); }
}
.control-hint {
    font-size: 0.75rem;
    opacity: 0.4;
    text-align: center;
}
</style>
</head>
<body>

<div class="game-wrapper">
    <div class="header">
        <div class="score-box">
            <div class="label">Счёт</div>
            <div class="value" id="currentScore">0</div>
        </div>
        <div style="font-size: 1.6rem;">🐍</div>
        <div class="score-box">
            <div class="label">Рекорд</div>
            <div class="value high" id="highScore">0</div>
        </div>
    </div>

    <canvas id="gameCanvas"></canvas>

    <div class="controls">
        <button class="primary" id="startBtn" onclick="startGame()">▶ Играть</button>
        <button class="danger" onclick="resetRecord()">🗑 Рекорд</button>
    </div>

    <div class="control-hint">Управление: стрелки на клавиатуре или свайпы</div>
</div>

<div class="overlay" id="gameOverOverlay">
    <div class="overlay-box">
        <div class="icon">💀</div>
        <h2>Игра окончена</h2>
        <div class="new-record" id="newRecordText" style="display:none;">🏆 Новый рекорд!</div>
        <div class="label-small">Ваш счёт</div>
        <div class="big-score" id="finalScore">0</div>
        <div class="label-small">Рекорд</div>
        <div class="big-score" style="font-size: 1.6rem; opacity: 0.6;" id="finalHighScore">0</div>
        <button onclick="startGame()">🔄 Играть снова</button>
        <button class="secondary" onclick="closeOverlay()">✕ Закрыть</button>
    </div>
</div>

<script>
// ============================================================
// ЗМЕЙКА — тестовое приложение для LoLAllApps
// ============================================================

const CANVAS_SIZE = 400;         // размер canvas в пикселях
const GRID = 20;                  // 20x20 клеток
const CELL = CANVAS_SIZE / GRID; // размер клетки

let canvas, ctx;
let snake = [];
let direction = { x: 1, y: 0 };
let nextDirection = { x: 1, y: 0 };
let food = { x: 0, y: 0 };
let score = 0;
let highScore = 0;
let gameRunning = false;
let gameLoopInterval = null;
let speed = 120;                  // мс между кадрами

// ============================================================
// ИНИЦИАЛИЗАЦИЯ
// ============================================================
async function init() {
    canvas = document.getElementById('gameCanvas');
    canvas.width = CANVAS_SIZE;
    canvas.height = CANVAS_SIZE;
    ctx = canvas.getContext('2d');

    // Загружаем рекорд через LoLOS API
    await loadHighScore();

    drawInitialState();

    // Управление клавиатурой
    document.addEventListener('keydown', handleKeyDown);

    // Управление свайпами
    let touchStartX = 0, touchStartY = 0;
    canvas.addEventListener('touchstart', e => {
        touchStartX = e.touches[0].clientX;
        touchStartY = e.touches[0].clientY;
    }, { passive: true });
    canvas.addEventListener('touchend', e => {
        const dx = e.changedTouches[0].clientX - touchStartX;
        const dy = e.changedTouches[0].clientY - touchStartY;
        if (Math.abs(dx) < 30 && Math.abs(dy) < 30) return;
        if (Math.abs(dx) > Math.abs(dy)) {
            if (dx > 0) setDirection(1, 0);
            else setDirection(-1, 0);
        } else {
            if (dy > 0) setDirection(0, 1);
            else setDirection(0, -1);
        }
    }, { passive: true });

    // Приветствие
    if (window.LoLOS) {
        try {
            const user = await LoLOS.getUser();
            if (user && user.nickname) {
                LoLOS.showToast('Привет, ' + user.nickname + '! 🐍');
            }
        } catch (e) {}
    }
}

// ============================================================
// РЕКОРД ЧЕРЕЗ API
// ============================================================
async function loadHighScore() {
    if (!window.LoLOS || !LoLOS.appData) {
        highScore = parseInt(localStorage.getItem('snake_high') || '0');
        return;
    }
    try {
        const data = await LoLOS.appData.load();
        if (data && data.highScore) {
            highScore = data.highScore;
        }
    } catch (e) {
        highScore = parseInt(localStorage.getItem('snake_high') || '0');
    }
    document.getElementById('highScore').textContent = highScore;
}

async function saveHighScore(newScore) {
    if (!window.LoLOS || !LoLOS.appData) {
        localStorage.setItem('snake_high', String(newScore));
        return;
    }
    try {
        const data = await LoLOS.appData.load() || {};
        await LoLOS.appData.save({
            highScore: newScore,
            games: (data.games || 0) + 1,
            lastPlayed: Date.now()
        });
    } catch (e) {
        localStorage.setItem('snake_high', String(newScore));
    }
}

async function resetRecord() {
    if (!confirm('Сбросить рекорд?')) return;
    highScore = 0;
    document.getElementById('highScore').textContent = '0';
    if (window.LoLOS && LoLOS.appData) {
        try {
            await LoLOS.appData.save({ highScore: 0, games: 0 });
            LoLOS.showToast('🗑 Рекорд сброшен');
        } catch (e) {}
    } else {
        localStorage.removeItem('snake_high');
    }
}

// ============================================================
// ИГРОВАЯ ЛОГИКА
// ============================================================
function startGame() {
    closeOverlay();

    snake = [
        { x: 10, y: 10 },
        { x: 9, y: 10 },
        { x: 8, y: 10 }
    ];
    direction = { x: 1, y: 0 };
    nextDirection = { x: 1, y: 0 };
    score = 0;
    gameRunning = true;

    document.getElementById('currentScore').textContent = '0';
    document.getElementById('startBtn').style.display = 'none';

    spawnFood();
    draw();

    if (gameLoopInterval) clearInterval(gameLoopInterval);
    gameLoopInterval = setInterval(gameLoop, speed);
}

function gameLoop() {
    if (!gameRunning) return;

    direction = { ...nextDirection };

    const head = { x: snake[0].x + direction.x, y: snake[0].y + direction.y };

    // Проверка столкновений
    if (head.x < 0 || head.x >= GRID || head.y < 0 || head.y >= GRID) {
        endGame();
        return;
    }
    for (let i = 0; i < snake.length; i++) {
        if (snake[i].x === head.x && snake[i].y === head.y) {
            endGame();
            return;
        }
    }

    snake.unshift(head);

    // Съели еду?
    if (head.x === food.x && head.y === food.y) {
        score++;
        document.getElementById('currentScore').textContent = score;
        spawnFood();
    } else {
        snake.pop();
    }

    draw();
}

function spawnFood() {
    let attempts = 0;
    while (attempts < 100) {
        const x = Math.floor(Math.random() * GRID);
        const y = Math.floor(Math.random() * GRID);
        if (!snake.some(s => s.x === x && s.y === y)) {
            food = { x, y };
            return;
        }
        attempts++;
    }
}

async function endGame() {
    gameRunning = false;
    if (gameLoopInterval) {
        clearInterval(gameLoopInterval);
        gameLoopInterval = null;
    }

    const isNewRecord = score > highScore;
    if (isNewRecord) {
        highScore = score;
        await saveHighScore(score);
        document.getElementById('highScore').textContent = highScore;
    }

    document.getElementById('finalScore').textContent = score;
    document.getElementById('finalHighScore').textContent = highScore;
    document.getElementById('newRecordText').style.display = isNewRecord ? 'block' : 'none';
    document.getElementById('gameOverOverlay').classList.add('active');

    document.getElementById('startBtn').style.display = 'block';
    document.getElementById('startBtn').textContent = '▶ Играть снова';

    if (isNewRecord && window.LoLOS) {
        LoLOS.showToast('🏆 Новый рекорд: ' + score + '!');
    }
}

function closeOverlay() {
    document.getElementById('gameOverOverlay').classList.remove('active');
}

// ============================================================
// УПРАВЛЕНИЕ
// ============================================================
function handleKeyDown(e) {
    switch (e.key) {
        case 'ArrowUp': e.preventDefault(); setDirection(0, -1); break;
        case 'ArrowDown': e.preventDefault(); setDirection(0, 1); break;
        case 'ArrowLeft': e.preventDefault(); setDirection(-1, 0); break;
        case 'ArrowRight': e.preventDefault(); setDirection(1, 0); break;
        case ' ': e.preventDefault(); if (!gameRunning) startGame(); break;
        case 'Escape': closeOverlay(); break;
    }
}

function setDirection(x, y) {
    if (!gameRunning) return;
    // Нельзя разворачиваться на 180°
    if (direction.x === -x && direction.y === -y) return;
    nextDirection = { x, y };
}

// ============================================================
// ОТРИСОВКА
// ============================================================
function drawInitialState() {
    ctx.fillStyle = '#0f0f24';
    ctx.fillRect(0, 0, CANVAS_SIZE, CANVAS_SIZE);

    // Сетка (тонкая)
    ctx.strokeStyle = 'rgba(74, 222, 128, 0.05)';
    ctx.lineWidth = 1;
    for (let i = 0; i <= GRID; i++) {
        ctx.beginPath();
        ctx.moveTo(i * CELL, 0);
        ctx.lineTo(i * CELL, CANVAS_SIZE);
        ctx.stroke();
        ctx.beginPath();
        ctx.moveTo(0, i * CELL);
        ctx.lineTo(CANVAS_SIZE, i * CELL);
        ctx.stroke();
    }

    // Текст "Нажми Играть"
    ctx.fillStyle = 'rgba(255,255,255,0.15)';
    ctx.font = 'bold 20px Segoe UI, sans-serif';
    ctx.textAlign = 'center';
    ctx.textBaseline = 'middle';
    ctx.fillText('🐍', CANVAS_SIZE/2, CANVAS_SIZE/2 - 15);
    ctx.font = '14px Segoe UI, sans-serif';
    ctx.fillText('Нажми «Играть»', CANVAS_SIZE/2, CANVAS_SIZE/2 + 20);
}

function draw() {
    // Фон
    ctx.fillStyle = '#0f0f24';
    ctx.fillRect(0, 0, CANVAS_SIZE, CANVAS_SIZE);

    // Сетка
    ctx.strokeStyle = 'rgba(74, 222, 128, 0.05)';
    ctx.lineWidth = 1;
    for (let i = 0; i <= GRID; i++) {
        ctx.beginPath();
        ctx.moveTo(i * CELL, 0);
        ctx.lineTo(i * CELL, CANVAS_SIZE);
        ctx.stroke();
        ctx.beginPath();
        ctx.moveTo(0, i * CELL);
        ctx.lineTo(CANVAS_SIZE, i * CELL);
        ctx.stroke();
    }

    // Еда (яблоко)
    ctx.font = `${CELL - 4}px serif`;
    ctx.textAlign = 'center';
    ctx.textBaseline = 'middle';
    ctx.fillText('🍎', food.x * CELL + CELL/2, food.y * CELL + CELL/2);

    // Змейка (свечение)
    snake.forEach((seg, i) => {
        const isHead = i === 0;

        // Свечение
        ctx.shadowColor = isHead ? '#4ade80' : '#22c55e';
        ctx.shadowBlur = isHead ? 15 : 8;

        // Тело
        ctx.fillStyle = isHead ? '#4ade80' : `hsl(140, 70%, ${50 - i * 1.5}%)`;
        ctx.beginPath();
        const padding = 2;
        const radius = isHead ? 6 : 4;
        roundRect(
            seg.x * CELL + padding,
            seg.y * CELL + padding,
            CELL - padding * 2,
            CELL - padding * 2,
            radius
        );
        ctx.fill();

        // Глаза на голове
        if (isHead) {
            ctx.shadowBlur = 0;
            ctx.fillStyle = '#0a0a1a';
            const eyeSize = 3;
            const eyeOffset = CELL / 4;
            const cx = seg.x * CELL + CELL/2;
            const cy = seg.y * CELL + CELL/2;

            // Позиция глаз зависит от направления
            let e1x = cx, e1y = cy, e2x = cx, e2y = cy;
            if (direction.x === 1) { e1x = cx + eyeOffset; e2x = cx + eyeOffset; e1y = cy - eyeOffset; e2y = cy + eyeOffset; }
            else if (direction.x === -1) { e1x = cx - eyeOffset; e2x = cx - eyeOffset; e1y = cy - eyeOffset; e2y = cy + eyeOffset; }
            else if (direction.y === 1) { e1y = cy + eyeOffset; e2y = cy + eyeOffset; e1x = cx - eyeOffset; e2x = cx + eyeOffset; }
            else { e1y = cy - eyeOffset; e2y = cy - eyeOffset; e1x = cx - eyeOffset; e2x = cx + eyeOffset; }

            ctx.beginPath();
            ctx.arc(e1x, e1y, eyeSize, 0, Math.PI * 2);
            ctx.arc(e2x, e2y, eyeSize, 0, Math.PI * 2);
            ctx.fill();
        }
    });

    // Сбрасываем тень
    ctx.shadowBlur = 0;
}

function roundRect(x, y, width, height, radius) {
    ctx.beginPath();
    ctx.moveTo(x + radius, y);
    ctx.lineTo(x + width - radius, y);
    ctx.quadraticCurveTo(x + width, y, x + width, y + radius);
    ctx.lineTo(x + width, y + height - radius);
    ctx.quadraticCurveTo(x + width, y + height, x + width - radius, y + height);
    ctx.lineTo(x + radius, y + height);
    ctx.quadraticCurveTo(x, y + height, x, y + height - radius);
    ctx.lineTo(x, y + radius);
    ctx.quadraticCurveTo(x, y, x + radius, y);
    ctx.closePath();
}

// ============================================================
// СТАРТ
// ============================================================
window.addEventListener('DOMContentLoaded', init);
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
