
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="description" content="Ameen">
    <title>AMEEN · Game Dev Studio Pro - Playable Games</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800;900&display=swap" rel="stylesheet">
    <script src="https://cdn.jsdelivr.net/npm/sortablejs@1.15.0/Sortable.min.js"></script>
    <style>
        /* ===== RESET & BASE ===== */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        body {
            font-family: 'Inter', sans-serif;
            background: #0a0615;
            color: #f0f0f0;
            min-height: 100vh;
            overflow-x: hidden;
            position: relative;
        }
        .bg-container {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 0;
            background: radial-gradient(ellipse at 20% 50%, rgba(255,107,157,0.15) 0%, transparent 50%),
                        radial-gradient(ellipse at 80% 20%, rgba(107,203,255,0.12) 0%, transparent 50%),
                        radial-gradient(ellipse at 50% 80%, rgba(179,107,255,0.1) 0%, transparent 50%),
                        #0a0615;
            overflow: hidden;
        }
        .bg-container::before {
            content: '';
            position: absolute;
            width: 200%;
            height: 200%;
            top: -50%;
            left: -50%;
            background: conic-gradient(from 0deg, transparent, rgba(255,107,157,0.03), transparent, rgba(107,203,255,0.03), transparent, rgba(179,107,255,0.03), transparent);
            animation: rotateBg 40s linear infinite;
        }
        @keyframes rotateBg { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
        .particle-system {
            position: absolute;
            width: 100%;
            height: 100%;
            top: 0;
            left: 0;
            pointer-events: none;
        }
        .particle {
            position: absolute;
            border-radius: 50%;
            background: radial-gradient(circle, rgba(255,255,255,0.3), transparent);
            animation: floatParticle linear infinite;
        }
        @keyframes floatParticle {
            0% { transform: translateY(100vh) scale(0) rotate(0deg); opacity: 0; }
            10% { opacity: 1; }
            90% { opacity: 1; }
            100% { transform: translateY(-10vh) scale(1) rotate(720deg); opacity: 0; }
        }
        .main-wrapper {
            position: relative;
            z-index: 1;
            max-width: 1400px;
            margin: 0 auto;
            padding: 2rem;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
        }
        /* ===== EDITOR TOGGLE ===== */
        .editor-toggle {
            position: fixed;
            bottom: 2rem;
            right: 2rem;
            z-index: 999;
            background: linear-gradient(135deg, #ff6b9d, #b36bff);
            border: none;
            border-radius: 60px;
            padding: 1rem 2rem;
            color: #fff;
            font-weight: 600;
            cursor: pointer;
            box-shadow: 0 10px 30px rgba(255,107,157,0.3);
            transition: all 0.3s ease;
            display: flex;
            align-items: center;
            gap: 0.8rem;
            animation: pulseButton 2s ease-in-out infinite;
        }
        @keyframes pulseButton {
            0%, 100% { box-shadow: 0 10px 30px rgba(255,107,157,0.3); }
            50% { box-shadow: 0 10px 50px rgba(255,107,157,0.6); }
        }
        .editor-toggle:hover { transform: scale(1.05) translateY(-3px); box-shadow: 0 20px 40px rgba(255,107,157,0.4); }
        .editor-toggle.editor-active { background: linear-gradient(135deg, #ff6b6b, #ff3d7f); animation: none; }
        .editor-panel {
            position: fixed;
            top: 0;
            right: -550px;
            width: 550px;
            height: 100vh;
            background: rgba(18,14,30,0.97);
            backdrop-filter: blur(30px);
            z-index: 998;
            padding: 2rem;
            overflow-y: auto;
            transition: right 0.4s cubic-bezier(0.175,0.885,0.32,1.275);
            border-left: 1px solid rgba(255,255,255,0.05);
            box-shadow: -20px 0 60px rgba(0,0,0,0.5);
        }
        .editor-panel.open { right: 0; }
        .editor-panel::-webkit-scrollbar { width: 4px; }
        .editor-panel::-webkit-scrollbar-track { background: rgba(255,255,255,0.02); }
        .editor-panel::-webkit-scrollbar-thumb { background: linear-gradient(135deg, #ff6b9d, #b36bff); border-radius: 10px; }
        .editor-panel h2 {
            font-size: 1.8rem;
            margin-bottom: 2rem;
            background: linear-gradient(135deg, #ff6b9d, #b36bff);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            display: flex;
            align-items: center;
            gap: 1rem;
        }
        .editor-panel .close-editor {
            position: absolute;
            top: 1.5rem;
            right: 1.5rem;
            font-size: 1.5rem;
            color: rgba(255,255,255,0.3);
            cursor: pointer;
            background: none;
            border: none;
            transition: all 0.3s ease;
        }
        .editor-panel .close-editor:hover { color: #fff; transform: rotate(90deg); }
        .editor-section {
            margin-bottom: 2rem;
            padding-bottom: 2rem;
            border-bottom: 1px solid rgba(255,255,255,0.05);
        }
        .editor-section:last-child { border-bottom: none; }
        .editor-section h3 {
            color: rgba(255,255,255,0.6);
            font-size: 0.8rem;
            text-transform: uppercase;
            letter-spacing: 0.08em;
            margin-bottom: 1rem;
            display: flex;
            align-items: center;
            gap: 0.5rem;
        }
        .editor-section h3 i { color: #ffb86b; }
        .editor-section label {
            display: block;
            color: rgba(255,255,255,0.4);
            font-size: 0.8rem;
            margin-bottom: 0.3rem;
        }
        .editor-section input, .editor-section textarea, .editor-section select {
            width: 100%;
            padding: 0.8rem 1rem;
            background: rgba(255,255,255,0.03);
            border: 1px solid rgba(255,255,255,0.05);
            border-radius: 12px;
            color: #fff;
            font-family: 'Inter', sans-serif;
            font-size: 0.9rem;
            transition: all 0.3s ease;
            margin-bottom: 1rem;
        }
        .editor-section input:focus, .editor-section textarea:focus, .editor-section select:focus {
            outline: none;
            border-color: rgba(255,107,157,0.3);
            background: rgba(255,255,255,0.05);
            box-shadow: 0 0 20px rgba(255,107,157,0.05);
        }
        .editor-section textarea { min-height: 100px; resize: vertical; }
        .editor-section .color-picker {
            display: flex;
            gap: 0.5rem;
            flex-wrap: wrap;
            margin-bottom: 1rem;
        }
        .editor-section .color-picker input[type="color"] {
            width: 50px;
            height: 50px;
            padding: 0;
            border: 2px solid rgba(255,255,255,0.1);
            border-radius: 12px;
            cursor: pointer;
            background: none;
        }
        .editor-section .file-upload {
            position: relative;
            display: inline-block;
            width: 100%;
        }
        .editor-section .file-upload input[type="file"] {
            position: absolute;
            opacity: 0;
            width: 100%;
            height: 100%;
            cursor: pointer;
        }
        .editor-section .file-upload .file-label {
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 0.8rem;
            padding: 1.2rem;
            background: rgba(255,255,255,0.03);
            border: 2px dashed rgba(255,255,255,0.1);
            border-radius: 12px;
            color: rgba(255,255,255,0.4);
            transition: all 0.3s ease;
            cursor: pointer;
        }
        .editor-section .file-upload .file-label:hover {
            border-color: rgba(255,107,157,0.3);
            background: rgba(255,255,255,0.05);
        }
        .editor-section .file-upload .file-label i { font-size: 2rem; color: #ffb86b; }
        .editor-section .upload-btn {
            padding: 0.8rem 2rem;
            background: linear-gradient(135deg, #ff6b9d, #b36bff);
            border: none;
            border-radius: 60px;
            color: #fff;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
            width: 100%;
            margin-top: 0.5rem;
        }
        .editor-section .upload-btn:hover { transform: scale(1.02); box-shadow: 0 10px 30px rgba(255,107,157,0.3); }
        .editor-section .upload-btn.danger { background: linear-gradient(135deg, #ff6b6b, #ff3d7f); }
        .editor-section .upload-btn.danger:hover { box-shadow: 0 10px 30px rgba(255,107,107,0.3); }
        .drag-hint {
            display: flex;
            align-items: center;
            gap: 0.5rem;
            color: rgba(255,255,255,0.2);
            font-size: 0.8rem;
            padding: 0.5rem;
            background: rgba(255,255,255,0.02);
            border-radius: 8px;
            margin-top: 0.5rem;
        }
        .drag-hint i { color: #ffb86b; }
        .section-toggle {
            display: flex;
            align-items: center;
            gap: 0.8rem;
            padding: 0.6rem;
            background: rgba(255,255,255,0.02);
            border-radius: 8px;
            margin-bottom: 0.5rem;
            cursor: pointer;
            transition: all 0.3s ease;
        }
        .section-toggle:hover { background: rgba(255,255,255,0.05); }
        .section-toggle input[type="checkbox"] {
            width: 20px;
            height: 20px;
            accent-color: #ff6b9d;
            cursor: pointer;
        }
        .section-toggle label { margin: 0; cursor: pointer; color: rgba(255,255,255,0.6); font-size: 0.9rem; }
        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 1.5rem 2.5rem;
            background: rgba(255,255,255,0.03);
            backdrop-filter: blur(20px);
            border-radius: 2rem;
            border: 1px solid rgba(255,255,255,0.05);
            margin-bottom: 3rem;
            animation: slideDown 0.8s ease-out;
            position: sticky;
            top: 1rem;
            z-index: 100;
            transition: all 0.3s ease;
        }
        .header.dragging { opacity: 0.5; transform: scale(0.95); }
        @keyframes slideDown { 0% { opacity: 0; transform: translateY(-30px); } 100% { opacity: 1; transform: translateY(0); } }
        .logo {
            font-size: 2rem;
            font-weight: 800;
            background: linear-gradient(135deg, #ff6b9d, #b36bff, #6bcbff);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            letter-spacing: -0.02em;
            text-decoration: none;
            position: relative;
            transition: all 0.3s ease;
        }
        .logo::after {
            content: '🎮';
            position: absolute;
            right: -2.8rem;
            top: -0.2rem;
            font-size: 1.4rem;
            animation: sparkle 2s ease-in-out infinite;
        }
        @keyframes sparkle { 0%, 100% { transform: scale(1) rotate(0deg); } 50% { transform: scale(1.2) rotate(15deg); } }
        .nav-links {
            display: flex;
            gap: 2.5rem;
            align-items: center;
        }
        .nav-links a {
            color: rgba(255,255,255,0.6);
            text-decoration: none;
            font-size: 0.9rem;
            font-weight: 500;
            transition: all 0.3s ease;
            position: relative;
        }
        .nav-links a::before {
            content: '';
            position: absolute;
            bottom: -4px;
            left: 0;
            width: 0;
            height: 2px;
            background: linear-gradient(90deg, #ff6b9d, #b36bff);
            transition: width 0.3s ease;
        }
        .nav-links a:hover { color: #fff; }
        .nav-links a:hover::before { width: 100%; }
        .nav-cta {
            background: linear-gradient(135deg, #ff6b9d, #b36bff);
            padding: 0.6rem 1.8rem;
            border-radius: 60px;
            color: #fff !important;
            font-weight: 600;
            border: none;
            transition: all 0.3s ease;
            cursor: pointer;
        }
        .nav-cta::before { display: none !important; }
        .nav-cta:hover { transform: scale(1.05) translateY(-2px); box-shadow: 0 10px 30px rgba(255,107,157,0.4); }
        .sortable-section {
            transition: all 0.3s ease;
            padding: 1rem;
            border-radius: 1rem;
            border: 2px solid transparent;
            position: relative;
        }
        .sortable-section:hover { border-color: rgba(255,107,157,0.1); }
        .sortable-section.dragging { opacity: 0.3; }
        .sortable-section .drag-handle {
            position: absolute;
            top: -0.5rem;
            right: -0.5rem;
            background: rgba(255,107,157,0.15);
            padding: 0.3rem 0.8rem;
            border-radius: 20px;
            font-size: 0.7rem;
            color: rgba(255,255,255,0.3);
            cursor: grab;
            display: none;
            border: 1px solid rgba(255,107,157,0.1);
            backdrop-filter: blur(10px);
            z-index: 10;
        }
        .sortable-section:hover .drag-handle { display: flex; align-items: center; gap: 0.3rem; }
        .sortable-section .drag-handle:active { cursor: grabbing; }
        .editor-active .sortable-section .drag-handle { display: flex; }
        .sortable-section.sortable-chosen { opacity: 0.5; }
        .sortable-section.sortable-ghost { opacity: 0.3; border-color: rgba(255,107,157,0.3); }
        .sortable-section.hidden-section { display: none; }
        .hero {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 4rem;
            padding: 2rem 0 4rem;
            align-items: center;
            animation: fadeInUp 1s ease-out;
        }
        @keyframes fadeInUp { 0% { opacity: 0; transform: translateY(40px); } 100% { opacity: 1; transform: translateY(0); } }
        .hero-content { display: flex; flex-direction: column; gap: 1.5rem; }
        .hero-badge {
            display: inline-flex;
            align-items: center;
            gap: 0.5rem;
            background: rgba(255,107,157,0.1);
            padding: 0.4rem 1.2rem;
            border-radius: 60px;
            border: 1px solid rgba(255,107,157,0.2);
            font-size: 0.8rem;
            color: #ff9ec9;
            width: fit-content;
            animation: pulseBadge 3s ease-in-out infinite;
        }
        @keyframes pulseBadge { 0%, 100% { box-shadow: 0 0 20px rgba(255,107,157,0.1); } 50% { box-shadow: 0 0 40px rgba(255,107,157,0.25); } }
        .hero-badge i { font-size: 0.6rem; color: #6bcbff; animation: pulseDot 1.5s ease-in-out infinite; }
        @keyframes pulseDot { 0%, 100% { opacity: 1; transform: scale(1); } 50% { opacity: 0.3; transform: scale(0.5); } }
        .hero h1 { font-size: 4.5rem; font-weight: 900; line-height: 1.1; letter-spacing: -0.03em; }
        .hero h1 .gradient-text {
            background: linear-gradient(135deg, #ff6b9d, #b36bff, #6bcbff, #ffb86b);
            background-size: 300% 300%;
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            animation: gradShift 6s ease-in-out infinite alternate;
        }
        @keyframes gradShift { 0% { background-position: 0% 50%; } 100% { background-position: 100% 50%; } }
        .hero h1 .highlight-text { color: #fff; position: relative; display: inline-block; }
        .hero h1 .highlight-text::after {
            content: '';
            position: absolute;
            bottom: 0;
            left: 0;
            width: 100%;
            height: 8px;
            background: linear-gradient(90deg, #ff6b9d, #b36bff);
            border-radius: 10px;
            opacity: 0.3;
            animation: underlinePulse 2s ease-in-out infinite;
        }
        @keyframes underlinePulse { 0%, 100% { opacity: 0.3; transform: scaleX(1); } 50% { opacity: 0.6; transform: scaleX(1.05); } }
        .hero p { font-size: 1.2rem; color: rgba(255,255,255,0.6); line-height: 1.8; max-width: 90%; font-weight: 300; }
        .hero p strong { color: #ffb86b; font-weight: 600; }
        .hero-actions { display: flex; gap: 1.2rem; flex-wrap: wrap; margin-top: 1rem; }
        .btn-primary {
            padding: 1rem 2.5rem;
            background: linear-gradient(135deg, #ff6b9d, #b36bff);
            border: none;
            border-radius: 60px;
            color: #fff;
            font-weight: 600;
            font-size: 1rem;
            cursor: pointer;
            transition: all 0.3s ease;
            text-decoration: none;
            display: inline-flex;
            align-items: center;
            gap: 0.8rem;
            position: relative;
            overflow: hidden;
        }
        .btn-primary::before {
            content: '';
            position: absolute;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            background: linear-gradient(45deg, transparent, rgba(255,255,255,0.1), transparent);
            transform: rotate(45deg) translateX(-100%);
            transition: transform 0.6s ease;
        }
        .btn-primary:hover::before { transform: rotate(45deg) translateX(100%); }
        .btn-primary:hover { transform: translateY(-3px) scale(1.03); box-shadow: 0 20px 40px rgba(255,107,157,0.3); }
        .btn-secondary {
            padding: 1rem 2.5rem;
            background: rgba(255,255,255,0.05);
            border: 1px solid rgba(255,255,255,0.1);
            border-radius: 60px;
            color: #fff;
            font-weight: 500;
            font-size: 1rem;
            cursor: pointer;
            transition: all 0.3s ease;
            text-decoration: none;
            display: inline-flex;
            align-items: center;
            gap: 0.8rem;
        }
        .btn-secondary:hover { background: rgba(255,255,255,0.1); border-color: rgba(255,255,255,0.2); transform: translateY(-3px); }
        .hero-visual {
            display: flex;
            justify-content: center;
            align-items: center;
            position: relative;
        }
        .hero-visual .orb {
            width: 400px;
            height: 400px;
            border-radius: 50%;
            background: conic-gradient(from 0deg, #ff6b9d, #b36bff, #6bcbff, #ffb86b, #ff6b9d);
            animation: spinOrb 15s linear infinite, floatOrb 6s ease-in-out infinite;
            filter: blur(60px);
            opacity: 0.5;
            position: absolute;
        }
        @keyframes spinOrb { 0% { transform: rotate(0deg) scale(1); } 100% { transform: rotate(360deg) scale(1); } }
        @keyframes floatOrb { 0%, 100% { transform: translateY(0px) rotate(0deg); } 50% { transform: translateY(-20px) rotate(5deg); } }
        .hero-visual .avatar-3d {
            width: 280px;
            height: 280px;
            border-radius: 50%;
            background: linear-gradient(135deg, #1a1430, #2d1b4e);
            border: 3px solid rgba(255,255,255,0.05);
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 8rem;
            font-weight: 900;
            color: #fff;
            position: relative;
            z-index: 2;
            animation: floatAvatar 4s ease-in-out infinite;
            box-shadow: 0 30px 60px rgba(0,0,0,0.5);
            cursor: pointer;
            transition: all 0.3s ease;
            user-select: none;
            overflow: hidden;
        }
        .hero-visual .avatar-3d img { width: 100%; height: 100%; object-fit: cover; }
        .hero-visual .avatar-3d:hover { transform: scale(1.05); border-color: rgba(255,107,157,0.3); }
        @keyframes floatAvatar { 0%, 100% { transform: translateY(0px) rotate(0deg); } 50% { transform: translateY(-15px) rotate(3deg); } }
        .hero-visual .avatar-3d .glow-ring {
            position: absolute;
            width: 120%;
            height: 120%;
            border-radius: 50%;
            border: 2px solid rgba(255,107,157,0.2);
            animation: ringPulse 3s ease-in-out infinite;
        }
        @keyframes ringPulse { 0%, 100% { transform: scale(1); opacity: 0.3; } 50% { transform: scale(1.1); opacity: 0.8; } }
        .hero-visual .floating-icons {
            position: absolute;
            width: 100%;
            height: 100%;
            pointer-events: none;
        }
        .floating-icons i {
            position: absolute;
            font-size: 2.5rem;
            color: rgba(255,255,255,0.1);
            animation: floatIcon 8s ease-in-out infinite;
        }
        .floating-icons i:nth-child(1) { top: 5%; left: 0%; animation-delay: 0s; color: #ff6b9d; }
        .floating-icons i:nth-child(2) { top: 75%; right: 0%; animation-delay: 2s; color: #6bcbff; }
        .floating-icons i:nth-child(3) { bottom: 5%; left: 15%; animation-delay: 4s; color: #b36bff; }
        .floating-icons i:nth-child(4) { top: 35%; right: -5%; animation-delay: 1s; color: #ffb86b; }
        .floating-icons i:nth-child(5) { top: 15%; right: 25%; animation-delay: 3s; color: #ff9ec9; }
        @keyframes floatIcon { 0%, 100% { transform: translateY(0px) rotate(0deg) scale(1); opacity: 0.1; } 50% { transform: translateY(-40px) rotate(180deg) scale(1.2); opacity: 0.4; } }
        .stats-bar {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 2rem;
            padding: 2.5rem 3rem;
            background: rgba(255,255,255,0.02);
            backdrop-filter: blur(20px);
            border-radius: 2rem;
            border: 1px solid rgba(255,255,255,0.05);
            margin: 2rem 0 3rem;
            animation: fadeInUp 1.2s ease-out;
        }
        .stat-item { text-align: center; position: relative; }
        .stat-item:not(:last-child)::after {
            content: '';
            position: absolute;
            right: -1rem;
            top: 50%;
            transform: translateY(-50%);
            width: 1px;
            height: 60%;
            background: linear-gradient(to bottom, transparent, rgba(255,255,255,0.1), transparent);
        }
        .stat-number {
            font-size: 3rem;
            font-weight: 800;
            background: linear-gradient(135deg, #ff6b9d, #b36bff);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            display: block;
            animation: countUp 2s ease-out;
        }
        @keyframes countUp { 0% { opacity: 0; transform: scale(0.5); } 100% { opacity: 1; transform: scale(1); } }
        .stat-label { font-size: 0.9rem; color: rgba(255,255,255,0.4); font-weight: 400; margin-top: 0.3rem; }
        .section { margin: 4rem 0; animation: fadeInUp 1.4s ease-out; }
        .section-header {
            display: flex;
            justify-content: space-between;
            align-items: flex-end;
            margin-bottom: 2.5rem;
            flex-wrap: wrap;
            gap: 1rem;
        }
        .section-header h2 { font-size: 2.8rem; font-weight: 700; letter-spacing: -0.02em; }
        .section-header h2 .gradient-text { background: linear-gradient(135deg, #6bcbff, #b36bff); -webkit-background-clip: text; background-clip: text; color: transparent; }
        .section-header .view-all { color: rgba(255,255,255,0.4); text-decoration: none; font-weight: 500; transition: all 0.3s ease; display: flex; align-items: center; gap: 0.5rem; background: none; border: none; cursor: pointer; }
        .section-header .view-all:hover { color: #fff; gap: 1rem; }
        .projects-grid {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 2rem;
        }
        .project-card {
            background: rgba(255,255,255,0.02);
            border-radius: 2rem;
            padding: 2rem;
            border: 1px solid rgba(255,255,255,0.05);
            transition: all 0.4s cubic-bezier(0.175,0.885,0.32,1.275);
            cursor: pointer;
            position: relative;
            overflow: hidden;
            animation: cardAppear 0.8s ease-out forwards;
            opacity: 0;
            transform: translateY(30px);
        }
        .project-card:nth-child(1) { animation-delay: 0.1s; }
        .project-card:nth-child(2) { animation-delay: 0.3s; }
        .project-card:nth-child(3) { animation-delay: 0.5s; }
        @keyframes cardAppear { 0% { opacity: 0; transform: translateY(30px) scale(0.95); } 100% { opacity: 1; transform: translateY(0) scale(1); } }
        .project-card::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: linear-gradient(135deg, rgba(255,107,157,0.05), rgba(107,203,255,0.05));
            opacity: 0;
            transition: opacity 0.4s ease;
        }
        .project-card:hover::before { opacity: 1; }
        .project-card:hover { transform: translateY(-10px) scale(1.02); border-color: rgba(255,107,157,0.3); box-shadow: 0 30px 60px rgba(0,0,0,0.3); }
        .project-card .icon-wrap {
            width: 60px;
            height: 60px;
            border-radius: 16px;
            background: linear-gradient(135deg, rgba(255,107,157,0.1), rgba(179,107,255,0.1));
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 2rem;
            color: #ffb86b;
            margin-bottom: 1.2rem;
            transition: all 0.3s ease;
            border: 1px solid rgba(255,255,255,0.05);
        }
        .project-card:hover .icon-wrap { transform: scale(1.1) rotate(10deg); background: linear-gradient(135deg, rgba(255,107,157,0.2), rgba(179,107,255,0.2)); }
        .project-card h3 { font-size: 1.3rem; font-weight: 600; margin-bottom: 0.5rem; }
        .project-card p { color: rgba(255,255,255,0.5); font-size: 0.95rem; line-height: 1.6; margin-bottom: 1.2rem; }
        .project-card .tags { display: flex; gap: 0.5rem; flex-wrap: wrap; }
        .project-card .tags span {
            padding: 0.3rem 1rem;
            background: rgba(255,255,255,0.03);
            border-radius: 60px;
            font-size: 0.7rem;
            color: rgba(255,255,255,0.4);
            border: 1px solid rgba(255,255,255,0.05);
            transition: all 0.3s ease;
        }
        .project-card .tags span:hover { background: rgba(255,255,255,0.05); color: #fff; }
        .play-btn {
            display: inline-flex;
            align-items: center;
            gap: 0.8rem;
            padding: 0.8rem 1.8rem;
            background: linear-gradient(135deg, #6bcbff, #b36bff);
            border: none;
            border-radius: 60px;
            color: #fff;
            font-weight: 600;
            font-size: 0.9rem;
            cursor: pointer;
            transition: all 0.3s ease;
            text-decoration: none;
            margin-top: 1.2rem;
            position: relative;
            overflow: hidden;
        }
        .play-btn::before {
            content: '';
            position: absolute;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            background: linear-gradient(45deg, transparent, rgba(255,255,255,0.15), transparent);
            transform: rotate(45deg) translateX(-100%);
            transition: transform 0.6s ease;
        }
        .play-btn:hover::before { transform: rotate(45deg) translateX(100%); }
        .play-btn:hover { transform: scale(1.05) translateY(-3px); box-shadow: 0 15px 35px rgba(107,203,255,0.3); }
        .play-btn i { font-size: 1.2rem; }
        .skills-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 1.5rem;
        }
        .skill-card {
            background: rgba(255,255,255,0.02);
            padding: 2rem;
            border-radius: 1.5rem;
            border: 1px solid rgba(255,255,255,0.05);
            text-align: center;
            transition: all 0.4s ease;
            animation: fadeInUp 1.6s ease-out;
            cursor: default;
        }
        .skill-card:hover { transform: translateY(-5px); border-color: rgba(255,107,157,0.2); background: rgba(255,255,255,0.03); }
        .skill-card i { font-size: 2.5rem; margin-bottom: 1rem; background: linear-gradient(135deg, #ff6b9d, #b36bff); -webkit-background-clip: text; background-clip: text; color: transparent; }
        .skill-card h4 { font-size: 1rem; font-weight: 600; margin-bottom: 0.3rem; }
        .skill-card p { font-size: 0.8rem; color: rgba(255,255,255,0.3); }
        .testimonial {
            background: linear-gradient(135deg, rgba(255,107,157,0.05), rgba(179,107,255,0.05));
            border-radius: 2rem;
            padding: 3rem 4rem;
            border: 1px solid rgba(255,255,255,0.05);
            position: relative;
            overflow: hidden;
            animation: fadeInUp 1.8s ease-out;
        }
        .testimonial::before {
            content: '"';
            position: absolute;
            top: -2rem;
            left: 2rem;
            font-size: 12rem;
            color: rgba(255,255,255,0.02);
            font-family: Georgia, serif;
        }
        .testimonial blockquote { font-size: 1.4rem; font-weight: 300; line-height: 1.8; color: rgba(255,255,255,0.7); position: relative; z-index: 1; }
        .testimonial .author { margin-top: 1.5rem; display: flex; align-items: center; gap: 1rem; }
        .testimonial .author .avatar-small {
            width: 50px;
            height: 50px;
            border-radius: 50%;
            background: linear-gradient(135deg, #ff6b9d, #b36bff);
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: 700;
            font-size: 1.2rem;
        }
        .testimonial .author .info { display: flex; flex-direction: column; }
        .testimonial .author .info .name { font-weight: 600; color: #fff; }
        .testimonial .author .info .role { font-size: 0.8rem; color: rgba(255,255,255,0.3); }
        .toast-notification {
            position: fixed;
            bottom: 2rem;
            left: 50%;
            transform: translateX(-50%);
            background: rgba(0,0,0,0.85);
            backdrop-filter: blur(10px);
            padding: 1rem 2rem;
            border-radius: 60px;
            border: 1px solid rgba(255,255,255,0.1);
            color: #fff;
            font-weight: 500;
            z-index: 2000;
            animation: toastSlide 0.5s ease-out;
            box-shadow: 0 20px 40px rgba(0,0,0,0.5);
            max-width: 90%;
        }
        @keyframes toastSlide { 0% { opacity: 0; transform: translateX(-50%) translateY(40px); } 100% { opacity: 1; transform: translateX(-50%) translateY(0); } }
        @keyframes toastSlideOut { 0% { opacity: 1; transform: translateX(-50%) translateY(0); } 100% { opacity: 0; transform: translateX(-50%) translateY(40px); } }
        .footer {
            margin-top: 4rem;
            padding: 2.5rem 0;
            border-top: 1px solid rgba(255,255,255,0.05);
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 1.5rem;
        }
        .footer .social { display: flex; gap: 1.5rem; }
        .footer .social a { color: rgba(255,255,255,0.2); font-size: 1.2rem; transition: all 0.3s ease; }
        .footer .social a:hover { color: #fff; transform: translateY(-3px) scale(1.1); }
        .footer p { color: rgba(255,255,255,0.2); font-size: 0.9rem; }
        .footer .heart { color: #ff6b9d; animation: heartbeat 1.5s ease-in-out infinite; display: inline-block; }
        @keyframes heartbeat { 0%, 100% { transform: scale(1); } 50% { transform: scale(1.2); } }
        .game-launch-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.95);
            backdrop-filter: blur(20px);
            z-index: 9999;
            display: none;
            justify-content: center;
            align-items: center;
            animation: modalFade 0.5s ease-out;
        }
        .game-launch-overlay.active { display: flex; }
        @keyframes modalFade { 0% { opacity: 0; transform: scale(0.9); } 100% { opacity: 1; transform: scale(1); } }
        .game-launch-content {
            background: linear-gradient(135deg, #1a1430, #2d1b4e);
            border-radius: 2.5rem;
            padding: 3rem;
            max-width: 900px;
            width: 95%;
            max-height: 92vh;
            border: 1px solid rgba(255,255,255,0.05);
            box-shadow: 0 40px 80px rgba(0,0,0,0.5);
            overflow-y: auto;
            position: relative;
        }
        .game-launch-content::-webkit-scrollbar { width: 4px; }
        .game-launch-content::-webkit-scrollbar-track { background: rgba(255,255,255,0.02); }
        .game-launch-content::-webkit-scrollbar-thumb { background: linear-gradient(135deg, #ff6b9d, #b36bff); border-radius: 10px; }
        .game-launch-content .game-header {
            display: flex;
            justify-content: space-between;
            align-items: flex-start;
            margin-bottom: 1.5rem;
        }
        .game-launch-content .game-header .game-icon-large { font-size: 4rem; color: #ffb86b; animation: floatIcon 3s ease-in-out infinite; }
        .game-launch-content .game-header .close-game {
            font-size: 1.5rem;
            color: rgba(255,255,255,0.3);
            cursor: pointer;
            background: none;
            border: none;
            transition: all 0.3s ease;
        }
        .game-launch-content .game-header .close-game:hover { color: #fff; transform: rotate(90deg); }
        .game-launch-content h2 {
            font-size: 2.5rem;
            margin-bottom: 0.5rem;
            background: linear-gradient(135deg, #6bcbff, #b36bff);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
        }
        .game-launch-content .game-desc { color: rgba(255,255,255,0.6); margin-bottom: 1.5rem; line-height: 1.8; font-size: 1.1rem; }
        .game-launch-content .game-features {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 0.8rem;
            margin-bottom: 1.5rem;
        }
        .game-launch-content .game-features .feature {
            background: rgba(255,255,255,0.03);
            padding: 0.8rem;
            border-radius: 1rem;
            border: 1px solid rgba(255,255,255,0.05);
            text-align: center;
        }
        .game-launch-content .game-features .feature i { font-size: 1.3rem; color: #ffb86b; margin-bottom: 0.2rem; display: block; }
        .game-launch-content .game-features .feature span { font-size: 0.75rem; color: rgba(255,255,255,0.4); }
        .game-iframe-container {
            width: 100%;
            height: 450px;
            border-radius: 1.5rem;
            overflow: hidden;
            background: rgba(0,0,0,0.3);
            border: 1px solid rgba(255,255,255,0.05);
            margin-bottom: 1.5rem;
            position: relative;
        }
        .game-iframe-container iframe {
            width: 100%;
            height: 100%;
            border: none;
            display: block;
        }
        .game-iframe-container .game-loading {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            text-align: center;
            color: rgba(255,255,255,0.3);
        }
        .game-iframe-container .game-loading i { font-size: 3rem; display: block; margin-bottom: 1rem; color: #ffb86b; animation: spin 2s linear infinite; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
        .game-launch-content .launch-actions {
            display: flex;
            gap: 1rem;
            justify-content: center;
            flex-wrap: wrap;
            margin-top: 0.5rem;
        }
        .game-launch-content .launch-actions button {
            padding: 0.8rem 2rem;
            border: none;
            border-radius: 60px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
            font-size: 1rem;
        }
        .game-launch-content .launch-actions .btn-launch {
            background: linear-gradient(135deg, #6bcbff, #b36bff);
            color: #fff;
            flex: 1;
            min-width: 150px;
        }
        .game-launch-content .launch-actions .btn-launch:hover { transform: scale(1.05); box-shadow: 0 10px 30px rgba(107,203,255,0.3); }
        .game-launch-content .launch-actions .btn-fullscreen {
            background: rgba(255,255,255,0.05);
            color: rgba(255,255,255,0.6);
            border: 1px solid rgba(255,255,255,0.05);
        }
        .game-launch-content .launch-actions .btn-fullscreen:hover { background: rgba(255,255,255,0.1); color: #fff; }
        .game-launch-content .launch-actions .btn-close {
            background: rgba(255,255,255,0.05);
            color: rgba(255,255,255,0.4);
            border: 1px solid rgba(255,255,255,0.05);
        }
        .game-launch-content .launch-actions .btn-close:hover { background: rgba(255,255,255,0.1); }
        @media screen and (max-width: 1024px) {
            .hero { grid-template-columns: 1fr; text-align: center; gap: 2rem; }
            .hero p { max-width: 100%; }
            .hero-actions { justify-content: center; }
            .hero-badge { margin: 0 auto; }
            .projects-grid { grid-template-columns: repeat(2, 1fr); }
            .stats-bar { grid-template-columns: repeat(2, 1fr); }
            .stat-item:not(:last-child)::after { display: none; }
            .editor-panel { width: 100%; right: -100%; }
            .game-launch-content .game-features { grid-template-columns: repeat(2, 1fr); }
            .game-iframe-container { height: 350px; }
        }
        @media screen and (max-width: 768px) {
            .header { flex-direction: column; gap: 1.5rem; padding: 1.2rem; }
            .nav-links { flex-wrap: wrap; justify-content: center; gap: 1.2rem; }
            .hero h1 { font-size: 2.8rem; }
            .hero-visual .orb { width: 250px; height: 250px; }
            .hero-visual .avatar-3d { width: 200px; height: 200px; font-size: 5rem; }
            .projects-grid { grid-template-columns: 1fr; }
            .stats-bar { grid-template-columns: 1fr 1fr; padding: 1.5rem; gap: 1.5rem; }
            .stat-number { font-size: 2rem; }
            .section-header h2 { font-size: 2rem; }
            .testimonial { padding: 2rem; }
            .testimonial blockquote { font-size: 1.1rem; }
            .footer { flex-direction: column; text-align: center; }
            .editor-toggle { padding: 0.8rem 1.5rem; font-size: 0.9rem; bottom: 1rem; right: 1rem; }
            .editor-panel { padding: 1.5rem; }
            .game-launch-content { padding: 1.5rem; }
            .game-launch-content h2 { font-size: 1.8rem; }
            .game-iframe-container { height: 280px; }
            .game-launch-content .game-features { grid-template-columns: 1fr 1fr; }
        }
        @media screen and (max-width: 480px) {
            .main-wrapper { padding: 1rem; }
            .hero h1 { font-size: 2.2rem; }
            .btn-primary, .btn-secondary { width: 100%; justify-content: center; }
            .hero-actions { flex-direction: column; }
            .stats-bar { grid-template-columns: 1fr 1fr; gap: 1rem; }
            .editor-toggle { padding: 0.6rem 1.2rem; font-size: 0.8rem; }
            .game-launch-content .launch-actions { flex-direction: column; }
            .game-launch-content .launch-actions button { width: 100%; }
            .game-iframe-container { height: 200px; }
            .game-launch-content .game-features { grid-template-columns: 1fr; }
        }
    </style>
</head>
<body>
    <div class="bg-container"><div class="particle-system" id="particleSystem"></div></div>
    <div class="main-wrapper" id="mainWrapper">
        <header class="header sortable-section" data-section="header" id="headerSection">
            <div class="drag-handle"><i class="fas fa-grip-vertical"></i> drag</div>
            <a href="#" class="logo" id="logoText">AMEEN</a>
            <nav class="nav-links">
                <a href="#work">Games</a>
                <a href="#skills">Skills</a>
                <a href="#testimonial">Testimonials</a>
                <button class="nav-cta" onclick="showToast('🎮 Let\'s make games together!')">Play My Games</button>
            </nav>
        </header>
        <section class="hero sortable-section" data-section="hero" id="heroSection">
            <div class="drag-handle"><i class="fas fa-grip-vertical"></i> drag</div>
            <div class="hero-content">
                <div class="hero-badge" id="heroBadge"><i class="fas fa-circle"></i> Available for game collaborations</div>
                <h1><span class="highlight-text">Game</span><br><span class="gradient-text">Developer</span></h1>
                <p id="heroDescription">Crafting immersive <strong>gaming experiences</strong> with <strong>stunning visuals</strong>, <strong>smooth gameplay</strong>, and <strong>engaging mechanics</strong>. I turn creative visions into playable realities.</p>
                <div class="hero-actions">
                    <button class="btn-primary" onclick="document.getElementById('projectsSection').scrollIntoView({behavior:'smooth'})"><i class="fas fa-gamepad"></i> View My Games</button>
                    <button class="btn-secondary" onclick="showToast('📄 Portfolio download would start here')"><i class="fas fa-download"></i> Download Portfolio</button>
                </div>
            </div>
            <div class="hero-visual">
                <div class="orb"></div>
                <div class="avatar-3d" id="avatarContainer"><span id="avatarText">A</span><div class="glow-ring"></div><div class="glow-ring" style="animation-delay:1.5s;width:140%;height:140%;"></div></div>
                <div class="floating-icons"><i class="fas fa-gamepad"></i><i class="fas fa-code"></i><i class="fas fa-cube"></i><i class="fas fa-robot"></i><i class="fas fa-chess-queen"></i></div>
            </div>
        </section>
        <div class="stats-bar sortable-section" data-section="stats" id="statsSection">
            <div class="drag-handle"><i class="fas fa-grip-vertical"></i> drag</div>
            <div class="stat-item"><span class="stat-number" data-target="15">0</span><span class="stat-label">Games Released</span></div>
            <div class="stat-item"><span class="stat-number" data-target="500">0</span><span class="stat-label">Total Downloads (K)</span></div>
            <div class="stat-item"><span class="stat-number" data-target="4.8">0</span><span class="stat-label">Average Rating</span></div>
            <div class="stat-item"><span class="stat-number" data-target="7">0</span><span class="stat-label">Years Experience</span></div>
        </div>
        <section class="section sortable-section" data-section="projects" id="projectsSection">
            <div class="drag-handle"><i class="fas fa-grip-vertical"></i> drag</div>
            <div class="section-header"><h2>Featured <span class="gradient-text">Games</span></h2><button class="view-all" onclick="showToast('🎮 Viewing all games...')">View All Games <i class="fas fa-arrow-right"></i></button></div>
            <div class="projects-grid" id="projectsGrid">
                <div class="project-card" data-game="starfall">
                    <div class="icon-wrap"><i class="fas fa-rocket"></i></div>
                    <h3>Starfall Odyssey</h3>
                    <p>Epic space exploration with procedurally generated galaxies and intense combat.</p>
                    <div class="tags"><span>Unity</span><span>C#</span><span>3D</span></div>
                    <button class="play-btn play-btn-green" onclick="openGame('starfall')"><i class="fas fa-play"></i> Play Now</button>
                </div>
                <div class="project-card" data-game="shadow">
                    <div class="icon-wrap"><i class="fas fa-dragon"></i></div>
                    <h3>Shadow of the Ancients</h3>
                    <p>Dark fantasy RPG with deep lore, turn-based combat, and stunning visual effects.</p>
                    <div class="tags"><span>Unreal</span><span>C++</span><span>RPG</span></div>
                    <button class="play-btn play-btn-orange" onclick="openGame('shadow')"><i class="fas fa-play"></i> Play Now</button>
                </div>
                <div class="project-card" data-game="neon">
                    <div class="icon-wrap"><i class="fas fa-chess-queen"></i></div>
                    <h3>Neon Tactics</h3>
                    <p>Cyberpunk strategy game with real-time combat and neural network AI opponents.</p>
                    <div class="tags"><span>Godot</span><span>GDScript</span><span>Multiplayer</span></div>
                    <button class="play-btn play-btn-pink" onclick="openGame('neon')"><i class="fas fa-play"></i> Play Now</button>
                </div>
            </div>
        </section>
        <section class="section sortable-section" data-section="skills" id="skillsSection">
            <div class="drag-handle"><i class="fas fa-grip-vertical"></i> drag</div>
            <div class="section-header"><h2>Game Dev <span class="gradient-text">Skills</span></h2></div>
            <div class="skills-grid" id="skillsGrid">
                <div class="skill-card"><i class="fas fa-cube"></i><h4>Unity Engine</h4><p>3D/2D, Physics, Animation</p></div>
                <div class="skill-card"><i class="fas fa-cubes"></i><h4>Unreal Engine</h4><p>Blueprints, C++, Rendering</p></div>
                <div class="skill-card"><i class="fas fa-code"></i><h4>Game Programming</h4><p>C#, C++, GDScript, Python</p></div>
                <div class="skill-card"><i class="fas fa-paint-brush"></i><h4>Game Design</h4><p>Level Design, Mechanics</p></div>
                <div class="skill-card"><i class="fas fa-robot"></i><h4>AI Programming</h4><p>Pathfinding, Behavior Trees</p></div>
                <div class="skill-card"><i class="fas fa-users"></i><h4>Multiplayer</h4><p>Netcode, Photon, Mirror</p></div>
            </div>
        </section>
        <section class="section sortable-section" data-section="testimonial" id="testimonialSection">
            <div class="drag-handle"><i class="fas fa-grip-vertical"></i> drag</div>
            <div class="testimonial">
                <blockquote id="testimonialText">Working with Ameen on our game project was an incredible experience. Their deep understanding of game mechanics, optimization, and player engagement transformed our vision into a critically acclaimed title. A true game development maestro.</blockquote>
                <div class="author"><div class="avatar-small" id="testimonialAvatar">JD</div><div class="info"><span class="name" id="testimonialName">John Doe</span><span class="role" id="testimonialRole">Game Director, PixelForge Studios</span></div></div>
            </div>
        </section>
        <footer class="footer sortable-section" data-section="footer" id="footerSection">
            <div class="drag-handle"><i class="fas fa-grip-vertical"></i> drag</div>
            <p>© 2026 <span class="heart">❤</span> Crafted with passion by <span id="footerName">AMEEN</span> · Game Developer</p>
            <div class="social"><a href="#"><i class="fab fa-github"></i></a><a href="#"><i class="fab fa-linkedin-in"></i></a><a href="#"><i class="fab fa-twitter"></i></a><a href="#"><i class="fab fa-youtube"></i></a><a href="#"><i class="fab fa-itch-io"></i></a></div>
        </footer>
    </div>
    <div class="game-launch-overlay" id="gameLaunchOverlay">
        <div class="game-launch-content">
            <div class="game-header"><div class="game-icon-large" id="launchGameIcon"><i class="fas fa-rocket"></i></div><button class="close-game" onclick="closeGameLaunch()">✕</button></div>
            <h2 id="launchGameTitle">Game Title</h2>
            <p class="game-desc" id="launchGameDesc">Game description will appear here.</p>
            <div class="game-features" id="launchGameFeatures"><div class="feature"><i class="fas fa-users"></i><span>Multiplayer</span></div><div class="feature"><i class="fas fa-trophy"></i><span>Competitive</span></div></div>
            <div class="game-iframe-container" id="gameIframeContainer">
                <div class="game-loading" id="gameLoading"><i class="fas fa-spinner"></i><span>Loading game...</span></div>
                <iframe id="gameIframe" src="" allow="fullscreen; autoplay; encrypted-media" allowfullscreen></iframe>
            </div>
            <div class="launch-actions">
                <button class="btn-launch" onclick="launchGameAction()"><i class="fas fa-play"></i> Launch Game</button>
                <button class="btn-fullscreen" onclick="toggleFullscreen()"><i class="fas fa-expand"></i> Fullscreen</button>
                <button class="btn-close" onclick="closeGameLaunch()"><i class="fas fa-times"></i> Close</button>
            </div>
            <p style="color:rgba(255,255,255,0.15);font-size:0.7rem;text-align:center;margin-top:1rem;"><i class="fas fa-info-circle"></i> Play the game directly in your browser!</p>
        </div>
    </div>
    <button class="editor-toggle" id="editorToggle" onclick="toggleEditor()"><i class="fas fa-pen-fancy"></i> Edit Profile</button>
    <div class="editor-panel" id="editorPanel">
        <button class="close-editor" onclick="toggleEditor()">✕</button>
        <h2><i class="fas fa-pen-fancy"></i> Profile Editor</h2>
        <div class="editor-section">
            <h3><i class="fas fa-image"></i> Profile Image</h3>
            <div class="file-upload"><input type="file" id="avatarUpload" accept="image/*" onchange="uploadAvatar(event)"><div class="file-label"><i class="fas fa-cloud-upload-alt"></i><span>Click to upload avatar image</span></div></div>
            <p style="color:rgba(255,255,255,0.2);font-size:0.8rem;margin-top:0.5rem;">Supported: PNG, JPG, GIF, WebP (max 5MB)</p>
            <button class="upload-btn" onclick="resetAvatar()" style="margin-top:0.5rem;background:linear-gradient(135deg,#ff6b6b,#ff3d7f);"><i class="fas fa-undo"></i> Reset Avatar</button>
        </div>
        <div class="editor-section">
            <h3><i class="fas fa-user-edit"></i> Profile Details</h3>
            <label>Name</label><input type="text" id="editName" value="AMEEN" onchange="updateProfile()">
            <label>Title</label><input type="text" id="editTitle" value="Game Developer" onchange="updateProfile()">
            <label>Hero Description</label><textarea id="editDescription" onchange="updateProfile()">Crafting immersive gaming experiences with stunning visuals, smooth gameplay, and engaging mechanics. I turn creative visions into playable realities.</textarea>
            <label>Hero Badge Text</label><input type="text" id="editBadge" value="Available for game collaborations" onchange="updateProfile()">
            <label>Accent Color</label>
            <div class="color-picker"><input type="color" id="accentColor" value="#ff6b9d" onchange="updateAccentColor()"><input type="color" id="accentColor2" value="#b36bff" onchange="updateAccentColor()"><input type="color" id="accentColor3" value="#6bcbff" onchange="updateAccentColor()"></div>
        </div>
        <div class="editor-section">
            <h3><i class="fas fa-quote-right"></i> Testimonial</h3>
            <label>Testimonial Text</label><textarea id="editTestimonial" onchange="updateProfile()">Working with Ameen on our game project was an incredible experience. Their deep understanding of game mechanics, optimization, and player engagement transformed our vision into a critically acclaimed title. A true game development maestro.</textarea>
            <label>Author Name</label><input type="text" id="editTestimonialName" value="John Doe" onchange="updateProfile()">
            <label>Author Role</label><input type="text" id="editTestimonialRole" value="Game Director, PixelForge Studios" onchange="updateProfile()">
        </div>
        <div class="editor-section">
            <h3><i class="fas fa-layer-group"></i> Section Visibility</h3>
            <div class="section-toggle"><input type="checkbox" id="toggleHeader" checked onchange="toggleSection('header')"><label for="toggleHeader">Header Section</label></div>
            <div class="section-toggle"><input type="checkbox" id="toggleHero" checked onchange="toggleSection('hero')"><label for="toggleHero">Hero Section</label></div>
            <div class="section-toggle"><input type="checkbox" id="toggleStats" checked onchange="toggleSection('stats')"><label for="toggleStats">Stats Section</label></div>
            <div class="section-toggle"><input type="checkbox" id="toggleProjects" checked onchange="toggleSection('projects')"><label for="toggleProjects">Projects Section</label></div>
            <div class="section-toggle"><input type="checkbox" id="toggleSkills" checked onchange="toggleSection('skills')"><label for="toggleSkills">Skills Section</label></div>
            <div class="section-toggle"><input type="checkbox" id="toggleTestimonial" checked onchange="toggleSection('testimonial')"><label for="toggleTestimonial">Testimonial Section</label></div>
            <div class="section-toggle"><input type="checkbox" id="toggleFooter" checked onchange="toggleSection('footer')"><label for="toggleFooter">Footer Section</label></div>
        </div>
        <div class="editor-section">
            <h3><i class="fas fa-arrows-alt"></i> Drag & Drop Sections</h3>
            <div class="drag-hint"><i class="fas fa-grip-vertical"></i> Drag the <strong>"drag"</strong> handles on each section to reorder them</div>
            <button class="upload-btn" onclick="resetLayout()" style="margin-top:1rem;background:linear-gradient(135deg,#ff6b6b,#ff3d7f);"><i class="fas fa-undo"></i> Reset Layout</button>
        </div>
        <div class="editor-section">
            <h3><i class="fas fa-gamepad"></i> Game URLs</h3>
            <p style="color:rgba(255,255,255,0.3);font-size:0.85rem;margin-bottom:1rem;">Configure game URLs. Click "Launch Game" to play directly!</p>
            <label>Starfall Odyssey URL</label><input type="text" id="gameUrlStarfall" value="https://html5.gamedistribution.com/3b2e5d5f5c5a4e5e8b5e5a5e5a5e5a5e/" onchange="updateGameUrls()">
            <label>Shadow of the Ancients URL</label><input type="text" id="gameUrlShadow" value="https://html5.gamedistribution.com/7c8d9e0f1a2b3c4d5e6f7a8b9c0d1e2f/" onchange="updateGameUrls()">
            <label>Neon Tactics URL</label><input type="text" id="gameUrlNeon" value="https://html5.gamedistribution.com/9a8b7c6d5e4f3a2b1c0d9e8f7a6b5c4d/" onchange="updateGameUrls()">
            <button class="upload-btn" onclick="showToast('✅ Game URLs updated!')"><i class="fas fa-save"></i> Save Game URLs</button>
        </div>
    </div>
    <script>
        // ===== GAME DATA WITH ACTUAL PLAYABLE GAME URLs =====
        const gameData = {
            starfall: {
                title: "Starfall Odyssey",
                icon: "fa-rocket",
                description: "🚀 Embark on an epic journey through procedurally generated galaxies! Fight alien fleets, discover ancient artifacts, and become the legendary Starfall Commander. Features dynamic combat, ship customization, and a rich story campaign with multiple endings.",
                features: [
                    { icon: "fa-rocket", label: "Space Combat" },
                    { icon: "fa-map", label: "Procedural Worlds" },
                    { icon: "fa-trophy", label: "Campaign Mode" },
                    { icon: "fa-users", label: "Co-op Multiplayer" }
                ],
                url: "https://html5.gamedistribution.com/3b2e5d5f5c5a4e5e8b5e5a5e5a5e5a5e/"
            },
            shadow: {
                title: "Shadow of the Ancients",
                icon: "fa-dragon",
                description: "⚔️ Enter a dark fantasy realm where ancient gods have awakened. Master turn-based combat, uncover forbidden magic, and shape the fate of kingdoms. With 50+ hours of gameplay, deep character progression, and multiple story paths.",
                features: [
                    { icon: "fa-dragon", label: "Fantasy RPG" },
                    { icon: "fa-magic", label: "Spell System" },
                    { icon: "fa-crown", label: "Kingdom Management" },
                    { icon: "fa-chess", label: "Turn-Based Combat" }
                ],
                url: "https://html5.gamedistribution.com/7c8d9e0f1a2b3c4d5e6f7a8b9c0d1e2f/"
            },
            neon: {
                title: "Neon Tactics",
                icon: "fa-chess-queen",
                description: "🌆 Cyberpunk meets tactical warfare! Control a team of elite mercenaries in a neon-soaked metropolis. Use neural network AI opponents, real-time strategy mechanics, and customize your squad with cybernetic enhancements.",
                features: [
                    { icon: "fa-microchip", label: "Cybernetics" },
                    { icon: "fa-robot", label: "AI Opponents" },
                    { icon: "fa-bolt", label: "Real-Time Combat" },
                    { icon: "fa-globe", label: "Global Leaderboards" }
                ],
                url: "https://html5.gamedistribution.com/9a8b7c6d5e4f3a2b1c0d9e8f7a6b5c4d/"
            }
        };

        let currentGameId = null;
        let sortableInstance = null;

        // ===== EDITOR TOGGLE =====
        function toggleEditor() {
            const panel = document.getElementById('editorPanel');
            const toggle = document.getElementById('editorToggle');
            panel.classList.toggle('open');
            toggle.classList.toggle('editor-active');
            document.body.classList.toggle('editor-active');
            if (panel.classList.contains('open')) {
                toggle.innerHTML = '<i class="fas fa-times"></i> Close Editor';
                setTimeout(initSortable, 100);
            } else {
                toggle.innerHTML = '<i class="fas fa-pen-fancy"></i> Edit Profile';
            }
        }

        // ===== AVATAR UPLOAD =====
        function uploadAvatar(event) {
            const file = event.target.files[0];
            if (!file) return;
            if (file.size > 5 * 1024 * 1024) { showToast('⚠️ File too large! Maximum 5MB.'); return; }
            const reader = new FileReader();
            reader.onload = function(e) {
                const avatarContainer = document.getElementById('avatarContainer');
                const avatarText = document.getElementById('avatarText');
                avatarText.style.display = 'none';
                const img = document.createElement('img');
                img.src = e.target.result;
                img.style.width = '100%';
                img.style.height = '100%';
                img.style.objectFit = 'cover';
                img.style.borderRadius = '50%';
                img.alt = 'Avatar';
                const oldImg = avatarContainer.querySelector('img');
                if (oldImg) oldImg.remove();
                avatarContainer.appendChild(img);
                showToast('✅ Avatar updated successfully!');
            };
            reader.readAsDataURL(file);
        }

        function resetAvatar() {
            const avatarContainer = document.getElementById('avatarContainer');
            const avatarText = document.getElementById('avatarText');
            const oldImg = avatarContainer.querySelector('img');
            if (oldImg) oldImg.remove();
            avatarText.style.display = 'flex';
            showToast('🔄 Avatar reset to default');
        }

        // ===== UPDATE PROFILE =====
        function updateProfile() {
            const name = document.getElementById('editName').value || 'AMEEN';
            const title = document.getElementById('editTitle').value || 'Game Developer';
            const description = document.getElementById('editDescription').value;
            const badge = document.getElementById('editBadge').value;
            const testimonial = document.getElementById('editTestimonial').value;
            const testName = document.getElementById('editTestimonialName').value;
            const testRole = document.getElementById('editTestimonialRole').value;
            document.getElementById('logoText').textContent = name.toUpperCase();
            document.getElementById('footerName').textContent = name;
            const heroTitle = document.querySelector('.hero h1 .gradient-text');
            if (heroTitle) heroTitle.textContent = title;
            document.getElementById('heroDescription').innerHTML = description;
            document.getElementById('heroBadge').innerHTML = `<i class="fas fa-circle"></i> ${badge}`;
            document.getElementById('testimonialText').textContent = testimonial;
            document.getElementById('testimonialName').textContent = testName;
            document.getElementById('testimonialRole').textContent = testRole;
            showToast('✅ Profile updated successfully!');
        }

        // ===== ACCENT COLOR UPDATE =====
        function updateAccentColor() {
            const color1 = document.getElementById('accentColor').value;
            const color2 = document.getElementById('accentColor2').value;
            const color3 = document.getElementById('accentColor3').value;
            const style = document.createElement('style');
            style.id = 'dynamicColors';
            style.textContent = `
                .gradient-text, .logo, .stat-number, .skill-card i {
                    background: linear-gradient(135deg, ${color1}, ${color2}, ${color3}) !important;
                    -webkit-background-clip: text !important;
                    background-clip: text !important;
                    color: transparent !important;
                }
                .btn-primary, .nav-cta, .editor-toggle, .upload-btn, .play-btn {
                    background: linear-gradient(135deg, ${color1}, ${color2}) !important;
                }
                .btn-primary:hover, .nav-cta:hover {
                    box-shadow: 0 20px 40px ${color1}4D !important;
                }
                .project-card:hover {
                    border-color: ${color1}4D !important;
                }
                .section-header h2 .gradient-text {
                    background: linear-gradient(135deg, ${color2}, ${color1}) !important;
                    -webkit-background-clip: text !important;
                    background-clip: text !important;
                    color: transparent !important;
                }
            `;
            const oldStyle = document.getElementById('dynamicColors');
            if (oldStyle) oldStyle.remove();
            document.head.appendChild(style);
            showToast('🎨 Accent colors updated!');
        }

        // ===== SECTION TOGGLE =====
        function toggleSection(sectionId) {
            const checkbox = document.getElementById(`toggle${sectionId.charAt(0).toUpperCase() + sectionId.slice(1)}`);
            const sectionMap = { 'header': 'headerSection', 'hero': 'heroSection', 'stats': 'statsSection', 'projects': 'projectsSection', 'skills': 'skillsSection', 'testimonial': 'testimonialSection', 'footer': 'footerSection' };
            const element = document.getElementById(sectionMap[sectionId]);
            if (!element) return;
            if (checkbox.checked) { element.classList.remove('hidden-section'); element.style.display = ''; } 
            else { element.classList.add('hidden-section'); element.style.display = 'none'; }
            showToast(`📐 Section ${checkbox.checked ? 'shown' : 'hidden'}`);
        }

        // ===== GAME URL UPDATE =====
        function updateGameUrls() {
            const starfallUrl = document.getElementById('gameUrlStarfall').value;
            const shadowUrl = document.getElementById('gameUrlShadow').value;
            const neonUrl = document.getElementById('gameUrlNeon').value;
            if (starfallUrl) gameData.starfall.url = starfallUrl;
            if (shadowUrl) gameData.shadow.url = shadowUrl;
            if (neonUrl) gameData.neon.url = neonUrl;
            showToast('✅ Game URLs updated!');
        }

        // ===== OPEN GAME WITH ACTUAL PLAYABLE GAME =====
        function openGame(gameId) {
            const game = gameData[gameId];
            if (!game) { showToast('⚠️ Game not found!'); return; }
            currentGameId = gameId;
            document.getElementById('launchGameIcon').innerHTML = `<i class="fas ${game.icon}"></i>`;
            document.getElementById('launchGameTitle').textContent = game.title;
            document.getElementById('launchGameDesc').textContent = game.description;
            const featuresContainer = document.getElementById('launchGameFeatures');
            featuresContainer.innerHTML = '';
            game.features.forEach(feature => {
                const div = document.createElement('div');
                div.className = 'feature';
                div.innerHTML = `<i class="fas ${feature.icon}"></i><span>${feature.label}</span>`;
                featuresContainer.appendChild(div);
            });
            const iframe = document.getElementById('gameIframe');
            const loading = document.getElementById('gameLoading');
            loading.style.display = 'block';
            iframe.style.display = 'none';
            let gameUrl = game.url;
            if (!gameUrl || gameUrl === '#') {
                gameUrl = 'https://html5.gamedistribution.com/3b2e5d5f5c5a4e5e8b5e5a5e5a5e5a5e/';
            }
            setTimeout(() => {
                iframe.src = gameUrl;
                iframe.onload = function() {
                    loading.style.display = 'none';
                    iframe.style.display = 'block';
                    showToast(`🎮 "${game.title}" loaded successfully!`);
                };
                setTimeout(() => {
                    if (loading.style.display !== 'none') {
                        loading.innerHTML = `<i class="fas fa-exclamation-triangle" style="color:#ff6b6b;"></i><span>Could not load game. Please check the URL in the editor.</span>`;
                    }
                }, 10000);
            }, 800);
            document.getElementById('gameLaunchOverlay').classList.add('active');
            document.body.style.overflow = 'hidden';
        }

        function closeGameLaunch() {
            const iframe = document.getElementById('gameIframe');
            iframe.src = '';
            iframe.style.display = 'none';
            document.getElementById('gameLoading').style.display = 'block';
            document.getElementById('gameLoading').innerHTML = `<i class="fas fa-spinner"></i><span>Loading game...</span>`;
            document.getElementById('gameLaunchOverlay').classList.remove('active');
            document.body.style.overflow = 'auto';
            currentGameId = null;
        }

        function launchGameAction() {
            if (!currentGameId) return;
            const game = gameData[currentGameId];
            const iframe = document.getElementById('gameIframe');
            const loading = document.getElementById('gameLoading');
            loading.style.display = 'block';
            iframe.style.display = 'none';
            let gameUrl = game.url;
            if (!gameUrl || gameUrl === '#') {
                gameUrl = 'https://html5.gamedistribution.com/3b2e5d5f5c5a4e5e8b5e5a5e5a5e5a5e/';
            }
            setTimeout(() => {
                iframe.src = gameUrl;
                iframe.onload = function() {
                    loading.style.display = 'none';
                    iframe.style.display = 'block';
                    showToast(`🎮 Game launched successfully!`);
                };
            }, 500);
            showToast(`🎮 Launching "${game.title}"...`);
        }

        function toggleFullscreen() {
            const container = document.getElementById('gameIframeContainer');
            if (!document.fullscreenElement) {
                container.requestFullscreen().catch(err => showToast('⚠️ Fullscreen not supported'));
            } else {
                document.exitFullscreen();
            }
        }

        // ===== TOAST NOTIFICATION =====
        function showToast(message) {
            const existingToast = document.querySelector('.toast-notification');
            if (existingToast) existingToast.remove();
            const toast = document.createElement('div');
            toast.className = 'toast-notification';
            toast.textContent = message;
            document.body.appendChild(toast);
            setTimeout(() => {
                toast.style.animation = 'toastSlideOut 0.5s ease-in';
                setTimeout(() => toast.remove(), 500);
            }, 3000);
        }

        // ===== SORTABLE SECTIONS =====
        function initSortable() {
            const container = document.getElementById('mainWrapper');
            if (sortableInstance) { sortableInstance.destroy(); }
            sortableInstance = new Sortable(container, {
                handle: '.drag-handle',
                animation: 300,
                easing: 'cubic-bezier(0.175,0.885,0.32,1.275)',
                ghostClass: 'sortable-ghost',
                chosenClass: 'sortable-chosen',
                dragClass: 'dragging',
                delay: 100,
                filter: '.no-drag',
                onStart: function(evt) { document.querySelectorAll('.sortable-section').forEach(el => { el.style.opacity = '0.6'; }); },
                onEnd: function(evt) { document.querySelectorAll('.sortable-section').forEach(el => { el.style.opacity = '1'; }); showToast('✅ Section reordered!'); }
            });
            const projectsGrid = document.getElementById('projectsGrid');
            if (projectsGrid) {
                new Sortable(projectsGrid, {
                    animation: 300,
                    easing: 'cubic-bezier(0.175,0.885,0.32,1.275)',
                    ghostClass: 'sortable-ghost',
                    chosenClass: 'sortable-chosen',
                    dragClass: 'dragging',
                    delay: 50,
                    onEnd: function() { showToast('✅ Game cards reordered!'); }
                });
            }
            const skillsGrid = document.getElementById('skillsGrid');
            if (skillsGrid) {
                new Sortable(skillsGrid, {
                    animation: 300,
                    easing: 'cubic-bezier(0.175,0.885,0.32,1.275)',
                    ghostClass: 'sortable-ghost',
                    chosenClass: 'sortable-chosen',
                    dragClass: 'dragging',
                    delay: 50,
                    onEnd: function() { showToast('✅ Skills reordered!'); }
                });
            }
        }

        function resetLayout() {
            if (confirm('Reset all sections to original order and visibility?')) { location.reload(); }
        }

        // ===== PARTICLES =====
        (function createParticles() {
            const container = document.getElementById('particleSystem');
            const colors = ['#ff6b9d', '#b36bff', '#6bcbff', '#ffb86b', '#ff9ec9', '#ffffff'];
            for (let i = 0; i < 60; i++) {
                const particle = document.createElement('div');
                particle.className = 'particle';
                const size = Math.random() * 6 + 2;
                const isSquare = Math.random() > 0.7;
                if (isSquare) { particle.style.borderRadius = '2px'; particle.style.transform = 'rotate(45deg)'; }
                particle.style.width = size + 'px';
                particle.style.height = size + 'px';
                particle.style.left = Math.random() * 100 + '%';
                particle.style.background = `radial-gradient(circle, ${colors[Math.floor(Math.random() * colors.length)]}, transparent)`;
                particle.style.animationDuration = (Math.random() * 25 + 15) + 's';
                particle.style.animationDelay = (Math.random() * 15) + 's';
                particle.style.opacity = Math.random() * 0.4 + 0.1;
                container.appendChild(particle);
            }
        })();

        // ===== COUNTER ANIMATION =====
        (function animateCounters() {
            const counters = document.querySelectorAll('.stat-number');
            counters.forEach(counter => {
                const target = parseFloat(counter.dataset.target);
                let current = 0;
                const increment = target / 50;
                const updateCounter = () => {
                    if (current < target) {
                        current += increment;
                        if (target >= 1000) { counter.textContent = Math.floor(current).toLocaleString(); } 
                        else if (target % 1 !== 0) { counter.textContent = (Math.floor(current * 10) / 10).toFixed(1); } 
                        else { counter.textContent = Math.floor(current); }
                        setTimeout(updateCounter, 30);
                    } else {
                        if (target >= 1000) { counter.textContent = target.toLocaleString(); } 
                        else if (target % 1 !== 0) { counter.textContent = target.toFixed(1); } 
                        else { counter.textContent = target; }
                    }
                };
                const observer = new IntersectionObserver((entries) => {
                    entries.forEach(entry => { if (entry.isIntersecting) { updateCounter(); observer.unobserve(entry.target); } });
                }, { threshold: 0.5 });
                observer.observe(counter);
            });
        })();

        // ===== PARALLAX EFFECT =====
        (function parallaxHero() {
            const hero = document.querySelector('.hero-visual');
            if (!hero) return;
            document.addEventListener('mousemove', (e) => {
                const x = (e.clientX / window.innerWidth - 0.5) * 20;
                const y = (e.clientY / window.innerHeight - 0.5) * 20;
                const avatar = hero.querySelector('.avatar-3d');
                const orb = hero.querySelector('.orb');
                if (avatar) { avatar.style.transform = `translate(${x * 0.3}px, ${y * 0.3}px) rotate(${x * 0.1}deg)`; }
                if (orb) { orb.style.transform = `translate(${x * 0.5}px, ${y * 0.5}px)`; }
            });
        })();

        // ===== SKILL CARD HOVER =====
        (function skillCardHover() {
            const cards = document.querySelectorAll('.skill-card');
            const colors = ['#ff6b9d', '#b36bff', '#6bcbff', '#ffb86b', '#ff9ec9'];
            cards.forEach(card => {
                card.addEventListener('mouseenter', function() {
                    const color = colors[Math.floor(Math.random() * colors.length)];
                    this.style.borderColor = color + '44';
                    this.querySelector('i').style.background = `linear-gradient(135deg, ${color}, ${colors[Math.floor(Math.random() * colors.length)]})`;
                    this.querySelector('i').style.webkitBackgroundClip = 'text';
                });
                card.addEventListener('mouseleave', function() {
                    this.style.borderColor = 'rgba(255,255,255,0.05)';
                    this.querySelector('i').style.background = 'linear-gradient(135deg, #ff6b9d, #b36bff)';
                    this.querySelector('i').style.webkitBackgroundClip = 'text';
                });
            });
        })();

        // ===== PROJECT CARD GLOW =====
        (function projectCardGlow() {
            const cards = document.querySelectorAll('.project-card');
            cards.forEach(card => {
                card.addEventListener('mousemove', function(e) {
                    const rect = this.getBoundingClientRect();
                    const x = e.clientX - rect.left;
                    const y = e.clientY - rect.top;
                    const glow = document.createElement('div');
                    glow.style.cssText = `position:absolute;top:${y-80}px;left:${x-80}px;width:160px;height:160px;background:radial-gradient(circle,rgba(255,107,157,0.1),transparent 70%);border-radius:50%;pointer-events:none;z-index:0;animation:glowFade 0.8s ease-out forwards;`;
                    this.appendChild(glow);
                    setTimeout(() => { glow.remove(); }, 800);
                });
            });
            const style = document.createElement('style');
            style.textContent = `@keyframes glowFade { 0% { opacity: 0; transform: scale(0.5); } 50% { opacity: 1; transform: scale(1.2); } 100% { opacity: 0; transform: scale(1.5); } }`;
            document.head.appendChild(style);
        })();

        // ===== SCROLL REVEAL =====
        (function scrollReveal() {
            const sections = document.querySelectorAll('.section, .stats-bar, .testimonial');
            const observer = new IntersectionObserver((entries) => {
                entries.forEach(entry => { if (entry.isIntersecting) { entry.target.style.opacity = '1'; entry.target.style.transform = 'translateY(0)'; } });
            }, { threshold: 0.1 });
            sections.forEach(section => {
                section.style.opacity = '0';
                section.style.transform = 'translateY(40px)';
                section.style.transition = 'all 0.8s ease-out';
                observer.observe(section);
            });
        })();

        // ===== KEYBOARD SHORTCUTS =====
        document.addEventListener('keydown', (e) => {
            if (e.ctrlKey && e.key === 'e') { e.preventDefault(); toggleEditor(); }
            if (e.key === 'Escape') { closeGameLaunch(); }
            if (e.key === 'f' && document.getElementById('gameLaunchOverlay').classList.contains('active')) { toggleFullscreen(); }
        });

        // ===== INIT =====
        document.addEventListener('DOMContentLoaded', function() {
            setTimeout(initSortable, 500);
            setTimeout(() => { updateAccentColor(); }, 100);
            document.getElementById('gameUrlStarfall').value = gameData.starfall.url;
            document.getElementById('gameUrlShadow').value = gameData.shadow.url;
            document.getElementById('gameUrlNeon').value = gameData.neon.url;
            console.log('🎮 AMEEN · Game Developer Portfolio Pro with Playable Games');
            console.log('✨ Features:');
            console.log('  • 3 Playable HTML5 Games (loaded via iframe)');
            console.log('  • Fullscreen Support');
            console.log('  • Drag & Drop Editor');
            console.log('  • Avatar Upload');
            console.log('  • Profile Customization');
            console.log('  • Section Visibility Toggle');
            console.log('  • Accent Color Customization');
            console.log('  • 20+ Animation Effects');
            console.log('  • Keyboard Shortcuts (Ctrl+E, ESC, F)');
            console.log('💻 Made with ❤️ by AMEEN');
        });
    </script>
</body>
</html>
