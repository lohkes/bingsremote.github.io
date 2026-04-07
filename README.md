<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="apple-mobile-web-app-title" content="LG Remote">
<meta name="theme-color" content="#0a0a0a">
<title>LG Remote</title>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; -webkit-tap-highlight-color: transparent; }
  :root {
    --bg: #0f0f0f;
    --surface: #1a1a1a;
    --surface2: #242424;
    --border: rgba(255,255,255,0.08);
    --text: #f0f0f0;
    --text2: #888;
    --accent: #c8102e;
    --accent2: #e8193a;
    --btn-bg: #222;
    --btn-active: #333;
    --nav-btn: #1e1e1e;
    --radius: 12px;
  }
  body {
    background: var(--bg);
    color: var(--text);
    font-family: -apple-system, BlinkMacSystemFont, 'SF Pro Display', sans-serif;
    min-height: 100vh;
    padding-bottom: env(safe-area-inset-bottom);
  }

  /* SCREENS */
  .screen { display: none; flex-direction: column; min-height: 100vh; padding: 0 16px 32px; }
  .screen.active { display: flex; }

  /* SETUP SCREEN */
  .setup-header { text-align: center; padding: 60px 0 32px; }
  .setup-header .logo { width: 56px; height: 56px; background: var(--accent); border-radius: 14px; margin: 0 auto 16px; display: flex; align-items: center; justify-content: center; }
  .setup-header h1 { font-size: 22px; font-weight: 600; margin-bottom: 6px; }
  .setup-header p { font-size: 14px; color: var(--text2); line-height: 1.5; }
  .field-group { margin-bottom: 20px; }
  .field-group label { font-size: 13px; color: var(--text2); margin-bottom: 8px; display: block; }
  .field-group input {
    width: 100%; background: var(--surface); border: 1px solid var(--border);
    border-radius: 10px; padding: 14px 16px; color: var(--text); font-size: 16px;
    outline: none; transition: border-color 0.2s;
  }
  .field-group input:focus { border-color: var(--accent); }
  .btn-primary {
    width: 100%; background: var(--accent); color: white; border: none;
    border-radius: 12px; padding: 16px; font-size: 16px; font-weight: 600;
    cursor: pointer; transition: background 0.15s, transform 0.1s;
  }
  .btn-primary:active { background: var(--accent2); transform: scale(0.98); }
  .status-msg { text-align: center; font-size: 13px; margin-top: 16px; min-height: 20px; }
  .status-msg.ok { color: #4caf50; }
  .status-msg.err { color: #f44336; }
  .status-msg.info { color: var(--text2); }

  /* PAIRING MODAL */
  .modal-overlay {
    display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.85);
    z-index: 100; align-items: center; justify-content: center; padding: 24px;
  }
  .modal-overlay.show { display: flex; }
  .modal {
    background: var(--surface); border-radius: 16px; padding: 28px 24px;
    width: 100%; max-width: 340px; text-align: center;
  }
  .modal h2 { font-size: 18px; margin-bottom: 10px; }
  .modal p { font-size: 14px; color: var(--text2); line-height: 1.6; margin-bottom: 20px; }
  .pair-code { font-size: 32px; letter-spacing: 8px; font-weight: 700; color: var(--accent); margin: 16px 0; }
  .modal-btns { display: flex; gap: 10px; }
  .btn-secondary {
    flex: 1; background: var(--surface2); border: 1px solid var(--border);
    color: var(--text); border-radius: 10px; padding: 13px; font-size: 15px;
    cursor: pointer; transition: background 0.15s;
  }
  .btn-secondary:active { background: #333; }

  /* REMOTE SCREEN */
  .remote-header {
    display: flex; align-items: center; justify-content: space-between;
    padding: 56px 0 12px;
  }
  .tv-name { font-size: 17px; font-weight: 600; }
  .conn-badge {
    font-size: 11px; padding: 4px 10px; border-radius: 20px;
    background: rgba(76,175,80,0.15); color: #4caf50; font-weight: 500;
  }
  .conn-badge.off { background: rgba(244,67,54,0.15); color: #f44336; }
  .settings-btn {
    background: none; border: none; color: var(--text2); cursor: pointer;
    padding: 6px; font-size: 18px;
  }

  /* POWER ROW */
  .power-row { display: flex; gap: 10px; margin-bottom: 14px; }
  .power-btn {
    flex: 1; background: var(--surface); border: 1px solid var(--border);
    border-radius: 12px; padding: 14px 10px; color: var(--text); font-size: 13px;
    font-weight: 500; cursor: pointer; transition: background 0.15s, transform 0.1s;
    display: flex; flex-direction: column; align-items: center; gap: 6px;
  }
  .power-btn:active { background: var(--btn-active); transform: scale(0.96); }
  .power-btn.pwr { background: rgba(200,16,46,0.15); border-color: rgba(200,16,46,0.3); }
  .power-btn.pwr:active { background: rgba(200,16,46,0.3); }
  .icon { width: 22px; height: 22px; }

  /* VOLUME + CHANNEL ROW */
  .vc-row { display: flex; gap: 10px; margin-bottom: 14px; }
  .vc-group {
    flex: 1; background: var(--surface); border: 1px solid var(--border);
    border-radius: 12px; overflow: hidden;
  }
  .vc-label { font-size: 11px; color: var(--text2); text-align: center; padding: 8px 0 4px; }
  .vc-btns { display: flex; }
  .vc-btn {
    flex: 1; background: none; border: none; color: var(--text);
    padding: 14px 10px; font-size: 20px; cursor: pointer;
    transition: background 0.15s;
  }
  .vc-btn:active { background: var(--btn-active); }
  .vc-sep { width: 1px; background: var(--border); }

  /* D-PAD */
  .dpad-wrap { margin-bottom: 14px; }
  .dpad {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    grid-template-rows: 1fr 1fr 1fr;
    gap: 6px; max-width: 220px; margin: 0 auto;
  }
  .dpad-btn {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: 12px; padding: 18px 10px; color: var(--text);
    font-size: 18px; cursor: pointer; display: flex; align-items: center;
    justify-content: center; transition: background 0.15s, transform 0.1s;
    min-height: 58px;
  }
  .dpad-btn:active { background: var(--btn-active); transform: scale(0.94); }
  .dpad-center {
    background: var(--accent); border-color: var(--accent);
    font-size: 13px; font-weight: 600;
  }
  .dpad-center:active { background: var(--accent2); }
  .dpad-empty { visibility: hidden; }

  /* NAV ROW */
  .nav-row { display: grid; grid-template-columns: repeat(4, 1fr); gap: 8px; margin-bottom: 14px; }
  .nav-btn {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: 12px; padding: 14px 8px; color: var(--text); font-size: 12px;
    font-weight: 500; cursor: pointer; text-align: center;
    transition: background 0.15s, transform 0.1s;
    display: flex; flex-direction: column; align-items: center; gap: 5px;
  }
  .nav-btn:active { background: var(--btn-active); transform: scale(0.96); }

  /* NUMBER PAD */
  .numpad-section { margin-bottom: 14px; }
  .section-label { font-size: 11px; color: var(--text2); margin-bottom: 8px; text-transform: uppercase; letter-spacing: 0.5px; }
  .numpad { display: grid; grid-template-columns: repeat(3, 1fr); gap: 8px; }
  .num-btn {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: 12px; padding: 16px 8px; color: var(--text); font-size: 18px;
    font-weight: 500; cursor: pointer; text-align: center;
    transition: background 0.15s, transform 0.1s;
  }
  .num-btn:active { background: var(--btn-active); transform: scale(0.96); }

  /* APP SHORTCUTS */
  .apps-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 8px; margin-bottom: 14px; }
  .app-btn {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: 12px; padding: 14px 6px 10px; color: var(--text); font-size: 11px;
    cursor: pointer; text-align: center; display: flex; flex-direction: column;
    align-items: center; gap: 7px; transition: background 0.15s, transform 0.1s;
    font-weight: 500;
  }
  .app-btn:active { background: var(--btn-active); transform: scale(0.96); }
  .app-icon {
    width: 32px; height: 32px; border-radius: 8px;
    display: flex; align-items: center; justify-content: center;
    font-size: 14px; font-weight: 700;
  }

  /* INPUT SELECT */
  .input-row { display: flex; gap: 8px; margin-bottom: 14px; overflow-x: auto; padding-bottom: 4px; }
  .input-row::-webkit-scrollbar { display: none; }
  .input-chip {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: 20px; padding: 8px 16px; color: var(--text); font-size: 13px;
    white-space: nowrap; cursor: pointer; transition: background 0.15s, border-color 0.15s;
  }
  .input-chip:active { background: var(--btn-active); }
  .input-chip.active-input { border-color: var(--accent); color: var(--accent); background: rgba(200,16,46,0.1); }

  /* TOAST */
  .toast {
    position: fixed; bottom: calc(24px + env(safe-area-inset-bottom));
    left: 50%; transform: translateX(-50%) translateY(80px);
    background: #333; color: white; padding: 10px 20px; border-radius: 20px;
    font-size: 13px; z-index: 200; transition: transform 0.3s ease;
    white-space: nowrap;
  }
  .toast.show { transform: translateX(-50%) translateY(0); }
</style>
</head>
<body>

<!-- SETUP SCREEN -->
<div class="screen active" id="screen-setup">
  <div class="setup-header">
    <div class="logo">
      <svg class="icon" viewBox="0 0 24 24" fill="none" stroke="white" stroke-width="2.5" stroke-linecap="round">
        <rect x="2" y="4" width="20" height="14" rx="2"/><path d="M8 20h8M12 18v2"/>
      </svg>
    </div>
    <h1>LG TV Remote</h1>
    <p>Enter your TV's IP address to connect.<br>Find it in TV Settings → Network → Wi-Fi → Advanced.</p>
  </div>

  <div class="field-group">
    <label>TV IP Address</label>
    <input type="text" id="ip-input" placeholder="192.168.50.19" value="192.168.50.19" inputmode="decimal">
  </div>
  <div class="field-group">
    <label>TV Name (optional)</label>
    <input type="text" id="name-input" placeholder="My LG TV" value="My LG TV">
  </div>

  <button class="btn-primary" id="connect-btn">Connect to TV</button>
  <div class="status-msg info" id="setup-status"></div>
</div>

<!-- PAIRING MODAL -->
<div class="modal-overlay" id="pair-modal">
  <div class="modal">
    <h2>Allow on your TV</h2>
    <p>A prompt should appear on your TV screen asking you to allow this connection. Accept it to continue.</p>
    <div class="pair-code" id="pair-code">- - - -</div>
    <p style="font-size:13px; color: var(--text2); margin-bottom: 16px;">If a PIN appears on your TV, enter it above and tap Confirm.</p>
    <input type="number" id="pin-input" placeholder="Enter PIN (if shown)" style="width:100%; background:var(--surface2); border:1px solid var(--border); border-radius:10px; padding:12px 14px; color:var(--text); font-size:16px; outline:none; margin-bottom:16px; text-align:center; display:block;">
    <div class="modal-btns">
      <button class="btn-secondary" id="pair-cancel">Cancel</button>
      <button class="btn-primary" id="pair-confirm" style="flex:1">Confirm</button>
    </div>
  </div>
</div>

<!-- REMOTE SCREEN -->
<div class="screen" id="screen-remote">
  <div class="remote-header">
    <div>
      <div class="tv-name" id="tv-label">My LG TV</div>
      <div style="font-size:12px; color:var(--text2); margin-top:2px;" id="tv-ip-label">192.168.50.19</div>
    </div>
    <div style="display:flex; align-items:center; gap:10px;">
      <span class="conn-badge" id="conn-badge">Connected</span>
      <button class="settings-btn" id="settings-btn">
        <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round">
          <circle cx="12" cy="12" r="3"/><path d="M19.4 15a1.65 1.65 0 0 0 .33 1.82l.06.06a2 2 0 0 1-2.83 2.83l-.06-.06a1.65 1.65 0 0 0-1.82-.33 1.65 1.65 0 0 0-1 1.51V21a2 2 0 0 1-4 0v-.09A1.65 1.65 0 0 0 9 19.4a1.65 1.65 0 0 0-1.82.33l-.06.06a2 2 0 0 1-2.83-2.83l.06-.06A1.65 1.65 0 0 0 4.68 15a1.65 1.65 0 0 0-1.51-1H3a2 2 0 0 1 0-4h.09A1.65 1.65 0 0 0 4.6 9a1.65 1.65 0 0 0-.33-1.82l-.06-.06a2 2 0 0 1 2.83-2.83l.06.06A1.65 1.65 0 0 0 9 4.68a1.65 1.65 0 0 0 1-1.51V3a2 2 0 0 1 4 0v.09a1.65 1.65 0 0 0 1 1.51 1.65 1.65 0 0 0 1.82-.33l.06-.06a2 2 0 0 1 2.83 2.83l-.06.06A1.65 1.65 0 0 0 19.4 9a1.65 1.65 0 0 0 1.51 1H21a2 2 0 0 1 0 4h-.09a1.65 1.65 0 0 0-1.51 1z"/>
        </svg>
      </button>
    </div>
  </div>

  <!-- POWER ROW -->
  <div class="power-row">
    <button class="power-btn pwr" data-key="POWER">
      <svg class="icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round"><path d="M18.36 6.64A9 9 0 1 1 5.64 6.64"/><line x1="12" y1="2" x2="12" y2="12"/></svg>
      Power
    </button>
    <button class="power-btn" data-key="MUTE">
      <svg class="icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><path d="M11 5L6 9H2v6h4l5 4V5z"/><line x1="23" y1="9" x2="17" y2="15"/><line x1="17" y1="9" x2="23" y2="15"/></svg>
      Mute
    </button>
    <button class="power-btn" data-key="CC">
      <svg class="icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><rect x="2" y="6" width="20" height="12" rx="2"/><path d="M7 10h2l-2 4h2M13 10h2l-2 4h2"/></svg>
      CC
    </button>
    <button class="power-btn" data-key="SCREEN_REMOTE">
      <svg class="icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><rect x="5" y="2" width="14" height="20" rx="2"/><line x1="12" y1="18" x2="12" y2="18.01"/></svg>
      Quick
    </button>
  </div>

  <!-- VOLUME + CHANNEL -->
  <div class="vc-row">
    <div class="vc-group">
      <div class="vc-label">Volume</div>
      <div class="vc-btns">
        <button class="vc-btn" data-key="VOLUMEDOWN">−</button>
        <div class="vc-sep"></div>
        <button class="vc-btn" data-key="VOLUMEUP">+</button>
      </div>
    </div>
    <div class="vc-group">
      <div class="vc-label">Channel</div>
      <div class="vc-btns">
        <button class="vc-btn" data-key="CHANNELDOWN">−</button>
        <div class="vc-sep"></div>
        <button class="vc-btn" data-key="CHANNELUP">+</button>
      </div>
    </div>
  </div>

  <!-- D-PAD -->
  <div class="dpad-wrap">
    <div class="dpad">
      <div class="dpad-empty"></div>
      <button class="dpad-btn" data-key="UP">▲</button>
      <div class="dpad-empty"></div>
      <button class="dpad-btn" data-key="LEFT">◀</button>
      <button class="dpad-btn dpad-center" data-key="ENTER">OK</button>
      <button class="dpad-btn" data-key="RIGHT">▶</button>
      <div class="dpad-empty"></div>
      <button class="dpad-btn" data-key="DOWN">▼</button>
      <div class="dpad-empty"></div>
    </div>
  </div>

  <!-- NAV ROW -->
  <div class="nav-row">
    <button class="nav-btn" data-key="BACK">
      <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round"><polyline points="15 18 9 12 15 6"/></svg>
      Back
    </button>
    <button class="nav-btn" data-key="HOME">
      <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"/><polyline points="9 22 9 12 15 12 15 22"/></svg>
      Home
    </button>
    <button class="nav-btn" data-key="INFO">
      <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><circle cx="12" cy="12" r="10"/><line x1="12" y1="8" x2="12" y2="12"/><line x1="12" y1="16" x2="12.01" y2="16"/></svg>
      Info
    </button>
    <button class="nav-btn" data-key="MENU">
      <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><line x1="3" y1="6" x2="21" y2="6"/><line x1="3" y1="12" x2="21" y2="12"/><line x1="3" y1="18" x2="21" y2="18"/></svg>
      Menu
    </button>
  </div>

  <!-- PLAYBACK -->
  <div class="nav-row" style="margin-bottom:14px;">
    <button class="nav-btn" data-key="REWIND">
      <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round"><polygon points="11 19 2 12 11 5 11 19"/><polygon points="22 19 13 12 22 5 22 19"/></svg>
      Rew
    </button>
    <button class="nav-btn" data-key="PLAY">
      <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><polygon points="5 3 19 12 5 21 5 3"/></svg>
      Play
    </button>
    <button class="nav-btn" data-key="PAUSE">
      <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round"><line x1="6" y1="4" x2="6" y2="20"/><line x1="18" y1="4" x2="18" y2="20"/></svg>
      Pause
    </button>
    <button class="nav-btn" data-key="FASTFORWARD">
      <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round"><polygon points="13 19 22 12 13 5 13 19"/><polygon points="2 19 11 12 2 5 2 19"/></svg>
      FF
    </button>
  </div>

  <!-- APP SHORTCUTS -->
  <div class="section-label">App shortcuts</div>
  <div class="apps-grid">
    <button class="app-btn" data-app="netflix" data-app-id="netflix">
      <div class="app-icon" style="background:#E50914; color:white">N</div>
      Netflix
    </button>
    <button class="app-btn" data-app="youtube" data-app-id="youtube.leanback.v4">
      <div class="app-icon" style="background:#FF0000; color:white">
        <svg width="16" height="16" viewBox="0 0 24 24" fill="white"><path d="M10 15l5.19-3L10 9v6m11.56-7.83c.13.47.22 1.1.28 1.9.07.8.1 1.49.1 2.09L22 12c0 2.19-.16 3.8-.44 4.83-.25.9-.83 1.48-1.73 1.73-.47.13-1.33.22-2.65.28-1.3.07-2.49.1-3.59.1L12 19c-4.19 0-6.8-.16-7.83-.44-.9-.25-1.48-.83-1.73-1.73-.13-.47-.22-1.1-.28-1.9-.07-.8-.1-1.49-.1-2.09L2 12c0-2.19.16-3.8.44-4.83.25-.9.83-1.48 1.73-1.73.47-.13 1.33-.22 2.65-.28 1.3-.07 2.49-.1 3.59-.1L12 5c4.19 0 6.8.16 7.83.44.9.25 1.48.83 1.73 1.73z"/></svg>
      </div>
      YouTube
    </button>
    <button class="app-btn" data-app="spotify" data-app-id="spotify-beehive-production">
      <div class="app-icon" style="background:#1DB954; color:white">
        <svg width="16" height="16" viewBox="0 0 24 24" fill="white"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm4.64 14.36c-.21.33-.66.43-.99.21-2.72-1.66-6.14-2.04-10.17-1.12-.39.09-.78-.15-.87-.54-.09-.39.15-.78.54-.87 4.41-1.01 8.19-.57 11.25 1.29.33.21.43.67.24.99v.04zm1.24-2.76c-.26.41-.81.54-1.22.28-3.11-1.91-7.85-2.47-11.53-1.35-.48.14-.98-.13-1.12-.61-.14-.48.13-.98.61-1.12 4.21-1.28 9.44-.65 13.02 1.54.41.26.54.81.24 1.26zm.11-2.87C14.25 8.84 8.56 8.64 5.13 9.67c-.57.17-1.18-.15-1.36-.73-.17-.57.15-1.18.73-1.36 3.97-1.21 10.56-.97 14.73 1.57.49.3.65.93.35 1.42-.3.49-.93.64-1.42.35l-.17-.13z"/></svg>
      </div>
      Spotify
    </button>
    <button class="app-btn" data-app="amazon" data-app-id="amazon">
      <div class="app-icon" style="background:#00A8E0; color:white">P</div>
      Prime
    </button>
    <button class="app-btn" data-app="disneyplus" data-app-id="disneyplus">
      <div class="app-icon" style="background:#113CCF; color:white">D+</div>
      Disney+
    </button>
    <button class="app-btn" data-app="tiktok" data-app-id="tiktok">
      <div class="app-icon" style="background:#010101; color:white">T</div>
      TikTok
    </button>
    <button class="app-btn" data-app="browser" data-app-id="com.webos.app.browser">
      <div class="app-icon" style="background:#4285F4; color:white">
        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="white" stroke-width="2.5" stroke-linecap="round"><circle cx="12" cy="12" r="10"/><line x1="2" y1="12" x2="22" y2="12"/><path d="M12 2a15.3 15.3 0 0 1 4 10 15.3 15.3 0 0 1-4 10 15.3 15.3 0 0 1-4-10 15.3 15.3 0 0 1 4-10z"/></svg>
      </div>
      Browser
    </button>
    <button class="app-btn" data-app-id="com.webos.app.hdmi1" data-key="HDMI">
      <div class="app-icon" style="background:#333; color:#aaa;">
        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><rect x="2" y="7" width="20" height="10" rx="2"/><path d="M7 7V5M12 7V5M17 7V5"/></svg>
      </div>
      HDMI
    </button>
  </div>

  <!-- INPUT SELECT -->
  <div class="section-label" style="margin-top:4px;">Input source</div>
  <div class="input-row">
    <button class="input-chip" data-input="HDMI1">HDMI 1</button>
    <button class="input-chip" data-input="HDMI2">HDMI 2</button>
    <button class="input-chip" data-input="HDMI3">HDMI 3</button>
    <button class="input-chip" data-input="AV1">AV</button>
    <button class="input-chip" data-input="COMPONENT1">Component</button>
    <button class="input-chip" data-input="TV">TV</button>
  </div>

  <!-- NUMBER PAD -->
  <div class="numpad-section">
    <div class="section-label">Channels</div>
    <div class="numpad">
      <button class="num-btn" data-key="1">1</button>
      <button class="num-btn" data-key="2">2</button>
      <button class="num-btn" data-key="3">3</button>
      <button class="num-btn" data-key="4">4</button>
      <button class="num-btn" data-key="5">5</button>
      <button class="num-btn" data-key="6">6</button>
      <button class="num-btn" data-key="7">7</button>
      <button class="num-btn" data-key="8">8</button>
      <button class="num-btn" data-key="9">9</button>
      <button class="num-btn" data-key="DASH" style="font-size:13px;">DASH</button>
      <button class="num-btn" data-key="0">0</button>
      <button class="num-btn" data-key="EXIT" style="font-size:13px; color:#f44336;">EXIT</button>
    </div>
  </div>
</div>

<!-- TOAST -->
<div class="toast" id="toast"></div>

<script>
const LGTV = {
  ip: localStorage.getItem('lg_ip') || '192.168.50.19',
  name: localStorage.getItem('lg_name') || 'My LG TV',
  clientKey: localStorage.getItem('lg_key') || null,
  ws: null,
  cmdId: 1,
  callbacks: {},
  connected: false,

  getWsUrl() { return `ws://${this.ip}:3000`; },

  connect(onPair, onConnected, onError) {
    if (this.ws) { try { this.ws.close(); } catch(e){} }
    this.ws = new WebSocket(this.getWsUrl());

    this.ws.onopen = () => {
      const hello = {
        type: 'register', id: 'reg0',
        payload: {
          forcePairing: false,
          pairingType: 'PROMPT',
          manifest: {
            manifestVersion: 1,
            appVersion: '1.1',
            signed: { created: '20140509', appId: 'com.lge.test', vendorId: 'com.lge', localizedAppNames: {'': 'LG Remote', 'ko-KR': 'LG 리모컨'}, localizedVendorNames: {'': 'LG Electronics'}, permissions: ['TEST_SECURE','CONTROL_INPUT_TEXT','CONTROL_MOUSE_AND_KEYBOARD','READ_INSTALLED_APPS','READ_LGE_SDX','READ_NOTIFICATIONS','SEARCH','WRITE_SETTINGS','WRITE_NOTIFICATION_ALERT','CONTROL_POWER','READ_CURRENT_CHANNEL','READ_RUNNING_APPS','READ_UPDATE_INFO','UPDATE_FROM_REMOTE_APP','READ_LGE_TV_INPUT_EVENTS','READ_TV_CURRENT_TIME'] }
          },
          'client-key': this.clientKey
        }
      };
      this.ws.send(JSON.stringify(hello));
    };

    this.ws.onmessage = (evt) => {
      let msg;
      try { msg = JSON.parse(evt.data); } catch(e) { return; }
      if (msg.type === 'registered') {
        this.clientKey = msg.payload['client-key'];
        localStorage.setItem('lg_key', this.clientKey);
        this.connected = true;
        onConnected && onConnected();
      } else if (msg.type === 'response' || msg.type === 'error') {
        const cb = this.callbacks[msg.id];
        if (cb) { cb(msg); delete this.callbacks[msg.id]; }
      } else if (msg.type === 'prompt' || (msg.payload && msg.payload.pairingType === 'PROMPT')) {
        onPair && onPair(msg);
      }
    };

    this.ws.onerror = () => { onError && onError(); };
    this.ws.onclose = () => {
      this.connected = false;
      updateBadge(false);
    };
  },

  send(uri, payload, cb) {
    if (!this.ws || this.ws.readyState !== 1) { showToast('Not connected'); return; }
    const id = 'msg_' + (this.cmdId++);
    const msg = { type: 'request', id, uri, payload: payload || {} };
    if (cb) this.callbacks[id] = cb;
    this.ws.send(JSON.stringify(msg));
  },

  sendKey(key) {
    if (!this.ws || this.ws.readyState !== 1) { showToast('Not connected'); return; }
    const id = 'key_' + (this.cmdId++);
    this.ws.send(JSON.stringify({ type: 'button', name: key }));
  },

  launch(appId) {
    this.send('ssap://system.launcher/launch', { id: appId });
  },

  switchInput(input) {
    this.send('ssap://tv/switchInput', { inputId: input });
  }
};

function showToast(msg, dur = 2000) {
  const t = document.getElementById('toast');
  t.textContent = msg;
  t.classList.add('show');
  setTimeout(() => t.classList.remove('show'), dur);
}

function updateBadge(connected) {
  const b = document.getElementById('conn-badge');
  if (connected) { b.textContent = 'Connected'; b.className = 'conn-badge'; }
  else { b.textContent = 'Disconnected'; b.className = 'conn-badge off'; }
}

function showRemote() {
  document.getElementById('screen-setup').classList.remove('active');
  document.getElementById('screen-remote').classList.add('active');
  document.getElementById('tv-label').textContent = LGTV.name;
  document.getElementById('tv-ip-label').textContent = LGTV.ip;
}

function showSetup() {
  document.getElementById('screen-remote').classList.remove('active');
  document.getElementById('screen-setup').classList.add('active');
}

function doConnect() {
  const ip = document.getElementById('ip-input').value.trim();
  const name = document.getElementById('name-input').value.trim() || 'My LG TV';
  if (!ip) { setStatus('Enter your TV IP address', 'err'); return; }
  LGTV.ip = ip;
  LGTV.name = name;
  localStorage.setItem('lg_ip', ip);
  localStorage.setItem('lg_name', name);
  setStatus('Connecting...', 'info');

  LGTV.connect(
    (pairMsg) => {
      document.getElementById('pair-modal').classList.add('show');
      setStatus('Check your TV screen', 'info');
    },
    () => {
      document.getElementById('pair-modal').classList.remove('show');
      setStatus('', '');
      updateBadge(true);
      showRemote();
      showToast('Connected to ' + LGTV.name);
    },
    () => {
      setStatus('Could not reach TV. Check IP and Wi-Fi.', 'err');
      document.getElementById('pair-modal').classList.remove('show');
    }
  );
}

function setStatus(msg, type) {
  const el = document.getElementById('setup-status');
  el.textContent = msg;
  el.className = 'status-msg ' + type;
}

document.getElementById('connect-btn').addEventListener('click', doConnect);
document.getElementById('ip-input').addEventListener('keydown', e => { if (e.key === 'Enter') doConnect(); });

document.getElementById('pair-confirm').addEventListener('click', () => {
  document.getElementById('pair-modal').classList.remove('show');
});
document.getElementById('pair-cancel').addEventListener('click', () => {
  document.getElementById('pair-modal').classList.remove('show');
  if (LGTV.ws) LGTV.ws.close();
  setStatus('Pairing cancelled', 'err');
});

document.getElementById('settings-btn').addEventListener('click', () => {
  showSetup();
  document.getElementById('ip-input').value = LGTV.ip;
  document.getElementById('name-input').value = LGTV.name;
});

document.querySelectorAll('[data-key]').forEach(btn => {
  btn.addEventListener('click', () => {
    const key = btn.dataset.key;
    LGTV.sendKey(key);
    showToast(btn.textContent.trim() || key);
  });
});

document.querySelectorAll('[data-app-id]').forEach(btn => {
  if (btn.dataset.key) return;
  btn.addEventListener('click', () => {
    LGTV.launch(btn.dataset.appId);
    showToast('Opening ' + btn.textContent.trim());
  });
});

document.querySelectorAll('[data-input]').forEach(btn => {
  btn.addEventListener('click', () => {
    document.querySelectorAll('[data-input]').forEach(b => b.classList.remove('active-input'));
    btn.classList.add('active-input');
    LGTV.switchInput(btn.dataset.input);
    showToast('Switched to ' + btn.dataset.input);
  });
});

if (LGTV.ip && LGTV.clientKey) {
  document.getElementById('ip-input').value = LGTV.ip;
  document.getElementById('name-input').value = LGTV.name;
  doConnect();
}
</script>
</body>
</html>
