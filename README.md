<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>DETECTIVE 2D · PROFESSIONAL</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            image-rendering: pixelated;
            image-rendering: crisp-edges;
            user-select: none;
        }

        body {
            background: #000;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            font-family: 'Courier New', monospace;
        }

        #gameWrapper {
            position: relative;
            width: 1280px;
            height: 720px;
            box-shadow: 0 0 50px rgba(139, 69, 19, 0.7);
            transition: all 0.3s;
        }

        #gameWrapper.fullscreen {
            width: 100vw;
            height: 100vh;
        }

        canvas {
            width: 100%;
            height: 100%;
            display: block;
            image-rendering: pixelated;
            background: #0a0a0a;
        }

        /* UI Overlay */
        .ui-overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 10;
        }

        /* Top Panel */
        .top-panel {
            position: absolute;
            top: 10px;
            left: 10px;
            right: 10px;
            background: rgba(10, 10, 10, 0.9);
            border: 2px solid #8b4513;
            padding: 10px 20px;
            display: flex;
            justify-content: space-between;
            pointer-events: auto;
            backdrop-filter: blur(5px);
            box-shadow: 0 5px 15px rgba(0,0,0,0.5);
        }

        .player-stats {
            display: flex;
            gap: 30px;
        }

        .stat {
            display: flex;
            align-items: center;
            gap: 8px;
            color: #e0e0e0;
            font-size: 16px;
            text-shadow: 2px 2px 0 #000;
        }

        .stat-icon {
            font-size: 20px;
        }

        .stat-value {
            color: #8b4513;
            font-weight: bold;
            min-width: 40px;
        }

        .case-info {
            display: flex;
            gap: 20px;
        }

        .badge {
            background: #8b4513;
            color: white;
            padding: 5px 15px;
            border: 1px solid #cd853f;
            font-size: 14px;
            box-shadow: inset 0 -2px 0 #5a2e0d;
        }

        /* Weather Indicator */
        .weather-indicator {
            position: absolute;
            top: 80px;
            right: 20px;
            background: rgba(0,0,0,0.7);
            border: 1px solid #8b4513;
            padding: 8px 15px;
            color: #e0e0e0;
            font-size: 14px;
            pointer-events: auto;
        }

        /* Inventory */
        .inventory-panel {
            position: absolute;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            background: rgba(10, 10, 10, 0.95);
            border: 3px solid #8b4513;
            padding: 15px;
            display: flex;
            gap: 10px;
            pointer-events: auto;
            box-shadow: 0 10px 30px rgba(0,0,0,0.7);
        }

        .inventory-slot {
            width: 60px;
            height: 60px;
            background: #1a1a1a;
            border: 2px solid #333;
            position: relative;
            transition: all 0.2s;
            cursor: pointer;
        }

        .inventory-slot:hover {
            border-color: #8b4513;
            transform: scale(1.05);
        }

        .inventory-slot.selected {
            border-color: #ffd700;
            box-shadow: 0 0 20px #ffd700;
        }

        .inventory-slot.has-item {
            background: #2a2a2a;
        }

        .inventory-slot.has-item::after {
            content: "🔍";
            position: absolute;
            bottom: 2px;
            right: 2px;
            font-size: 14px;
            color: #8b4513;
        }

        .slot-number {
            position: absolute;
            top: 2px;
            left: 2px;
            color: #666;
            font-size: 10px;
        }

        /* Mini Map */
        .minimap {
            position: absolute;
            top: 100px;
            left: 20px;
            width: 200px;
            height: 150px;
            background: rgba(0,0,0,0.8);
            border: 2px solid #8b4513;
            pointer-events: auto;
            overflow: hidden;
        }

        .minimap canvas {
            width: 100%;
            height: 100%;
        }

        /* Case Journal */
        .journal {
            position: absolute;
            top: 100px;
            right: 20px;
            width: 300px;
            background: rgba(10, 10, 10, 0.95);
            border: 3px solid #8b4513;
            padding: 15px;
            pointer-events: auto;
            color: #e0e0e0;
            max-height: 400px;
            overflow-y: auto;
            display: none;
        }

        .journal.active {
            display: block;
        }

        .journal-title {
            color: #8b4513;
            font-size: 18px;
            margin-bottom: 15px;
            border-bottom: 1px solid #333;
            padding-bottom: 5px;
        }

        .journal-entry {
            background: #1a1a1a;
            border-left: 3px solid #8b4513;
            padding: 10px;
            margin-bottom: 10px;
            font-size: 12px;
        }

        .journal-entry .date {
            color: #666;
            font-size: 10px;
        }

        /* Dialogue Box */
        .dialogue-box {
            position: absolute;
            bottom: 150px;
            left: 50%;
            transform: translateX(-50%);
            width: 700px;
            background: rgba(10, 10, 10, 0.98);
            border: 4px solid #8b4513;
            padding: 25px;
            display: none;
            pointer-events: auto;
            z-index: 100;
            box-shadow: 0 20px 50px rgba(0,0,0,0.9);
            border-radius: 10px 10px 0 0;
        }

        .dialogue-box.active {
            display: block;
            animation: slideUp 0.3s;
        }

        @keyframes slideUp {
            from { bottom: -500px; opacity: 0; }
            to { bottom: 150px; opacity: 1; }
        }

        .dialogue-name {
            color: #8b4513;
            font-size: 22px;
            margin-bottom: 15px;
            border-bottom: 2px solid #333;
            padding-bottom: 10px;
        }

        .dialogue-portrait {
            width: 80px;
            height: 80px;
            background: #2a2a2a;
            border: 2px solid #8b4513;
            float: left;
            margin-right: 20px;
            margin-bottom: 20px;
        }

        .dialogue-text {
            font-size: 16px;
            line-height: 1.8;
            margin-bottom: 25px;
            min-height: 100px;
            color: #fff;
            text-shadow: 1px 1px 0 #000;
        }

        .dialogue-options {
            display: flex;
            flex-direction: column;
            gap: 12px;
            clear: both;
        }

        .dialogue-option {
            background: #1a1a1a;
            border: 2px solid #333;
            padding: 15px 20px;
            color: white;
            cursor: pointer;
            font-size: 15px;
            transition: all 0.2s;
            border-radius: 5px;
        }

        .dialogue-option:hover {
            border-color: #8b4513;
            background: #2a2a2a;
            transform: translateX(10px);
            box-shadow: 0 0 15px #8b4513;
        }

        .dialogue-option:active {
            transform: scale(0.98);
        }

        /* Notification */
        .notification {
            position: absolute;
            top: 100px;
            right: 20px;
            background: rgba(10, 10, 10, 0.95);
            border: 2px solid #8b4513;
            padding: 15px 25px;
            z-index: 50;
            animation: slideInRight 0.3s;
            pointer-events: none;
            border-radius: 5px;
            box-shadow: 0 5px 20px rgba(0,0,0,0.5);
        }

        @keyframes slideInRight {
            from { right: -100%; opacity: 0; }
            to { right: 20px; opacity: 1; }
        }

        .notification-title {
            color: #8b4513;
            font-size: 16px;
            margin-bottom: 5px;
        }

        .notification-message {
            color: #fff;
            font-size: 14px;
        }

        /* Controls Hint */
        .controls-hint {
            position: absolute;
            bottom: 100px;
            left: 20px;
            color: #888;
            font-size: 12px;
            background: rgba(0,0,0,0.6);
            padding: 8px 15px;
            border: 1px solid #333;
            border-radius: 20px;
            pointer-events: none;
        }

        /* Fullscreen Button */
        .fullscreen-btn {
            position: absolute;
            top: 20px;
            right: 20px;
            width: 40px;
            height: 40px;
            background: rgba(0,0,0,0.7);
            border: 2px solid #8b4513;
            color: white;
            font-size: 20px;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            pointer-events: auto;
            z-index: 200;
        }

        .fullscreen-btn:hover {
            background: #8b4513;
        }

        /* Auth Screen */
        .auth-screen {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(135deg, #0a0a0a 0%, #1a1a1a 100%);
            z-index: 1000;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        .auth-box {
            background: rgba(10, 10, 10, 0.95);
            border: 4px solid #8b4513;
            padding: 50px;
            width: 450px;
            text-align: center;
            box-shadow: 0 20px 50px rgba(0,0,0,0.9);
            border-radius: 10px;
        }

        .auth-title {
            font-size: 48px;
            color: #8b4513;
            margin-bottom: 40px;
            text-shadow: 3px 3px 0 #2a2a2a;
            letter-spacing: 4px;
        }

        .auth-input {
            width: 100%;
            padding: 15px;
            margin: 15px 0;
            background: #1a1a1a;
            border: 2px solid #333;
            color: #fff;
            font-family: 'Courier New', monospace;
            font-size: 18px;
            border-radius: 5px;
        }

        .auth-input:focus {
            border-color: #8b4513;
            outline: none;
        }

        .auth-button {
            width: 100%;
            padding: 15px;
            background: #8b4513;
            color: white;
            border: none;
            font-size: 20px;
            cursor: pointer;
            margin: 20px 0;
            border-radius: 5px;
            transition: all 0.2s;
        }

        .auth-button:hover {
            background: #a05a1e;
            transform: scale(1.02);
            box-shadow: 0 0 30px #8b4513;
        }

        /* Solved Screen */
        .solved-screen {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.98);
            z-index: 2000;
            display: none;
            justify-content: center;
            align-items: center;
        }

        .solved-box {
            background: #0f0f0f;
            border: 4px solid #8b4513;
            padding: 50px;
            text-align: center;
            max-width: 700px;
            border-radius: 15px;
            box-shadow: 0 0 50px #8b4513;
            animation: pulse 2s infinite;
        }

        @keyframes pulse {
            0%, 100% { box-shadow: 0 0 50px #8b4513; }
            50% { box-shadow: 0 0 100px #8b4513; }
        }

        .solved-title {
            font-size: 64px;
            color: #8b4513;
            margin-bottom: 30px;
            text-shadow: 3px 3px 0 #000;
        }

        .solved-stats {
            font-size: 20px;
            color: #fff;
            margin: 40px 0;
            line-height: 2.5;
        }

        .solved-button {
            padding: 20px 40px;
            background: #8b4513;
            color: white;
            border: none;
            font-size: 22px;
            cursor: pointer;
            border-radius: 5px;
            transition: all 0.2s;
        }

        .solved-button:hover {
            background: #a05a1e;
            transform: scale(1.05);
        }

        /* Loading Screen */
        .loading-screen {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: #000;
            z-index: 3000;
            display: none;
            justify-content: center;
            align-items: center;
        }

        .loading-spinner {
            width: 60px;
            height: 60px;
            border: 5px solid #333;
            border-top-color: #8b4513;
            border-radius: 50%;
            animation: spin 1s infinite;
        }

        @keyframes spin {
            to { transform: rotate(360deg); }
        }

        /* Scrollbar */
        ::-webkit-scrollbar {
            width: 8px;
        }

        ::-webkit-scrollbar-track {
            background: #1a1a1a;
        }

        ::-webkit-scrollbar-thumb {
            background: #8b4513;
            border-radius: 4px;
        }
    </style>
</head>
<body>
    <div id="gameWrapper">
        <canvas id="gameCanvas" width="1280" height="720"></canvas>

        <!-- UI Overlay -->
        <div class="ui-overlay">
            <!-- Top Panel -->
            <div class="top-panel">
                <div class="player-stats">
                    <div class="stat"><span class="stat-icon">🔍</span> <span class="stat-value" id="playerName">DETECTIVE</span></div>
                    <div class="stat"><span class="stat-icon">❤️</span> <span class="stat-value" id="playerHealth">100</span></div>
                    <div class="stat"><span class="stat-icon">⚡</span> <span class="stat-value" id="playerStamina">100</span></div>
                    <div class="stat"><span class="stat-icon">💰</span> <span class="stat-value" id="playerMoney">500</span></div>
                </div>
                <div class="case-info">
                    <span class="badge" id="currentDistrict">DOWNTOWN</span>
                    <span class="badge" id="currentTime">08:00</span>
                    <span class="badge" id="caseCount">0 CASES</span>
                </div>
            </div>

            <!-- Weather -->
            <div class="weather-indicator" id="weatherIndicator">
                ☀️ Ясно · 18°C
            </div>

            <!-- Mini Map -->
            <div class="minimap" id="minimap">
                <canvas id="minimapCanvas" width="200" height="150"></canvas>
            </div>

            <!-- Inventory -->
            <div class="inventory-panel" id="inventoryPanel">
                <!-- Will be populated by JS -->
            </div>

            <!-- Case Journal -->
            <div class="journal" id="journal">
                <div class="journal-title">📋 ЖУРНАЛ ДЕЛ</div>
                <div id="journalEntries"></div>
            </div>

            <!-- Controls Hint -->
            <div class="controls-hint">
                ← → движение | W/S этажи | E действие | J журнал | M карта | F полноэкранный
            </div>

            <!-- Fullscreen Button -->
            <div class="fullscreen-btn" id="fullscreenBtn">⛶</div>

            <!-- Dialogue Box -->
            <div class="dialogue-box" id="dialogueBox">
                <div class="dialogue-name" id="dialogueName">Witness</div>
                <div class="dialogue-portrait" id="dialoguePortrait"></div>
                <div class="dialogue-text" id="dialogueText">Hello, detective...</div>
                <div class="dialogue-options" id="dialogueOptions"></div>
            </div>
        </div>

        <!-- Auth Screen -->
        <div class="auth-screen" id="authScreen">
            <div class="auth-box">
                <div class="auth-title">DETECTIVE 2D</div>
                <input type="text" class="auth-input" id="authUsername" placeholder="ВАШЕ ИМЯ">
                <button class="auth-button" id="authButton">НАЧАТЬ РАССЛЕДОВАНИЕ</button>
            </div>
        </div>

        <!-- Solved Screen -->
        <div class="solved-screen" id="solvedScreen">
            <div class="solved-box">
                <div class="solved-title">ДЕЛО ЗАКРЫТО</div>
                <div class="solved-stats" id="solvedStats"></div>
                <button class="solved-button" id="continueButton">ПРОДОЛЖИТЬ</button>
            </div>
        </div>

        <!-- Loading Screen -->
        <div class="loading-screen" id="loadingScreen">
            <div class="loading-spinner"></div>
        </div>
    </div>

    <script>
        // ==================== КОНФИГУРАЦИЯ ====================
        const CONFIG = {
            GROUND_LEVEL: 550,
            TILE_SIZE: 32,
            PLAYER_SPEED: 4,
            GRAVITY: 0.5,
            JUMP_FORCE: -10,
            MAX_HEALTH: 100,
            MAX_STAMINA: 100,
            WEATHER_TYPES: ['☀️ Ясно', '☁️ Облачно', '🌧️ Дождь', '🌫️ Туман', '🌙 Ночь'],
            DISTRICTS: [
                { name: "ДАУНТАУН", color: "#8b4513", x: 0, width: 300, crimeRate: 40, wealth: 80 },
                { name: "ИНДУСТРИАЛЬНЫЙ", color: "#555", x: 300, width: 250, crimeRate: 85, wealth: 20 },
                { name: "РИВЕРСАЙД", color: "#2a5a2a", x: 550, width: 250, crimeRate: 20, wealth: 90 },
                { name: "СТАРЫЙ ГОРОД", color: "#b8860b", x: 800, width: 250, crimeRate: 55, wealth: 60 },
                { name: "ПОРТ", color: "#2a5a8b", x: 1050, width: 230, crimeRate: 75, wealth: 30 }
            ]
        };

        // ==================== КЛАСС ГОРОДА ====================
        class City {
            constructor() {
                this.districts = CONFIG.DISTRICTS;
                this.buildings = this.generateBuildings();
                this.npcs = [];
                this.weather = CONFIG.WEATHER_TYPES[Math.floor(Math.random() * CONFIG.WEATHER_TYPES.length)];
                this.time = { hour: 8, minute: 0, day: 1 };
            }

            generateBuildings() {
                const buildings = [];
                this.districts.forEach(district => {
                    const buildingCount = 5 + Math.floor(Math.random() * 5);
                    for (let i = 0; i < buildingCount; i++) {
                        buildings.push({
                            name: this.generateBuildingName(),
                            district: district.name,
                            x: district.x + 30 + Math.random() * (district.width - 100),
                            floors: 2 + Math.floor(Math.random() * 8),
                            type: ['жилой', 'офисный', 'торговый', 'промышленный'][Math.floor(Math.random() * 4)],
                            color: `hsl(${Math.random() * 360}, 50%, 30%)`
                        });
                    }
                });
                return buildings;
            }

            generateBuildingName() {
                const prefixes = ['Центральный', 'Северный', 'Южный', 'Старый', 'Новый', 'Главный'];
                const types = ['Дом', 'Офис', 'Башня', 'Центр', 'Комплекс', 'Плаза'];
                return prefixes[Math.floor(Math.random() * prefixes.length)] + ' ' + 
                       types[Math.floor(Math.random() * types.length)];
            }

            getDistrictAt(x) {
                return this.districts.find(d => x >= d.x && x < d.x + d.width) || this.districts[0];
            }

            getBuildingAt(x) {
                return this.buildings.find(b => Math.abs(b.x - x) < 50);
            }

            updateTime() {
                this.time.minute += 0.5;
                if (this.time.minute >= 60) {
                    this.time.minute = 0;
                    this.time.hour++;
                    if (this.time.hour >= 24) {
                        this.time.hour = 0;
                        this.time.day++;
                    }
                }
                
                // Change weather occasionally
                if (Math.random() < 0.001) {
                    this.weather = CONFIG.WEATHER_TYPES[Math.floor(Math.random() * CONFIG.WEATHER_TYPES.length)];
                }
            }
        }

        // ==================== КЛАСС ИГРОКА ====================
        class Player {
            constructor(name, city) {
                this.name = name;
                this.x = 100;
                this.y = CONFIG.GROUND_LEVEL - 50;
                this.width = 24;
                this.height = 48;
                this.health = CONFIG.MAX_HEALTH;
                this.stamina = CONFIG.MAX_STAMINA;
                this.money = 500;
                this.speed = CONFIG.PLAYER_SPEED;
                this.direction = 1;
                this.inventory = [];
                this.maxInventory = 12;
                this.activeCase = null;
                this.cases = [];
                this.solvedCases = 0;
                this.reputation = 50;
                this.currentBuilding = null;
                this.currentFloor = 0;
                this.isMoving = false;
                this.animationFrame = 0;
            }

            move(dx, city) {
                const newX = this.x + dx * this.speed;
                if (newX >= 20 && newX < 1260 - this.width) {
                    this.x = newX;
                    this.direction = dx > 0 ? 1 : -1;
                    this.isMoving = true;
                    
                    // Check building entry
                    const building = city.getBuildingAt(this.x);
                    if (building && !this.currentBuilding) {
                        this.currentBuilding = building;
                        this.y = CONFIG.GROUND_LEVEL - 50;
                    } else if (!building) {
                        this.currentBuilding = null;
                        this.y = CONFIG.GROUND_LEVEL - 50;
                    }
                } else {
                    this.isMoving = false;
                }
            }

            goUp() {
                if (this.currentBuilding && this.currentFloor < this.currentBuilding.floors - 1) {
                    this.currentFloor++;
                    this.y -= 50;
                    playSound('step');
                }
            }

            goDown() {
                if (this.currentBuilding && this.currentFloor > 0) {
                    this.currentFloor--;
                    this.y += 50;
                    playSound('step');
                } else if (this.currentBuilding && this.currentFloor === 0) {
                    this.currentBuilding = null;
                    this.y = CONFIG.GROUND_LEVEL - 50;
                }
            }

            addToInventory(item) {
                if (this.inventory.length < this.maxInventory) {
                    this.inventory.push(item);
                    return true;
                }
                return false;
            }

            removeFromInventory(index) {
                if (index >= 0 && index < this.inventory.length) {
                    return this.inventory.splice(index, 1)[0];
                }
                return null;
            }

            takeDamage(amount) {
                this.health = Math.max(0, this.health - amount);
                if (this.health === 0) {
                    this.die();
                }
            }

            die() {
                alert('ВЫ ПОГИБЛИ... НАЧНИТЕ ЗАНОВО');
                location.reload();
            }
        }

        // ==================== КЛАСС NPC ====================
        class NPC {
            constructor(id, building, floor) {
                this.id = id;
                this.building = building;
                this.floor = floor;
                this.x = building.x + 30 + Math.random() * 40;
                this.y = CONFIG.GROUND_LEVEL - 50 - (floor * 50);
                this.name = this.generateName();
                this.occupation = this.generateOccupation();
                this.dialogue = this.generateDialogue();
                this.trust = 50 + Math.floor(Math.random() * 50);
                this.isKiller = false;
                this.mood = ['normal', 'nervous', 'aggressive', 'friendly'][Math.floor(Math.random() * 4)];
            }

            generateName() {
                const firstNames = ['Джеймс', 'Мэри', 'Джон', 'Патриция', 'Роберт', 'Дженнифер', 
                                    'Майкл', 'Линда', 'Уильям', 'Элизабет', 'Дэвид', 'Барбара',
                                    'Алексей', 'Елена', 'Дмитрий', 'Ольга', 'Михаил', 'Татьяна'];
                const lastNames = ['Смит', 'Джонсон', 'Уильямс', 'Браун', 'Джонс', 'Гарсия',
                                  'Миллер', 'Дэвис', 'Родригес', 'Мартинес', 'Иванов', 'Петров'];
                return firstNames[Math.floor(Math.random() * firstNames.length)] + ' ' + 
                       lastNames[Math.floor(Math.random() * lastNames.length)];
            }

            generateOccupation() {
                const occupations = ['детектив', 'журналист', 'адвокат', 'врач', 'учитель', 'художник',
                                    'музыкант', 'повар', 'водитель', 'охранник', 'медсестра', 'инженер',
                                    'архитектор', 'фотограф', 'писатель', 'ученый', 'профессор'];
                return occupations[Math.floor(Math.random() * occupations.length)];
            }

            generateDialogue() {
                const dialogue = [];
                for (let i = 0; i < 15; i++) {
                    dialogue.push({
                        question: this.generateQuestion(),
                        answer: this.generateAnswer()
                    });
                }
                return dialogue;
            }

            generateQuestion() {
                const questions = [
                    'Где вы были прошлой ночью?',
                    'Вы знали жертву?',
                    'Видели что-то подозрительное?',
                    'Есть алиби?',
                    'Кого вы подозреваете?',
                    'Почему вы нервничаете?',
                    'Что вы делали в 22:00?',
                    'Ваши отношения с жертвой?',
                    'Есть враги?',
                    'Где вы работаете?'
                ];
                return questions[Math.floor(Math.random() * questions.length)];
            }

            generateAnswer() {
                const answers = [
                    'Был дома один.',
                    'Нет, не знал.',
                    'Ничего не видел.',
                    'Был с друзьями.',
                    'Не знаю.',
                    'Я не нервничаю.',
                    'Спал.',
                    'Мы были знакомы.',
                    'Нет врагов.',
                    'Работаю здесь.'
                ];
                return answers[Math.floor(Math.random() * answers.length)];
            }
        }

        // ==================== КЛАСС ДЕЛА ====================
        class Case {
            constructor(district) {
                this.id = Math.floor(Math.random() * 10000).toString().padStart(4, '0');
                this.district = district.name;
                this.victim = this.generateVictim();
                this.crime = this.generateCrime();
                this.npcs = [];
                this.evidence = [];
                this.killerId = null;
                this.solved = false;
                this.reward = 500 + Math.floor(Math.random() * 500);
            }

            generateVictim() {
                const names = ['Виктор Блэквуд', 'Элеонора Вэнс', 'Маркус Уэбб', 'Диана Принс',
                              'Томас Кроун', 'Виктория Стерлинг', 'Ричард Касл', 'Патриция Хейл'];
                return names[Math.floor(Math.random() * names.length)];
            }

            generateCrime() {
                const crimes = ['УБИЙСТВО', 'ОГРАБЛЕНИЕ', 'НАПАДЕНИЕ', 'ПОДЖОГ', 'МОШЕННИЧЕСТВО',
                               'ПОХИЩЕНИЕ', 'ВЫМОГАТЕЛЬСТВО', 'ОТРАВЛЕНИЕ'];
                return crimes[Math.floor(Math.random() * crimes.length)];
            }
        }

        // ==================== ЗВУКИ ====================
        function playSound(type) {
            // Имитация звуков (в реальности здесь был бы Audio API)
            console.log('🔊 Sound:', type);
        }

        // ==================== ГЛОБАЛЬНЫЕ ПЕРЕМЕННЫЕ ====================
        let canvas = document.getElementById('gameCanvas');
        let ctx = canvas.getContext('2d');
        ctx.imageSmoothingEnabled = false;
        
        let city = new City();
        let player = null;
        let cases = [];
        let keys = {};
        let mouseX = 0, mouseY = 0;
        
        // UI
        let dialogueBox = document.getElementById('dialogueBox');
        let currentNPC = null;
        let dialogueIndex = 0;
        let journalVisible = false;
        let minimapVisible = true;

        // ==================== ИНИЦИАЛИЗАЦИЯ ====================
        document.getElementById('authButton').addEventListener('click', () => {
            let name = document.getElementById('authUsername').value.trim();
            if (!name) name = "ДЕТЕКТИВ";
            
            player = new Player(name, city);
            document.getElementById('playerName').textContent = name;
            document.getElementById('authScreen').style.display = 'none';
            
            // Генерация первого дела
            let firstDistrict = city.districts[Math.floor(Math.random() * city.districts.length)];
            let firstCase = new Case(firstDistrict);
            
            // Генерация NPC
            city.buildings.forEach(building => {
                if (building.district === firstDistrict.name) {
                    for (let floor = 0; floor < building.floors; floor++) {
                        let npcCount = 2 + Math.floor(Math.random() * 3);
                        for (let i = 0; i < npcCount; i++) {
                            let npc = new NPC(firstCase.npcs.length, building, floor);
                            firstCase.npcs.push(npc);
                            city.npcs.push(npc);
                        }
                    }
                }
            });
            
            // Выбор убийцы
            firstCase.killerId = Math.floor(Math.random() * firstCase.npcs.length);
            firstCase.npcs[firstCase.killerId].isKiller = true;
            
            // Генерация улик
            for (let i = 0; i < 10; i++) {
                firstCase.evidence.push({
                    id: i,
                    type: ['КРОВЬ', 'ОТПЕЧАТКИ', 'ОРУЖИЕ', 'ЗАПИСКА', 'ФОТО', 'ВИДЕО'][Math.floor(Math.random() * 6)],
                    found: false,
                    x: firstDistrict.x + Math.random() * firstDistrict.width,
                    y: CONFIG.GROUND_LEVEL - 30 - Math.random() * 100,
                    description: 'Важная улика'
                });
            }
            
            cases.push(firstCase);
            player.activeCase = firstCase;
            
            updateUI();
            gameLoop();
        });

        // ==================== УПРАВЛЕНИЕ ====================
        window.addEventListener('keydown', (e) => {
            const key = e.key.toLowerCase();
            keys[key] = true;
            
            if (key === 'e' && !dialogueBox.classList.contains('active')) {
                interact();
            }
            
            if (key === 'w' || key === 'arrowup') {
                player.goUp();
            }
            
            if (key === 's' || key === 'arrowdown') {
                player.goDown();
            }
            
            if (key === 'j') {
                journalVisible = !journalVisible;
                document.getElementById('journal').classList.toggle('active', journalVisible);
            }
            
            if (key === 'm') {
                minimapVisible = !minimapVisible;
                document.getElementById('minimap').style.display = minimapVisible ? 'block' : 'none';
            }
            
            if (key === 'f') {
                toggleFullscreen();
            }
            
            if (key >= '1' && key <= '9') {
                selectInventorySlot(parseInt(key) - 1);
            }
        });

        window.addEventListener('keyup', (e) => {
            keys[e.key.toLowerCase()] = false;
        });

        window.addEventListener('mousemove', (e) => {
            const rect = canvas.getBoundingClientRect();
            mouseX = (e.clientX - rect.left) * (canvas.width / rect.width);
            mouseY = (e.clientY - rect.top) * (canvas.height / rect.height);
        });

        function handleMovement() {
            if (!player || dialogueBox.classList.contains('active')) return;
            
            if (keys['a'] || keys['arrowleft']) {
                player.move(-1, city);
            } else if (keys['d'] || keys['arrowright']) {
                player.move(1, city);
            } else {
                player.isMoving = false;
            }
            
            // Stamina regeneration
            if (!player.isMoving) {
                player.stamina = Math.min(CONFIG.MAX_STAMINA, player.stamina + 0.2);
            }
            
            // Health regeneration
            if (player.health < CONFIG.MAX_HEALTH && Math.random() < 0.01) {
                player.health = Math.min(CONFIG.MAX_HEALTH, player.health + 1);
            }
        }

        function interact() {
            if (!player || !player.activeCase) return;
            
            // Check NPCs
            player.activeCase.npcs.forEach(npc => {
                if (npc.building === player.currentBuilding?.name && npc.floor === player.currentFloor) {
                    const dist = Math.abs(player.x - npc.x);
                    if (dist < 60) {
                        startDialogue(npc);
                    }
                }
            });
            
            // Check evidence
            player.activeCase.evidence.forEach(evidence => {
                if (!evidence.found) {
                    const dist = Math.abs(player.x - evidence.x);
                    const yDist = Math.abs(player.y - evidence.y);
                    if (dist < 30 && yDist < 30) {
                        evidence.found = true;
                        if (player.addToInventory(evidence)) {
                            showNotification("УЛИКА НАЙДЕНА", evidence.type);
                            playSound('evidence');
                        }
                        updateUI();
                    }
                }
            });
        }

        // ==================== ДИАЛОГИ ====================
        function startDialogue(npc) {
            currentNPC = npc;
            dialogueIndex = 0;
            
            document.getElementById('dialogueName').textContent = npc.name + ' · ' + npc.occupation;
            document.getElementById('dialogueText').textContent = '...';
            
            showDialogueOptions();
            dialogueBox.classList.add('active');
            playSound('dialogue');
        }

        function showDialogueOptions() {
            if (!currentNPC) return;
            
            const options = document.getElementById('dialogueOptions');
            options.innerHTML = '';
            
            const startIdx = dialogueIndex * 4;
            for (let i = 0; i < 4; i++) {
                const q = currentNPC.dialogue[startIdx + i];
                if (!q) break;
                
                let opt = document.createElement('div');
                opt.className = 'dialogue-option';
                opt.textContent = '❓ ' + q.question;
                opt.addEventListener('click', () => {
                    document.getElementById('dialogueText').textContent = q.answer;
                    
                    // Update trust
                    currentNPC.trust += Math.random() * 5;
                    
                    // Chance for evidence
                    if (Math.random() > 0.7) {
                        let evidence = player.activeCase.evidence.find(e => !e.found);
                        if (evidence) {
                            evidence.found = true;
                            player.addToInventory(evidence);
                            showNotification("УЛИКА ПОЛУЧЕНА", evidence.type);
                        }
                    }
                    
                    playSound('click');
                    
                    setTimeout(() => {
                        dialogueIndex++;
                        if (dialogueIndex * 4 < currentNPC.dialogue.length) {
                            showDialogueOptions();
                        } else {
                            endDialogue();
                        }
                    }, 500);
                });
                options.appendChild(opt);
            }
            
            // Accuse option
            let accuseOpt = document.createElement('div');
            accuseOpt.className = 'dialogue-option';
            accuseOpt.textContent = '⚖️ ОБВИНИТЬ';
            accuseOpt.addEventListener('click', () => {
                if (currentNPC.isKiller) {
                    solveCase(currentNPC);
                } else {
                    document.getElementById('dialogueText').textContent = "Вы ошибаетесь! Я невиновен!";
                    currentNPC.trust -= 20;
                    playSound('error');
                }
            });
            options.appendChild(accuseOpt);
            
            // Leave option
            let leaveOpt = document.createElement('div');
            leaveOpt.className = 'dialogue-option';
            leaveOpt.textContent = '🚪 ЗАВЕРШИТЬ';
            leaveOpt.addEventListener('click', () => {
                dialogueBox.classList.remove('active');
                playSound('close');
            });
            options.appendChild(leaveOpt);
        }

        function endDialogue() {
            document.getElementById('dialogueText').textContent = "Это всё, что я знаю.";
            const options = document.getElementById('dialogueOptions');
            options.innerHTML = '';
            
            let closeOpt = document.createElement('div');
            closeOpt.className = 'dialogue-option';
            closeOpt.textContent = '🚪 ЗАКРЫТЬ';
            closeOpt.addEventListener('click', () => {
                dialogueBox.classList.remove('active');
            });
            options.appendChild(closeOpt);
        }

        // ==================== РЕШЕНИЕ ДЕЛА ====================
        function solveCase(killer) {
            let solved = document.getElementById('solvedScreen');
            let stats = document.getElementById('solvedStats');
            
            let evidenceFound = player.activeCase.evidence.filter(e => e.found).length;
            let totalEvidence = player.activeCase.evidence.length;
            let bonus = Math.floor(evidenceFound * 50);
            
            stats.innerHTML = `
                УБИЙЦА: ${killer.name}<br>
                ЖЕРТВА: ${player.activeCase.victim}<br>
                ПРЕСТУПЛЕНИЕ: ${player.activeCase.crime}<br>
                УЛИК: ${evidenceFound}/${totalEvidence}<br>
                НАГРАДА: $${player.activeCase.reward + bonus}<br>
                РЕПУТАЦИЯ: +15
            `;
            
            player.money += player.activeCase.reward + bonus;
            player.reputation += 15;
            player.solvedCases++;
            
            playSound('win');
            solved.style.display = 'flex';
            
            // Удаляем решённое дело
            cases = cases.filter(c => c.id !== player.activeCase.id);
            
            if (cases.length === 0) {
                // Генерируем новое
                let district = city.districts[Math.floor(Math.random() * city.districts.length)];
                let newCase = new Case(district);
                cases.push(newCase);
                player.activeCase = newCase;
            } else {
                player.activeCase = cases[0];
            }
            
            updateUI();
        }

        document.getElementById('continueButton').addEventListener('click', () => {
            document.getElementById('solvedScreen').style.display = 'none';
        });

        // ==================== UI ФУНКЦИИ ====================
        function updateUI() {
            if (!player) return;
            
            document.getElementById('playerHealth').textContent = Math.floor(player.health);
            document.getElementById('playerStamina').textContent = Math.floor(player.stamina);
            document.getElementById('playerMoney').textContent = player.money;
            
            let district = city.getDistrictAt(player.x);
            document.getElementById('currentDistrict').textContent = district ? district.name : "?";
            
            document.getElementById('caseCount').textContent = cases.length + " ДЕЛ";
            
            // Update inventory
            let invPanel = document.getElementById('inventoryPanel');
            invPanel.innerHTML = '';
            
            for (let i = 0; i < player.maxInventory; i++) {
                let slot = document.createElement('div');
                slot.className = 'inventory-slot';
                if (player.inventory[i]) {
                    slot.classList.add('has-item');
                }
                if (i === player.selectedSlot) {
                    slot.classList.add('selected');
                }
                
                let number = document.createElement('span');
                number.className = 'slot-number';
                number.textContent = i + 1;
                slot.appendChild(number);
                
                slot.addEventListener('click', () => selectInventorySlot(i));
                
                invPanel.appendChild(slot);
            }
            
            // Update journal
            let journalEntries = document.getElementById('journalEntries');
            journalEntries.innerHTML = '';
            
            cases.forEach(c => {
                let entry = document.createElement('div');
                entry.className = 'journal-entry';
                entry.innerHTML = `
                    <div style="color: #8b4513;">ДЕЛО #${c.id}</div>
                    <div>${c.victim}</div>
                    <div>${c.crime}</div>
                    <div class="date">${city.time.hour}:${city.time.minute.toString().padStart(2,'0')}</div>
                `;
                journalEntries.appendChild(entry);
            });
            
            // Update weather
            document.getElementById('weatherIndicator').innerHTML = 
                `${city.weather} · ${Math.floor(18 + Math.sin(city.time.hour) * 5)}°C`;
            
            // Update time
            document.getElementById('currentTime').textContent = 
                `${city.time.hour.toString().padStart(2,'0')}:${city.time.minute.toString().padStart(2,'0')}`;
        }

        function selectInventorySlot(index) {
            player.selectedSlot = index;
            updateUI();
        }

        function showNotification(title, message) {
            let notif = document.createElement('div');
            notif.className = 'notification';
            notif.innerHTML = `
                <div class="notification-title">${title}</div>
                <div class="notification-message">${message}</div>
            `;
            document.getElementById('gameWrapper').appendChild(notif);
            
            setTimeout(() => {
                notif.remove();
            }, 3000);
        }

        // ==================== ПОЛНОЭКРАННЫЙ РЕЖИМ ====================
        function toggleFullscreen() {
            const wrapper = document.getElementById('gameWrapper');
            if (!document.fullscreenElement) {
                wrapper.requestFullscreen();
                wrapper.classList.add('fullscreen');
            } else {
                document.exitFullscreen();
                wrapper.classList.remove('fullscreen');
            }
        }

        document.getElementById('fullscreenBtn').addEventListener('click', toggleFullscreen);

        // ==================== ОТРИСОВКА ====================
        function render() {
            if (!player) return;
            
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            // Sky gradient
            let gradient = ctx.createLinearGradient(0, 0, 0, canvas.height);
            gradient.addColorStop(0, '#0a1a2a');
            gradient.addColorStop(0.7, '#2a2a2a');
            ctx.fillStyle = gradient;
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            
            // Draw weather effects
            if (city.weather.includes('Дождь')) {
                ctx.fillStyle = 'rgba(100, 100, 255, 0.1)';
                for (let i = 0; i < 50; i++) {
                    ctx.fillRect(Math.random() * canvas.width, Math.random() * canvas.height, 1, 10);
                }
            } else if (city.weather.includes('Туман')) {
                ctx.fillStyle = 'rgba(200, 200, 200, 0.2)';
                ctx.fillRect(0, 0, canvas.width, canvas.height);
            }
            
            // Draw ground
            ctx.fillStyle = '#3a3a3a';
            ctx.fillRect(0, CONFIG.GROUND_LEVEL, canvas.width, canvas.height - CONFIG.GROUND_LEVEL);
            
            // Ground texture
            ctx.strokeStyle = '#555';
            ctx.lineWidth = 1;
            for (let i = 0; i < canvas.width; i += 10) {
                ctx.beginPath();
                ctx.moveTo(i, CONFIG.GROUND_LEVEL);
                ctx.lineTo(i + 5, CONFIG.GROUND_LEVEL + 5);
                ctx.strokeStyle = '#555';
                ctx.stroke();
            }
            
            // Draw buildings
            city.buildings.forEach(building => {
                if (Math.abs(building.x - player.x) < 800) {
                    const floors = building.floors;
                    const height = floors * 50;
                    
                    // Building shadow
                    ctx.fillStyle = 'rgba(0,0,0,0.3)';
                    ctx.fillRect(building.x + 5, CONFIG.GROUND_LEVEL - height - 5, 80, height);
                    
                    // Building
                    ctx.fillStyle = building.color;
                    ctx.fillRect(building.x, CONFIG.GROUND_LEVEL - height, 80, height);
                    
                    // Windows
                    ctx.fillStyle = '#ffd700';
                    for (let floor = 0; floor < floors; floor++) {
                        for (let w = 0; w < 3; w++) {
                            if (Math.random() > 0.3) {
                                ctx.fillStyle = Math.random() > 0.7 ? '#ffd700' : '#2a2a2a';
                            }
                            ctx.fillRect(building.x + 10 + w * 20, 
                                        CONFIG.GROUND_LEVEL - (floor * 50) - 35, 
                                        15, 20);
                        }
                    }
                    
                    // Building name
                    ctx.fillStyle = '#fff';
                    ctx.font = 'bold 12px Courier New';
                    ctx.fillText(building.name.substring(0, 8), building.x + 5, CONFIG.GROUND_LEVEL - height - 10);
                    
                    // Door
                    ctx.fillStyle = '#5a3a2a';
                    ctx.fillRect(building.x + 30, CONFIG.GROUND_LEVEL - 45, 20, 45);
                }
            });
            
            // Draw NPCs
            if (player.activeCase) {
                player.activeCase.npcs.forEach(npc => {
                    if (npc.building === player.currentBuilding?.name && npc.floor === player.currentFloor) {
                        // NPC shadow
                        ctx.fillStyle = 'rgba(0,0,0,0.3)';
                        ctx.fillRect(npc.x - 5, npc.y + 45, 30, 5);
                        
                        // NPC body
                        ctx.fillStyle = npc.isKiller ? '#8b0000' : 
                                       (npc.trust > 70 ? '#2a8b2a' : '#8b4513');
                        ctx.fillRect(npc.x, npc.y, 20, 45);
                        
                        // Head
                        ctx.fillStyle = '#ffdbac';
                        ctx.fillRect(npc.x + 2, npc.y - 15, 16, 15);
                        
                        // Eyes
                        ctx.fillStyle = '#000';
                        ctx.fillRect(npc.x + 6, npc.y - 10, 3, 3);
                        ctx.fillRect(npc.x + 11, npc.y - 10, 3, 3);
                        
                        // Mood indicator
                        if (npc.mood === 'nervous') {
                            ctx.fillStyle = '#f00';
                            ctx.fillRect(npc.x + 20, npc.y - 20, 5, 5);
                        }
                        
                        // Name
                        ctx.fillStyle = '#fff';
                        ctx.font = '10px Courier New';
                        ctx.fillText(npc.name.split(' ')[0], npc.x - 5, npc.y - 25);
                    }
                });
                
                // Draw evidence
                player.activeCase.evidence.forEach(evidence => {
                    if (!evidence.found) {
                        // Glow effect
                        ctx.shadowColor = '#ffd700';
                        ctx.shadowBlur = 15;
                        
                        ctx.fillStyle = '#ffd700';
                        ctx.beginPath();
                        ctx.arc(evidence.x, evidence.y, 10, 0, Math.PI * 2);
                        ctx.fill();
                        
                        ctx.shadowBlur = 0;
                        
                        ctx.fillStyle = '#000';
                        ctx.font = 'bold 16px Courier New';
                        ctx.fillText('?', evidence.x - 6, evidence.y + 6);
                    }
                });
            }
            
            // Draw player
            // Shadow
            ctx.fillStyle = 'rgba(0,0,0,0.3)';
            ctx.fillRect(player.x - 5, player.y + 45, 30, 5);
            
            // Body
            ctx.fillStyle = '#8b4513';
            ctx.fillRect(player.x, player.y, player.width, player.height);
            
            // Jacket details
            ctx.fillStyle = '#6b3410';
            ctx.fillRect(player.x + 4, player.y + 10, 4, 20);
            ctx.fillRect(player.x + 16, player.y + 10, 4, 20);
            
            // Head
            ctx.fillStyle = '#ffdbac';
            ctx.fillRect(player.x + 4, player.y - 15, 16, 15);
            
            // Hat
            ctx.fillStyle = '#2a2a2a';
            ctx.fillRect(player.x - 2, player.y - 25, 28, 8);
            ctx.fillRect(player.x + 4, player.y - 30, 16, 5);
            
            // Eyes (direction)
            ctx.fillStyle = '#000';
            if (player.direction > 0) {
                ctx.fillRect(player.x + 8, player.y - 10, 3, 3);
                ctx.fillRect(player.x + 13, player.y - 10, 3, 3);
            } else {
                ctx.fillRect(player.x + 5, player.y - 10, 3, 3);
                ctx.fillRect(player.x + 10, player.y - 10, 3, 3);
            }
            
            // Walking animation
            if (player.isMoving) {
                player.animationFrame = (player.animationFrame + 0.2) % 2;
                if (player.animationFrame > 1) {
                    // Move legs
                }
            }
            
            // Update minimap
            renderMinimap();
            
            // Update time
            city.updateTime();
            updateUI();
        }

        function renderMinimap() {
            let minimapCanvas = document.getElementById('minimapCanvas');
            let miniCtx = minimapCanvas.getContext('2d');
            miniCtx.clearRect(0, 0, 200, 150);
            
            // Draw districts
            city.districts.forEach(district => {
                let x = (district.x / 1280) * 200;
                let width = (district.width / 1280) * 200;
                miniCtx.fillStyle = district.color;
                miniCtx.fillRect(x, 20, width, 30);
                
                miniCtx.strokeStyle = '#8b4513';
                miniCtx.strokeRect(x, 20, width, 30);
            });
            
            // Draw player
            let playerX = (player.x / 1280) * 200;
            miniCtx.fillStyle = '#ffd700';
            miniCtx.beginPath();
            miniCtx.arc(playerX, 35, 3, 0, Math.PI * 2);
            miniCtx.fill();
        }

        // ==================== ГЛАВНЫЙ ЦИКЛ ====================
        function gameLoop() {
            handleMovement();
            render();
            requestAnimationFrame(gameLoop);
        }
    </script>
</body>
</html>
