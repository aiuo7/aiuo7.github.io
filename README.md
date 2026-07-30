[Uploading index.html.html…]()
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.5, user-scalable=yes" />
    <title>液态玻璃 · 个人主页</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css" />
    <style>
        /* ===== CSS变量 ===== */
        :root {
            --glass-border: rgba(255, 255, 255, 0.35);
            --glass-border-strong: rgba(255, 255, 255, 0.5);
            --glass-shadow: 0 20px 60px rgba(0, 0, 0, 0.4);
            --glass-radius: 28px;
            --text-primary: #fff;
            --text-secondary: rgba(255, 255, 255, 0.8);
            --transition: 0.4s cubic-bezier(0.22, 1, 0.36, 1);
            --font: 'Segoe UI', system-ui, -apple-system, sans-serif;
            --nick-font: 'Segoe UI', sans-serif;
            --nick-size: 28px;
            --nick-color1: #f093fb;
            --nick-color2: #f5576c;
            --nick-dir: 135deg;
            --avatar-border-color: #ffffff;
            --avatar-border-width: 3px;
            --avatar-border-style: solid;
            --avatar-border-radius: 50%;
            --weather-type: sunny;
            --bg-mode: dynamic;
            --border-color: rgba(255, 255, 255, 0.25);
            --border-width: 2px;
            --border-opacity: 1;
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: var(--font);
            min-height: 100vh;
            overflow-x: hidden;
            color: var(--text-primary);
            background: #0a0a1a;
            user-select: none;
        }

        /* ===== 背景层 ===== */
        #bg-layer {
            position: fixed;
            inset: 0;
            z-index: 0;
            background: linear-gradient(145deg, #0f0c2a, #1a1a3e, #2d1b4e);
            transition: all 0.8s ease;
            overflow: hidden;
        }
        #bg-layer.dynamic {
            background: linear-gradient(-45deg, #0f0c2a, #1a1a3e, #2d1b4e, #1f1f4a);
            background-size: 400% 400%;
            animation: bgShift 20s ease infinite;
        }
        @keyframes bgShift {
            0%,
            100% {
                background-position: 0% 50%;
            }
            50% {
                background-position: 100% 50%;
            }
        }
        #bg-layer.static {
            background-size: cover;
            background-position: center;
            background-repeat: no-repeat;
        }
        #bg-layer.static .bg-image {
            width: 100%;
            height: 100%;
            object-fit: cover;
            display: block;
        }
        #bg-layer.slideshow .bg-slide {
            position: absolute;
            inset: 0;
            width: 100%;
            height: 100%;
            object-fit: cover;
            opacity: 0;
            transition: opacity 1.8s ease;
        }
        #bg-layer.slideshow .bg-slide.active {
            opacity: 1;
        }

        /* ===== 天气 Canvas ===== */
        #weatherCanvas {
            position: fixed;
            inset: 0;
            z-index: 1;
            pointer-events: none;
            width: 100%;
            height: 100%;
        }

        /* ===== 歌词雨 Canvas ===== */
        #lyricRainCanvas {
            position: fixed;
            inset: 0;
            z-index: 1;
            pointer-events: none;
            width: 100%;
            height: 100%;
            opacity: 0;
            transition: opacity 1.2s ease;
        }
        #lyricRainCanvas.active {
            opacity: 1;
        }

        /* ===== 顶部栏 ===== */
        .top-bar {
            position: fixed;
            top: 18px;
            right: 18px;
            z-index: 300;
            display: flex;
            justify-content: flex-end;
            align-items: center;
            pointer-events: none;
            gap: 10px;
        }
        .top-bar>* {
            pointer-events: auto;
        }

        .settings-toggle {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            background: rgba(255, 255, 255, 0.04);
            backdrop-filter: blur(16px);
            -webkit-backdrop-filter: blur(16px);
            border: 1px solid rgba(255, 255, 255, 0.06);
            color: #fff;
            font-size: 16px;
            cursor: pointer;
            transition: all var(--transition);
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.12);
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .settings-toggle:hover {
            transform: rotate(90deg) scale(1.06);
            background: rgba(255, 255, 255, 0.08);
        }

        /* 天气信息 - 极简显示 */
        .weather-display {
            position: fixed;
            top: 20px;
            left: 20px;
            z-index: 300;
            font-size: 13px;
            color: rgba(255, 255, 255, 0.3);
            background: rgba(255, 255, 255, 0.02);
            backdrop-filter: blur(8px);
            padding: 6px 14px;
            border-radius: 20px;
            border: 1px solid rgba(255, 255, 255, 0.03);
            pointer-events: none;
            display: flex;
            align-items: center;
            gap: 6px;
            font-weight: 300;
            letter-spacing: 0.3px;
        }
        .weather-display i {
            font-size: 14px;
            color: rgba(255, 255, 255, 0.2);
        }
        .weather-display span {
            color: rgba(255, 255, 255, 0.15);
        }

        /* ===== 主容器 ===== */
        .app-container {
            position: relative;
            z-index: 2;
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 76px 16px 100px;
        }

        /* ===== 液态玻璃边框 ===== */
        .glass-border-wrap {
            padding: var(--border-width);
            border-radius: var(--glass-radius);
            background: transparent;
            box-shadow: var(--glass-shadow), inset 0 1px 0 rgba(255, 255, 255, 0.06);
            width: 100%;
            max-width: 780px;
            position: relative;
            transition: all var(--transition);
        }
        .glass-border-wrap::before {
            content: '';
            position: absolute;
            inset: calc(-1 * var(--border-width));
            border-radius: calc(var(--glass-radius) + var(--border-width));
            padding: var(--border-width);
            background: conic-gradient(from 0deg at 50% 50%,
                    rgba(255, 255, 255, calc(0.5 * var(--border-opacity))) 0%,
                    rgba(255, 255, 255, calc(0.06 * var(--border-opacity))) 25%,
                    rgba(255, 255, 255, calc(0.35 * var(--border-opacity))) 50%,
                    rgba(255, 255, 255, calc(0.04 * var(--border-opacity))) 75%,
                    rgba(255, 255, 255, calc(0.5 * var(--border-opacity))) 100%);
            -webkit-mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0);
            -webkit-mask-composite: xor;
            mask-composite: exclude;
            pointer-events: none;
            z-index: 0;
            transition: all 0.6s ease;
        }
        .glass-border-wrap::after {
            content: '';
            position: absolute;
            inset: 0px;
            border-radius: var(--glass-radius);
            background: radial-gradient(ellipse at 25% 20%, rgba(255, 255, 255, 0.06) 0%, transparent 60%),
                radial-gradient(ellipse at 75% 80%, rgba(255, 255, 255, 0.03) 0%, transparent 50%);
            pointer-events: none;
            z-index: 0;
        }

        /* ===== 玻璃卡片 ===== */
        .glass-card {
            background: rgba(255, 255, 255, 0);
            backdrop-filter: blur(0px);
            -webkit-backdrop-filter: blur(0px);
            border-radius: calc(var(--glass-radius) - var(--border-width));
            padding: 28px 24px 24px;
            position: relative;
            z-index: 1;
            border: 1px solid rgba(255, 255, 255, 0.02);
        }
        .glass-card>* {
            position: relative;
            z-index: 2;
        }

        /* ===== 头像 ===== */
        .profile-header {
            display: flex;
            flex-direction: column;
            align-items: center;
            margin-bottom: 4px;
        }

        .avatar-wrapper {
            width: 120px;
            height: 120px;
            flex-shrink: 0;
            margin-bottom: 10px;
            position: relative;
            border-radius: var(--avatar-border-radius);
            border: var(--avatar-border-width) solid var(--avatar-border-color);
            border-style: var(--avatar-border-style);
            box-shadow: 0 12px 48px rgba(0, 0, 0, 0.25), inset 0 0 30px rgba(255, 255, 255, 0.02);
            transition: all var(--transition);
            overflow: hidden;
            cursor: pointer;
        }
        .avatar-wrapper.glow {
            box-shadow: 0 0 40px rgba(255, 255, 255, 0.06), 0 12px 48px rgba(0, 0, 0, 0.25);
        }
        .avatar-wrapper.double {
            border-style: double;
            border-width: calc(var(--avatar-border-width) * 2);
        }
        .avatar-wrapper.dashed {
            border-style: dashed;
        }
        .avatar-wrapper.dotted {
            border-style: dotted;
        }
        .avatar-wrapper img {
            width: 100%;
            height: 100%;
            object-fit: cover;
            display: block;
            border-radius: var(--avatar-border-radius);
            transition: all var(--transition);
        }
        .avatar-wrapper:hover img {
            transform: scale(1.02);
        }
        .avatar-wrapper .avatar-hint {
            position: absolute;
            inset: 0;
            display: flex;
            align-items: center;
            justify-content: center;
            background: rgba(0, 0, 0, 0.3);
            backdrop-filter: blur(4px);
            color: #fff;
            font-size: 13px;
            opacity: 0;
            transition: all var(--transition);
            border-radius: var(--avatar-border-radius);
            pointer-events: none;
        }
        .avatar-wrapper:hover .avatar-hint {
            opacity: 1;
        }

        /* ===== 昵称 ===== */
        .nickname-wrap {
            text-align: center;
            padding: 0 8px;
            margin-top: 2px;
        }
        .nickname-text {
            font-family: var(--nick-font);
            font-size: var(--nick-size);
            font-weight: 700;
            background: linear-gradient(var(--nick-dir), var(--nick-color1), var(--nick-color2));
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
            letter-spacing: 0.5px;
            cursor: pointer;
            transition: all var(--transition);
            padding: 4px 16px;
            border-radius: 16px;
            display: inline-block;
            text-shadow: 0 2px 20px rgba(0, 0, 0, 0.1);
        }
        .nickname-text:hover {
            transform: scale(1.02);
            filter: brightness(1.08);
        }

        /* ===== 留言板 ===== */
        .message-board {
            margin: 10px auto 14px;
            max-width: 520px;
            padding: 0 12px;
            text-align: center;
            min-height: 44px;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .message-text {
            font-size: 15px;
            color: rgba(255, 255, 255, 0.65);
            font-weight: 400;
            letter-spacing: 0.3px;
            line-height: 1.7;
            padding: 6px 18px;
            border-radius: 16px;
            background: rgba(255, 255, 255, 0.02);
            border: 1px solid rgba(255, 255, 255, 0.03);
            cursor: pointer;
            transition: all var(--transition);
            min-height: 38px;
            display: inline-block;
            position: relative;
            backdrop-filter: blur(4px);
            max-width: 100%;
        }
        .message-text:hover {
            background: rgba(255, 255, 255, 0.04);
            border-color: rgba(255, 255, 255, 0.06);
        }
        .message-text .cursor-blink {
            display: inline-block;
            width: 2px;
            height: 1.1em;
            background: rgba(255, 255, 255, 0.3);
            margin-left: 2px;
            vertical-align: text-bottom;
            animation: blink 0.7s step-end infinite;
        }
        @keyframes blink {
            0%,
            100% {
                opacity: 1;
            }
            50% {
                opacity: 0;
            }
        }
        .message-text .char {
            display: inline;
            transition: all 0.02s;
        }
        .message-text .char.highlight {
            color: rgba(255, 255, 255, 0.85);
        }
        .message-empty {
            color: rgba(255, 255, 255, 0.15);
            font-size: 13px;
            font-style: italic;
        }

        /* ===== 文件网格 ===== */
        .files-section {
            margin-top: 14px;
        }
        .files-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(130px, 1fr));
            gap: 12px;
            min-height: 120px;
        }
        .files-grid .file-item {
            aspect-ratio: 1;
            border-radius: 14px;
            overflow: hidden;
            border: 2px solid rgba(255, 255, 255, 0.04);
            transition: all var(--transition);
            background: rgba(255, 255, 255, 0.02);
            position: relative;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
        }
        .files-grid .file-item:hover {
            transform: scale(1.04);
            border-color: rgba(255, 255, 255, 0.12);
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.15);
        }
        .files-grid .file-item img {
            width: 100%;
            height: 100%;
            object-fit: cover;
            display: block;
        }
        .files-grid .file-item video {
            width: 100%;
            height: 100%;
            object-fit: cover;
            display: block;
        }
        .files-grid .file-item .file-icon {
            font-size: 40px;
            color: rgba(255, 255, 255, 0.12);
        }
        .files-grid .file-item .file-type-badge {
            position: absolute;
            top: 6px;
            left: 6px;
            font-size: 10px;
            padding: 2px 8px;
            border-radius: 6px;
            background: rgba(0, 0, 0, 0.35);
            backdrop-filter: blur(4px);
            color: rgba(255, 255, 255, 0.4);
            pointer-events: none;
        }
        .files-grid .file-item .file-del {
            position: absolute;
            top: 4px;
            right: 4px;
            width: 24px;
            height: 24px;
            border-radius: 50%;
            background: rgba(0, 0, 0, 0.35);
            backdrop-filter: blur(4px);
            border: none;
            color: #fff;
            font-size: 10px;
            cursor: pointer;
            opacity: 0;
            transition: all var(--transition);
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .files-grid .file-item:hover .file-del {
            opacity: 1;
        }
        .files-grid .file-item .file-del:hover {
            background: rgba(220, 50, 50, 0.5);
        }
        .files-grid .file-item .play-overlay {
            position: absolute;
            inset: 0;
            display: flex;
            align-items: center;
            justify-content: center;
            background: rgba(0, 0, 0, 0.1);
            font-size: 34px;
            color: rgba(255, 255, 255, 0.2);
            pointer-events: none;
            transition: all var(--transition);
        }
        .files-grid .file-item:hover .play-overlay {
            color: rgba(255, 255, 255, 0.5);
        }
        .files-empty {
            grid-column: 1 / -1;
            text-align: center;
            padding: 40px 20px;
            color: rgba(255, 255, 255, 0.12);
            font-size: 13px;
        }
        .files-empty i {
            font-size: 34px;
            display: block;
            margin-bottom: 10px;
            opacity: 0.3;
        }

        .upload-area {
            display: flex;
            justify-content: center;
            gap: 10px;
            margin-top: 14px;
            flex-wrap: wrap;
        }
        .upload-btn {
            display: inline-flex;
            align-items: center;
            gap: 6px;
            padding: 8px 18px;
            background: rgba(255, 255, 255, 0.02);
            border: 1px dashed rgba(255, 255, 255, 0.06);
            border-radius: 12px;
            color: rgba(255, 255, 255, 0.25);
            font-size: 12px;
            cursor: pointer;
            transition: all var(--transition);
            font-weight: 500;
        }
        .upload-btn:hover {
            background: rgba(255, 255, 255, 0.04);
            border-color: rgba(255, 255, 255, 0.12);
            color: rgba(255, 255, 255, 0.5);
        }

        /* ===== 底部播放器 ===== */
        .player-bar {
            position: fixed;
            bottom: 12px;
            left: 50%;
            transform: translateX(-50%);
            z-index: 100;
            width: 92%;
            max-width: 780px;
            background: rgba(12, 12, 32, 0.4);
            backdrop-filter: blur(28px);
            -webkit-backdrop-filter: blur(28px);
            border-radius: 20px;
            border: 1px solid rgba(255, 255, 255, 0.04);
            padding: 10px 16px;
            display: flex;
            align-items: center;
            gap: 10px;
            flex-wrap: wrap;
            transition: all var(--transition);
            box-shadow: 0 12px 48px rgba(0, 0, 0, 0.2);
        }
        .player-bar::before {
            content: '';
            position: absolute;
            inset: 0;
            border-radius: 20px;
            background: radial-gradient(ellipse at 20% 30%, rgba(255, 255, 255, 0.03) 0%, transparent 60%);
            pointer-events: none;
        }

        .player-bar .p-controls {
            display: flex;
            align-items: center;
            gap: 2px;
            flex-shrink: 0;
        }
        .player-bar .p-controls button {
            background: none;
            border: none;
            color: rgba(255, 255, 255, 0.3);
            font-size: 14px;
            width: 30px;
            height: 30px;
            border-radius: 50%;
            cursor: pointer;
            transition: all var(--transition);
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .player-bar .p-controls button:hover {
            color: #fff;
            background: rgba(255, 255, 255, 0.03);
        }
        .player-bar .p-controls .play-btn {
            font-size: 17px;
            color: #fff;
            background: rgba(255, 255, 255, 0.04);
            width: 36px;
            height: 36px;
        }
        .player-bar .p-controls .play-btn:hover {
            background: rgba(255, 255, 255, 0.08);
        }

        .player-bar .disc-wrap {
            width: 36px;
            height: 36px;
            flex-shrink: 0;
            border-radius: 50%;
            background: radial-gradient(circle at 40% 40%, #2a2a3a, #0a0a12);
            box-shadow: inset 0 0 20px rgba(0, 0, 0, 0.5), 0 4px 20px rgba(0, 0, 0, 0.2);
            position: relative;
            display: flex;
            align-items: center;
            justify-content: center;
            animation: discSpin 4s linear infinite;
            animation-play-state: paused;
            border: 2px solid rgba(255, 255, 255, 0.03);
        }
        .player-bar .disc-wrap.playing {
            animation-play-state: running;
        }
        .player-bar .disc-wrap::after {
            content: '';
            width: 10px;
            height: 10px;
            border-radius: 50%;
            background: radial-gradient(circle at 45% 45%, #555, #111);
            box-shadow: inset 0 0 10px rgba(0, 0, 0, 0.4);
        }
        .player-bar .disc-wrap .disc-label {
            position: absolute;
            font-size: 5px;
            color: rgba(255, 255, 255, 0.05);
            letter-spacing: 2px;
            text-transform: uppercase;
        }
        @keyframes discSpin {
            0% {
                transform: rotate(0deg);
            }
            100% {
                transform: rotate(360deg);
            }
        }

        .player-bar .p-info {
            font-size: 12px;
            color: rgba(255, 255, 255, 0.35);
            min-width: 50px;
            max-width: 100px;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
            flex-shrink: 0;
        }

        .player-bar .p-progress {
            flex: 1;
            min-width: 50px;
            display: flex;
            align-items: center;
            gap: 6px;
        }
        .player-bar .p-progress input[type="range"] {
            flex: 1;
            height: 3px;
            -webkit-appearance: none;
            appearance: none;
            background: rgba(255, 255, 255, 0.06);
            border-radius: 4px;
            outline: none;
        }
        .player-bar .p-progress input[type="range"]::-webkit-slider-thumb {
            -webkit-appearance: none;
            appearance: none;
            width: 11px;
            height: 11px;
            border-radius: 50%;
            background: #fff;
            cursor: pointer;
            box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
        }
        .player-bar .p-progress input[type="range"]::-moz-range-thumb {
            width: 11px;
            height: 11px;
            border-radius: 50%;
            background: #fff;
            cursor: pointer;
            border: none;
        }
        .player-bar .p-time {
            font-size: 10px;
            color: rgba(255, 255, 255, 0.15);
            min-width: 32px;
            font-variant-numeric: tabular-nums;
        }

        .player-bar .p-actions {
            display: flex;
            align-items: center;
            gap: 2px;
            flex-shrink: 0;
        }
        .player-bar .p-actions button {
            background: none;
            border: none;
            color: rgba(255, 255, 255, 0.15);
            font-size: 14px;
            cursor: pointer;
            transition: all var(--transition);
            padding: 4px 6px;
            border-radius: 6px;
        }
        .player-bar .p-actions button:hover {
            color: rgba(255, 255, 255, 0.4);
            background: rgba(255, 255, 255, 0.02);
        }
        .player-bar .p-actions button.active {
            color: #6dd5ed;
        }

        /* ===== 设置面板 ===== */
        .settings-overlay {
            position: fixed;
            inset: 0;
            z-index: 400;
            background: rgba(0, 0, 0, 0.12);
            backdrop-filter: blur(6px);
            opacity: 0;
            pointer-events: none;
            transition: opacity 0.5s ease;
        }
        .settings-overlay.show {
            opacity: 1;
            pointer-events: auto;
        }

        .settings-panel {
            position: fixed;
            top: 0;
            right: -460px;
            width: 440px;
            max-width: 92vw;
            height: 100vh;
            z-index: 500;
            background: rgba(10, 10, 30, 0.7);
            backdrop-filter: blur(40px);
            -webkit-backdrop-filter: blur(40px);
            border-left: 1px solid rgba(255, 255, 255, 0.03);
            padding: 24px 20px 30px;
            overflow-y: auto;
            transition: right 0.5s cubic-bezier(0.22, 1, 0.36, 1);
            box-shadow: -12px 0 60px rgba(0, 0, 0, 0.15);
        }
        .settings-panel.open {
            right: 0;
        }
        .settings-panel::-webkit-scrollbar {
            width: 3px;
        }
        .settings-panel::-webkit-scrollbar-thumb {
            background: rgba(255, 255, 255, 0.05);
            border-radius: 4px;
        }

        .settings-panel .s-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding-bottom: 14px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.02);
            margin-bottom: 18px;
        }
        .settings-panel .s-header h2 {
            font-size: 17px;
            font-weight: 600;
            color: #fff;
            display: flex;
            align-items: center;
            gap: 8px;
        }
        .settings-panel .s-header .s-close {
            background: none;
            border: none;
            color: rgba(255, 255, 255, 0.12);
            font-size: 20px;
            cursor: pointer;
            transition: all var(--transition);
            padding: 4px;
        }
        .settings-panel .s-header .s-close:hover {
            color: #fff;
            transform: rotate(90deg);
        }

        .settings-panel .s-group {
            margin-bottom: 16px;
        }
        .settings-panel .s-group label {
            display: block;
            font-size: 10px;
            font-weight: 500;
            color: rgba(255, 255, 255, 0.18);
            margin-bottom: 4px;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }
        .settings-panel .s-group .s-row {
            display: flex;
            flex-wrap: wrap;
            gap: 4px;
            align-items: center;
        }
        .settings-panel .s-group .s-btn {
            padding: 4px 12px;
            border: 1px solid rgba(255, 255, 255, 0.03);
            border-radius: 8px;
            background: rgba(255, 255, 255, 0.01);
            color: rgba(255, 255, 255, 0.25);
            font-size: 11px;
            cursor: pointer;
            transition: all var(--transition);
        }
        .settings-panel .s-group .s-btn:hover {
            background: rgba(255, 255, 255, 0.02);
            color: rgba(255, 255, 255, 0.5);
        }
        .settings-panel .s-group .s-btn.active {
            background: rgba(255, 255, 255, 0.03);
            border-color: rgba(255, 255, 255, 0.05);
            color: #fff;
        }
        .settings-panel .s-group input[type="file"] {
            display: none;
        }
        .settings-panel .s-group .file-btn {
            display: inline-flex;
            align-items: center;
            gap: 4px;
            padding: 4px 12px;
            border: 1px solid rgba(255, 255, 255, 0.03);
            border-radius: 8px;
            background: rgba(255, 255, 255, 0.01);
            color: rgba(255, 255, 255, 0.25);
            font-size: 11px;
            cursor: pointer;
            transition: all var(--transition);
        }
        .settings-panel .s-group .file-btn:hover {
            background: rgba(255, 255, 255, 0.02);
            color: rgba(255, 255, 255, 0.5);
        }
        .settings-panel .s-group input[type="text"],
        .settings-panel .s-group input[type="number"],
        .settings-panel .s-group select,
        .settings-panel .s-group input[type="color"] {
            padding: 4px 10px;
            border-radius: 8px;
            border: 1px solid rgba(255, 255, 255, 0.03);
            background: rgba(255, 255, 255, 0.01);
            color: #fff;
            font-size: 12px;
            outline: none;
            transition: all var(--transition);
        }
        .settings-panel .s-group input[type="text"]:focus,
        .settings-panel .s-group select:focus {
            border-color: rgba(255, 255, 255, 0.06);
            background: rgba(255, 255, 255, 0.02);
        }
        .settings-panel .s-group select option {
            background: #1a1a3e;
        }
        .settings-panel .s-group .color-wrap {
            display: flex;
            align-items: center;
            gap: 4px;
        }
        .settings-panel .s-group input[type="color"] {
            width: 26px;
            height: 26px;
            padding: 2px;
            border-radius: 6px;
            cursor: pointer;
        }
        .settings-panel .s-group .range-wrap {
            display: flex;
            align-items: center;
            gap: 6px;
            flex: 1;
            min-width: 70px;
        }
        .settings-panel .s-group .range-wrap input[type="range"] {
            flex: 1;
            height: 3px;
            -webkit-appearance: none;
            appearance: none;
            background: rgba(255, 255, 255, 0.05);
            border-radius: 4px;
            outline: none;
        }
        .settings-panel .s-group .range-wrap input[type="range"]::-webkit-slider-thumb {
            -webkit-appearance: none;
            appearance: none;
            width: 11px;
            height: 11px;
            border-radius: 50%;
            background: #fff;
            cursor: pointer;
        }
        .settings-panel .s-group .range-wrap span {
            font-size: 11px;
            color: rgba(255, 255, 255, 0.15);
            min-width: 26px;
            text-align: center;
        }

        .settings-panel .share-row {
            display: flex;
            gap: 5px;
            flex-wrap: wrap;
            margin-top: 4px;
        }
        .settings-panel .share-row .share-btn {
            padding: 4px 12px;
            border: 1px solid rgba(255, 255, 255, 0.03);
            border-radius: 8px;
            background: rgba(255, 255, 255, 0.01);
            color: rgba(255, 255, 255, 0.25);
            font-size: 11px;
            cursor: pointer;
            transition: all var(--transition);
            display: inline-flex;
            align-items: center;
            gap: 4px;
        }
        .settings-panel .share-row .share-btn:hover {
            background: rgba(255, 255, 255, 0.02);
            color: #fff;
        }
        .settings-panel .share-row .share-btn.primary {
            background: rgba(100, 180, 255, 0.04);
            border-color: rgba(100, 180, 255, 0.04);
            color: rgba(100, 180, 255, 0.4);
        }
        .settings-panel .share-row .share-btn.primary:hover {
            background: rgba(100, 180, 255, 0.06);
            color: rgba(100, 180, 255, 0.7);
        }
        .settings-panel .deploy-tip {
            font-size: 10px;
            color: rgba(255, 255, 255, 0.08);
            margin-top: 4px;
            line-height: 1.5;
        }
        .settings-panel .deploy-tip a {
            color: rgba(100, 180, 255, 0.3);
            text-decoration: none;
        }
        .settings-panel .deploy-tip a:hover {
            color: rgba(100, 180, 255, 0.5);
        }

        /* ===== Toast ===== */
        .toast {
            position: fixed;
            bottom: 90px;
            left: 50%;
            transform: translateX(-50%);
            z-index: 600;
            background: rgba(10, 10, 30, 0.55);
            backdrop-filter: blur(16px);
            border: 1px solid rgba(255, 255, 255, 0.03);
            border-radius: 12px;
            padding: 10px 22px;
            color: rgba(255, 255, 255, 0.6);
            font-size: 13px;
            opacity: 0;
            transition: all 0.5s ease;
            pointer-events: none;
            box-shadow: 0 12px 48px rgba(0, 0, 0, 0.15);
            white-space: nowrap;
            max-width: 90vw;
            overflow: hidden;
            text-overflow: ellipsis;
        }
        .toast.show {
            opacity: 1;
            transform: translateX(-50%) translateY(-6px);
        }

        /* ===== 响应式 ===== */
        @media (max-width: 640px) {
            .glass-card {
                padding: 20px 14px 16px;
            }
            .avatar-wrapper {
                width: 96px;
                height: 96px;
            }
            .nickname-text {
                font-size: calc(var(--nick-size) * 0.78);
            }
            .files-grid {
                grid-template-columns: repeat(auto-fill, minmax(90px, 1fr));
                gap: 8px;
            }
            .player-bar {
                padding: 8px 12px;
                gap: 6px;
                bottom: 8px;
                width: 96%;
                border-radius: 16px;
            }
            .player-bar .p-info {
                max-width: 50px;
                font-size: 11px;
            }
            .player-bar .disc-wrap {
                width: 30px;
                height: 30px;
            }
            .player-bar .p-controls .play-btn {
                width: 30px;
                height: 30px;
                font-size: 15px;
            }
            .settings-panel {
                width: 100%;
                max-width: 100vw;
                right: -100%;
                padding: 16px 14px;
            }
            .top-bar {
                top: 12px;
                right: 12px;
            }
            .settings-toggle {
                width: 34px;
                height: 34px;
                font-size: 14px;
            }
            .weather-display {
                font-size: 11px;
                padding: 4px 10px;
                top: 14px;
                left: 14px;
            }
            .toast {
                bottom: 76px;
                font-size: 12px;
                padding: 8px 16px;
            }
            .app-container {
                padding: 60px 10px 80px;
            }
            .message-text {
                font-size: 14px;
                padding: 4px 12px;
            }
        }

        @media (max-width: 400px) {
            .files-grid {
                grid-template-columns: repeat(auto-fill, minmax(72px, 1fr));
                gap: 6px;
            }
            .glass-card {
                padding: 14px 10px 12px;
            }
            .player-bar .p-progress {
                min-width: 30px;
            }
            .player-bar .p-time {
                font-size: 9px;
                min-width: 26px;
            }
        }

        @media (orientation: landscape) and (max-height: 500px) {
            .app-container {
                padding: 50px 16px 70px;
            }
            .avatar-wrapper {
                width: 68px;
                height: 68px;
            }
            .nickname-text {
                font-size: 20px;
            }
            .files-grid {
                grid-template-columns: repeat(auto-fill, minmax(80px, 1fr));
                gap: 6px;
            }
            .glass-card {
                padding: 12px 16px 10px;
            }
            .player-bar {
                padding: 6px 12px;
                bottom: 6px;
            }
            .player-bar .disc-wrap {
                width: 26px;
                height: 26px;
            }
            .message-text {
                font-size: 13px;
                padding: 3px 10px;
                min-height: 30px;
            }
            .weather-display {
                display: none;
            }
        }

        /* ===== 工具 ===== */
        .mt-8 {
            margin-top: 8px;
        }
        .mb-8 {
            margin-bottom: 8px;
        }
        .gap-8 {
            gap: 8px;
        }
        .flex-center {
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .w-full {
            width: 100%;
        }
        .text-center {
            text-align: center;
        }
        .hidden {
            display: none !important;
        }
    </style>
</head>
<body>

    <!-- ===== 背景层 ===== -->
    <div id="bg-layer" class="dynamic"></div>

    <!-- ===== 天气 Canvas ===== -->
    <canvas id="weatherCanvas"></canvas>

    <!-- ===== 歌词雨 Canvas ===== -->
    <canvas id="lyricRainCanvas"></canvas>

    <!-- ===== 天气信息 ===== -->
    <div class="weather-display" id="weatherDisplay">
        <i class="fas fa-cloud-sun"></i>
        <span id="weatherTempDisplay">26°</span>
        <span id="weatherCityDisplay">北京</span>
    </div>

    <!-- ===== 顶部栏 ===== -->
    <div class="top-bar">
        <button class="settings-toggle" id="settingsToggle">
            <i class="fas fa-sliders-h"></i>
        </button>
    </div>

    <!-- ===== 设置面板 ===== -->
    <div class="settings-overlay" id="settingsOverlay"></div>
    <div class="settings-panel hide-scrollbar" id="settingsPanel">
        <div class="s-header">
            <h2><i class="fas fa-cog" style="font-size:15px;"></i> 个性设置</h2>
            <button class="s-close" id="settingsClose"><i class="fas fa-times"></i></button>
        </div>

        <!-- 分享 -->
        <div class="s-group">
            <label>🔗 分享与链接</label>
            <div class="share-row">
                <button class="share-btn primary" id="shareLinkBtn"><i class="fas fa-link"></i> 复制链接</button>
                <button class="share-btn" id="exportDataBtn"><i class="fas fa-download"></i> 导出配置</button>
                <button class="share-btn" id="importDataBtn"><i class="fas fa-upload"></i> 导入配置</button>
                <input type="file" id="importFileInput" accept=".json" style="display:none;" />
            </div>
            <div class="deploy-tip">
                💡 复制链接需部署到Web服务器 (如 GitHub Pages ) 方可分享
            </div>
        </div>

        <!-- 背景 -->
        <div class="s-group">
            <label>🖼️ 背景模式</label>
            <div class="s-row">
                <button class="s-btn active" data-bg="dynamic">动态</button>
                <button class="s-btn" data-bg="static">静态</button>
                <button class="s-btn" data-bg="slideshow">轮播</button>
            </div>
            <div class="s-row mt-8">
                <label class="file-btn" for="bgUpload"><i class="fas fa-upload"></i> 上传背景</label>
                <input type="file" id="bgUpload" accept="image/*" />
            </div>
        </div>

        <!-- 边框 -->
        <div class="s-group">
            <label>💎 液态玻璃边框</label>
            <div class="s-row">
                <div class="color-wrap">
                    <input type="color" id="borderColor" value="#ffffff" />
                    <span style="font-size:10px;color:rgba(255,255,255,0.1);">颜色</span>
                </div>
                <input type="number" id="borderWidth" value="2" min="1" max="8" style="width:48px;" />
                <span style="font-size:10px;color:rgba(255,255,255,0.1);">宽度</span>
                <div class="range-wrap">
                    <input type="range" id="borderOpacity" min="0" max="100" value="100" />
                    <span id="borderOpacityLabel">100%</span>
                </div>
            </div>
        </div>

        <!-- 头像 -->
        <div class="s-group">
            <label>👤 头像框</label>
            <div class="s-row">
                <button class="s-btn active" data-avatar-style="solid">实线</button>
                <button class="s-btn" data-avatar-style="double">双线</button>
                <button class="s-btn" data-avatar-style="dashed">虚线</button>
                <button class="s-btn" data-avatar-style="dotted">点线</button>
                <button class="s-btn" data-avatar-style="glow">发光</button>
            </div>
            <div class="s-row mt-8">
                <div class="color-wrap">
                    <input type="color" id="avatarColor" value="#ffffff" />
                </div>
                <input type="number" id="avatarWidth" value="3" min="1" max="12" style="width:48px;" />
                <label class="file-btn" for="avatarUpload"><i class="fas fa-upload"></i> 换头像</label>
                <input type="file" id="avatarUpload" accept="image/*" />
            </div>
            <div style="font-size:9px;color:rgba(255,255,255,0.06);margin-top:3px;">💡 点击头像直接上传 · 自动裁剪</div>
        </div>

        <!-- 昵称 -->
        <div class="s-group">
            <label>✍️ 昵称</label>
            <div class="s-row">
                <input type="text" id="nickInput" placeholder="输入昵称..." style="flex:1;min-width:70px;" />
            </div>
            <div class="s-row mt-8">
                <select id="nickFont" style="flex:1;min-width:60px;">
                    <option value="'Segoe UI', sans-serif">Segoe UI</option>
                    <option value="'Poppins', sans-serif">Poppins</option>
                    <option value="'Playfair Display', serif">Playfair</option>
                    <option value="'Montserrat', sans-serif">Montserrat</option>
                    <option value="'Georgia', serif">Georgia</option>
                    <option value="'SimSun', serif">宋体</option>
                    <option value="'SimHei', sans-serif">黑体</option>
                    <option value="'KaiTi', serif">楷体</option>
                    <option value="'FangSong', serif">仿宋</option>
                </select>
                <select id="nickSize" style="flex:1;min-width:50px;">
                    <option value="18px">18</option>
                    <option value="22px">22</option>
                    <option value="28px" selected>28</option>
                    <option value="34px">34</option>
                    <option value="42px">42</option>
                    <option value="52px">52</option>
                </select>
                <div class="color-wrap">
                    <input type="color" id="nickColor1" value="#f093fb" />
                    <input type="color" id="nickColor2" value="#f5576c" />
                </div>
                <select id="nickDir" style="width:50px;">
                    <option value="135deg">↘</option>
                    <option value="45deg">↗</option>
                    <option value="90deg">→</option>
                    <option value="0deg">↓</option>
                </select>
            </div>
        </div>

        <!-- 留言 -->
        <div class="s-group">
            <label>💬 留言板</label>
            <div class="s-row">
                <input type="text" id="messageInput" placeholder="输入留言..." style="flex:1;min-width:80px;" />
                <button class="s-btn" id="messageApplyBtn">应用</button>
            </div>
            <div style="font-size:9px;color:rgba(255,255,255,0.06);margin-top:3px;">💡 点击留言可编辑 · 打字机效果</div>
        </div>

        <!-- 天气 -->
        <div class="s-group">
            <label>🌤️ 天气特效</label>
            <div class="s-row">
                <button class="s-btn active" data-weather="sunny">晴</button>
                <button class="s-btn" data-weather="rainy">雨</button>
                <button class="s-btn" data-weather="snowy">雪</button>
                <button class="s-btn" data-weather="foggy">雾</button>
                <button class="s-btn" data-weather="cloudy">多云</button>
                <button class="s-btn" data-weather="none">关</button>
            </div>
            <div class="s-row mt-8">
                <input type="text" id="weatherCity" placeholder="城市" style="flex:1;min-width:60px;" />
                <input type="number" id="weatherTemp" placeholder="温度" style="width:60px;" />
                <span style="font-size:9px;color:rgba(255,255,255,0.06);">°C</span>
            </div>
        </div>

        <!-- 文件 -->
        <div class="s-group">
            <label>📁 文件管理</label>
            <div class="s-row">
                <label class="file-btn" for="fileUpload"><i class="fas fa-plus"></i> 上传</label>
                <input type="file" id="fileUpload" accept="image/*,audio/*,video/*" multiple />
                <button class="file-btn" id="clearFiles" style="color:rgba(220,80,80,0.3);"><i class="fas fa-trash"></i> 清空</button>
            </div>
            <div style="font-size:9px;color:rgba(255,255,255,0.06);margin-top:3px;">图片 · 音乐 · 视频 统一管理</div>
        </div>

        <!-- 歌词雨 -->
        <div class="s-group">
            <label>🌧️ 歌词雨</label>
            <div class="s-row">
                <button class="s-btn" id="lyricRainToggle">⏸️ 关闭</button>
                <span style="font-size:9px;color:rgba(255,255,255,0.06);">播放音乐时歌词如雨飘落</span>
            </div>
        </div>

        <div style="text-align:center;font-size:8px;color:rgba(255,255,255,0.03);padding-top:10px;border-top:1px solid rgba(255,255,255,0.01);">
            💾 自动保存 · 所有配置可分享
        </div>
    </div>

    <!-- ===== 主应用 ===== -->
    <div class="app-container">
        <div class="glass-border-wrap" id="glassBorderWrap">
            <div class="glass-card">

                <!-- 头像 -->
                <div class="profile-header">
                    <div class="avatar-wrapper" id="avatarWrapper">
                        <img id="avatarImg" src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='200' height='200'%3E%3Crect width='200' height='200' fill='%236c5ce7'/%3E%3Ctext x='100' y='120' font-size='70' text-anchor='middle' fill='white' font-family='sans-serif'%3E👤%3C/text%3E%3C/svg%3E" alt="头像" />
                        <div class="avatar-hint"><i class="fas fa-camera" style="margin-right:5px;"></i>更换头像</div>
                    </div>
                    <div class="nickname-wrap">
                        <div class="nickname-text" id="nickDisplay">你没有名字吗</div>
                    </div>
                </div>

                <!-- 留言板 -->
                <div class="message-board">
                    <div class="message-text" id="messageDisplay">
                        <span class="message-empty">点击输入你的留言...</span>
                    </div>
                </div>

                <!-- 文件网格 -->
                <div class="files-section">
                    <div class="files-grid" id="filesGrid">
                        <div class="files-empty"><i class="fas fa-folder-open"></i>上传你的图片、音乐或视频</div>
                    </div>
                    <div class="upload-area">
                        <button class="upload-btn" id="quickUploadBtn"><i class="fas fa-upload"></i> 上传文件</button>
                    </div>
                </div>

            </div>
        </div>
    </div>

    <!-- ===== 底部播放器 ===== -->
    <div class="player-bar" id="playerBar">
        <div class="p-controls">
            <button id="playerPrev"><i class="fas fa-step-backward"></i></button>
            <button class="play-btn" id="playerPlay"><i class="fas fa-play"></i></button>
            <button id="playerNext"><i class="fas fa-step-forward"></i></button>
        </div>

        <div class="disc-wrap" id="discWrap">
            <span class="disc-label">♫</span>
        </div>

        <div class="p-info" id="playerSongInfo">未播放</div>

        <div class="p-progress">
            <span class="p-time" id="playerCurrent">00:00</span>
            <input type="range" id="playerProgress" min="0" max="100" value="0" />
            <span class="p-time" id="playerDuration">00:00</span>
        </div>

        <div class="p-actions">
            <button id="lyricRainBtn" title="歌词雨"><i class="fas fa-cloud-rain"></i></button>
            <button id="playerVolumeBtn" title="音量"><i class="fas fa-volume-up"></i></button>
        </div>
    </div>

    <!-- ===== 音频元素 ===== -->
    <audio id="audioPlayer"></audio>

    <!-- ===== Toast ===== -->
    <div class="toast" id="toast"></div>

    <script>
        // ================================================================
        //  液态玻璃个人主页 - 最终完整版
        //  支持：留言板、天气特效、边框调节、URL分享、JSON导入导出
        // ================================================================
        (function() {
            'use strict';

            // ---------- DOM ----------
            const $ = (s) => document.querySelector(s);
            const $$ = (s) => document.querySelectorAll(s);

            const bgLayer = $('#bg-layer');
            const weatherCanvas = $('#weatherCanvas');
            const wctx = weatherCanvas.getContext('2d');
            const rainCanvas = $('#lyricRainCanvas');
            const rctx = rainCanvas.getContext('2d');

            const avatarWrapper = $('#avatarWrapper');
            const avatarImg = $('#avatarImg');
            const nickDisplay = $('#nickDisplay');
            const messageDisplay = $('#messageDisplay');
            const filesGrid = $('#filesGrid');

            const settingsToggle = $('#settingsToggle');
            const settingsPanel = $('#settingsPanel');
            const settingsOverlay = $('#settingsOverlay');
            const settingsClose = $('#settingsClose');

            const playerPlay = $('#playerPlay');
            const playerPrev = $('#playerPrev');
            const playerNext = $('#playerNext');
            const playerProgress = $('#playerProgress');
            const playerVolumeBtn = $('#playerVolumeBtn');
            const playerCurrent = $('#playerCurrent');
            const playerDuration = $('#playerDuration');
            const playerSongInfo = $('#playerSongInfo');
            const discWrap = $('#discWrap');
            const audioEl = $('#audioPlayer');
            const toast = $('#toast');

            const glassBorderWrap = $('#glassBorderWrap');
            const weatherDisplay = $('#weatherDisplay');
            const weatherTempDisplay = $('#weatherTempDisplay');
            const weatherCityDisplay = $('#weatherCityDisplay');

            // ---------- 存储 ----------
            const STORAGE_KEY = 'liquid_home_final';

            let data = {
                bgMode: 'dynamic',
                bgImage: null,
                avatarImage: null,
                avatarStyle: 'solid',
                avatarColor: '#ffffff',
                avatarWidth: 3,
                nickText: '请输入文本',
                nickFont: "'Segoe UI', sans-serif",
                nickSize: '28px',
                nickColor1: '#f093fb',
                nickColor2: '#f5576c',
                nickDir: '135deg',
                messageText: '你难道没有个性签名吗？',
                weatherType: 'sunny',
                weatherEnabled: true,
                weatherCity: '中国-北京',
                weatherTemp: 36℃,
                files: [],
                player: {
                    currentIndex: -1,
                    isPlaying: false,
                    volume: 80,
                    progress: 0,
                },
                lyricRain: false,
                currentLyrics: [],
                borderColor: '#ffffff',
                borderWidth: 2,
                borderOpacity: 1,
            };

            let isSettingsOpen = false;
            let slideIndex = 0;
            let slideTimer = null;
            let weatherRunning = false;
            let animFrame = null;
            let rainAnimFrame = null;
            let rainParticles = [];
            let isLyricRainActive = false;
            let isTyping = false;
            let typingTimer = null;

            // ---------- 工具 ----------
            function formatTime(s) {
                if (!s || isNaN(s)) return '00:00';
                const m = Math.floor(s / 60);
                const sec = Math.floor(s % 60);
                return `${String(m).padStart(2,'0')}:${String(sec).padStart(2,'0')}`;
            }

            function saveData() {
                try { localStorage.setItem(STORAGE_KEY, JSON.stringify(data)); } catch (e) {}
            }

            function loadData() {
                try {
                    const raw = localStorage.getItem(STORAGE_KEY);
                    if (raw) {
                        const parsed = JSON.parse(raw);
                        data = { ...data, ...parsed };
                        data.files = data.files || [];
                        data.player = { ...data.player };
                        data.currentLyrics = data.currentLyrics || [];
                        if (!data.player.volume) data.player.volume = 80;
                        if (!data.weatherCity) data.weatherCity = '北京';
                        if (!data.weatherTemp) data.weatherTemp = 26;
                        if (!data.borderColor) data.borderColor = '#ffffff';
                        if (!data.borderWidth) data.borderWidth = 2;
                        if (data.borderOpacity === undefined) data.borderOpacity = 1;
                        if (!data.messageText) data.messageText = '你难道没有个性签名吗？';
                    }
                } catch (e) {}
            }

            function showToast(msg, dur = 2000) {
                toast.textContent = msg;
                toast.classList.add('show');
                clearTimeout(toast._timer);
                toast._timer = setTimeout(() => toast.classList.remove('show'), dur);
            }

            function parseLRC(text) {
                if (!text || !text.trim()) return [];
                const lines = text.split('\n');
                const result = [];
                const regex = /\[(\d{2}):(\d{2})\.?(\d{2,3})?\]/;
                for (const line of lines) {
                    const m = line.match(regex);
                    if (m) {
                        const min = parseInt(m[1]),
                            sec = parseInt(m[2]),
                            ms = parseInt(m[3] || '0');
                        const time = min * 60 + sec + ms / 1000;
                        const content = line.replace(regex, '').trim();
                        if (content) result.push({ time, content });
                    } else if (line.trim()) {
                        if (result.length > 0) {
                            const last = result[result.length - 1];
                            last.content += ' ' + line.trim();
                        }
                    }
                }
                result.sort((a, b) => a.time - b.time);
                return result;
            }

            function cropImage(file, callback) {
                const reader = new FileReader();
                reader.onload = (e) => {
                    const img = new Image();
                    img.onload = () => {
                        const size = Math.min(img.width, img.height);
                        const canvas = document.createElement('canvas');
                        canvas.width = 400;
                        canvas.height = 400;
                        const ctx = canvas.getContext('2d');
                        const sx = (img.width - size) / 2;
                        const sy = (img.height - size) / 2;
                        ctx.drawImage(img, sx, sy, size, size, 0, 0, 400, 400);
                        callback(canvas.toDataURL('image/jpeg', 0.92));
                    };
                    img.src = e.target.result;
                };
                reader.readAsDataURL(file);
            }

            // ---------- 打字机 ----------
            function startTyping(text) {
                if (typingTimer) { clearTimeout(typingTimer);
                    typingTimer = null; }
                if (isTyping) return;
                isTyping = true;
                const msg = text || data.messageText || '';
                if (!msg) {
                    messageDisplay.innerHTML = `<span class="message-empty">点击输入你的留言...</span>`;
                    isTyping = false;
                    return;
                }
                messageDisplay.innerHTML = '';
                const chars = msg.split('');
                let i = 0;

                function typeChar() {
                    if (i >= chars.length) {
                        const cursor = document.createElement('span');
                        cursor.className = 'cursor-blink';
                        messageDisplay.appendChild(cursor);
                        isTyping = false;
                        return;
                    }
                    const span = document.createElement('span');
                    span.className = 'char highlight';
                    span.textContent = chars[i];
                    messageDisplay.appendChild(span);
                    i++;
                    typingTimer = setTimeout(typeChar, 30 + Math.random() * 25);
                }
                typeChar();
            }

            function stopTyping() {
                if (typingTimer) { clearTimeout(typingTimer);
                    typingTimer = null; }
                isTyping = false;
            }

            function resetMessage(text) {
                stopTyping();
                if (text !== undefined) {
                    data.messageText = text;
                    saveData();
                }
                messageDisplay.innerHTML = '';
                setTimeout(() => startTyping(data.messageText), 80);
            }

            // ---------- 渲染 ----------
            function renderBackground() {
                bgLayer.className = '';
                const old = bgLayer.querySelectorAll('.bg-image, .bg-slide');
                old.forEach(el => el.remove());

                if (data.bgMode === 'dynamic') {
                    bgLayer.classList.add('dynamic');
                } else if (data.bgMode === 'static') {
                    bgLayer.classList.add('static');
                    if (data.bgImage) {
                        const img = document.createElement('img');
                        img.className = 'bg-image';
                        img.src = data.bgImage;
                        img.alt = '背景';
                        bgLayer.appendChild(img);
                    } else {
                        bgLayer.style.background = '#0a0a1a';
                    }
                } else if (data.bgMode === 'slideshow') {
                    bgLayer.classList.add('slideshow');
                    const images = data.files.filter(f => f.type === 'image');
                    if (images.length > 0) {
                        images.forEach((f, i) => {
                            const img = document.createElement('img');
                            img.className = 'bg-slide' + (i === slideIndex ? ' active' : '');
                            img.src = f.url;
                            img.alt = '轮播';
                            bgLayer.appendChild(img);
                        });
                        startSlideshow(images);
                    } else {
                        bgLayer.style.background = '#0a0a1a';
                    }
                }
                $$('[data-bg]').forEach(btn => {
                    btn.classList.toggle('active', btn.dataset.bg === data.bgMode);
                });
            }

            let slideshowImages = [];

            function startSlideshow(images) {
                stopSlideshow();
                slideshowImages = images || data.files.filter(f => f.type === 'image');
                if (slideshowImages.length < 2) return;
                slideTimer = setInterval(() => {
                    const slides = bgLayer.querySelectorAll('.bg-slide');
                    if (slides.length === 0) return;
                    slides.forEach(el => el.classList.remove('active'));
                    slideIndex = (slideIndex + 1) % slides.length;
                    slides[slideIndex].classList.add('active');
                }, 4000);
            }

            function stopSlideshow() {
                if (slideTimer) { clearInterval(slideTimer);
                    slideTimer = null; }
            }

            function renderBorder() {
                const alpha = Math.min(1, Math.max(0, data.borderOpacity || 1));
                let color = data.borderColor || '#ffffff';
                const r = parseInt(color.slice(1, 3), 16);
                const g = parseInt(color.slice(3, 5), 16);
                const b = parseInt(color.slice(5, 7), 16);
                const rgba = `rgba(${r},${g},${b},${alpha * 0.5})`;
                glassBorderWrap.style.setProperty('--border-width', (data.borderWidth || 2) + 'px');
                glassBorderWrap.style.setProperty('--border-opacity', alpha);
                // 更新伪元素背景
                const style = glassBorderWrap.style;
                style.setProperty('--border-color', rgba);
                // 重新触发伪元素更新
                glassBorderWrap.style.background = `conic-gradient(from 0deg at 50% 50%, 
                    ${rgba} 0%, rgba(255,255,255,${alpha * 0.06}) 25%, 
                    ${rgba} 50%, rgba(255,255,255,${alpha * 0.04}) 75%, ${rgba} 100%)`;
                glassBorderWrap.style.backgroundSize = '300% 300%';
                // 更新设置面板
                $('#borderColor').value = data.borderColor || '#ffffff';
                $('#borderWidth').value = data.borderWidth || 2;
                $('#borderOpacity').value = Math.round((data.borderOpacity || 1) * 100);
                $('#borderOpacityLabel').textContent = Math.round((data.borderOpacity || 1) * 100) + '%';
            }

            function renderAvatar() {
                if (data.avatarImage) avatarImg.src = data.avatarImage;
                else {
                    avatarImg.src =
                        "data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='200' height='200'%3E%3Crect width='200' height='200' fill='%236c5ce7'/%3E%3Ctext x='100' y='120' font-size='70' text-anchor='middle' fill='white' font-family='sans-serif'%3E👤%3C/text%3E%3C/svg%3E";
                }
                avatarWrapper.style.borderColor = data.avatarColor;
                avatarWrapper.style.borderWidth = data.avatarWidth + 'px';
                avatarWrapper.className = 'avatar-wrapper';
                if (data.avatarStyle === 'glow') {
                    avatarWrapper.classList.add('glow');
                    avatarWrapper.style.borderStyle = 'solid';
                } else {
                    avatarWrapper.style.borderStyle = data.avatarStyle;
                    avatarWrapper.style.boxShadow = '0 12px 48px rgba(0,0,0,0.25)';
                }
                $$('[data-avatar-style]').forEach(btn => {
                    btn.classList.toggle('active', btn.dataset.avatarStyle === data.avatarStyle);
                });
                $('#avatarColor').value = data.avatarColor;
                $('#avatarWidth').value = data.avatarWidth;
            }

            function renderNickname() {
                nickDisplay.textContent = data.nickText || '点击编辑昵称';
                nickDisplay.style.fontFamily = data.nickFont;
                nickDisplay.style.fontSize = data.nickSize;
                const grad = `linear-gradient(${data.nickDir}, ${data.nickColor1}, ${data.nickColor2})`;
                nickDisplay.style.background = grad;
                nickDisplay.style.webkitBackgroundClip = 'text';
                nickDisplay.style.webkitTextFillColor = 'transparent';
                nickDisplay.style.backgroundClip = 'text';
                $('#nickInput').value = data.nickText || '';
                $('#nickFont').value = data.nickFont;
                $('#nickSize').value = data.nickSize;
                $('#nickColor1').value = data.nickColor1;
                $('#nickColor2').value = data.nickColor2;
                $('#nickDir').value = data.nickDir;
            }

            function renderFiles() {
                if (data.files.length === 0) {
                    filesGrid.innerHTML =
                        `<div class="files-empty"><i class="fas fa-folder-open"></i>上传你的图片、音乐或视频</div>`;
                    return;
                }
                let html = '';
                data.files.forEach((f, i) => {
                    const isAudio = f.type === 'audio';
                    const isVideo = f.type === 'video';
                    const isImage = f.type === 'image';
                    let content = '';
                    if (isImage) {
                        content = `<img src="${f.url}" alt="${f.name}" loading="lazy" />`;
                    } else if (isVideo) {
                        content = `<video src="${f.url}" muted playsinline></video>`;
                    } else {
                        content = `<div class="file-icon"><i class="fas fa-music"></i></div>`;
                    }
                    const badge = isImage ? '📷' : isVideo ? '🎬' : '🎵';
                    html += `
                        <div class="file-item" data-idx="${i}" data-type="${f.type}">
                            ${content}
                            ${isVideo ? `<div class="play-overlay"><i class="fas fa-play-circle"></i></div>` : ''}
                            ${isAudio ? `<div class="play-overlay"><i class="fas fa-play-circle"></i></div>` : ''}
                            <span class="file-type-badge">${badge}</span>
                            <button class="file-del" data-action="delFile" data-idx="${i}"><i class="fas fa-times"></i></button>
                        </div>
                    `;
                });
                filesGrid.innerHTML = html;

                filesGrid.querySelectorAll('[data-action="delFile"]').forEach(btn => {
                    btn.addEventListener('click', (e) => {
                        e.stopPropagation();
                        const idx = parseInt(btn.dataset.idx);
                        data.files.splice(idx, 1);
                        renderFiles();
                        saveData();
                        if (data.bgMode === 'slideshow') renderBackground();
                    });
                });

                filesGrid.querySelectorAll('.file-item').forEach(el => {
                    const idx = parseInt(el.dataset.idx);
                    const f = data.files[idx];
                    if (!f) return;

                    el.addEventListener('click', (e) => {
                        if (e.target.closest('.file-del')) return;
                        if (f.type === 'image') {
                            window.open(f.url, '_blank');
                        } else if (f.type === 'video') {
                            const vid = el.querySelector('video');
                            if (vid) {
                                if (vid.paused) {
                                    filesGrid.querySelectorAll('.file-item video').forEach(v => { if (v !==
                                            vid) v.pause(); });
                                    vid.play();
                                    const ov = el.querySelector('.play-overlay i');
                                    if (ov) ov.className = 'fas fa-pause-circle';
                                } else {
                                    vid.pause();
                                    const ov = el.querySelector('.play-overlay i');
                                    if (ov) ov.className = 'fas fa-play-circle';
                                }
                            }
                        } else if (f.type === 'audio') {
                            const audioIdx = data.files.findIndex(f => f.type === 'audio');
                            if (audioIdx >= 0) playMusic(audioIdx);
                        }
                    });

                    const vid = el.querySelector('video');
                    if (vid) {
                        vid.addEventListener('ended', () => {
                            const ov = el.querySelector('.play-overlay i');
                            if (ov) ov.className = 'fas fa-play-circle';
                        });
                        vid.addEventListener('pause', () => {
                            const ov = el.querySelector('.play-overlay i');
                            if (ov) ov.className = 'fas fa-play-circle';
                        });
                        vid.addEventListener('play', () => {
                            const ov = el.querySelector('.play-overlay i');
                            if (ov) ov.className = 'fas fa-pause-circle';
                        });
                    }
                });

                updatePlayerUI();
            }

            function renderWeatherInfo() {
                const icons = {
                    sunny: 'fa-sun',
                    rainy: 'fa-cloud-rain',
                    snowy: 'fa-snowflake',
                    foggy: 'fa-smog',
                    cloudy: 'fa-cloud',
                    none: 'fa-cloud-sun'
                };
                const icon = icons[data.weatherType] || 'fa-cloud-sun';
                weatherDisplay.querySelector('i').className = `fas ${icon}`;
                weatherTempDisplay.textContent = (data.weatherTemp || 26) + '°';
                weatherCityDisplay.textContent = data.weatherCity || '北京';
                if (data.weatherType === 'none' || !data.weatherEnabled) {
                    weatherDisplay.style.opacity = '0.2';
                } else {
                    weatherDisplay.style.opacity = '1';
                }
                $('#weatherCity').value = data.weatherCity || '北京';
                $('#weatherTemp').value = data.weatherTemp || 26;
                $$('[data-weather]').forEach(btn => {
                    btn.classList.toggle('active', btn.dataset.weather === data.weatherType);
                });
            }

            // ---------- 播放器 ----------
            function getAudioFiles() {
                return data.files.filter(f => f.type === 'audio');
            }

            function playMusic(index) {
                const audios = getAudioFiles();
                if (audios.length === 0 || index < 0 || index >= audios.length) {
                    data.player.currentIndex = -1;
                    data.player.isPlaying = false;
                    audioEl.pause();
                    updatePlayerUI();
                    return;
                }
                const song = audios[index];
                const globalIdx = data.files.indexOf(song);
                data.player.currentIndex = globalIdx;
                audioEl.src = song.url;
                audioEl.load();
                audioEl.play().then(() => {
                    data.player.isPlaying = true;
                    updatePlayerUI();
                    if (song.lyrics) {
                        data.currentLyrics = parseLRC(song.lyrics);
                    } else {
                        data.currentLyrics = [];
                    }
                    if (isLyricRainActive) startLyricRain();
                }).catch(() => {
                    data.player.isPlaying = false;
                    updatePlayerUI();
                });
                saveData();
            }

            function togglePlay() {
                const audios = getAudioFiles();
                if (audios.length === 0) return;
                if (data.player.currentIndex < 0 || data.player.currentIndex >= data.files.length) {
                    const idx = data.files.indexOf(audios[0]);
                    playMusic(idx);
                    return;
                }
                if (data.player.isPlaying) {
                    audioEl.pause();
                    data.player.isPlaying = false;
                } else {
                    audioEl.play().then(() => { data.player.isPlaying = true; }).catch(() => {});
                }
                updatePlayerUI();
                saveData();
            }

            function playPrev() {
                const audios = getAudioFiles();
                if (audios.length === 0) return;
                let idx = audios.indexOf(data.files[data.player.currentIndex] || audios[0]);
                idx = (idx - 1 + audios.length) % audios.length;
                playMusic(data.files.indexOf(audios[idx]));
            }

            function playNext() {
                const audios = getAudioFiles();
                if (audios.length === 0) return;
                let idx = audios.indexOf(data.files[data.player.currentIndex] || audios[0]);
                idx = (idx + 1) % audios.length;
                playMusic(data.files.indexOf(audios[idx]));
            }

            function updatePlayerUI() {
                const audios = getAudioFiles();
                const has = data.player.currentIndex >= 0 && data.player.currentIndex < data.files.length &&
                    data.files[data.player.currentIndex]?.type === 'audio';
                if (has) {
                    const song = data.files[data.player.currentIndex];
                    playerSongInfo.textContent = song.name || '未命名';
                } else {
                    playerSongInfo.textContent = '未播放';
                }
                const icon = playerPlay.querySelector('i');
                if (data.player.isPlaying && has) {
                    icon.className = 'fas fa-pause';
                    discWrap.classList.add('playing');
                } else {
                    icon.className = 'fas fa-play';
                    discWrap.classList.remove('playing');
                }
                if (!has) {
                    playerProgress.value = 0;
                    playerCurrent.textContent = '00:00';
                    playerDuration.textContent = '00:00';
                }
                audioEl.volume = (data.player.volume || 80) / 100;
                renderFiles();
            }

            // ---------- 歌词雨 ----------
            function startLyricRain() {
                if (!isLyricRainActive) return;
                if (rainAnimFrame) { cancelAnimationFrame(rainAnimFrame);
                    rainAnimFrame = null; }
                rainParticles = [];
                const lrc = data.currentLyrics;
                const texts = lrc.length > 0 ? lrc.map(l => l.content) : ['逍遥法外', '零容忍', '八面薇风', '莉挽狂澜',
                    '临容忍', '全无心肝'
                ];
                for (let i = 0; i < 30; i++) {
                    const idx = Math.floor(Math.random() * texts.length);
                    rainParticles.push({
                        text: texts[idx] || '🎵',
                        x: Math.random() * rainCanvas.width,
                        y: Math.random() * rainCanvas.height - rainCanvas.height,
                        speed: 0.3 + Math.random() * 0.7,
                        size: 13 + Math.random() * 22,
                        opacity: 0.1 + Math.random() * 0.3,
                        wobble: Math.random() * 0.8,
                        phase: Math.random() * Math.PI * 2,
                    });
                }
                animateRain();
            }

            function animateRain() {
                if (!isLyricRainActive) {
                    rctx.clearRect(0, 0, rainCanvas.width, rainCanvas.height);
                    return;
                }
                rctx.clearRect(0, 0, rainCanvas.width, rainCanvas.height);
                rainParticles.forEach(p => {
                    p.y += p.speed;
                    p.x += Math.sin(Date.now() / 3000 + p.phase) * p.wobble * 0.3;
                    if (p.y > rainCanvas.height + 20) {
                        p.y = -20;
                        p.x = Math.random() * rainCanvas.width;
                        const lrc = data.currentLyrics;
                        if (lrc && lrc.length > 0) {
                            const idx = Math.floor(Math.random() * lrc.length);
                            p.text = lrc[idx].content;
                        }
                    }
                    if (p.x < -20) p.x = rainCanvas.width + 20;
                    if (p.x > rainCanvas.width + 20) p.x = -20;
                    rctx.font = `${p.size}px 'Segoe UI', sans-serif`;
                    rctx.fillStyle = `rgba(255,255,255,${p.opacity})`;
                    rctx.shadowColor = 'rgba(255,255,255,0.02)';
                    rctx.shadowBlur = 10;
                    rctx.fillText(p.text, p.x, p.y);
                    rctx.shadowBlur = 0;
                });
                rainAnimFrame = requestAnimationFrame(animateRain);
            }

            function toggleLyricRain() {
                isLyricRainActive = !isLyricRainActive;
                rainCanvas.classList.toggle('active', isLyricRainActive);
                const btn = $('#lyricRainBtn');
                if (isLyricRainActive) {
                    btn.innerHTML = '<i class="fas fa-cloud-rain" style="color:#6dd5ed;"></i>';
                    if (data.player.isPlaying) startLyricRain();
                    else {
                        const texts = [''逍遥法外', '零容忍', '八面薇风', '莉挽狂澜',
            '临容忍', '全无心肝];
                        rainParticles = [];
                        for (let i = 0; i < 25; i++) {
                            const idx = Math.floor(Math.random() * texts.length);
                            rainParticles.push({
                                text: texts[idx],
                                x: Math.random() * rainCanvas.width,
                                y: Math.random() * rainCanvas.height - rainCanvas.height,
                                speed: 0.3 + Math.random() * 0.7,
                                size: 13 + Math.random() * 22,
                                opacity: 0.1 + Math.random() * 0.3,
                                wobble: Math.random() * 0.8,
                                phase: Math.random() * Math.PI * 2,
                            });
                        }
                        animateRain();
                    }
                    data.lyricRain = true;
                    $('#lyricRainToggle').textContent = '▶️ 开启';
                    $('#lyricRainToggle').classList.add('active');
                } else {
                    btn.innerHTML = '<i class="fas fa-cloud-rain"></i>';
                    if (rainAnimFrame) { cancelAnimationFrame(rainAnimFrame);
                        rainAnimFrame = null; }
                    rctx.clearRect(0, 0, rainCanvas.width, rainCanvas.height);
                    data.lyricRain = false;
                    $('#lyricRainToggle').textContent = '⏸️ 关闭';
                    $('#lyricRainToggle').classList.remove('active');
                }
                saveData();
            }

            // ---------- 天气特效 ----------
            let weatherParticles = [];

            function initWeatherCanvas() {
                weatherCanvas.width = window.innerWidth;
                weatherCanvas.height = window.innerHeight;
                rainCanvas.width = window.innerWidth;
                rainCanvas.height = window.innerHeight;
            }

            function getWeatherConfig(type) {
                if (type === 'none' || !data.weatherEnabled) return { count: 0, size: [0, 0], speed: [0, 0],
                    color: 'transparent', type: 'none' };
                switch (type) {
                    case 'sunny':
                        return { count: 60, size: [3, 12], speed: [0.05, 0.3], color: 'rgba(255,240,180,0.3)',
                            type: 'sunny' };
                    case 'rainy':
                        return { count: 280, size: [1, 3], speed: [10, 20], color: 'rgba(180,210,255,0.2)',
                            type: 'rainy' };
                    case 'snowy':
                        return { count: 160, size: [3, 10], speed: [0.3, 1.4], color: 'rgba(255,255,255,0.5)',
                            type: 'snowy' };
                    case 'foggy':
                        return { count: 70, size: [30, 90], speed: [0.15, 0.5], color: 'rgba(220,220,230,0.05)',
                            type: 'foggy' };
                    case 'cloudy':
                        return { count: 30, size: [40, 100], speed: [0.1, 0.3], color: 'rgba(200,210,230,0.05)',
                            type: 'cloudy' };
                    default:
                        return { count: 0, size: [0, 0], speed: [0, 0], color: 'transparent', type: 'none' };
                }
            }

            class WPart {
                constructor(cfg) { this.cfg = cfg;
                    this.reset(); }
                reset() {
                    const c = this.cfg;
                    this.x = Math.random() * weatherCanvas.width;
                    this.y = Math.random() * weatherCanvas.height - weatherCanvas.height * 0.2;
                    this.size = c.size[0] + Math.random() * (c.size[1] - c.size[0]);
                    this.speed = c.speed[0] + Math.random() * (c.speed[1] - c.speed[0]);
                    this.opacity = 0.3 + Math.random() * 0.5;
                    this.angle = Math.random() * Math.PI * 0.2 - 0.1;
                    if (c.type === 'sunny') {
                        this.size = 4 + Math.random() * 12;
                        this.glow = 0.3 + Math.random() * 0.5;
                    }
                    if (c.type === 'rainy') this.length = 12 + Math.random() * 18;
                    if (c.type === 'snowy') { this.wobble = Math.random() * 0.6;
                        this.phase = Math.random() * Math.PI * 2; }
                    if (c.type === 'foggy' || c.type === 'cloudy') {
                        this.size = c.size[0] + Math.random() * (c.size[1] - c.size[0]);
                        this.speed = c.speed[0] + Math.random() * (c.speed[1] - c.speed[0]);
                        this.opacity = 0.03 + Math.random() * 0.06;
                    }
                }
                update() {
                    const c = this.cfg;
                    if (c.type === 'sunny') {
                        this.x += Math.sin(this.angle) * 0.3;
                        this.y -= 0.12;
                        this.size += Math.sin(Date.now() / 2000 + this.x) * 0.3;
                        if (this.y < -20) { this.reset();
                            this.y = weatherCanvas.height + 20; }
                        if (this.x < -20 || this.x > weatherCanvas.width + 20) this.reset();
                    } else if (c.type === 'rainy') {
                        this.x += Math.sin(this.angle) * 1.5;
                        this.y += this.speed;
                        if (this.y > weatherCanvas.height + 20) { this.reset();
                            this.y = -20; }
                        if (this.x < -20 || this.x > weatherCanvas.width + 20) this.reset();
                    } else if (c.type === 'snowy') {
                        this.x += Math.sin(Date.now() / 3000 + this.phase) * 0.6;
                        this.y += this.speed * 0.5;
                        if (this.y > weatherCanvas.height + 20) { this.reset();
                            this.y = -20; }
                        if (this.x < -20 || this.x > weatherCanvas.width + 20) this.reset();
                    } else if (c.type === 'foggy' || c.type === 'cloudy') {
                        this.x += this.speed * 0.3;
                        this.y += Math.sin(Date.now() / 5000 + this.x / 100) * 0.1;
                        if (this.x > weatherCanvas.width + 100) { this.x = -100;
                            this.y = Math.random() * weatherCanvas.height; }
                    }
                }
                draw(ctx) {
                    const c = this.cfg;
                    if (c.type === 'sunny') {
                        const g = ctx.createRadialGradient(this.x, this.y, 0, this.x, this.y, this.size);
                        g.addColorStop(0, `rgba(255,240,180,${this.opacity * 0.5})`);
                        g.addColorStop(1, `rgba(255,200,100,0)`);
                        ctx.fillStyle = g;
                        ctx.beginPath();
                        ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
                        ctx.fill();
                    } else if (c.type === 'rainy') {
                        ctx.strokeStyle = `rgba(180,210,255,${this.opacity * 0.3})`;
                        ctx.lineWidth = 1.2;
                        ctx.beginPath();
                        ctx.moveTo(this.x, this.y);
                        ctx.lineTo(this.x + Math.sin(this.angle) * 4, this.y + this.length);
                        ctx.stroke();
                    } else if (c.type === 'snowy') {
                        ctx.fillStyle = `rgba(255,255,255,${this.opacity * 0.5})`;
                        ctx.shadowColor = 'rgba(255,255,255,0.04)';
                        ctx.shadowBlur = 6;
                        ctx.beginPath();
                        ctx.arc(this.x, this.y, this.size * 0.4, 0, Math.PI * 2);
                        ctx.fill();
                        ctx.shadowBlur = 0;
                    } else if (c.type === 'foggy' || c.type === 'cloudy') {
                        const g = ctx.createRadialGradient(this.x, this.y, 0, this.x, this.y, this.size);
                        g.addColorStop(0, `rgba(220,225,240,${this.opacity * 0.3})`);
                        g.addColorStop(1, `rgba(220,225,240,0)`);
                        ctx.fillStyle = g;
                        ctx.beginPath();
                        ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
                        ctx.fill();
                    }
                }
            }

            function initWeather() {
                if (animFrame) { cancelAnimationFrame(animFrame);
                    animFrame = null; }
                weatherParticles = [];
                const cfg = getWeatherConfig(data.weatherType);
                if (cfg.count === 0 || !data.weatherEnabled) {
                    wctx.clearRect(0, 0, weatherCanvas.width, weatherCanvas.height);
                    weatherRunning = false;
                    return;
                }
                for (let i = 0; i < cfg.count; i++) {
                    weatherParticles.push(new WPart(cfg));
                }
                weatherRunning = true;
                animateWeather();
            }

            function animateWeather() {
                if (!weatherRunning) return;
                wctx.clearRect(0, 0, weatherCanvas.width, weatherCanvas.height);
                weatherParticles.forEach(p => { p.update();
                    p.draw(wctx); });
                animFrame = requestAnimationFrame(animateWeather);
            }

            function resizeWeather() {
                weatherCanvas.width = window.innerWidth;
                weatherCanvas.height = window.innerHeight;
                rainCanvas.width = window.innerWidth;
                rainCanvas.height = window.innerHeight;
            }

            // ---------- 分享 ----------
            function encodeData() {
                try {
                    const json = JSON.stringify(data);
                    return btoa(unescape(encodeURIComponent(json)));
                } catch (e) { return null; }
            }

            function decodeData(encoded) {
                try {
                    const json = decodeURIComponent(escape(atob(encoded)));
                    return JSON.parse(json);
                } catch (e) { return null; }
            }

            function generateShareLink() {
                const encoded = encodeData();
                if (!encoded) { showToast('数据编码失败', 2000); return null; }
                const url = window.location.origin + window.location.pathname + '?data=' + encoded;
                return url;
            }

            function loadFromURL() {
                const params = new URLSearchParams(window.location.search);
                const encoded = params.get('data');
                if (!encoded) return false;
                const decoded = decodeData(encoded);
                if (!decoded) { showToast('链接数据无效', 2000); return false; }
                data = { ...data, ...decoded };
                data.files = data.files || [];
                data.player = { ...data.player };
                data.currentLyrics = data.currentLyrics || [];
                if (!data.player.volume) data.player.volume = 80;
                if (!data.weatherCity) data.weatherCity = '北京';
                if (!data.weatherTemp) data.weatherTemp = 26;
                if (!data.borderColor) data.borderColor = '#ffffff';
                if (!data.borderWidth) data.borderWidth = 2;
                if (data.borderOpacity === undefined) data.borderOpacity = 1;
                if (!data.messageText) data.messageText = '你难道没有个性签名吗？';
                saveData();
                showToast('✅ 已加载分享配置！', 2000);
                return true;
            }

            function copyShareLink() {
                const link = generateShareLink();
                if (!link) return;
                // 检测是否在本地环境
                if (window.location.protocol === 'file:') {
                    showToast('⚠️ 本地环境无法生成分享链接，请部署到Web服务器', 3000);
                    return;
                }
                if (navigator.clipboard) {
                    navigator.clipboard.writeText(link).then(() => {
                        showToast('✅ 专属链接已复制！', 2000);
                    }).catch(() => { fallbackCopy(link); });
                } else { fallbackCopy(link); }
            }

            function fallbackCopy(text) {
                const ta = document.createElement('textarea');
                ta.value = text;
                ta.style.cssText = 'position:fixed;opacity:0;';
                document.body.appendChild(ta);
                ta.select();
                try { document.execCommand('copy');
                    showToast('✅ 链接已复制！', 2000); } catch (e) { showToast('⚠️ 复制失败', 2000); }
                document.body.removeChild(ta);
            }

            function exportConfig() {
                const json = JSON.stringify(data, null, 2);
                const blob = new Blob([json], { type: 'application/json' });
                const url = URL.createObjectURL(blob);
                const a = document.createElement('a');
                a.href = url;
                a.download = 'my_home_config.json';
                a.click();
                URL.revokeObjectURL(url);
                showToast('📥 配置已导出', 1500);
            }

            function importConfig(file) {
                const reader = new FileReader();
                reader.onload = (e) => {
                    try {
                        const parsed = JSON.parse(e.target.result);
                        data = { ...data, ...parsed };
                        data.files = data.files || [];
                        data.player = { ...data.player };
                        data.currentLyrics = data.currentLyrics || [];
                        if (!data.player.volume) data.player.volume = 80;
                        if (!data.weatherCity) data.weatherCity = '北京';
                        if (!data.weatherTemp) data.weatherTemp = 26;
                        if (!data.borderColor) data.borderColor = '#ffffff';
                        if (!data.borderWidth) data.borderWidth = 2;
                        if (data.borderOpacity === undefined) data.borderOpacity = 1;
                        if (!data.messageText) data.messageText = '你难道没有个性签名吗？';
                        saveData();
                        fullRender();
                        showToast('配置导入成功！', 2000);
                    } catch (err) { showToast('配置文件无效', 2000); }
                };
                reader.readAsText(file);
            }

            // ---------- 全量渲染 ----------
            function fullRender() {
                renderBackground();
                renderBorder();
                renderAvatar();
                renderNickname();
                renderFiles();
                renderWeatherInfo();
                updatePlayerUI();

                // 留言
                if (data.messageText) {
                    resetMessage(data.messageText);
                } else {
                    messageDisplay.innerHTML = `<span class="message-empty">点击输入你的留言...</span>`;
                }

                // 天气
                if (data.weatherEnabled) {
                    initWeather();
                } else {
                    weatherRunning = false;
                    if (animFrame) { cancelAnimationFrame(animFrame);
                        animFrame = null; }
                    wctx.clearRect(0, 0, weatherCanvas.width, weatherCanvas.height);
                }

                // 歌词雨
                if (data.lyricRain) {
                    isLyricRainActive = true;
                    rainCanvas.classList.add('active');
                    $('#lyricRainBtn').innerHTML = '<i class="fas fa-cloud-rain" style="color:#6dd5ed;"></i>';
                    $('#lyricRainToggle').textContent = '▶️ 开启';
                    $('#lyricRainToggle').classList.add('active');
                    if (data.player.isPlaying) startLyricRain();
                    else {
                        const texts = [''逍遥法外', '零容忍', '八面薇风', '莉挽狂澜',
                    '临容忍', '全无心肝];
                        rainParticles = [];
                        for (let i = 0; i < 25; i++) {
                            const idx = Math.floor(Math.random() * texts.length);
                            rainParticles.push({
                                text: texts[idx],
                                x: Math.random() * rainCanvas.width,
                                y: Math.random() * rainCanvas.height - rainCanvas.height,
                                speed: 0.3 + Math.random() * 0.7,
                                size: 13 + Math.random() * 22,
                                opacity: 0.1 + Math.random() * 0.3,
                                wobble: Math.random() * 0.8,
                                phase: Math.random() * Math.PI * 2,
                            });
                        }
                        animateRain();
                    }
                } else {
                    isLyricRainActive = false;
                    rainCanvas.classList.remove('active');
                    $('#lyricRainBtn').innerHTML = '<i class="fas fa-cloud-rain"></i>';
                    $('#lyricRainToggle').textContent = '⏸️ 关闭';
                    $('#lyricRainToggle').classList.remove('active');
                    if (rainAnimFrame) { cancelAnimationFrame(rainAnimFrame);
                        rainAnimFrame = null; }
                    rctx.clearRect(0, 0, rainCanvas.width, rainCanvas.height);
                }

                // 恢复播放
                if (data.player.currentIndex >= 0 && data.player.currentIndex < data.files.length &&
                    data.files[data.player.currentIndex]?.type === 'audio') {
                    const song = data.files[data.player.currentIndex];
                    if (song && song.url) {
                        audioEl.src = song.url;
                        audioEl.volume = (data.player.volume || 80) / 100;
                        if (data.player.isPlaying) {
                            audioEl.play().catch(() => {});
                        }
                        if (song.lyrics) {
                            data.currentLyrics = parseLRC(song.lyrics);
                        }
                    }
                }

                if (data.bgMode === 'slideshow') {
                    const images = data.files.filter(f => f.type === 'image');
                    if (images.length > 0) startSlideshow(images);
                }

                // 更新设置面板中的边框值
                $('#borderColor').value = data.borderColor || '#ffffff';
                $('#borderWidth').value = data.borderWidth || 2;
                $('#borderOpacity').value = Math.round((data.borderOpacity || 1) * 100);
                $('#borderOpacityLabel').textContent = Math.round((data.borderOpacity || 1) * 100) + '%';
            }

            // ---------- 事件 ----------
            function initEvents() {
                // 设置
                settingsToggle.addEventListener('click', () => toggleSettings());
                settingsClose.addEventListener('click', () => toggleSettings(false));
                settingsOverlay.addEventListener('click', () => toggleSettings(false));

                // 分享
                $('#shareLinkBtn').addEventListener('click', copyShareLink);
                $('#exportDataBtn').addEventListener('click', exportConfig);
                $('#importDataBtn').addEventListener('click', () => { $('#importFileInput').click(); });
                $('#importFileInput').addEventListener('change', (e) => {
                    if (e.target.files[0]) importConfig(e.target.files[0]);
                    e.target.value = '';
                });

                // 背景
                $$('[data-bg]').forEach(btn => {
                    btn.addEventListener('click', () => {
                        data.bgMode = btn.dataset.bg;
                        renderBackground();
                        saveData();
                    });
                });
                $('#bgUpload').addEventListener('change', (e) => {
                    const file = e.target.files[0];
                    if (!file) return;
                    const reader = new FileReader();
                    reader.onload = (ev) => {
                        data.bgImage = ev.target.result;
                        data.bgMode = 'static';
                        renderBackground();
                        saveData();
                        $$('[data-bg]').forEach(b => b.classList.toggle('active', b.dataset.bg === 'static'));
                    };
                    reader.readAsDataURL(file);
                    e.target.value = '';
                });

                // 边框
                $('#borderColor').addEventListener('input', (e) => {
                    data.borderColor = e.target.value;
                    renderBorder();
                    saveData();
                });
                $('#borderWidth').addEventListener('input', (e) => {
                    data.borderWidth = parseInt(e.target.value) || 2;
                    renderBorder();
                    saveData();
                });
                $('#borderOpacity').addEventListener('input', (e) => {
                    data.borderOpacity = parseInt(e.target.value) / 100;
                    renderBorder();
                    saveData();
                });

                // 头像
                $$('[data-avatar-style]').forEach(btn => {
                    btn.addEventListener('click', () => {
                        data.avatarStyle = btn.dataset.avatarStyle;
                        renderAvatar();
                        saveData();
                    });
                });
                $('#avatarColor').addEventListener('input', (e) => {
                    data.avatarColor = e.target.value;
                    renderAvatar();
                    saveData();
                });
                $('#avatarWidth').addEventListener('input', (e) => {
                    data.avatarWidth = parseInt(e.target.value) || 3;
                    renderAvatar();
                    saveData();
                });
                $('#avatarUpload').addEventListener('change', (e) => {
                    const file = e.target.files[0];
                    if (!file) return;
                    cropImage(file, (cropped) => {
                        data.avatarImage = cropped;
                        renderAvatar();
                        saveData();
                        showToast('已上传', 1200);
                    });
                    e.target.value = '';
                });
                avatarWrapper.addEventListener('click', () => {
                    $('#avatarUpload').click();
                });

                // 昵称
                $('#nickInput').addEventListener('input', (e) => {
                    data.nickText = e.target.value;
                    renderNickname();
                    saveData();
                });
                $('#nickFont').addEventListener('change', (e) => {
                    data.nickFont = e.target.value;
                    renderNickname();
                    saveData();
                });
                $('#nickSize').addEventListener('change', (e) => {
                    data.nickSize = e.target.value;
                    renderNickname();
                    saveData();
                });
                $('#nickColor1').addEventListener('input', (e) => {
                    data.nickColor1 = e.target.value;
                    renderNickname();
                    saveData();
                });
                $('#nickColor2').addEventListener('input', (e) => {
                    data.nickColor2 = e.target.value;
                    renderNickname();
                    saveData();
                });
                $('#nickDir').addEventListener('change', (e) => {
                    data.nickDir = e.target.value;
                    renderNickname();
                    saveData();
                });
                nickDisplay.addEventListener('click', () => {
                    toggleSettings(true);
                    $('#nickInput').focus();
                });

                // 留言
                $('#messageApplyBtn').addEventListener('click', () => {
                    const val = $('#messageInput').value.trim();
                    if (val) {
                        data.messageText = val;
                        saveData();
                        resetMessage(val);
                    }
                });
                $('#messageInput').addEventListener('keydown', (e) => {
                    if (e.key === 'Enter') $('#messageApplyBtn').click();
                });
                messageDisplay.addEventListener('click', () => {
                    toggleSettings(true);
                    $('#messageInput').value = data.messageText || '';
                    $('#messageInput').focus();
                });

                // 天气
                $$('[data-weather]').forEach(btn => {
                    btn.addEventListener('click', () => {
                        data.weatherType = btn.dataset.weather;
                        $$('[data-weather]').forEach(b => b.classList.toggle('active', b === btn));
                        renderWeatherInfo();
                        if (data.weatherType !== 'none' && data.weatherEnabled) initWeather();
                        else {
                            weatherRunning = false;
                            if (animFrame) { cancelAnimationFrame(animFrame);
                                animFrame = null; }
                            wctx.clearRect(0, 0, weatherCanvas.width, weatherCanvas.height);
                        }
                        saveData();
                    });
                });
                $('#weatherCity').addEventListener('change', (e) => {
                    data.weatherCity = e.target.value || '北京';
                    renderWeatherInfo();
                    saveData();
                });
                $('#weatherTemp').addEventListener('change', (e) => {
                    data.weatherTemp = parseInt(e.target.value) || 26;
                    renderWeatherInfo();
                    saveData();
                });

                // 文件
                $('#fileUpload').addEventListener('change', (e) => {
                    const files = e.target.files;
                    if (!files || files.length === 0) return;
                    Array.from(files).forEach(file => {
                        const reader = new FileReader();
                        reader.onload = (ev) => {
                            let type = 'image';
                            if (file.type.startsWith('audio')) type = 'audio';
                            else if (file.type.startsWith('video')) type = 'video';
                            const name = file.name.replace(/\.[^.]+$/, '');
                            data.files.push({
                                type: type,
                                name: name,
                                url: ev.target.result,
                                lyrics: '',
                            });
                            renderFiles();
                            saveData();
                            if (data.bgMode === 'slideshow' && type === 'image') renderBackground();
                        };
                        reader.readAsDataURL(file);
                    });
                    e.target.value = '';
                });
                $('#clearFiles').addEventListener('click', () => {
                    if (data.files.length === 0) return;
                    if (confirm('清空所有文件？')) {
                        data.files = [];
                        data.player.currentIndex = -1;
                        data.player.isPlaying = false;
                        audioEl.pause();
                        renderFiles();
                        updatePlayerUI();
                        saveData();
                        if (data.bgMode === 'slideshow') renderBackground();
                    }
                });
                $('#quickUploadBtn').addEventListener('click', () => { $('#fileUpload').click(); });

                // 播放器
                playerPlay.addEventListener('click', togglePlay);
                playerPrev.addEventListener('click', playPrev);
                playerNext.addEventListener('click', playNext);
                playerVolumeBtn.addEventListener('click', () => {
                    const vol = audioEl.volume;
                    if (vol > 0.5) { audioEl.volume = 0;
                        data.player.volume = 0; } else { audioEl.volume = 0.8;
                        data.player.volume = 80; }
                    saveData();
                });
                playerProgress.addEventListener('input', (e) => {
                    if (audioEl.duration && !isNaN(audioEl.duration)) {
                        audioEl.currentTime = (parseInt(e.target.value) / 100) * audioEl.duration;
                    }
                });

                // 歌词雨
                $('#lyricRainBtn').addEventListener('click', toggleLyricRain);
                $('#lyricRainToggle').addEventListener('click', toggleLyricRain);

                // 音频事件
                audioEl.addEventListener('timeupdate', () => {
                    if (audioEl.duration && !isNaN(audioEl.duration)) {
                        const pct = (audioEl.currentTime / audioEl.duration) * 100;
                        playerProgress.value = pct;
                        playerCurrent.textContent = formatTime(audioEl.currentTime);
                        playerDuration.textContent = formatTime(audioEl.duration);
                    }
                });
                audioEl.addEventListener('ended', () => {
                    data.player.isPlaying = false;
                    updatePlayerUI();
                    playNext();
                });
                audioEl.addEventListener('play', () => {
                    data.player.isPlaying = true;
                    updatePlayerUI();
                    if (isLyricRainActive) startLyricRain();
                });
                audioEl.addEventListener('pause', () => {
                    data.player.isPlaying = false;
                    updatePlayerUI();
                });

                // 键盘
                document.addEventListener('keydown', (e) => {
                    if (e.key === ' ' && !e.target.matches('input, textarea, select')) {
                        e.preventDefault();
                        togglePlay();
                    }
                    if (e.key === 'ArrowRight' && !e.target.matches('input, textarea, select')) {
                        e.preventDefault();
                        playNext();
                    }
                    if (e.key === 'ArrowLeft' && !e.target.matches('input, textarea, select')) {
                        e.preventDefault();
                        playPrev();
                    }
                    if (e.key === 'Escape') { if (isSettingsOpen) toggleSettings(false); }
                });

                // 窗口
                window.addEventListener('resize', () => {
                    resizeWeather();
                    if (data.weatherEnabled && data.weatherType !== 'none') initWeather();
                    if (isLyricRainActive) startLyricRain();
                });

                // 关闭设置点击外部
                document.addEventListener('click', (e) => {
                    if (isSettingsOpen) {
                        const panel = settingsPanel;
                        const toggle = settingsToggle;
                        if (!panel.contains(e.target) && !toggle.contains(e.target) && !settingsOverlay.contains(e
                            .target)) {
                            toggleSettings(false);
                        }
                    }
                });
            }

            // ---------- 设置面板 ----------
            function toggleSettings(open) {
                isSettingsOpen = (open !== undefined) ? open : !isSettingsOpen;
                settingsPanel.classList.toggle('open', isSettingsOpen);
                settingsOverlay.classList.toggle('show', isSettingsOpen);
                document.body.style.overflow = isSettingsOpen ? 'hidden' : '';
            }

            // ---------- 初始化 ----------
            function init() {
                loadData();
                initWeatherCanvas();

                const fromURL = loadFromURL();

                fullRender();

                initEvents();

                if (fromURL) {
                    setTimeout(() => {
                        showToast('✨ 已加载分享配置 · 可自由编辑并生成新链接', 3000);
                    }, 600);
                }

                // 检测本地环境
                if (window.location.protocol === 'file:') {
                    setTimeout(() => {
                        showToast('📌 本地环境 · 分享链接需部署到Web服务器', 3000);
                    }, 1000);
                }

                console.log('💎 液态玻璃个人主页 v3.0');
                console.log('📖 快捷键: 空格=播放/暂停, ←/→=上一首/下一首, ESC=关闭面板');
                console.log('🔗 设置 → 复制链接 可生成分享链接 (需部署)');
            }

            if (document.readyState === 'complete') { init(); } else { window.addEventListener('load', init); }

        })();
    </script>

</body>
</html>
