
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DANA Spin Simulasi V19.1 | Auto Hoki Bertingkat</title>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;500;600;700;800;900&family=Share+Tech+Mono&display=swap" rel="stylesheet">
    <style>
        /* --- General Reset & Layout --- */
        body {
            font-family: 'Share Tech Mono', monospace;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            background: linear-gradient(135deg, #0a0a0a 0%, #1a1a1a 100%);
            margin: 0;
            color: #E0E0E0;
            overflow-x: hidden;
            font-size: 14px;
        }
        .container {
            width: 95%;
            max-width: 380px;
            background-color: #1c1c1c;
            border-radius: 15px;
            box-shadow: 0 10px 25px rgba(0, 255, 0, 0.4), 0 0 15px rgba(0, 255, 0, 0.2) inset;
            overflow: hidden;
            transition: all 0.6s ease-in-out;
            border: 1px solid rgba(0, 255, 0, 0.5);
        }
        
        /* --- Custom Color Palette --- */
        :root {
            --neon-green: #39FF14;
            --text-light: #E0E0E0;
            --glow-shadow: 0 0 8px var(--neon-green), 0 0 12px var(--neon-green);
            --neon-blue: #00F0FF;
            --neon-magenta: #FF00FF;
            --gold: #FFD700;
        }

        /* --- UI Elements --- */
        .balance-info #saldo, .balance-info span[id$="-current-saldo"] {
            font-family: 'Orbitron', sans-serif;
            font-size: 3.0em;
            margin-top: 5px;
            color: var(--neon-green);
            text-shadow: 0 0 15px rgba(57, 255, 20, 0.8);
        }
        .spin-options {
            display: grid; 
            grid-template-columns: 1fr 1fr;
            gap: 15px;
            margin-top: 15px;
        }
        .spin-options .spin-hoki {
            grid-column: 1 / 3; 
        }
        .spin-options .spin-murah {
            background-color: #00BFFF;
            box-shadow: 0 0 8px #00BFFF, 0 0 12px #00BFFF;
            border-color: #00BFFF;
        }
        .transaction-button { padding: 14px 15px; border-radius: 8px; font-weight: bold; font-size: 1.1em; color: #0a0a0a; transition: all 0.3s ease; box-shadow: var(--glow-shadow); border: none; background-color: var(--neon-green); width: 100%; }
        .spin-options button { padding: 16px; font-size: 1.1em; border-radius: 12px; font-weight: 700; text-align: left; line-height: 1.4; color: #1a1a1a; box-shadow: var(--glow-shadow); border: 1px solid rgba(57, 255, 20, 0.4); position: relative; overflow: hidden; background-color: var(--neon-green); width: 100%; }
        #result { margin-top: 25px; font-weight: 700; min-height: 40px; font-size: 1.3em; padding: 8px; background-color: #2a2a2a; border-radius: 10px; box-shadow: inset 0 0 8px rgba(0, 255, 0, 0.3); color: var(--text-light); text-align: center; }
        .hidden { display: none !important; }
        .nav-tabs { display: flex; justify-content: space-around; background-color: #1a1a1a; padding: 10px 0; border-bottom: 2px solid var(--neon-green); }
        .nav-tabs button { flex-grow: 1; background: none; border: none; padding: 10px 5px; color: #aaa; font-weight: bold; font-family: 'Orbitron', sans-serif; transition: color 0.3s, background-color 0.3s; cursor: pointer; border-radius: 0; }
        .nav-tabs button.active { color: var(--neon-green); border-bottom: 3px solid var(--neon-green); background-color: #2a2a2a; }

        /* --- CSS Animasi Kemenangan --- */
        #animation-container {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 1000; 
            overflow: hidden;
        }
        .win-coin {
            position: absolute;
            width: 40px;
            height: 40px;
            background-color: var(--gold);
            border-radius: 50%;
            opacity: 0;
            transform: translate(-50%, -50%) scale(0.5);
            box-shadow: 0 0 10px var(--gold);
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 1.5em;
            color: #1a1a1a;
            font-weight: bold;
        }
        @keyframes flyToBalance {
            0% { 
                opacity: 1; 
                transform: translate(-50%, -50%) scale(1); 
                top: 50%; 
                left: 50%;
            }
            100% { 
                opacity: 0.5; 
                transform: translate(-50%, -50%) scale(0.2); 
                top: var(--target-top); 
                left: var(--target-left);
            }
        }

        /* --- Audio Control Button --- */
        #audio-control-container {
            position: fixed;
            top: 10px;
            right: 10px;
            z-index: 1001;
        }
        #audioControlButton {
            background-color: #333;
            color: var(--neon-green);
            border: 1px solid var(--neon-green);
            padding: 8px 12px;
            border-radius: 5px;
            cursor: pointer;
            font-size: 0.8em;
            font-weight: bold;
            box-shadow: 0 0 5px var(--neon-green);
        }
        #audioControlButton.active {
            background-color: var(--neon-green);
            color: #1a1a1a;
            box-shadow: 0 0 8px var(--neon-green);
        }

        /* --- Sisa CSS dari V17 (disederhanakan) --- */
        .balance-info { background: #282828; color: var(--neon-green); font-weight: bold; padding: 20px; border-radius: 12px; margin-bottom: 20px; box-shadow: var(--glow-shadow); border: 1px solid rgba(57, 255, 20, 0.4); }
        .page-content { padding: 20px; background-color: #1c1c1c; }
        .app-header { padding-bottom: 15px; margin-bottom: 15px; border-bottom: 1px dashed rgba(57, 255, 20, 0.3); }
        .app-header h2 { color: var(--neon-green); font-family: 'Orbitron', sans-serif; font-size: 1.6em; font-weight: 700; text-shadow: 0 0 10px var(--neon-green); }
        .welcome-user { font-size: 1.0em; color: var(--neon-blue); font-weight: bold; margin-bottom: 5px; }
        .transaction-form { margin-bottom: 20px; padding: 18px; border-radius: 12px; background-color: #2a2a2a; box-shadow: 0 4px 10px rgba(0, 0, 0, 0.3); border: 1px solid rgba(57, 255, 20, 0.2); }
        input { padding: 12px; margin-bottom: 15px; border-radius: 8px; font-size: 1.0em; background-color: #3a3a3a; color: var(--neon-green); border: 1px solid rgba(57, 255, 20, 0.3); text-align: center; box-shadow: inset 0 0 5px rgba(0, 255, 0, 0.1); width: calc(100% - 24px); }
        .package-container { margin-top: 25px; padding-top: 15px; border-top: 1px dashed rgba(57, 255, 20, 0.3); }
        .package-container h3 { color: var(--neon-blue); text-shadow: 0 0 5px var(--neon-blue); font-family: 'Orbitron', sans-serif; margin-bottom: 15px; }
        .package-options { display: grid; grid-template-columns: repeat(auto-fit, minmax(100px, 1fr)); gap: 10px; margin-bottom: 15px; }
        .package-options button { padding: 10px; font-size: 0.9em; font-weight: bold; background-color: #3a3a3a; color: var(--text-light); border: 1px solid #555; border-radius: 8px; transition: all 0.2s; }
        .package-options button.selected { background-color: var(--neon-blue); color: #1a1a1a; box-shadow: 0 0 10px var(--neon-blue); transform: scale(1.05); border-color: var(--neon-blue); }
        
        /* --- CSS BARU/MODIFIKASI UNTUK AUTO HOKI BERTINGKAT --- */
        .hoki-toggle-area { 
            display: block; /* Ubah dari flex */
            padding: 10px; 
            background: #2a2a2a; 
            border-radius: 8px; 
            margin-bottom: 15px; 
            border: 1px solid rgba(255, 0, 255, 0.3); 
        }
        .hoki-toggle-area h4 {
            color: var(--neon-magenta);
            text-shadow: 0 0 5px var(--neon-magenta);
            margin: 0 0 5px 0;
            font-family: 'Orbitron', sans-serif;
        }
        .hoki-options {
            display: flex;
            flex-direction: column; /* Ubah ke kolom untuk tata letak yang lebih rapi */
            gap: 8px;
            margin-top: 10px;
        }
        .hoki-options button {
            font-size: 0.85em;
            padding: 10px;
            background-color: #333;
            border: 1px solid rgba(255, 0, 255, 0.3);
            color: var(--text-light);
            box-shadow: none;
            transition: all 0.2s;
        }
        .hoki-options button.active {
            background-color: var(--neon-magenta);
            color: #1a1a1a;
            box-shadow: 0 0 8px var(--neon-magenta);
            transform: scale(1.02);
        }
        
        /* --- Progress & Messages --- */
        .progress-bar { background-color: #4a4a4a; border-radius: 5px; height: 20px; margin-top: 10px; overflow: hidden; }
        .progress { height: 100%; width: 0%; background-color: var(--neon-blue); transition: width 0.3s; text-align: center; color: #1a1a1a; font-size: 0.9em; font-weight: bold; line-height: 20px; }
        .success-message { color: var(--neon-green); text-shadow: 0 0 5px rgba(57, 255, 20, 0.5); }
        .error-message { color: #FF0057; text-shadow: 0 0 5px rgba(255, 0, 87, 0.5); }
        .warning-message { color: #FFFF00; text-shadow: 0 0 5px rgba(255, 255, 0, 0.5); }
        .info-message { color: #00BFFF; text-shadow: 0 0 5px rgba(0, 191, 255, 0.5); }
    </style>
</head>
<body>

<div id="animation-container"></div>

<div id="audio-control-container">
    <button id="audioControlButton" title="Klik untuk mengaktifkan/menonaktifkan suara">
        🔇 Suara Nonaktif
    </button>
</div>

<audio id="spinSound" src="https://assets.mixkit.co/sfx/preview/mixkit-casino-slot-machine-pull-3081.mp3" preload="auto"></audio>
<audio id="winSound" src="https://assets.mixkit.co/sfx/preview/mixkit-achievement-bell-600.mp3" preload="auto"></audio>
<audio id="loseSound" src="https://assets.mixkit.co/sfx/preview/mixkit-wrong-answer-bass-949.mp3" preload="auto"></audio>
<audio id="coinSound" src="https://assets.mixkit.co/sfx/preview/mixkit-game-bling-2007.mp3" preload="auto"></audio>


<div id="initial-screen" class="hidden">
    <div class="container registration-container">
        <div class="page-content">
            <div class="app-header">
                <h2>✨ <span style="color: var(--neon-green);">DANA Spin</span> Sim ⚡</h2>
                <p style="color: #ccc; font-size: 0.8em;">Daftar identitas Anda untuk memulai petualangan.</p>
            </div>
            <div class="registration-form">
                <h3>Daftar Pengguna Baru</h3>
                <input type="text" id="nameInput" placeholder="Masukkan Nama/ID Unik Anda" required>
                <button id="registerButton" class="transaction-button register-button">Mulai Sekarang & Ambil Bonus!</button>
            </div>
            <div id="registrationMessage" class="note">
                <span class="error-message">*Nama/ID tidak dapat diubah setelah terdaftar.</span>
                <p style="margin-top: 10px;">🎁 Bonus saldo awal untuk pengguna baru: **Rp 10.000**</p>
            </div>
        </div>
    </div>
</div>

<div class="container" id="main-app">
    
    <div class="nav-tabs" id="app-tabs">
        <button id="tab-balance" class="active" onclick="showPage('balance')">💰 Saldo</button>
        <button id="tab-topup" onclick="showPage('topup')">💳 Top Up</button>
        <button id="tab-withdraw" onclick="showPage('withdraw')">💸 Withdraw</button>
        <button id="tab-game" onclick="showPage('game')">🎰 Spin</button>
    </div>

    <div id="balance-page" class="page-content">
        <div class="app-header">
            <div class="welcome-user">Halo, <span id="display-username">Pengguna</span>!</div>
            <h2>Total Saldo Anda</h2>
        </div>
        <div class="balance-info" id="balance-box"> 
            Saldo Tersedia
            <span id="saldo">Rp 0</span>
        </div>
        
        <button id="changeUserButton" class="transaction-button change-user-button">
            🔄 Ganti Pengguna (Bayar Rp 100.000)
        </button>

        <div id="transactionMessage" class="note"></div>
    </div>
    
    <div id="topup-page" class="page-content hidden">
        <div class="app-header">
            <h2>Isi Saldo (Top Up)</h2>
        </div>
        
        <div class="balance-info" style="font-size: 1.1em; padding: 12px; margin-bottom: 15px;">
            Saldo Anda: <span id="topup-current-saldo">Rp 0</span>
        </div>

        <div class="transaction-form">
            <h3>💳 Masukkan Detail Top Up</h3>
            <input type="text" id="topUpCodeInput" placeholder="Masukkan Kode Rahasia" required>
            <input type="number" id="topUpAmountInput" placeholder="Nominal Top Up (cth: 100000)" required>
            <button id="topUpButton" class="transaction-button topup-button">Tambah Saldo</button>
        </div>
        
        <div id="topupMessage" class="note"></div>
    </div>
    
    <div id="withdraw-page" class="page-content hidden">
        <div class="app-header">
            <h2>Tarik Saldo (Withdraw)</h2>
        </div>
        
        <div class="balance-info" style="font-size: 1.1em; padding: 12px; margin-bottom: 15px;">
            Saldo Anda: <span id="withdraw-current-saldo">Rp 0</span>
        </div>

        <div class="transaction-form">
            <h3>💸 Masukkan Detail Penarikan</h3>
            <input type="text" id="withdrawCodeInput" placeholder="Masukkan Kode Rahasia" required>
            <input type="number" id="withdrawAmountInput" placeholder="Nominal Tarik Saldo (cth: 50000)" required>
            <button id="withdrawButton" class="transaction-button withdraw-button">Tarik Saldo</button>
        </div>
        
        <div id="withdrawMessage" class="note"></div>
    </div>

    <div id="game-page" class="page-content hidden">
        <div class="app-header">
             <div class="welcome-user">Halo, <span id="game-display-username">Pengguna</span>!</div>
            <h2>Putar Roda Keberuntungan!</h2>
        </div>
       
        <div class="balance-info" style="font-size: 1.1em; padding: 12px; margin-bottom: 15px;">
            Saldo Anda: <span id="game-current-saldo">Rp 0</span>
        </div>
        
        <div id="beginnerModeStatus" class="beginner-status hidden">
            ✨ **MODE PEMULA AKTIF!** Peluang Menang **70%** (Sisa: <span id="beginnerStatus">0/5</span> Putaran)
        </div>

        <div class="spin-options">
            <button id="spinButtonMurah" class="spin-murah" data-cost="1000" data-win-amount="2000" data-chance="0.20">
                Putar Murah <br>
                <span class="cost">Biaya: Rp 1.000 | Hadiah: Rp 2.000 (20% Win)</span>
            </button>

            <button id="spinButtonStandard" data-cost="10000" data-win-amount="20000" data-chance="0.20">
                Putar Standard <br>
                <span class="cost">Biaya: Rp 10.000 | Hadiah: Rp 20.000 (20% Win)</span>
            </button>
            
            <button id="spinButtonHoki" class="spin-hoki" data-cost="50000" data-win-amount="70000"> 
                ✨ HOKI SPIN! (70% Win) <br>
                <span class="cost">Biaya: Rp 50.000 | Hadiah: Rp 70.000</span>
            </button>
        </div>

        <div class="package-container">
            <h3>🚀 Paket Spin Kilat (30% Win Rate)</h3>
            <p class="note" style="text-align: left; border: none; margin-bottom: 15px;">Beli paket spin dan dapatkan hadiah acak nominal kecil!</p>

            <div class="package-options" id="spinPackageOptions">
                </div>
            
            <div class="hoki-toggle-area">
                <h4>✨ Mode Auto Hoki Bertingkat</h4>
                <p style="font-size: 0.8em; color: #ccc; margin-bottom: 5px;">Peluang menang ini berlaku untuk *Spin Kilat*.</p>
                <div class="hoki-options" id="hokiOptionsContainer">
                    <button id="hoki-max" data-chance="0.60" data-cost="1200000">MAX HOKI (60% Win) - Rp 1000.000</button>
                    <button id="hoki-mid" data-chance="0.30" data-cost="50000">MID HOKI (30% Win) - Rp 50.000</button>
                    <button id="hoki-low" data-chance="0.15" data-cost="10000">LOW HOKI (15% Win) - Rp 10.000</button>
                </div>
            </div>
            <button id="buyPackageButton" class="transaction-button buy-package-button" disabled>
                Beli Paket
            </button>
            
            <div id="packageResultDisplay" class="note"></div>
            <div class="progress-bar hidden" id="packageProgressBar">
                <div class="progress" id="packageProgress"></div>
            </div>
        </div>

        <div id="result"></div>
        <div class="note">
            *Kemenangan Standard didapat jika angka yang keluar adalah **1, 5, 8, atau 10**.
        </div>
    </div>
</div>

<script>
    // --- KONFIGURASI APLIKASI ---
    let balance = 0; 
    const SECRET_CODE = "110611"; 
    const MAX_BEGINNER_SPINS = 30;
    const NEW_USER_BONUS = 10000; 
    const WINNING_NUMBERS = [1, 5, 8, 10]; 
    const CHANGE_USER_FEE = 100000; 

    // --- KONFIGURASI SPIN KILAT ---
    const SPIN_PACKAGES = [
        { price: 10000, spins: 10 },
        { price: 20000, spins: 20 },
        { price: 50000, spins: 50 },
        { price: 100000, spins: 100 },
        { price: 300000, spins: 300 },
        { price: 500000, spins: 500 },
        { price: 1000000, spins: 1000 },
    ];
    
    // Daftar Hadiah Receh
    const RANDOM_PRIZES = [500, 1000, 1500, 2000]; 
    // Daftar Hadiah Besar sesuai permintaan (10k, 20k, 12k, 5k, 8k, 7k)
    const BIG_PRIZES = [10000, 5000, 12000, 8000, 7000]; 
    
    const KILAT_CHANCE = 0.30; 
    const BIG_PRIZE_CHANCE_IN_WIN = 0.30; // 30% dari kemenangan akan mendapatkan hadiah besar
    
    // --- KONFIGURASI BARU AUTO HOKI BERTINGKAT ---
    const AUTO_HOKI_MODES = {
        'max': { chance: 0.60, cost: 900000 },
        'mid': { chance: 0.30, cost: 150000 },
        'low': { chance: 0.15, cost: 50000 }
    };

    // --- VARIABEL PERSISTENSI & STATUS PENGGUNA ---
    let userName = '';
    let userRegistered = false;
    let spinCount = 0;
    let newUserSettingsActive = false; 
    let activeHokiMode = null; // Menyimpan mode Hoki yang aktif ('max', 'mid', 'low', atau null)
    let selectedPackagePrice = 0; 
    let isAudioActive = false;

    // --- ELEMEN DOM ---
    const saldoDisplayElements = document.querySelectorAll('#saldo, #game-current-saldo, #topup-current-saldo, #withdraw-current-saldo');
    const balanceBox = document.getElementById('balance-box'); 
    const animationContainer = document.getElementById('animation-container'); 
    
    // --- ELEMEN AUDIO ---
    const spinAudio = document.getElementById('spinSound');
    const winAudio = document.getElementById('winSound');
    const loseAudio = document.getElementById('loseSound');
    const coinAudio = document.getElementById('coinSound');
    const audioControlButton = document.getElementById('audioControlButton');

    // Atur volume default
    winAudio.volume = 0.4;
    loseAudio.volume = 0.3;
    coinAudio.volume = 0.1;
    spinAudio.volume = 0.2;


    // --- FUNGSI UTILITAS & UI ---
    function formatRupiah(number) {
        return 'Rp ' + Math.round(number).toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".");
    }

    function playSound(audioElement) {
        if (!isAudioActive) return;
        audioElement.currentTime = 0;
        audioElement.play().catch(e => console.warn("Audio Playback Error:", e)); 
    }

    function updateBalanceDisplay() {
        const spinButtonMurah = document.getElementById('spinButtonMurah');
        const spinButtonStandard = document.getElementById('spinButtonStandard');
        const spinButtonHoki = document.getElementById('spinButtonHoki');
        const displayUsernameElements = document.querySelectorAll('#display-username, #game-display-username');
        const buyPackageButton = document.getElementById('buyPackageButton');


        saldoDisplayElements.forEach(el => {
            el.textContent = formatRupiah(balance);
        });

        displayUsernameElements.forEach(el => {
            el.textContent = userName;
        });

        const costMurah = parseInt(spinButtonMurah.getAttribute('data-cost'));
        const costStandard = parseInt(spinButtonStandard.getAttribute('data-cost'));
        const costHoki = parseInt(spinButtonHoki.getAttribute('data-cost'));

        spinButtonMurah.disabled = balance < costMurah;
        spinButtonStandard.disabled = balance < costStandard;
        spinButtonHoki.disabled = balance < costHoki;
        document.getElementById('changeUserButton').disabled = balance < CHANGE_USER_FEE;
        
        // --- LOGIKA BARU UNTUK AUTO HOKI BERTINGKAT ---
        const hokiCost = activeHokiMode ? AUTO_HOKI_MODES[activeHokiMode].cost : 0;
        const costWithHoki = selectedPackagePrice + hokiCost;

        buyPackageButton.disabled = selectedPackagePrice === 0 || balance < costWithHoki;
        buyPackageButton.textContent = selectedPackagePrice > 0 ? `Beli Paket (${formatRupiah(costWithHoki)})` : 'Pilih Paket Dulu';

        // Update status tombol Hoki
        document.querySelectorAll('#hokiOptionsContainer button').forEach(button => {
            const mode = button.id.split('-')[1];
            const cost = AUTO_HOKI_MODES[mode].cost;
            
            button.disabled = balance < cost && mode !== activeHokiMode;
            
            if (activeHokiMode === mode) {
                button.classList.add('active');
                button.textContent = button.textContent.split(' - ')[0] + ' - AKTIF ✅';
            } else {
                button.classList.remove('active');
                button.textContent = button.textContent.split(' - ')[0] + ` - ${formatRupiah(cost)}`;
            }
        });
        
        updateSpinButtonLabels();
    }
    
    function animateWin(amount) {
        if (amount <= 0) return;

        let numCoins = Math.min(10, Math.ceil(amount / 5000)); 
        if (amount >= 10000) {
            numCoins = Math.min(20, Math.ceil(amount / 5000));
        }

        const rect = balanceBox.getBoundingClientRect();

        const targetX = rect.left + rect.width / 2;
        const targetY = rect.top + rect.height / 2;

        for (let i = 0; i < numCoins; i++) {
            const coin = document.createElement('div');
            coin.className = 'win-coin';
            coin.innerHTML = '💸'; 

            const startX = window.innerWidth / 2 + (Math.random() - 0.5) * 50; 
            const startY = window.innerHeight / 2 + (Math.random() - 0.5) * 50; 

            coin.style.left = `${startX}px`;
            coin.style.top = `${startY}px`;
            
            coin.style.setProperty('--target-top', `${targetY}px`);
            coin.style.setProperty('--target-left', `${targetX}px`);

            coin.style.animation = `flyToBalance 0.8s ease-in-out ${i * 0.05}s forwards`; 
            
            animationContainer.appendChild(coin);

            setTimeout(() => {
                playSound(coinAudio);
            }, i * 50);

            setTimeout(() => {
                coin.remove();
            }, 800 + i * 50);
        }
    }
    
    function toggleAudio() {
        isAudioActive = !isAudioActive;
        if (isAudioActive) {
            audioControlButton.textContent = '🔊 Suara Aktif';
            audioControlButton.classList.add('active');
            playSound(winAudio);
        } else {
            audioControlButton.textContent = '🔇 Suara Nonaktif';
            audioControlButton.classList.remove('active');
            spinAudio.pause(); spinAudio.currentTime = 0;
            winAudio.pause(); winAudio.currentTime = 0;
            loseAudio.pause(); loseAudio.currentTime = 0;
            coinAudio.pause(); coinAudio.currentTime = 0;
        }
        localStorage.setItem('isAudioActive', isAudioActive);
    }


    // --- FUNGSI GAME SPIN SATUAN ---
    function handleSpin(cost, winAmount, buttonId) {
        const spinButtonMurah = document.getElementById('spinButtonMurah');
        const spinButtonStandard = document.getElementById('spinButtonStandard');
        const spinButtonHoki = document.getElementById('spinButtonHoki');
        const resultDisplay = document.getElementById('result');

        if (balance < cost) {
             resultDisplay.innerHTML = '<span class="error-message">Saldo tidak cukup!</span>';
            playSound(loseAudio);
            return;
        }
        
        const isHokiSpin = (buttonId === 'spinButtonHoki');
        const spinResult = Math.floor(Math.random() * 10) + 1;
        const isWinning = checkWin(spinResult, isHokiSpin); 
        const isBeginnerMode = newUserSettingsActive;

        balance -= cost;
        updateBalanceDisplay();
        saveState(); 

        resultDisplay.innerHTML = '<span class="info-message">Memutar Roda Keberuntungan...</span>';
        
        playSound(spinAudio);

        spinButtonMurah.disabled = true;
        spinButtonStandard.disabled = true;
        spinButtonHoki.disabled = true;
        
        setTimeout(() => {
            let resultMessage = '';

            if (isWinning) {
                balance += winAmount; 
                animateWin(winAmount); 
                playSound(winAudio); 
                resultMessage = `<span class="success-message">🎉 SELAMAT, KAMU MENANG!</span> <br>Angka keluar: **${spinResult}**. Saldo bertambah ${formatRupiah(winAmount)}.`;
            } else {
                playSound(loseAudio);
                resultMessage = `<span class="warning-message">😭 ANDA KURANG BERUNTUNG.</span> <br>Angka keluar: **${spinResult}**. Biaya spin ${formatRupiah(cost)} sudah terpotong.`;
            }

            if (isBeginnerMode && !isHokiSpin) { 
                spinCount++;
                if (spinCount >= MAX_BEGINNER_SPINS) {
                    newUserSettingsActive = false;
                    resultMessage += `<br><span class="info-message" style="font-size: 0.9em;">Mode Pemula Selesai. Peluang kembali ke mode Angka Kemenangan!</span>`;
                }
            }
            
            resultDisplay.innerHTML = resultMessage;
            
            setTimeout(() => {
                updateBalanceDisplay();
                saveState(); 
                spinButtonMurah.disabled = balance < parseInt(spinButtonMurah.getAttribute('data-cost'));
                spinButtonStandard.disabled = balance < parseInt(spinButtonStandard.getAttribute('data-cost'));
                spinButtonHoki.disabled = balance < parseInt(spinButtonHoki.getAttribute('data-cost'));
            }, 800);
            
        }, 2000); 
    }

    // --- FUNGSI PAKET SPIN KILAT ---
    
    async function handleBuySpinPackage() {
        const packageResultDisplay = document.getElementById('packageResultDisplay');
        const packageProgressBar = document.getElementById('packageProgressBar');
        const packageProgress = document.getElementById('packageProgress');
        const buyPackageButton = document.getElementById('buyPackageButton');
        const spinButtonMurah = document.getElementById('spinButtonMurah');
        const spinButtonStandard = document.getElementById('spinButtonStandard');
        const spinButtonHoki = document.getElementById('spinButtonHoki');

        if (selectedPackagePrice === 0) {
            showSpecificMessage(packageResultDisplay, '❌ Silakan pilih paket spin terlebih dahulu.', 'error');
            return;
        }
        
        const packageInfo = SPIN_PACKAGES.find(p => p.price === selectedPackagePrice);
        const hokiCost = activeHokiMode ? AUTO_HOKI_MODES[activeHokiMode].cost : 0;
        const totalCost = packageInfo.price + hokiCost;

        if (balance < totalCost) {
            showSpecificMessage(packageResultDisplay, `❌ Saldo tidak cukup! Total biaya ${formatRupiah(totalCost)}.`, 'error');
            playSound(loseAudio);
            return;
        }
        
        // --- TRANSAKSI AWAL ---
        balance -= totalCost;
        saveState();
        updateBalanceDisplay();
        
        let totalWinnings = 0;
        let bigWinCount = 0;
        let smallWinCount = 0;
        
        // Menonaktifkan semua kontrol saat spin berjalan
        buyPackageButton.disabled = true;
        spinButtonMurah.disabled = true; 
        spinButtonStandard.disabled = true;
        spinButtonHoki.disabled = true;
        document.querySelectorAll('#hokiOptionsContainer button').forEach(btn => btn.disabled = true);
        
        packageProgressBar.classList.remove('hidden');
        packageResultDisplay.innerHTML = `<span class="info-message">Memulai ${packageInfo.spins} putaran. Tunggu...</span>`;
        
        playSound(spinAudio); 

        // --- LOOP SPIN KILAT ---
        for (let i = 1; i <= packageInfo.spins; i++) {
            const winAmount = await handleRandomSpin(activeHokiMode);
            totalWinnings += winAmount;
            
            if (winAmount > 2000) { // Cek jika hadiah besar
                bigWinCount++;
            } else if (winAmount > 0) {
                smallWinCount++;
            }
            
            const percentage = (i / packageInfo.spins) * 100;
            packageProgress.style.width = percentage + '%';
            packageProgress.textContent = `${i}/${packageInfo.spins} Spin`;

            balance += winAmount;
            if (winAmount > 0) {
                animateWin(winAmount); 
            }
            saldoDisplayElements.forEach(el => el.textContent = formatRupiah(balance));

            await new Promise(resolve => setTimeout(resolve, 50)); 
        }

        // --- TRANSAKSI AKHIR ---
        packageProgressBar.classList.add('hidden');
        saveState(); 
        
        const profit = totalWinnings - packageInfo.price;
        let finalMessage = '';
        
        if (profit >= 0) {
            finalMessage = `<span class="success-message">✅ PAKET SELESAI!</span> Total menang: ${formatRupiah(totalWinnings)}. **Profit:** ${formatRupiah(profit)}.`;
            playSound(winAudio); 
        } else {
            finalMessage = `<span class="warning-message">⚠️ PAKET SELESAI.</span> Total menang: ${formatRupiah(totalWinnings)}. **Rugi:** ${formatRupiah(Math.abs(profit))}.`;
            playSound(loseAudio); 
        }
        
        if (activeHokiMode) {
            finalMessage += `<br><span class="info-message" style="font-size: 0.8em;">(Mode Hoki **${activeHokiMode.toUpperCase()}** Aktif, Biaya: ${formatRupiah(hokiCost)} sudah terpotong)</span>`;
            finalMessage += `<br><span class="info-message" style="font-size: 0.8em;">Big Win (${formatRupiah(10000)}+) : ${bigWinCount}x. Small Win : ${smallWinCount}x.</span>`;
        }
        
        packageResultDisplay.innerHTML = finalMessage;
        
        // Reset status
        activeHokiMode = null; 
        selectedPackagePrice = 0;
        renderSpinPackages();
        updateBalanceDisplay(); 
    }
    
    // FUNGSI SPIN BARU DENGAN LOGIKA HADIAH BERTINGKAT
    function handleRandomSpin(activeMode) {
        return new Promise(resolve => {
            
            let chance = KILAT_CHANCE; // Default 30%

            if (activeMode) {
                chance = AUTO_HOKI_MODES[activeMode].chance; // Ganti dengan peluang mode aktif
            }

            let winAmount = 0;
            const isWinning = Math.random() < chance;

            if (isWinning) {
                if (activeMode && Math.random() < BIG_PRIZE_CHANCE_IN_WIN) {
                    // 30% dari kemenangan (ketika Auto Hoki aktif) akan mendapatkan Hadiah Besar
                    const randomIndex = Math.floor(Math.random() * BIG_PRIZES.length);
                    winAmount = BIG_PRIZES[randomIndex];
                } else {
                    // Sisanya (atau 100% jika tidak Auto Hoki) akan mendapatkan Hadiah Receh
                    const randomIndex = Math.floor(Math.random() * RANDOM_PRIZES.length);
                    winAmount = RANDOM_PRIZES[randomIndex];
                }
            } 
            
            resolve(winAmount);
        });
    }

    // --- FUNGSI PERSISTENSI & INIT ---
    function loadState() {
        const savedState = localStorage.getItem('danaSpinV7State'); 
        if (savedState) {
            try {
                const state = JSON.parse(savedState);
                balance = state.balance || 0;
                userName = state.userName || '';
                userRegistered = state.userRegistered || false;
                spinCount = state.spinCount || 0;
                newUserSettingsActive = state.newUserSettingsActive || false;
            } catch (e) {
                console.error("Gagal memuat state dari localStorage:", e);
                localStorage.removeItem('danaSpinV7State');
            }
        }
        const savedAudioStatus = localStorage.getItem('isAudioActive');
        isAudioActive = savedAudioStatus === 'true';
    }

    function saveState() {
        const state = {
            balance: balance,
            userName: userName,
            userRegistered: userRegistered,
            spinCount: spinCount,
            newUserSettingsActive: newUserSettingsActive,
        };
        localStorage.setItem('danaSpinV7State', JSON.stringify(state));
        localStorage.setItem('isAudioActive', isAudioActive);
    }
    
    function showPage(pageId) {
        document.getElementById('balance-page').classList.add('hidden');
        document.getElementById('game-page').classList.add('hidden');
        document.getElementById('topup-page').classList.add('hidden'); 
        document.getElementById('withdraw-page').classList.add('hidden'); 

        document.querySelectorAll('.nav-tabs button').forEach(btn => {
            btn.classList.remove('active');
        });

        document.getElementById(pageId + '-page').classList.remove('hidden');
        document.getElementById('tab-' + pageId).classList.add('active');

        updateBalanceDisplay();
    }
    
    function showSpecificMessage(messageElement, message, type = 'info') {
        let className;
        if (type === 'success') className = 'success-message';
        else if (type === 'error') className = 'error-message';
        else if (type === 'warning') className = 'warning-message';
        else className = 'info-message';

        messageElement.innerHTML = `<span class="${className}">${message}</span>`;
        setTimeout(() => messageElement.textContent = '', 4000);
    }

    function updateSpinButtonLabels() {
        const beginnerModeStatus = document.getElementById('beginnerModeStatus');
        const beginnerStatusCounter = document.getElementById('beginnerStatus');
        if (newUserSettingsActive) {
            beginnerModeStatus.classList.remove('hidden');
            beginnerStatusCounter.textContent = `${spinCount}/${MAX_BEGINNER_SPINS}`;
        } else {
            beginnerModeStatus.classList.add('hidden');
        }
    }
    
    function checkWin(spinResult, isHokiSpin) {
        if (isHokiSpin) {
            return Math.random() < 0.70; 
        } 
        if (newUserSettingsActive) {
            return Math.random() < 0.70; 
        }
        return WINNING_NUMBERS.includes(spinResult);
    }

    function renderSpinPackages() {
        const spinPackageOptions = document.getElementById('spinPackageOptions');
        spinPackageOptions.innerHTML = '';
        SPIN_PACKAGES.forEach(pkg => {
            const button = document.createElement('button');
            button.textContent = `${formatRupiah(pkg.price)} (${pkg.spins}x)`;
            button.setAttribute('data-price', pkg.price);
            button.setAttribute('data-spins', pkg.spins);
            if (pkg.price === selectedPackagePrice) {
                 button.classList.add('selected');
            }
            button.addEventListener('click', () => selectSpinPackage(pkg.price));
            spinPackageOptions.appendChild(button);
        });
    }

    function selectSpinPackage(price) {
        selectedPackagePrice = price;
        renderSpinPackages(); 
        updateBalanceDisplay();
    }
    
    function toggleHokiFeature(mode) {
        const packageResultDisplay = document.getElementById('packageResultDisplay');

        if (activeHokiMode === mode) {
            // Menonaktifkan jika sudah aktif
            activeHokiMode = null;
            showSpecificMessage(packageResultDisplay, `⛔ Auto Hoki **${mode.toUpperCase()}** DINONAKTIFKAN.`, 'info');
        } else {
            // Mengaktifkan mode baru
            const cost = AUTO_HOKI_MODES[mode].cost;
            if (balance < cost) { 
                showSpecificMessage(packageResultDisplay, `❌ Saldo tidak cukup untuk mengaktifkan Auto Hoki **${mode.toUpperCase()}** (Bayar ${formatRupiah(cost)}).`, 'error');
                return;
            }
            activeHokiMode = mode;
            showSpecificMessage(packageResultDisplay, `✅ Auto Hoki **${mode.toUpperCase()}** AKTIF! Tambahkan ${formatRupiah(cost)} ke biaya paket.`, 'success');
        }
        updateBalanceDisplay();
    }

    function handleRegistration() {
        const inputName = document.getElementById('nameInput').value.trim();
        const registrationMessage = document.getElementById('registrationMessage');
        const initialScreen = document.getElementById('initial-screen');
        const mainApp = document.getElementById('main-app');


        if (inputName.length < 3) {
            registrationMessage.innerHTML = '<span class="error-message">Nama minimal 3 karakter.</span>';
            playSound(loseAudio);
            return;
        }

        userName = inputName;
        userRegistered = true;
        balance = NEW_USER_BONUS; 
        newUserSettingsActive = true;
        spinCount = 0;

        saveState();
        playSound(winAudio);

        registrationMessage.innerHTML = `<span class="success-message">Halo, ${userName}! Saldo awal ${formatRupiah(NEW_USER_BONUS)} sudah ditambahkan. Mode pemula (70% Win Rate) aktif untuk ${MAX_BEGINNER_SPINS} putaran.</span>`;

        initialScreen.classList.add('hidden');
        mainApp.classList.remove('hidden');
        
        updateBalanceDisplay();
        showPage('balance');
        updateSpinButtonLabels();
    }

    function handleChangeUser() {
        const transactionMessage = document.getElementById('transactionMessage');
        const initialScreen = document.getElementById('initial-screen');
        const mainApp = document.getElementById('main-app');

        if (balance < CHANGE_USER_FEE) {
            showSpecificMessage(transactionMessage, `❌ Saldo tidak cukup untuk ganti pengguna. Dibutuhkan ${formatRupiah(CHANGE_USER_FEE)}.`, 'error');
            playSound(loseAudio);
            return;
        }

        if (confirm(`Apakah Anda yakin ingin mengganti pengguna? Anda akan dikenakan biaya ${formatRupiah(CHANGE_USER_FEE)} dan semua data pengguna saat ini akan direset.`)) {
            balance -= CHANGE_USER_FEE; 
            
            userName = '';
            userRegistered = false;
            spinCount = 0;
            newUserSettingsActive = false;
            activeHokiMode = null; // Reset Hoki mode
            selectedPackagePrice = 0; // Reset selected package

            saveState(); 
            playSound(loseAudio);

            showSpecificMessage(transactionMessage, `✅ Berhasil! Biaya ${formatRupiah(CHANGE_USER_FEE)} terpotong. Silakan daftar sebagai pengguna baru.`, 'success');
            
            mainApp.classList.add('hidden');
            initialScreen.classList.remove('hidden');

            document.getElementById('nameInput').value = '';
            document.getElementById('registrationMessage').textContent = ''; 
            
            updateBalanceDisplay();
            showPage('balance'); 
            updateSpinButtonLabels();
        } else {
            showSpecificMessage(transactionMessage, 'Pembatalan ganti pengguna.', 'info');
        }
    }

    function handleTopUp() {
        const topUpCodeInput = document.getElementById('topUpCodeInput');
        const topUpAmountInput = document.getElementById('topUpAmountInput');
        const topupMessage = document.getElementById('topupMessage');
        const code = topUpCodeInput.value.trim();
        const amount = parseInt(topUpAmountInput.value);
        
        if (code !== SECRET_CODE) {
            showSpecificMessage(topupMessage, '❌ Kode Rahasia Salah!', 'error'); 
            playSound(loseAudio);
            return;
        }

        if (isNaN(amount) || amount <= 0) {
            showSpecificMessage(topupMessage, '❌ Masukkan nominal Top Up yang valid!', 'error'); 
            playSound(loseAudio);
            return;
        }
        
        balance += amount;
        updateBalanceDisplay();
        saveState();
        showSpecificMessage(topupMessage, `✅ Top Up Berhasil! Saldo bertambah ${formatRupiah(amount)}.`, 'success'); 
        playSound(winAudio); 
        topUpCodeInput.value = '';
        topUpAmountInput.value = '';
    }

    function handleWithdraw() {
        const withdrawCodeInput = document.getElementById('withdrawCodeInput');
        const withdrawAmountInput = document.getElementById('withdrawAmountInput');
        const withdrawMessage = document.getElementById('withdrawMessage');
        const code = withdrawCodeInput.value.trim();
        const amount = parseInt(withdrawAmountInput.value);

        if (code !== SECRET_CODE) {
            showSpecificMessage(withdrawMessage, '❌ Kode Rahasia Salah!', 'error'); 
            playSound(loseAudio);
            return;
        }

        if (isNaN(amount) || amount <= 0) {
            showSpecificMessage(withdrawMessage, '❌ Masukkan nominal Tarik Saldo yang valid!', 'error'); 
            playSound(loseAudio);
            return;
        }
        
        if (amount > balance) {
            showSpecificMessage(withdrawMessage, '❌ Saldo Anda tidak cukup untuk penarikan ini!', 'error'); 
            playSound(loseAudio);
            return;
        }

        balance -= amount;
        updateBalanceDisplay();
        saveState();
        showSpecificMessage(withdrawMessage, `✅ Tarik Saldo Berhasil! Saldo berkurang ${formatRupiah(amount)}.`, 'success'); 
        playSound(loseAudio); 
        withdrawCodeInput.value = '';
        withdrawAmountInput.value = '';
    }

    // --- INISIALISASI ---
    function initializeApp() {
        loadState();
        renderSpinPackages();
        
        // Atur status Audio saat startup
        if (isAudioActive) {
            audioControlButton.textContent = '🔊 Suara Aktif';
            audioControlButton.classList.add('active');
        } else {
            audioControlButton.textContent = '🔇 Suara Nonaktif';
            audioControlButton.classList.remove('active');
        }
        
        if (userRegistered) {
            document.getElementById('initial-screen').classList.add('hidden');
            document.getElementById('main-app').classList.remove('hidden');
            updateBalanceDisplay();
            updateSpinButtonLabels();
            showPage('balance'); 
        } else {
            document.getElementById('initial-screen').classList.remove('hidden');
            document.getElementById('main-app').classList.add('hidden');
        }

        // Event Listeners Audio Control
        audioControlButton.addEventListener('click', toggleAudio);

        // Event Listeners untuk Spin Satuan
        document.getElementById('spinButtonMurah').addEventListener('click', () => { 
            const cost = parseInt(document.getElementById('spinButtonMurah').getAttribute('data-cost'));
            const winAmount = parseInt(document.getElementById('spinButtonMurah').getAttribute('data-win-amount'));
            handleSpin(cost, winAmount, 'spinButtonMurah'); 
        });

        document.getElementById('spinButtonStandard').addEventListener('click', () => {
            const cost = parseInt(document.getElementById('spinButtonStandard').getAttribute('data-cost'));
            const winAmount = parseInt(document.getElementById('spinButtonStandard').getAttribute('data-win-amount'));
            handleSpin(cost, winAmount, 'spinButtonStandard'); 
        });

        document.getElementById('spinButtonHoki').addEventListener('click', () => {
            const cost = parseInt(document.getElementById('spinButtonHoki').getAttribute('data-cost'));
            const winAmount = parseInt(document.getElementById('spinButtonHoki').getAttribute('data-win-amount'));
            handleSpin(cost, winAmount, 'spinButtonHoki'); 
        });

        // Event Listeners Fitur Paket
        document.getElementById('buyPackageButton').addEventListener('click', handleBuySpinPackage);
        
        // Event Listeners untuk Auto Hoki Bertingkat
        document.querySelectorAll('#hokiOptionsContainer button').forEach(button => {
            button.addEventListener('click', function() {
                const mode = this.id.split('-')[1];
                toggleHokiFeature(mode);
            });
        });

        // Event Listeners untuk Transaksi & Registrasi
        document.getElementById('topUpButton').addEventListener('click', handleTopUp);
        document.getElementById('withdrawButton').addEventListener('click', handleWithdraw);
        document.getElementById('registerButton').addEventListener('click', handleRegistration);
        document.getElementById('changeUserButton').addEventListener('click', handleChangeUser); 
        
        // Event Listeners untuk navigasi tab
        document.querySelectorAll('.nav-tabs button').forEach(button => {
            button.addEventListener('click', function() {
                const pageId = this.id.split('-')[1];
                showPage(pageId);
            });
        });
    }

    initializeApp();
</script>

</body>
</html>
