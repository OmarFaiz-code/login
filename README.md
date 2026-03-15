<?php
session_start();


if (!isset($_SESSION['users'])) {
    $_SESSION['users'] = [
        ['nama' => 'Admin Hiker', 'email' => 'admin@hiker.com', 'password' => md5('admin123'), 'role' => 'admin'],
        ['nama' => 'Pendaki Sejati', 'email' => 'pendaki@hiker.com', 'password' => md5('123'), 'role' => 'user'],
    ];
}

$error = '';

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $email    = trim($_POST['email'] ?? '');
    $password = trim($_POST['password'] ?? '');

    if (empty($email) || empty($password)) {
        $error = 'Email dan kata sandi wajib diisi!';
    } else {
        $found = false;
        foreach ($_SESSION['users'] as $user) {
            if ($user['email'] === $email && $user['password'] === md5($password)) {
                $_SESSION['logged_in'] = true;
                $_SESSION['user_nama'] = $user['nama'];
                $_SESSION['user_email'] = $user['email'];
                $_SESSION['user_role'] = $user['role'];
                $found = true;
                break;
            }
        }

        if ($found) {
            header('Location: destinasi.php');
            exit;
        } else {
            $error = 'Email atau kata sandi salah!';
        }
    }
}
?>
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Login — Hiker Best</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@700;900&family=Lato:wght@300;400;700&display=swap" rel="stylesheet">
    <style>
        *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

        :root {
            --green-dark: #1a4731;
            --green-mid:  #2f8f4a;
            --green-light:#4ab868;
            --cream:      #f5f0e8;
            --gold:       #c9a84c;
            --text-dark:  #1a1a1a;
        }

        body {
            min-height: 100vh;
            font-family: 'Lato', sans-serif;
            background: var(--green-dark);
            display: flex;
            align-items: center;
            justify-content: center;
            overflow: hidden;
            position: relative;
        }

        /* Layered mountain silhouette background */
        body::before {
            content: '';
            position: fixed; inset: 0;
            background:
                linear-gradient(180deg, #0d2a1c 0%, #1a4731 40%, #2f6b45 100%);
            z-index: 0;
        }
        body::after {
            content: '';
            position: fixed; bottom: 0; left: 0; right: 0; height: 55vh;
            background:
                url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 1440 400'%3E%3Cpath fill='%230d2a1c' opacity='0.7' d='M0,400 L0,250 L180,80 L360,200 L540,50 L720,180 L900,30 L1080,160 L1260,90 L1440,200 L1440,400Z'/%3E%3Cpath fill='%230a1f15' opacity='0.5' d='M0,400 L0,300 L200,150 L400,260 L600,120 L800,240 L1000,100 L1200,220 L1440,140 L1440,400Z'/%3E%3C/svg%3E")
                no-repeat bottom center / cover;
            z-index: 0;
        }

        .stars {
            position: fixed; inset: 0; z-index: 0;
            background-image:
                radial-gradient(circle, rgba(255,255,255,0.8) 1px, transparent 1px),
                radial-gradient(circle, rgba(255,255,255,0.5) 1px, transparent 1px);
            background-size: 200px 200px, 120px 120px;
            background-position: 0 0, 60px 60px;
            animation: twinkle 8s ease-in-out infinite alternate;
        }
        @keyframes twinkle { from { opacity: 0.4; } to { opacity: 0.9; } }

        .card-wrapper {
            position: relative; z-index: 10;
            display: flex; gap: 0;
            width: min(820px, 95vw);
            border-radius: 28px;
            overflow: hidden;
            box-shadow: 0 40px 80px rgba(0,0,0,0.6), 0 0 0 1px rgba(255,255,255,0.08);
            animation: slideUp 0.7s cubic-bezier(0.16, 1, 0.3, 1) both;
        }
        @keyframes slideUp {
            from { opacity: 0; transform: translateY(40px) scale(0.97); }
            to   { opacity: 1; transform: translateY(0) scale(1); }
        }

      
        .panel-left {
            width: 42%;
            background: linear-gradient(160deg, #1a4731, #0d2a1c);
            padding: 48px 36px;
            display: flex; flex-direction: column; justify-content: space-between;
            position: relative; overflow: hidden;
        }
        .panel-left::before {
            content: '';
            position: absolute; top: -60px; right: -60px;
            width: 220px; height: 220px;
            border-radius: 50%;
            background: rgba(74, 184, 104, 0.12);
            border: 1px solid rgba(74,184,104,0.2);
        }
        .panel-left::after {
            content: '';
            position: absolute; bottom: -40px; left: -40px;
            width: 160px; height: 160px;
            border-radius: 50%;
            background: rgba(201, 168, 76, 0.08);
            border: 1px solid rgba(201,168,76,0.15);
        }

        .brand { position: relative; z-index: 1; }
        .brand-icon {
            width: 52px; height: 52px;
            background: linear-gradient(135deg, var(--green-light), var(--gold));
            border-radius: 14px;
            display: flex; align-items: center; justify-content: center;
            font-size: 24px; margin-bottom: 18px;
        }
        .brand h1 {
            font-family: 'Playfair Display', serif;
            color: var(--cream);
            font-size: 2.2rem; font-weight: 900;
            letter-spacing: 2px; line-height: 1.1;
        }
        .brand .tagline {
            color: rgba(245,240,232,0.55);
            font-size: 0.78rem; letter-spacing: 3px;
            text-transform: uppercase; margin-top: 6px;
        }

        .panel-bottom { position: relative; z-index: 1; }
        .panel-bottom p {
            color: rgba(245,240,232,0.45);
            font-size: 0.78rem; line-height: 1.7;
        }
        .gold-line {
            width: 40px; height: 2px;
            background: var(--gold);
            margin-bottom: 12px;
        }
        .demo-accounts {
            margin-top: 16px;
        }
        .demo-accounts .label {
            font-size: 0.7rem; letter-spacing: 2px;
            color: var(--gold); text-transform: uppercase;
            margin-bottom: 8px;
        }
        .demo-item {
            background: rgba(255,255,255,0.05);
            border: 1px solid rgba(255,255,255,0.08);
            border-radius: 8px;
            padding: 8px 12px;
            margin-bottom: 6px;
            font-size: 0.72rem;
            color: rgba(245,240,232,0.6);
        }
        .demo-item strong { color: var(--green-light); font-weight: 700; }

        /* Right panel */
        .panel-right {
            flex: 1;
            background: var(--cream);
            padding: 48px 40px;
            display: flex; flex-direction: column; justify-content: center;
        }

        .tab-nav {
            display: flex; gap: 0;
            background: rgba(0,0,0,0.06);
            border-radius: 12px;
            padding: 4px;
            margin-bottom: 36px;
        }
        .tab-btn {
            flex: 1; padding: 10px;
            border: none; background: transparent;
            border-radius: 9px;
            font-family: 'Lato', sans-serif;
            font-size: 0.82rem; font-weight: 700;
            letter-spacing: 1.5px; text-transform: uppercase;
            color: #888; cursor: pointer; transition: all 0.3s;
        }
        .tab-btn.active {
            background: var(--green-mid);
            color: white;
            box-shadow: 0 4px 12px rgba(47,143,74,0.4);
        }

        .form-section { display: none; }
        .form-section.active { display: block; }

        .form-title {
            font-family: 'Playfair Display', serif;
            font-size: 1.7rem; font-weight: 700;
            color: var(--green-dark);
            margin-bottom: 6px;
        }
        .form-subtitle {
            font-size: 0.82rem; color: #888;
            margin-bottom: 28px; font-weight: 300;
        }

        .error-box {
            background: #fff0f0; border: 1px solid #ffcccc;
            border-radius: 10px; padding: 10px 14px;
            margin-bottom: 20px;
            font-size: 0.82rem; color: #c0392b;
        }
        .success-box {
            background: #f0fff4; border: 1px solid #a8ddb9;
            border-radius: 10px; padding: 10px 14px;
            margin-bottom: 20px;
            font-size: 0.82rem; color: #1a6b35;
        }

        .field { margin-bottom: 20px; }
        .field label {
            display: block;
            font-size: 0.72rem; font-weight: 700;
            letter-spacing: 1.5px; text-transform: uppercase;
            color: var(--green-dark); margin-bottom: 7px;
        }
        .field input {
            width: 100%; padding: 12px 16px;
            border: 1.5px solid #ddd;
            border-radius: 10px;
            font-family: 'Lato', sans-serif;
            font-size: 0.92rem; color: var(--text-dark);
            background: white; transition: border-color 0.3s, box-shadow 0.3s;
            outline: none;
        }
        .field input:focus {
            border-color: var(--green-mid);
            box-shadow: 0 0 0 3px rgba(47,143,74,0.12);
        }

        .btn-submit {
            width: 100%; padding: 14px;
            background: linear-gradient(135deg, var(--green-mid), var(--green-dark));
            color: white; border: none; border-radius: 12px;
            font-family: 'Lato', sans-serif;
            font-size: 0.85rem; font-weight: 700;
            letter-spacing: 2px; text-transform: uppercase;
            cursor: pointer; transition: all 0.3s;
            margin-top: 8px;
        }
        .btn-submit:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 24px rgba(47,143,74,0.45);
        }
        .btn-submit:active { transform: translateY(0); }

        @media (max-width: 600px) {
            .panel-left { display: none; }
            .panel-right { padding: 36px 28px; }
        }
    </style>
</head>
<body>
<div class="stars"></div>

<div class="card-wrapper">
    <!-- LEFT PANEL -->
    <div class="panel-left">
        <div class="brand">
            <div class="brand-icon">🏔️</div>
            <h1>HIKER<br>BEST</h1>
            <p class="tagline">Jelajahi Nusantara</p>
        </div>
        <div class="panel-bottom">
            <div class="gold-line"></div>
            <p>Platform pendakian gunung terpercaya untuk para petualang sejati Indonesia.</p>
            <div class="demo-accounts">
                <div class="label">Akun Demo</div>
                <div class="demo-item"><strong>Admin:</strong> admin@hiker.com / admin123</div>
                <div class="demo-item"><strong>User:</strong> pendaki@hiker.com / 123</div>
            </div>
        </div>
    </div>

    <!-- RIGHT PANEL -->
    <div class="panel-right">
        <div class="tab-nav">
            <button class="tab-btn active" onclick="switchTab('login')">Masuk</button>
            <button class="tab-btn" onclick="switchTab('register')">Daftar</button>
        </div>

        <!-- LOGIN FORM -->
        <div class="form-section active" id="tab-login">
            <h2 class="form-title">Selamat Datang</h2>
            <p class="form-subtitle">Masuk untuk melanjutkan perjalanan</p>

            <?php if ($error): ?>
                <div class="error-box">⚠️ <?= htmlspecialchars($error) ?></div>
            <?php endif; ?>
            <?php if (isset($_GET['registered'])): ?>
                <div class="success-box">✅ Registrasi berhasil! Silakan masuk.</div>
            <?php endif; ?>

            <form method="POST" action="login.php">
                <div class="field">
                    <label>Alamat Email</label>
                    <input type="email" name="email" placeholder="contoh@email.com"
                           value="<?= htmlspecialchars($_POST['email'] ?? '') ?>" required>
                </div>
                <div class="field">
                    <label>Kata Sandi</label>
                    <input type="password" name="password" placeholder="••••••••" required>
                </div>
                <button type="submit" class="btn-submit">Masuk Sekarang</button>
            </form>
        </div>

        <!-- REGISTER FORM (redirect to register.php) -->
        <div class="form-section" id="tab-register">
            <h2 class="form-title">Buat Akun</h2>
            <p class="form-subtitle">Bergabung dengan komunitas pendaki</p>
            <form method="POST" action="register.php">
                <div class="field">
                    <label>Nama Lengkap</label>
                    <input type="text" name="nama" placeholder="Nama kamu" required>
                </div>
                <div class="field">
                    <label>Alamat Email</label>
                    <input type="email" name="email" placeholder="contoh@email.com" required>
                </div>
                <div class="field">
                    <label>Kata Sandi</label>
                    <input type="password" name="password" placeholder="Min. 6 karakter" minlength="6" required>
                </div>
                <div class="field">
                    <label>Konfirmasi Kata Sandi</label>
                    <input type="password" name="confirm_password" placeholder="Ulangi kata sandi" required>
                </div>
                <button type="submit" class="btn-submit">Daftar Sekarang</button>
            </form>
        </div>
    </div>
</div>

<script>
function switchTab(tab) {
    document.querySelectorAll('.tab-btn').forEach((b, i) => {
        b.classList.toggle('active', (i === 0 && tab === 'login') || (i === 1 && tab === 'register'));
    });
    document.querySelectorAll('.form-section').forEach(s => s.classList.remove('active'));
    document.getElementById('tab-' + tab).classList.add('active');
}

// Kalau ada param ?tab=register di URL
const urlParams = new URLSearchParams(window.location.search);
if (urlParams.get('tab') === 'register') switchTab('register');
</script>
</body>
</html>
# login
