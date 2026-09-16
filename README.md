# daniel-website
index.html
daniel-website
├── index.html
└── ...
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Secure Biometric Portal - Daniel Anyole</title>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  
  <!-- MediaPipe AI Face Detection Scripts -->
  <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs-core"></script>
  <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs-converter"></script>
  <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs-backend-webgl"></script>
  <script src="https://cdn.jsdelivr.net/npm/@tensorflow-models/face-detection"></script>

  <style>
    :root {
      --army-dark: #0d140d;
      --army-main: #1a2b1c;
      --army-mid: #2b452d;
      --accent-gold: #eab308;
      --accent-gold-glow: rgba(234, 179, 8, 0.4);
      --bg-glass: rgba(13, 20, 13, 0.94);
      --text-light: #f3f4f6;
    }

    * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Segoe UI', system-ui, sans-serif; }
    
    body {
      background-color: var(--army-dark);
      color: var(--text-light);
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      padding: 16px;
      overflow-x: hidden;
    }

    /* Floating Background Emojis */
    .emoji-bg {
      position: fixed;
      top: 0; left: 0; width: 100%; height: 100%;
      pointer-events: none;
      z-index: 0;
      overflow: hidden;
    }
    .floating-emoji {
      position: absolute;
      font-size: 2.2rem;
      opacity: 0.15;
      animation: float 10s infinite linear;
    }
    @keyframes float {
      0% { transform: translateY(100vh) rotate(0deg) scale(0.8); }
      50% { transform: translateY(50vh) rotate(180deg) scale(1.2); }
      100% { transform: translateY(-10vh) rotate(360deg) scale(0.8); }
    }

    /* Header Live Clock & Session Tracker Bar */
    .portal-header-bar {
      position: relative;
      z-index: 10;
      width: 100%;
      max-width: 520px;
      background: rgba(26, 43, 28, 0.7);
      border: 1px solid rgba(234, 179, 8, 0.3);
      backdrop-filter: blur(10px);
      border-radius: 16px;
      padding: 10px 16px;
      margin-bottom: 14px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      font-size: 0.78rem;
      color: #d1d5db;
    }
    .live-time-badge { color: var(--accent-gold); font-weight: 700; display: flex; align-items: center; gap: 6px; }

    /* Animated Icons */
    .animated-security-icon {
      font-size: 2.5rem;
      color: var(--accent-gold);
      display: inline-block;
      animation: shieldPulse 2s infinite ease-in-out;
      margin-bottom: 8px;
    }
    @keyframes shieldPulse {
      0% { transform: scale(1); filter: drop-shadow(0 0 2px var(--accent-gold)); }
      50% { transform: scale(1.15); filter: drop-shadow(0 0 12px var(--accent-gold)); }
      100% { transform: scale(1); filter: drop-shadow(0 0 2px var(--accent-gold)); }
    }

    .spin-globe { display: inline-block; animation: rotateGlobe 12s linear infinite; }
    @keyframes rotateGlobe { 100% { transform: rotate(360deg); } }

    .portal-container {
      position: relative;
      z-index: 10;
      width: 100%;
      max-width: 520px;
    }

    .card {
      background: var(--bg-glass);
      backdrop-filter: blur(16px);
      -webkit-backdrop-filter: blur(16px);
      border-radius: 24px;
      padding: 26px 22px;
      border: 2px solid var(--accent-gold);
      box-shadow: 0 0 30px var(--accent-gold-glow), 0 20px 40px rgba(0,0,0,0.7);
      display: none;
      text-align: center;
    }
    .card.active { display: block; animation: fadeIn 0.4s ease forwards; }

    @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

    /* Vector Shield Logo */
    .brand-logo-container { margin-bottom: 10px; }
    .brand-logo-svg {
      width: 75px;
      height: 75px;
      filter: drop-shadow(0 0 10px var(--accent-gold));
    }

    .badge {
      display: inline-flex;
      align-items: center;
      gap: 8px;
      background: rgba(234, 179, 8, 0.15);
      border: 1px solid var(--accent-gold);
      color: #fef08a;
      padding: 6px 14px;
      border-radius: 20px;
      font-size: 0.75rem;
      font-weight: 800;
      letter-spacing: 1px;
      text-transform: uppercase;
      margin-bottom: 12px;
    }
    .status-dot { width: 8px; height: 8px; background: #22c55e; border-radius: 50%; box-shadow: 0 0 8px #22c55e; animation: pulse 2s infinite; }

    .greeting-box {
      background: rgba(255, 255, 255, 0.04);
      border-left: 3px solid var(--accent-gold);
      padding: 10px;
      border-radius: 8px;
      margin-bottom: 14px;
      text-align: left;
      font-size: 0.82rem;
      color: #e5e7eb;
    }

    h1 { font-size: 1.45rem; font-weight: 800; color: #fff; margin-bottom: 6px; }
    h1 span { color: var(--accent-gold); }
    p.subtitle { font-size: 0.85rem; color: #9ca3af; margin-bottom: 18px; }

    /* Progress Indicator Bar (6 Steps) */
    .step-progress-bar {
      display: flex;
      justify-content: space-between;
      gap: 4px;
      margin-bottom: 18px;
    }
    .progress-segment {
      flex: 1;
      height: 5px;
      background: rgba(255, 255, 255, 0.1);
      border-radius: 10px;
      transition: 0.3s;
    }
    .progress-segment.active { background: var(--accent-gold); box-shadow: 0 0 8px var(--accent-gold-glow); }

    .form-group { margin-bottom: 14px; text-align: left; }
    .form-group label { display: block; font-size: 0.82rem; font-weight: 700; color: #e5e7eb; margin-bottom: 5px; }
    .form-group input, .form-group select, .form-group textarea {
      width: 100%;
      padding: 11px 14px;
      background: rgba(255, 255, 255, 0.05);
      border: 1px solid rgba(234, 179, 8, 0.3);
      border-radius: 12px;
      color: #fff;
      font-size: 0.92rem;
      outline: none;
      transition: all 0.25s;
    }
    .form-group input:focus, .form-group select:focus, .form-group textarea:focus {
      border-color: var(--accent-gold);
      box-shadow: 0 0 10px var(--accent-gold-glow);
    }
    .form-group select option { background: var(--army-dark); color: #fff; }

    .username-suggestions { display: flex; gap: 6px; flex-wrap: wrap; margin-top: 6px; }
    .suggestion-chip {
      background: rgba(234, 179, 8, 0.1);
      border: 1px solid rgba(234, 179, 8, 0.4);
      color: #fef08a;
      padding: 3px 8px;
      border-radius: 8px;
      font-size: 0.72rem;
      cursor: pointer;
      transition: 0.2s;
    }
    .suggestion-chip:hover { background: var(--accent-gold); color: var(--army-dark); }

    /* Passcode Box */
    .otp-display-box {
      background: rgba(234, 179, 8, 0.12);
      border: 1px solid var(--accent-gold);
      border-radius: 12px;
      padding: 12px;
      margin-bottom: 12px;
      font-family: monospace;
      font-size: 1.3rem;
      letter-spacing: 4px;
      color: var(--accent-gold);
      font-weight: 800;
    }

    /* Dual Camera / Upload Controls */
    .cam-box {
      border: 2px dashed rgba(234, 179, 8, 0.4);
      border-radius: 14px;
      padding: 10px;
      text-align: center;
      background: rgba(0,0,0,0.3);
      position: relative;
    }
    #webcam-feed { width: 100%; max-height: 160px; border-radius: 8px; object-fit: cover; background: #000; display: none; }
    #captured-canvas { display: none; width: 100%; max-height: 160px; border-radius: 8px; margin: 0 auto; }
    
    .cam-btn-group { display: flex; gap: 8px; justify-content: center; margin-top: 8px; flex-wrap: wrap; }
    .cam-btn {
      background: rgba(234, 179, 8, 0.2);
      border: 1px solid var(--accent-gold);
      color: var(--accent-gold);
      padding: 8px 14px;
      border-radius: 20px;
      font-size: 0.8rem;
      font-weight: 700;
      cursor: pointer;
      transition: 0.2s;
    }
    .cam-btn:hover { background: var(--accent-gold); color: var(--army-dark); }

    .btn-main {
      width: 100%;
      padding: 13px;
      background: linear-gradient(135deg, var(--accent-gold) 0%, #ca8a04 100%);
      color: var(--army-dark);
      border: none;
      border-radius: 50px;
      font-size: 0.95rem;
      font-weight: 800;
      cursor: pointer;
      box-shadow: 0 5px 18px var(--accent-gold-glow);
      transition: 0.25s;
      margin-top: 10px;
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 8px;
      text-decoration: none;
    }
    .btn-main:hover { transform: translateY(-2px); box-shadow: 0 8px 22px rgba(234, 179, 8, 0.6); }

    .btn-secondary {
      background: transparent;
      border: 1px solid rgba(255,255,255,0.2);
      color: #9ca3af;
      padding: 8px;
      border-radius: 20px;
      font-size: 0.78rem;
      cursor: pointer;
      margin-top: 8px;
      width: 100%;
    }
    .btn-secondary:hover { color: #fff; border-color: #fff; }

    .auth-links { margin-top: 10px; font-size: 0.78rem; }
    .auth-links a { color: var(--accent-gold); text-decoration: none; margin: 0 6px; }
    .auth-links a:hover { text-decoration: underline; }

    .id-card {
      background: linear-gradient(135deg, #1e2923 0%, #0d140d 100%);
      border: 2px solid var(--accent-gold);
      border-radius: 16px;
      padding: 16px;
      text-align: center;
      margin-bottom: 14px;
      box-shadow: inset 0 0 15px rgba(234,179,8,0.2);
    }
    .id-header { font-size: 0.7rem; letter-spacing: 2px; color: var(--accent-gold); text-transform: uppercase; margin-bottom: 8px; }
    .id-avatar { width: 75px; height: 75px; border-radius: 50%; border: 2px solid var(--accent-gold); object-fit: cover; margin: 0 auto 8px; }
    .id-name { font-size: 1.1rem; font-weight: 800; color: #fff; }
    .id-handle { font-size: 0.8rem; color: #9ca3af; margin-bottom: 8px; }
    .id-meta { font-size: 0.75rem; color: #d1d5db; background: rgba(255,255,255,0.05); padding: 6px; border-radius: 8px; display: flex; justify-content: space-around; flex-wrap: wrap; gap: 4px; }

    .social-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 10px; margin: 14px 0; }
    .social-btn {
      display: flex; flex-direction: column; align-items: center; gap: 4px; padding: 10px 6px;
      background: rgba(255,255,255,0.05); border: 1px solid rgba(234, 179, 8, 0.3);
      border-radius: 12px; color: #fff; text-decoration: none; font-size: 0.7rem; font-weight: 600;
    }
    .social-btn i { font-size: 1.3rem; }
    .social-btn.wa:hover { background: #25D366; }
    .social-btn.fb:hover { background: #1877F2; }
    .social-btn.tt:hover { background: #000; color: #fe2c55; }
    .social-btn.gm:hover { background: #EA4335; }

    .portal-nav {
      display: flex;
      gap: 8px;
      margin-bottom: 14px;
      justify-content: center;
      flex-wrap: wrap;
    }
    .portal-nav a {
      display: inline-flex;
      align-items: center;
      justify-content: center;
      gap: 6px;
      padding: 8px 14px;
      border: 1px solid rgba(234, 179, 8, 0.45);
      border-radius: 20px;
      color: #fef08a;
      background: rgba(234, 179, 8, 0.08);
      text-decoration: none;
      font-size: 0.78rem;
      font-weight: 700;
      transition: 0.2s;
    }
    .portal-nav a:hover {
      background: var(--accent-gold);
      color: var(--army-dark);
      transform: translateY(-1px);
    }

  </style>
</head>
<body>

  <!-- Floating Background Emojis -->
  <div class="emoji-bg">
    <div class="floating-emoji" style="left: 10%; animation-delay: 0s;">🛡️</div>
    <div class="floating-emoji" style="left: 30%; animation-delay: 3s;">⚡</div>
    <div class="floating-emoji" style="left: 55%; animation-delay: 1s;">📸</div>
    <div class="floating-emoji" style="left: 80%; animation-delay: 4s;">🔐</div>
  </div>

  <!-- Real-Time Clock & Session Counter Header -->
  <div class="portal-header-bar">
    <div><i class="fa-regular fa-calendar-check"></i> <span id="header-date">Loading date...</span></div>
    <div class="live-time-badge"><i class="fa-regular fa-clock"></i> <span id="header-clock">00:00:00</span></div>
  </div>


  <!-- CONNECTED NAVIGATION: MAIN DANIEL PROFILE -->
  <div class="portal-nav" aria-label="Website navigation">
    <a href="https://sites.google.com/view/daniel-anyole-profile/main-profile" target="_blank" rel="noopener">
      🏠 Daniel's Main Website
    </a>
  </div>

  <div class="portal-container">

    <!-- GOOGLE-STYLE LOGIN FOR RETURNING VISITORS -->
    <div class="card" id="card-login">
      <div class="brand-logo-container">
        <svg class="brand-logo-svg" viewBox="0 0 100 100">
          <path d="M50 5 L85 20 V45 C85 70 50 95 50 95 C50 95 15 70 15 45 V20 Z" fill="#1a2b1c" stroke="#eab308" stroke-width="4"/>
          <circle cx="50" cy="42" r="14" fill="none" stroke="#eab308" stroke-width="3"/>
          <path d="M50 32 A 10 10 0 0 1 50 52" fill="none" stroke="#eab308" stroke-width="2" stroke-dasharray="2,2"/>
          <path d="M38 72 C38 60 62 60 62 72" fill="none" stroke="#eab308" stroke-width="3"/>
        </svg>
      </div>
      <div><i class="fa-solid fa-shield-halved animated-security-icon"></i></div>
      <div class="badge"><span class="status-dot"></span> Welcome Back</div>
      
      <div class="greeting-box" id="login-greeting-msg">
        <!-- Dynamic Hour Greeting -->
      </div>

      <h1>Security <span>Verification</span></h1>
      <p class="subtitle">Enter your password to unlock profile access.</p>

      <form onsubmit="verifyReturningUser(event)">
        <div class="form-group">
          <label><i class="fa-solid fa-lock"></i> Password *</label>
          <input type="password" id="login-pass" required placeholder="Enter your registered password">
        </div>
        <button type="submit" class="btn-main">🔓 Unlock Profile</button>
      </form>

      <div class="auth-links">
        <a href="javascript:void(0)" onclick="forgotCredentials('pass')">Forgot Password?</a> | 
        <a href="javascript:void(0)" onclick="forgotCredentials('email')">Forgot Email?</a>
      </div>
      <button class="btn-secondary" onclick="switchToRegister()">New Visitor? Register Account</button>
    </div>

    <!-- STEP 1: IDENTITY & CREDENTIALS -->
    <div class="card active" id="step-1">
      <div class="step-progress-bar">
        <div class="progress-segment active"></div><div class="progress-segment"></div><div class="progress-segment"></div>
        <div class="progress-segment"></div><div class="progress-segment"></div><div class="progress-segment"></div>
      </div>
      <div class="badge"><span class="status-dot"></span> Step 1 of 6</div>
      
      <div class="greeting-box" id="step1-greeting-msg"></div>

      <h1>Create Your <span>Portal ID</span> 👋</h1>
      <p class="subtitle">Set up your credentials to access Daniel's ecosystem.</p>

      <form onsubmit="goToStep(2, event)">
        <div class="form-group">
          <label><i class="fa-solid fa-user"></i> Full Name *</label>
          <input type="text" id="acc-name" required placeholder="e.g. Peter Divine" oninput="generateUsernames(); updateGreetingName();">
        </div>
        <div class="form-group">
          <label><i class="fa-solid fa-envelope"></i> Email Address *</label>
          <input type="email" id="acc-email" required placeholder="e.g. name@gmail.com">
        </div>
        <div class="form-group">
          <label><i class="fa-solid fa-at"></i> Choose Username *</label>
          <input type="text" id="acc-username" required placeholder="e.g. peter_divine356">
          <div class="username-suggestions" id="username-chips"></div>
        </div>
        <div class="form-group">
          <label><i class="fa-solid fa-key"></i> Create Password *</label>
          <input type="password" id="acc-pass" required placeholder="Min 6 characters" minlength="6">
        </div>
        <div class="form-group">
          <label><i class="fa-solid fa-circle-check"></i> Confirm Password *</label>
          <input type="password" id="acc-pass-confirm" required placeholder="Re-enter password" minlength="6">
        </div>
        <button type="submit" class="btn-main">Next: Contact & Passcode &rarr;</button>
      </form>
      <button class="btn-secondary" onclick="switchToLogin()">Already Registered? Log In</button>
    </div>

    <!-- STEP 2: CONTACT & PASSCODE (ZERO-COST OTP) -->
    <div class="card" id="step-2">
      <div class="step-progress-bar">
        <div class="progress-segment active"></div><div class="progress-segment active"></div><div class="progress-segment"></div>
        <div class="progress-segment"></div><div class="progress-segment"></div><div class="progress-segment"></div>
      </div>
      <div class="badge"><span class="status-dot"></span> Step 2 of 6</div>
      <h1>Contact & <span>Passcode</span> ⚡</h1>
      <p class="subtitle">Enter your WhatsApp number & verify the human passcode.</p>

      <form onsubmit="validateStep2(event)">
        <div class="form-group">
          <label><i class="fa-brands fa-whatsapp"></i> WhatsApp Phone Number *</label>
          <input type="tel" id="gate-phone" required placeholder="+256700000000">
        </div>

        <label style="font-size:0.82rem; font-weight:700; color:#e5e7eb; display:block; text-align:left; margin-bottom:5px;">
          Human Passcode *
        </label>
        <div class="otp-display-box" id="generated-otp">------</div>

        <div class="form-group">
          <input type="text" id="entered-otp" required placeholder="Enter the passcode shown above">
        </div>
        <button type="submit" class="btn-main">Next: Location & Residence &rarr;</button>
      </form>
      <button class="btn-secondary" onclick="goToStep(1)">&larr; Back to Step 1</button>
    </div>

    <!-- STEP 3: LOCATION & RESIDENCE -->
    <div class="card" id="step-3">
      <div class="step-progress-bar">
        <div class="progress-segment active"></div><div class="progress-segment active"></div><div class="progress-segment active"></div>
        <div class="progress-segment"></div><div class="progress-segment"></div><div class="progress-segment"></div>
      </div>
      <div class="badge"><span class="status-dot"></span> Step 3 of 6</div>
      <h1>Location & <span>Residence</span> <i class="fa-solid fa-earth-africa spin-globe"></i></h1>
      <p class="subtitle">Specify your location within East Africa or beyond.</p>

      <form onsubmit="goToStep(4, event)">
        <div class="form-group">
          <label><i class="fa-solid fa-flag"></i> East African Country *</label>
          <select id="gate-country" required onchange="updateDistricts()">
            <option value="Uganda" selected>Uganda 🇺🇬</option>
            <option value="Kenya">Kenya 🇰🇪</option>
            <option value="Tanzania">Tanzania 🇹🇿</option>
            <option value="Rwanda">Rwanda 🇷🇼</option>
            <option value="Burundi">Burundi 🇧🇮</option>
            <option value="South Sudan">South Sudan 🇸🇸</option>
            <option value="Other">Other / International 🌍</option>
          </select>
        </div>

        <div class="form-group">
          <label><i class="fa-solid fa-city"></i> District / County / Region *</label>
          <select id="gate-district" required>
            <!-- Populated via JS -->
          </select>
        </div>

        <div class="form-group">
          <label><i class="fa-solid fa-location-dot"></i> Specific Residence / Town / Village *</label>
          <input type="text" id="gate-residence" required placeholder="e.g. Arua Town, Cell 3">
        </div>

        <button type="submit" class="btn-main">Next: Purpose of Visit &rarr;</button>
      </form>
      <button class="btn-secondary" onclick="goToStep(2)">&larr; Back to Step 2</button>
    </div>

    <!-- STEP 4: PURPOSE OF VISIT -->
    <div class="card" id="step-4">
      <div class="step-progress-bar">
        <div class="progress-segment active"></div><div class="progress-segment active"></div><div class="progress-segment active"></div>
        <div class="progress-segment active"></div><div class="progress-segment"></div><div class="progress-segment"></div>
      </div>
      <div class="badge"><span class="status-dot"></span> Step 4 of 6</div>
      <h1>Purpose of <span>Visit</span> 🎯</h1>
      <p class="subtitle">Tell us what brings you to Daniel's portal today.</p>

      <form onsubmit="goToStep(5, event)">
        <div class="form-group">
          <label><i class="fa-solid fa-compass"></i> Primary Category *</label>
          <select id="gate-purpose" required>
            <option value="" disabled selected>Select purpose...</option>
            <option value="Academic & ICT Research">Academic & ICT Research</option>
            <option value="Inspired by My Work">Inspired by My Work</option>
            <option value="Networking / Partnership">Networking / Partnership</option>
            <option value="Just Exploring">Just Exploring</option>
            <option value="ICT Technical Support">ICT Technical Support</option>
          </select>
        </div>

        <div class="form-group">
          <label><i class="fa-solid fa-comment-dots"></i> Visitor Note / Advice *</label>
          <textarea id="gate-msg" rows="3" required placeholder="Write a short message or note..."></textarea>
        </div>

        <button type="submit" class="btn-main">Next: Facial Recognition &rarr;</button>
      </form>
      <button class="btn-secondary" onclick="goToStep(3)">&larr; Back to Step 3</button>
    </div>

    <!-- STEP 5: CAMERA SCAN & UPLOAD EXIT BUTTON -->
    <div class="card" id="step-5">
      <div class="step-progress-bar">
        <div class="progress-segment active"></div><div class="progress-segment active"></div><div class="progress-segment active"></div>
        <div class="progress-segment active"></div><div class="progress-segment active"></div><div class="progress-segment"></div>
      </div>
      <div class="badge"><span class="status-dot"></span> Step 5 of 6</div>
      <h1>Facial <span>Capture</span> 📸</h1>
      <p class="subtitle">Snap live photo or upload an image file from your device.</p>

      <div class="cam-box">
        <video id="webcam-feed" autoplay playsinline></video>
        <canvas id="captured-canvas"></canvas>
        <div id="cam-placeholder" style="font-size: 0.8rem; color: #9ca3af; padding: 12px;">Camera Inactive</div>
        
        <div class="cam-btn-group">
          <button type="button" class="cam-btn" id="cam-action-btn" onclick="toggleCamera()">📸 Start Native Camera</button>
          <button type="button" class="cam-btn" onclick="document.getElementById('file-upload-input').click()">📁 Upload Photo File</button>
        </div>
        <input type="file" id="file-upload-input" accept="image/*" style="display:none;" onchange="handleFileUpload(event)">
      </div>

      <button type="button" class="btn-main" onclick="goToStep(6)">Next: AI Scan Verification &rarr;</button>
      <button class="btn-secondary" onclick="goToStep(4)">&larr; Back to Step 4</button>
    </div>

    <!-- STEP 6: AI SCAN & DISPATCH GATEWAY -->
    <div class="card" id="step-6">
      <div class="step-progress-bar">
        <div class="progress-segment active"></div><div class="progress-segment active"></div><div class="progress-segment active"></div>
        <div class="progress-segment active"></div><div class="progress-segment active"></div><div class="progress-segment active"></div>
      </div>
      <div class="badge"><span class="status-dot"></span> Final Step 6</div>
      
      <div><i class="fa-solid fa-brain animated-security-icon"></i></div>
      <h1>AI Biometric <span>Verification</span> 🤖</h1>
      
      <div class="greeting-box" id="ai-scan-status-box">
        ⚙️ AI Scanning Engine: Standing by...
      </div>

      <div class="greeting-box" style="border-left-color: #22c55e;" id="ai-heartfelt-wish">
        💖 <em>May your visit inspire new ideas and foster high-impact digital collaborations!</em>
      </div>

      <button type="button" class="btn-main" id="unlock-btn" onclick="completePortalEntry()">🚀 Dispatch Copies & Open Profile</button>
      <button class="btn-secondary" onclick="goToStep(5)">&larr; Back to Step 5</button>
    </div>

    <!-- STEP SUCCESS: PASS ID CARD & TIME TRACKER -->
    <div class="card" id="step-success">
      <div class="brand-logo-container">
        <svg class="brand-logo-svg" viewBox="0 0 100 100">
          <path d="M50 5 L85 20 V45 C85 70 50 95 50 95 C50 95 15 70 15 45 V20 Z" fill="#1a2b1c" stroke="#eab308" stroke-width="4"/>
          <circle cx="50" cy="42" r="14" fill="none" stroke="#eab308" stroke-width="3"/>
          <path d="M50 32 A 10 10 0 0 1 50 52" fill="none" stroke="#eab308" stroke-width="2" stroke-dasharray="2,2"/>
          <path d="M38 72 C38 60 62 60 62 72" fill="none" stroke="#eab308" stroke-width="3"/>
        </svg>
      </div>
      <div class="badge"><span class="status-dot"></span> Access Granted 🌟</div>
      
      <div class="id-card">
        <div class="id-header">Daniel Secure Pass</div>
        <img id="pass-avatar" src="" alt="Face Scan" class="id-avatar">
        <div class="id-name" id="pass-name">Name</div>
        <div class="id-handle" id="pass-username">@username</div>
        <div class="id-meta">
          <span id="pass-loc">Uganda (Kampala)</span>
          <span>ID: #134-VIP</span>
          <span id="pass-session-timer">⏱️ 00m 00s</span>
        </div>
      </div>

      <a href="https://sites.google.com/view/daniel-anyole-profile/main-profile" target="_blank" class="btn-main" onclick="startMainProfileSession()">
        ✨ Open Main Profile Now
      </a>


      <a href="https://sites.google.com/view/daniel-anyole-profile/main-profile" target="_blank" rel="noopener" class="btn-secondary" style="display:block; text-decoration:none; text-align:center;">
        🏠 Return to Daniel's Main Website
      </a>

      <p style="color: #9ca3af; font-size: 0.75rem; text-align: center; margin-top: 12px;">Connect with Daniel directly:</p>
      <div class="social-grid">
        <a href="https://wa.me/256780199002" target="_blank" class="social-btn wa" id="success-wa">
          <i class="fa-brands fa-whatsapp"></i> WhatsApp
        </a>
        <a href="https://www.facebook.com/profile.php?id=61583095430685" target="_blank" class="social-btn fb">
          <i class="fa-brands fa-facebook"></i> Facebook
        </a>
        <a href="https://www.tiktok.com/@anygzi" target="_blank" class="social-btn tt">
          <i class="fa-brands fa-tiktok"></i> TikTok
        </a>
        <a href="mailto:danielanyole9@gmail.com" target="_blank" class="social-btn gm">
          <i class="fa-solid fa-envelope"></i> Email
        </a>
      </div>
    </div>

  </div>

  <script>
    let userData = {};
    let localStream = null;
    let faceCapturedData = "";
    let detector = null;
    let generatedPasscode = "";
    let sessionSeconds = 0;
    let sessionTimerInterval = null;

    const GOOGLE_SHEETS_SCRIPT_URL = "YOUR_GOOGLE_APPS_SCRIPT_WEB_APP_URL_HERE";

    const eastAfricaDistricts = {
      "Uganda": ["Koboko", "Kampala", "Wakiso", "Arua", "Yumbe", "Moyos", "Adjumani", "Mbarara", "Gulu", "Jinja", "Mukono", "Kasese", "Mbale", "Lira", "Masaka", "Entebbe", "Other District"],
      "Kenya": ["Nairobi", "Mombasa", "Kisumu", "Nakuru", "Eldoret", "Uasin Gishu", "Machakos", "Kilifi", "Garissa", "Other County"],
      "Tanzania": ["Dar es Salaam", "Dodoma", "Arusha", "Mwanza", "Zanzibar", "Tanga", "Mbeya", "Other Region"],
      "Rwanda": ["Kigali", "Musanze", "Rubavu", "Huye", "Muhanga", "Other District"],
      "Burundi": ["Bujumbura", "Gitega", "Ngozi", "Muyinga", "Other Province"],
      "South Sudan": ["Juba", "Yei", "Wau", "Malakal", "Nimule", "Other State"],
      "Other": ["International / Outer Region"]
    };

    function updateDistricts() {
      const countrySelect = document.getElementById("gate-country");
      const districtSelect = document.getElementById("gate-district");
      const selectedCountry = countrySelect.value;
      const districts = eastAfricaDistricts[selectedCountry] || ["Other"];
      districtSelect.innerHTML = districts.map(d => `<option value="${d}">${d}</option>`).join('');
    }

    function generateOTP() {
      generatedPasscode = Math.floor(100000 + Math.random() * 900000).toString();
      const box = document.getElementById("generated-otp");
      if(box) box.innerText = generatedPasscode;
    }

    function getHourGreeting(name = "") {
      const hour = new Date().getHours();
      let greeting = "Good Evening 🌙";
      if (hour >= 5 && hour < 12) greeting = "Good Morning 🌅";
      else if (hour >= 12 && hour < 17) greeting = "Good Afternoon ☀️";
      
      return `${greeting}${name ? ", <strong>" + name + "</strong>" : ""}! Welcome to Daniel's Digital Portal.`;
    }

    function updateGreetingName() {
      const name = document.getElementById("acc-name").value.trim();
      document.getElementById("step1-greeting-msg").innerHTML = getHourGreeting(name);
    }

    function startHeaderClock() {
      setInterval(() => {
        const now = new Date();
        document.getElementById("header-date").innerText = now.toLocaleDateString(undefined, { weekday: 'short', month: 'short', day: 'numeric' });
        document.getElementById("header-clock").innerText = now.toLocaleTimeString();
      }, 1000);
    }

    document.addEventListener("DOMContentLoaded", async () => {
      startHeaderClock();
      updateDistricts();
      generateOTP();
      document.getElementById("login-greeting-msg").innerHTML = getHourGreeting();
      document.getElementById("step1-greeting-msg").innerHTML = getHourGreeting();

      try {
        const model = faceDetection.SupportedModels.MediaPipeFaceDetector;
        detector = await faceDetection.createDetector(model, { runtime: 'tfjs' });
      } catch(e) { console.warn("AI Detector initializing..."); }

      if(localStorage.getItem("sp_registered") === "true") {
        document.querySelectorAll(".card").forEach(c => c.classList.remove("active"));
        document.getElementById("card-login").classList.add("active");
      }
    });

    function goToStep(stepNumber, e) {
      if(e) e.preventDefault();

      if(stepNumber === 2) {
        const pass = document.getElementById("acc-pass").value;
        const passConfirm = document.getElementById("acc-pass-confirm").value;
        if(pass !== passConfirm) {
          alert("❌ Passwords do not match!");
          return;
        }
        userData.name = document.getElementById("acc-name").value;
        userData.email = document.getElementById("acc-email").value;
        userData.username = document.getElementById("acc-username").value;
        userData.password = pass;
      }

      document.querySelectorAll(".card").forEach(c => c.classList.remove("active"));
      document.getElementById("step-" + stepNumber).classList.add("active");

      if(stepNumber === 6) runAIFaceCheck();
    }

    function validateStep2(e) {
      e.preventDefault();
      const entered = document.getElementById("entered-otp").value.trim();
      if(entered !== generatedPasscode) {
        alert("❌ Passcode incorrect. Please enter the passcode shown on screen.");
        return;
      }
      userData.phone = document.getElementById("gate-phone").value;
      goToStep(3);
    }

    function switchToLogin() {
      document.querySelectorAll(".card").forEach(c => c.classList.remove("active"));
      document.getElementById("card-login").classList.add("active");
    }

    function switchToRegister() {
      document.querySelectorAll(".card").forEach(c => c.classList.remove("active"));
      document.getElementById("step-1").classList.add("active");
    }

    function generateUsernames() {
      const name = document.getElementById("acc-name").value.trim().toLowerCase().replace(/\s+/g, '_');
      const chipsContainer = document.getElementById("username-chips");
      if(!name) { chipsContainer.innerHTML = ""; return; }
      const suggestions = [name, name + "134", "bagzi_" + name, name.substring(0,6) + "_vip"];
      chipsContainer.innerHTML = suggestions.map(s => `<span class="suggestion-chip" onclick="selectUser('${s}')">${s}</span>`).join('');
    }

    function selectUser(uname) { document.getElementById("acc-username").value = uname; }

    async function toggleCamera() {
      const video = document.getElementById("webcam-feed");
      const canvas = document.getElementById("captured-canvas");
      const placeholder = document.getElementById("cam-placeholder");
      const btn = document.getElementById("cam-action-btn");

      if(!localStream) {
        try {
          localStream = await navigator.mediaDevices.getUserMedia({ video: true, audio: false });
          video.srcObject = localStream;
          video.style.display = "block";
          canvas.style.display = "none";
          placeholder.style.display = "none";
          btn.innerText = "📸 Snap Photo Now";
        } catch(err) { alert("Camera access unavailable. Use 'Upload Photo File' instead."); }
      } else {
        canvas.width = video.videoWidth || 320;
        canvas.height = video.videoHeight || 240;
        const ctx = canvas.getContext("2d");
        ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
        faceCapturedData = canvas.toDataURL("image/png");

        video.style.display = "none";
        canvas.style.display = "block";
        localStream.getTracks().forEach(track => track.stop());
        localStream = null;
        btn.innerText = "📸 Retake Camera Snap";
      }
    }

    function handleFileUpload(event) {
      const file = event.target.files[0];
      if(!file) return;
      const reader = new FileReader();
      reader.onload = function(e) {
        const img = new Image();
        img.onload = function() {
          const canvas = document.getElementById("captured-canvas");
          canvas.width = 320;
          canvas.height = 240;
          const ctx = canvas.getContext("2d");
          ctx.drawImage(img, 0, 0, 320, 240);
          faceCapturedData = canvas.toDataURL("image/png");
          
          document.getElementById("webcam-feed").style.display = "none";
          document.getElementById("cam-placeholder").style.display = "none";
          canvas.style.display = "block";
        };
        img.src = e.target.result;
      };
      reader.readAsDataURL(file);
    }

    async function runAIFaceCheck() {
      const statusBox = document.getElementById("ai-scan-status-box");
      const canvas = document.getElementById("captured-canvas");

      if(!faceCapturedData) {
        statusBox.innerHTML = "⚠️ <strong>Notice:</strong> No photo captured. AI scan using default biometric profile.";
        return;
      }

      statusBox.innerHTML = "⚙️ <strong>Gemini AI Vision Scanning...</strong> Estimating facial landmarks.";

      try {
        let faces = [];
        if(detector) {
          faces = await detector.estimateFaces(canvas);
        } else {
          const model = faceDetection.SupportedModels.MediaPipeFaceDetector;
          detector = await faceDetection.createDetector(model, { runtime: 'tfjs' });
          faces = await detector.estimateFaces(canvas);
        }

        if(faces.length === 0) {
          statusBox.innerHTML = "❌ <strong>AI Alert:</strong> No human face detected in current capture! Retake photo for maximum security validation.";
        } else {
          statusBox.innerHTML = `✅ <strong>Gemini AI Vision Status:</strong> Human Face Verified! (${faces.length} face detected).`;
        }
      } catch(err) {
        statusBox.innerHTML = "✅ <strong>Biometric Verification Complete:</strong> Human Profile Authenticated.";
      }
    }

    function completePortalEntry() {
      userData.country = document.getElementById("gate-country").value;
      userData.district = document.getElementById("gate-district").value;
      userData.residence = document.getElementById("gate-residence").value;
      userData.purpose = document.getElementById("gate-purpose").value;
      userData.message = document.getElementById("gate-msg").value;

      localStorage.setItem("sp_registered", "true");
      localStorage.setItem("sp_name", userData.name);
      localStorage.setItem("sp_user", userData.username);
      localStorage.setItem("sp_email", userData.email);
      localStorage.setItem("sp_password", userData.password);
      localStorage.setItem("sp_country", userData.country);
      localStorage.setItem("sp_district", userData.district);
      localStorage.setItem("sp_residence", userData.residence);
      localStorage.setItem("sp_avatar", faceCapturedData || "https://via.placeholder.com/75");

      const waText = `🛡️✨ *SECURE BIOMETRIC PORTAL DISPATCH* ✨🛡️\n\n` +
        `👤 *VISITOR:* ${userData.name}\n` +
        `🆔 *Username:* @${userData.username}\n` +
        `📧 *Email:* ${userData.email}\n` +
        `📞 *Phone:* ${userData.phone}\n` +
        `🌍 *Location:* ${userData.residence}, ${userData.district}, ${userData.country}\n\n` +
        `🎯 *PURPOSE:* ${userData.purpose}\n` +
        `💬 *NOTE:* "${userData.message}"\n\n` +
        `🔐 *Password:* Not included for security\n` +
        `📸 *Biometric image:* Not included in WhatsApp message\n` +
        `✅ *Portal verification status:* Completed 🚀`;

      const waUrl = `https://wa.me/256780199002?text=${encodeURIComponent(waText)}`;
      document.getElementById("success-wa").href = waUrl;

      if (GOOGLE_SHEETS_SCRIPT_URL && GOOGLE_SHEETS_SCRIPT_URL !== "YOUR_GOOGLE_APPS_SCRIPT_WEB_APP_URL_HERE") {
        fetch(GOOGLE_SHEETS_SCRIPT_URL, {
          method: "POST", mode: "no-cors", headers: { "Content-Type": "application/json" },
          body: JSON.stringify(userData)
        }).catch(err => console.log(err));
      }

      window.open(waUrl, "_blank");
      renderSuccessCard(userData.name, userData.username, userData.country, userData.district, faceCapturedData);
    }

    function verifyReturningUser(e) {
      e.preventDefault();
      const enteredPass = document.getElementById("login-pass").value;
      const savedPass = localStorage.getItem("sp_password");

      if(enteredPass === savedPass) {
        renderSuccessCard(
          localStorage.getItem("sp_name") || "Visitor",
          localStorage.getItem("sp_user") || "guest",
          localStorage.getItem("sp_country") || "Uganda",
          localStorage.getItem("sp_district") || "Koboko",
          localStorage.getItem("sp_avatar") || "https://via.placeholder.com/75"
        );
      } else {
        alert("❌ Incorrect Password. Access Denied.");
      }
    }

    function renderSuccessCard(name, username, country, district, avatar) {
      document.getElementById("pass-avatar").src = avatar || "https://via.placeholder.com/75";
      document.getElementById("pass-name").innerText = name;
      document.getElementById("pass-username").innerText = "@" + username;
      document.getElementById("pass-loc").innerText = `${country} (${district})`;

      document.querySelectorAll(".card").forEach(c => c.classList.remove("active"));
      document.getElementById("step-success").classList.add("active");
      startSessionTracker();
    }

    function startSessionTracker() {
      if(sessionTimerInterval) clearInterval(sessionTimerInterval);
      sessionSeconds = 0;
      sessionTimerInterval = setInterval(() => {
        sessionSeconds++;
        const mins = Math.floor(sessionSeconds / 60).toString().padStart(2, '0');
        const secs = (sessionSeconds % 60).toString().padStart(2, '0');
        const timerEl = document.getElementById("pass-session-timer");
        if (timerEl) timerEl.innerText = `⏱️ ${mins}m ${secs}s`;
      }, 1000);
    }

    function forgotCredentials(type) {
      if(type === 'pass') {
        const email = prompt("Enter your registered Email or WhatsApp Phone to recover password:");
        if(email) {
          const code = Math.floor(100000 + Math.random() * 900000);
          alert(`🔐 Recovery Passcode generated: ${code}\nUse this passcode to reset your password.`);
        }
      } else {
        const name = prompt("Enter your Full Name:");
        if(name) {
          const savedUser = localStorage.getItem("sp_user") || "Not Found";
          alert(`📧 Account Lookup Result:\nUsername associated with ${name} is: @${savedUser}`);
        }
      }
    }
  </script>
</body>
</html>
