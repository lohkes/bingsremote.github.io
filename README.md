<!DOCTYPE html>
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
  .input-row { display: flex; gap: 8px; margin
