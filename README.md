<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FerreSubasta | Plataforma Industrial B2B</title>
    
    <!-- Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=DM+Sans:opsz,wght@9..40,400;500;600;700&family=Syne:wght@500;600;700;800&display=swap" rel="stylesheet">
    
    <!-- Font Awesome 6 -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    
    <!-- Socket.IO Client (Simulation requirement) -->
    <script src="https://cdn.socket.io/4.7.2/socket.io.min.js"></script>

    <style>
        /* ==================== VARIABLES ==================== */
        :root {
            --color-bg: #0a0f1e;
            --color-surface: #111827;
            --color-surface-hover: #1f2937;
            --color-border: #1e2d40;
            --color-accent: #f97316;
            --color-accent-rgb: 249, 115, 22;
            --color-accent-2: #3b82f6;
            --color-success: #22c55e;
            --color-success-rgb: 34, 197, 94;
            --color-danger: #ef4444;
            --color-danger-rgb: 239, 68, 68;
            --color-warning: #eab308;
            --color-text: #f1f5f9;
            --color-muted: #64748b;
            
            --font-heading: 'Syne', sans-serif;
            --font-body: 'DM Sans', sans-serif;
            
            --sidebar-width: 260px;
            --sidebar-collapsed-width: 80px;
            --topbar-height: 70px;
            
            --radius: 12px;
            --transition: all 0.25s ease;
        }

        /* ==================== RESET & BASE ==================== */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: var(--font-body);
            background-color: var(--color-bg);
            color: var(--color-text);
            overflow-x: hidden;
            line-height: 1.5;
        }

        h1, h2, h3, h4, h5, h6 {
            font-family: var(--font-heading);
            font-weight: 700;
        }

        a {
            text-decoration: none;
            color: inherit;
        }

        ul {
            list-style: none;
        }

        button {
            font-family: inherit;
            cursor: pointer;
            border: none;
            background: none;
            outline: none;
            transition: var(--transition);
        }

        input, textarea, select {
            font-family: inherit;
            background: var(--color-bg);
            border: 1px solid var(--color-border);
            color: var(--color-text);
            padding: 0.75rem 1rem;
            border-radius: 8px;
            width: 100%;
            outline: none;
            transition: var(--transition);
        }

        input:focus, textarea:focus {
            border-color: var(--color-accent);
            box-shadow: 0 0 0 3px rgba(var(--color-accent-rgb), 0.2);
        }

        /* ==================== UTILITIES ==================== */
        .text-accent { color: var(--color-accent); }
        .text-success { color: var(--color-success); }
        .text-danger { color: var(--color-danger); }
        .text-warning { color: var(--color-warning); }
        .text-muted { color: var(--color-muted); }
        .text-sm { font-size: 0.875rem; }
        .text-xs { font-size: 0.75rem; }
        
        .flex { display: flex; }
        .items-center { align-items: center; }
        .justify-between { justify-content: space-between; }
        .justify-center { justify-content: center; }
        .gap-2 { gap: 0.5rem; }
        .gap-4 { gap: 1rem; }
        .w-full { width: 100%; }
        
        .card {
            background-color: var(--color-surface);
            border: 1px solid var(--color-border);
            border-radius: var(--radius);
            padding: 1.5rem;
            transition: var(--transition);
        }

        .card:hover {
            box-shadow: 0 0 24px rgba(var(--color-accent-rgb), 0.04);
        }

        .card-active {
            border-left: 3px solid var(--color-accent);
            box-shadow: 0 0 24px rgba(var(--color-accent-rgb), 0.08);
        }

        .btn {
            display: inline-flex;
            align-items: center;
            justify-content: center;
            gap: 0.5rem;
            padding: 0.75rem 1.5rem;
            border-radius: 8px;
            font-weight: 600;
            font-size: 0.9rem;
        }

        .btn-primary {
            background-color: var(--color-accent);
            color: #fff;
        }
        
        .btn-primary:hover {
            background-color: #e06510;
            transform: translateY(-2px);
            box-shadow: 0 4px 12px rgba(var(--color-accent-rgb), 0.3);
        }

        .btn-outline {
            border: 1px solid var(--color-border);
            color: var(--color-text);
        }

        .btn-outline:hover {
            border-color: var(--color-accent);
            color: var(--color-accent);
        }

        .badge {
            display: inline-flex;
            align-items: center;
            padding: 0.25rem 0.75rem;
            border-radius: 999px;
            font-size: 0.75rem;
            font-weight: 600;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        .badge-success { background: rgba(var(--color-success-rgb), 0.1); color: var(--color-success); border: 1px solid rgba(var(--color-success-rgb), 0.2); }
        .badge-danger { background: rgba(var(--color-danger-rgb), 0.1); color: var(--color-danger); border: 1px solid rgba(var(--color-danger-rgb), 0.2); }
        .badge-warning { background: rgba(234, 179, 8, 0.1); color: var(--color-warning); border: 1px solid rgba(234, 179, 8, 0.2); }
        .badge-accent { background: rgba(var(--color-accent-rgb), 0.1); color: var(--color-accent); border: 1px solid rgba(var(--color-accent-rgb), 0.2); }

        .table-container {
            overflow-x: auto;
            width: 100%;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            text-align: left;
        }

        th {
            background-color: rgba(0,0,0,0.2);
            padding: 1rem;
            font-size: 0.8rem;
            color: var(--color-muted);
            text-transform: uppercase;
            letter-spacing: 0.5px;
            border-bottom: 1px solid var(--color-border);
        }

        td {
            padding: 1rem;
            border-bottom: 1px solid var(--color-border);
            font-size: 0.9rem;
            white-space: nowrap;
        }

        tbody tr {
            transition: var(--transition);
        }

        tbody tr:hover {
            background-color: var(--color-surface-hover);
        }

        /* ==================== ANIMATIONS ==================== */
        @keyframes fadeInUp {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }

        @keyframes fadeInDown {
            from { opacity: 0; transform: translateY(-20px); }
            to { opacity: 1; transform: translateY(0); }
        }

        @keyframes pulse {
            0% { transform: scale(1); box-shadow: 0 0 0 0 rgba(var(--color-success-rgb), 0.7); }
            70% { transform: scale(1); box-shadow: 0 0 0 6px rgba(var(--color-success-rgb), 0); }
            100% { transform: scale(1); box-shadow: 0 0 0 0 rgba(var(--color-success-rgb), 0); }
        }

        @keyframes pulseAccent {
            0% { transform: scale(1); box-shadow: 0 0 0 0 rgba(var(--color-accent-rgb), 0.7); }
            70% { transform: scale(1); box-shadow: 0 0 0 6px rgba(var(--color-accent-rgb), 0); }
            100% { transform: scale(1); box-shadow: 0 0 0 0 rgba(var(--color-accent-rgb), 0); }
        }

        .animate-fade-in-up { animation: fadeInUp 0.6s cubic-bezier(0.16, 1, 0.3, 1) forwards; opacity: 0; }
        .stagger-1 { animation-delay: 0.1s; }
        .stagger-2 { animation-delay: 0.2s; }
        .stagger-3 { animation-delay: 0.3s; }
        .stagger-4 { animation-delay: 0.4s; }
        
        .anim-pulse-success { animation: pulse 2s infinite; }
        .anim-pulse-accent { animation: pulseAccent 2s infinite; }

        /* ==================== LAYOUT ARCHITECTURE ==================== */
        #app {
            display: flex;
            flex-direction: column;
            min-height: 100vh;
        }

        .view-section {
            display: none;
            opacity: 0;
            transition: opacity 0.3s ease;
        }

        .view-section.active {
            display: block;
            opacity: 1;
        }

        /* --- SECTION 1: LANDING --- */
        #landing-nav {
            position: fixed;
            top: 0; left: 0; right: 0;
            height: var(--topbar-height);
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 0 2rem;
            background: rgba(10, 15, 30, 0.9);
            backdrop-filter: blur(10px);
            border-bottom: 1px solid var(--color-border);
            z-index: 100;
        }

        .logo {
            font-family: var(--font-heading);
            font-size: 1.5rem;
            font-weight: 800;
            color: var(--color-text);
            display: flex;
            align-items: center;
            gap: 0.5rem;
        }

        .logo i { color: var(--color-accent); }

        .landing-hero {
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            text-align: center;
            padding: 6rem 2rem 4rem;
            background: radial-gradient(circle at 50% 30%, #1a1f35 0%, var(--color-bg) 60%);
            position: relative;
            overflow: hidden;
        }

        .hero-bg-grid {
            position: absolute;
            inset: 0;
            background-image: radial-gradient(var(--color-border) 1px, transparent 1px);
            background-size: 40px 40px;
            opacity: 0.3;
            z-index: 0;
        }

        .hero-line {
            position: absolute;
            width: 200%;
            height: 2px;
            background: linear-gradient(90deg, transparent, var(--color-accent), transparent);
            transform: rotate(-15deg);
            top: 40%;
            left: -50%;
            opacity: 0.2;
            z-index: 0;
        }

        .hero-content {
            position: relative;
            z-index: 1;
            max-width: 900px;
        }

        .hero-title {
            font-size: clamp(2.5rem, 6vw, 4.5rem);
            line-height: 1.1;
            margin-bottom: 1.5rem;
            letter-spacing: -1px;
        }

        .hero-title span {
            background: linear-gradient(135deg, var(--color-text), var(--color-muted));
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }
        
        .hero-title .highlight {
            background: linear-gradient(135deg, #f97316, #fcd34d);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .hero-subtitle {
            font-size: 1.25rem;
            color: var(--color-muted);
            margin-bottom: 3rem;
            max-width: 600px;
            margin-inline: auto;
        }

        .hero-actions {
            display: flex;
            gap: 1rem;
            justify-content: center;
            margin-bottom: 4rem;
        }

        .floating-badge {
            position: absolute;
            top: 20%;
            right: 15%;
            background: var(--color-surface);
            border: 1px solid var(--color-border);
            padding: 0.75rem 1.25rem;
            border-radius: 999px;
            display: flex;
            align-items: center;
            gap: 0.5rem;
            box-shadow: 0 10px 25px rgba(0,0,0,0.5);
            animation: float 4s ease-in-out infinite;
            z-index: 2;
        }

        @keyframes float {
            0%, 100% { transform: translateY(0); }
            50% { transform: translateY(-15px); }
        }

        .landing-steps, .landing-stats {
            padding: 5rem 2rem;
            max-width: 1200px;
            margin: 0 auto;
            position: relative;
            z-index: 1;
        }

        .section-title {
            text-align: center;
            font-size: 2.5rem;
            margin-bottom: 3rem;
        }

        .steps-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 2rem;
        }

        .step-card {
            text-align: center;
            padding: 2rem;
        }
        
        .step-icon {
            width: 80px;
            height: 80px;
            background: rgba(var(--color-accent-rgb), 0.1);
            color: var(--color-accent);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 2rem;
            margin: 0 auto 1.5rem;
            border: 1px solid rgba(var(--color-accent-rgb), 0.2);
        }

        .stats-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 2rem;
            text-align: center;
            background: var(--color-surface);
            padding: 3rem;
            border-radius: var(--radius);
            border: 1px solid var(--color-border);
        }

        .stat-number {
            font-size: 3rem;
            font-weight: 800;
            font-family: var(--font-heading);
            color: var(--color-accent);
            margin-bottom: 0.5rem;
        }

        footer {
            background: var(--color-surface);
            border-top: 1px solid var(--color-border);
            padding: 3rem 2rem;
            text-align: center;
            color: var(--color-muted);
            margin-top: auto;
        }

        /* --- DASHBOARD LAYOUT (SECTIONS 2-5) --- */
        #dashboard-layout {
            display: flex;
            height: 100vh;
            overflow: hidden;
            background: var(--color-bg);
        }

        /* Sidebar */
        #sidebar {
            width: var(--sidebar-width);
            background: var(--color-surface);
            border-right: 1px solid var(--color-border);
            display: flex;
            flex-direction: column;
            transition: var(--transition);
            z-index: 50;
        }

        #sidebar.collapsed {
            width: var(--sidebar-collapsed-width);
        }

        #sidebar.collapsed .sidebar-text,
        #sidebar.collapsed .sidebar-logo-text {
            display: none;
        }
        
        #sidebar.collapsed .sidebar-link {
            justify-content: center;
            padding: 1rem 0;
        }

        .sidebar-header {
            height: var(--topbar-height);
            display: flex;
            align-items: center;
            padding: 0 1.5rem;
            border-bottom: 1px solid var(--color-border);
            justify-content: space-between;
        }

        .sidebar-nav {
            padding: 1.5rem 0;
            flex: 1;
            display: flex;
            flex-direction: column;
            gap: 0.5rem;
        }

        .sidebar-link {
            display: flex;
            align-items: center;
            gap: 1rem;
            padding: 0.75rem 1.5rem;
            color: var(--color-muted);
            font-weight: 500;
            transition: var(--transition);
            border-left: 3px solid transparent;
            cursor: pointer;
        }

        .sidebar-link:hover, .sidebar-link.active {
            background: rgba(var(--color-accent-rgb), 0.05);
            color: var(--color-text);
            border-left-color: var(--color-accent);
        }

        .sidebar-link i {
            width: 20px;
            text-align: center;
            font-size: 1.2rem;
        }

        /* Main Content Area */
        #main-content {
            flex: 1;
            display: flex;
            flex-direction: column;
            height: 100vh;
            overflow: hidden;
        }

        /* Topbar */
        #topbar {
            height: var(--topbar-height);
            background: var(--color-surface);
            border-bottom: 1px solid var(--color-border);
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 0 2rem;
            z-index: 40;
        }

        .topbar-actions {
            display: flex;
            align-items: center;
            gap: 1.5rem;
        }

        .notification-wrapper {
            position: relative;
        }

        .bell-icon {
            font-size: 1.25rem;
            color: var(--color-muted);
            position: relative;
        }

        .bell-icon:hover { color: var(--color-text); }

        .bell-badge {
            position: absolute;
            top: -5px; right: -5px;
            background: var(--color-danger);
            color: white;
            font-size: 0.6rem;
            font-weight: 700;
            width: 16px; height: 16px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            border: 2px solid var(--color-surface);
        }

        .notification-dropdown {
            position: absolute;
            top: 150%; right: 0;
            width: 320px;
            background: var(--color-surface);
            border: 1px solid var(--color-border);
            border-radius: var(--radius);
            box-shadow: 0 10px 30px rgba(0,0,0,0.5);
            opacity: 0;
            visibility: hidden;
            transform: translateY(10px);
            transition: var(--transition);
        }

        .notification-dropdown.show {
            opacity: 1;
            visibility: visible;
            transform: translateY(0);
        }

        .noti-header {
            padding: 1rem;
            border-bottom: 1px solid var(--color-border);
            font-weight: 600;
        }

        .noti-item {
            padding: 1rem;
            border-bottom: 1px solid var(--color-border);
            display: flex;
            gap: 1rem;
            transition: var(--transition);
        }

        .noti-item:hover {
            background: var(--color-bg);
        }

        .noti-item:last-child { border-bottom: none; }

        .noti-icon {
            width: 36px; height: 36px;
            border-radius: 50%;
            display: flex; align-items: center; justify-content: center;
            flex-shrink: 0;
        }

        .user-profile {
            display: flex;
            align-items: center;
            gap: 0.75rem;
            cursor: pointer;
        }

        .avatar {
            width: 40px; height: 40px;
            border-radius: 50%;
            background: var(--color-accent);
            display: flex; align-items: center; justify-content: center;
            font-weight: 700; color: white;
        }

        /* Content Scroll Area */
        .content-scroll {
            flex: 1;
            overflow-y: auto;
            padding: 2rem;
            background: var(--color-bg);
        }

        /* --- DASHBOARD SPECIFICS --- */
        .dashboard-header {
            display: flex;
            justify-content: space-between;
            align-items: flex-end;
            margin-bottom: 2rem;
        }

        .kpi-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
            gap: 1.5rem;
            margin-bottom: 2rem;
        }

        .kpi-card {
            display: flex;
            align-items: center;
            gap: 1.5rem;
            padding: 1.5rem;
        }

        .kpi-icon {
            width: 50px; height: 50px;
            border-radius: 12px;
            display: flex; align-items: center; justify-content: center;
            font-size: 1.5rem;
        }

        .kpi-info h4 { color: var(--color-muted); font-size: 0.9rem; font-weight: 500; font-family: var(--font-body); }
        .kpi-info .value { font-size: 1.8rem; font-weight: 700; color: var(--color-text); margin-top: 0.25rem; }

        .dashboard-main-grid {
            display: grid;
            grid-template-columns: 2fr 1fr;
            gap: 1.5rem;
        }

        .chart-container {
            height: 300px;
            width: 100%;
        }

        /* --- LIVE AUCTION SPECIFICS --- */
        .auction-header-card {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 2rem;
            background: linear-gradient(135deg, var(--color-surface) 0%, #151f32 100%);
            border-left: 4px solid var(--color-success);
        }

        .timer-display {
            font-family: 'Courier New', Courier, monospace;
            font-size: 2.5rem;
            font-weight: 800;
            color: var(--color-text);
            background: rgba(0,0,0,0.3);
            padding: 0.5rem 1rem;
            border-radius: 8px;
            border: 1px solid var(--color-border);
            letter-spacing: 2px;
        }

        .timer-danger { color: var(--color-danger); border-color: var(--color-danger); animation: pulseAccent 1s infinite; }

        .auction-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 1.5rem;
        }

        .bids-panel {
            position: relative;
        }

        .live-indicator {
            display: inline-flex;
            align-items: center;
            gap: 0.5rem;
            font-size: 0.8rem;
            color: var(--color-success);
        }

        .dot {
            width: 8px; height: 8px;
            background: var(--color-success);
            border-radius: 50%;
            display: inline-block;
        }

        .best-offer-row {
            background: rgba(var(--color-accent-rgb), 0.05) !important;
            border-left: 3px solid var(--color-accent);
        }

        /* --- CATALOG SPECIFICS --- */
        .catalog-filters {
            display: flex;
            gap: 1rem;
            margin-bottom: 2rem;
        }

        .search-bar {
            flex: 1;
            position: relative;
        }

        .search-bar i {
            position: absolute;
            left: 1rem; top: 50%; transform: translateY(-50%);
            color: var(--color-muted);
        }

        .search-bar input {
            padding-left: 2.5rem;
        }

        .catalog-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
            gap: 1.5rem;
        }

        .product-card {
            display: flex;
            flex-direction: column;
            overflow: hidden;
            padding: 0;
        }

        .product-img-placeholder {
            height: 150px;
            background: var(--color-bg);
            display: flex; align-items: center; justify-content: center;
            font-size: 3rem; color: var(--color-border);
            border-bottom: 1px solid var(--color-border);
        }

        .product-info { padding: 1.5rem; flex: 1; display: flex; flex-direction: column; }
        .product-info h4 { margin-bottom: 0.5rem; font-size: 1.1rem; }
        .product-btn { margin-top: auto; width: 100%; opacity: 0.8; }
        .product-card:hover .product-btn { opacity: 1; background: var(--color-accent); color: white; border-color: var(--color-accent); }

        /* --- MODALS & TOASTS --- */
        .modal-overlay {
            position: fixed;
            inset: 0;
            background: rgba(0,0,0,0.7);
            backdrop-filter: blur(5px);
            display: flex; align-items: center; justify-content: center;
            z-index: 1000;
            opacity: 0; visibility: hidden;
            transition: var(--transition);
        }

        .modal-overlay.show { opacity: 1; visibility: visible; }

        .modal-content {
            background: var(--color-surface);
            border: 1px solid var(--color-border);
            border-radius: var(--radius);
            width: 100%; max-width: 450px;
            padding: 2rem;
            transform: scale(0.9);
            transition: var(--transition);
        }

        .modal-overlay.show .modal-content { transform: scale(1); }

        .form-group { margin-bottom: 1.25rem; }
        .form-group label { display: block; margin-bottom: 0.5rem; font-size: 0.875rem; color: var(--color-muted); }

        #toast-container {
            position: fixed;
            bottom: 2rem; right: 2rem;
            display: flex; flex-direction: column; gap: 1rem;
            z-index: 9999;
        }

        .toast {
            background: var(--color-surface);
            border: 1px solid var(--color-border);
            border-left: 4px solid var(--color-accent);
            padding: 1rem 1.5rem;
            border-radius: 8px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.5);
            display: flex; align-items: center; gap: 1rem;
            animation: fadeInDown 0.3s ease forwards;
        }

        /* --- WHATSAPP FLOATING --- */
        .wsp-floating {
            position: fixed;
            bottom: 2rem; right: 2rem;
            width: 60px; height: 60px;
            background: #25D366;
            border-radius: 50%;
            display: flex; align-items: center; justify-content: center;
            font-size: 2rem; color: white;
            box-shadow: 0 4px 15px rgba(37, 211, 102, 0.4);
            z-index: 900;
            transition: var(--transition);
        }

        .wsp-floating:hover {
            transform: scale(1.1);
            box-shadow: 0 6px 20px rgba(37, 211, 102, 0.6);
        }

        .wsp-badge {
            position: absolute;
            top: -2px; right: -2px;
            background: var(--color-danger);
            font-size: 0.7rem; font-weight: bold;
            width: 20px; height: 20px;
            border-radius: 50%;
            display: flex; align-items: center; justify-content: center;
            border: 2px solid var(--color-bg);
        }

        /* --- AI FEATURES --- */
        .ai-shimmer {
            background: linear-gradient(90deg, var(--color-surface) 0%, #1e293b 50%, var(--color-surface) 100%);
            background-size: 200% 100%;
            animation: shimmer 2s infinite;
        }

        @keyframes shimmer {
            0% { background-position: -200% 0; }
            100% { background-position: 200% 0; }
        }

        .ai-result-box {
            background: rgba(59, 130, 246, 0.05);
            border: 1px solid rgba(59, 130, 246, 0.3);
            border-radius: var(--radius);
            padding: 1rem;
            margin-top: 1rem;
            border-left: 3px solid var(--color-accent-2);
        }

        /* Responsive */
        @media (max-width: 1024px) {
            .dashboard-main-grid, .auction-grid { grid-template-columns: 1fr; }
        }

        @media (max-width: 768px) {
            #sidebar { position: fixed; transform: translateX(-100%); height: 100vh; }
            #sidebar.open { transform: translateX(0); }
            .hero-title { font-size: 2.5rem; }
            .auction-header-card { flex-direction: column; align-items: flex-start; gap: 1rem; }
            .timer-display { width: 100%; text-align: center; }
        }
        
        .hamburger { display: none; font-size: 1.5rem; color: var(--color-text); }
        @media (max-width: 768px) { .hamburger { display: block; } #sidebar-toggle { display: none; } }
    </style>
</head>
<body>

<div id="app">

    <!-- ==================== SECTION 1: LANDING ==================== -->
    <div id="view-landing" class="view-section active">
        <nav id="landing-nav">
            <div class="logo">
                <i class="fa-solid fa-hammer text-accent"></i> FerreSubasta
            </div>
            <div class="flex gap-4">
                <button class="btn btn-outline" onclick="login()">Iniciar sesión</button>
                <button class="btn btn-primary" onclick="login()">Registrarse</button>
            </div>
        </nav>

        <section class="landing-hero">
            <div class="hero-bg-grid"></div>
            <div class="hero-line"></div>
            
            <div class="floating-badge anim-pulse-success">
                <span class="dot anim-pulse-success"></span> 5 subastas activas ahora
            </div>

            <div class="hero-content">
                <h1 class="hero-title animate-fade-in-up stagger-1">
                    <span>Compra herramientas al mejor precio.</span><br>
                    <span class="highlight">Los proveedores compiten por ti.</span>
                </h1>
                <p class="hero-subtitle animate-fade-in-up stagger-2">
                    Publica tus requerimientos y recibe ofertas en tiempo real. El modelo de subasta inversa B2B que optimiza los costos de tu obra o ferretería.
                </p>
                <div class="hero-actions animate-fade-in-up stagger-3">
                    <button class="btn btn-primary btn-lg" onclick="login()"><i class="fa-solid fa-file-invoice"></i> Publicar solicitud</button>
                    <button class="btn btn-outline btn-lg">Soy proveedor &rarr;</button>
                </div>
            </div>
        </section>

        <section class="landing-steps">
            <h2 class="section-title">¿Cómo funciona?</h2>
            <div class="steps-grid">
                <div class="card step-card">
                    <div class="step-icon"><i class="fa-solid fa-clipboard-list"></i></div>
                    <h3>1. Publicas lo que necesitas</h3>
                    <p class="text-muted mt-2">Sube tu lista de materiales, especificaciones y tiempo límite.</p>
                </div>
                <div class="card step-card">
                    <div class="step-icon"><i class="fa-solid fa-gavel"></i></div>
                    <h3>2. Proveedores compiten</h3>
                    <p class="text-muted mt-2">Recibe múltiples cotizaciones que bajan en tiempo real.</p>
                </div>
                <div class="card step-card">
                    <div class="step-icon"><i class="fa-solid fa-handshake"></i></div>
                    <h3>3. Eliges y pagas</h3>
                    <p class="text-muted mt-2">Selecciona la mejor oferta, confirma el pedido y ahorra.</p>
                </div>
            </div>
        </section>

        <section class="landing-stats">
            <div class="stats-grid">
                <div>
                    <div class="stat-number" id="stat1">0</div>
                    <div class="text-muted">Subastas Completadas</div>
                </div>
                <div>
                    <div class="stat-number" id="stat2">0</div>
                    <div class="text-muted">Proveedores Verificados</div>
                </div>
                <div>
                    <div class="stat-number">S/ <span id="stat3">0</span>M</div>
                    <div class="text-muted">Ahorro Generado</div>
                </div>
                <div>
                    <div class="stat-number"><span id="stat4">0</span>%</div>
                    <div class="text-muted">Satisfacción</div>
                </div>
            </div>
        </section>
        
        <footer>
            <div class="logo justify-center mb-4"><i class="fa-solid fa-hammer"></i> FerreSubasta</div>
            <p>&copy; 2026 FerreSubasta B2B. Plataforma de Abastecimiento Industrial.</p>
        </footer>
    </div>


    <!-- ==================== DASHBOARD LAYOUT ==================== -->
    <div id="dashboard-layout" style="display: none;">
        
        <!-- Sidebar -->
        <aside id="sidebar">
            <div class="sidebar-header">
                <div class="logo sidebar-logo-text"><i class="fa-solid fa-hammer text-accent"></i> FerreSubasta</div>
                <i class="fa-solid fa-hammer text-accent logo" style="display:none;" id="logo-icon-only"></i>
                <button id="sidebar-toggle" class="text-muted hover:text-white"><i class="fa-solid fa-chevron-left"></i></button>
            </div>
            <nav class="sidebar-nav">
                <a class="sidebar-link active" onclick="nav('dashboard')">
                    <i class="fa-solid fa-chart-line"></i> <span class="sidebar-text">Dashboard</span>
                </a>
                <a class="sidebar-link" onclick="nav('dashboard')">
                    <i class="fa-solid fa-gavel"></i> <span class="sidebar-text">Mis Subastas</span>
                </a>
                <a class="sidebar-link" onclick="nav('catalog')">
                    <i class="fa-solid fa-boxes-stacked"></i> <span class="sidebar-text">Catálogo</span>
                </a>
                <a class="sidebar-link" onclick="nav('orders')">
                    <i class="fa-solid fa-file-invoice-dollar"></i> <span class="sidebar-text">Mis Pedidos</span>
                </a>
                <a class="sidebar-link">
                    <i class="fa-solid fa-bell"></i> 
                    <span class="sidebar-text flex justify-between w-full items-center">
                        Notificaciones <span class="badge badge-danger">3</span>
                    </span>
                </a>
                <div style="margin-top: auto;">
                    <a class="sidebar-link">
                        <i class="fa-solid fa-gear"></i> <span class="sidebar-text">Configuración</span>
                    </a>
                    <a class="sidebar-link text-danger" onclick="logout()">
                        <i class="fa-solid fa-right-from-bracket"></i> <span class="sidebar-text">Salir</span>
                    </a>
                </div>
            </nav>
        </aside>

        <!-- Main Content -->
        <main id="main-content">
            <!-- Topbar -->
            <header id="topbar">
                <div class="flex items-center gap-4">
                    <button class="hamburger" onclick="toggleMobileMenu()"><i class="fa-solid fa-bars"></i></button>
                    <div class="text-muted" id="current-date">Cargando fecha...</div>
                </div>
                
                <div class="topbar-actions">
                    <div class="notification-wrapper">
                        <button class="bell-icon" onclick="toggleNotifications(event)">
                            <i class="fa-solid fa-bell"></i>
                            <span class="bell-badge">3</span>
                        </button>
                        
                        <!-- Notification Dropdown -->
                        <div class="notification-dropdown" id="noti-dropdown">
                            <div class="noti-header">Notificaciones (3)</div>
                            <div class="noti-item">
                                <div class="noti-icon" style="background: rgba(var(--color-accent-rgb),0.1); color: var(--color-accent)"><i class="fa-solid fa-bolt"></i></div>
                                <div>
                                    <p class="text-sm">Nueva puja en tu subasta <strong>#SUB-047</strong></p>
                                    <span class="text-xs text-muted">Hace 2 min</span>
                                </div>
                            </div>
                            <div class="noti-item">
                                <div class="noti-icon" style="background: rgba(var(--color-success-rgb),0.1); color: var(--color-success)"><i class="fa-solid fa-check"></i></div>
                                <div>
                                    <p class="text-sm">Tu pedido <strong>#ORD-089</strong> fue confirmado</p>
                                    <span class="text-xs text-muted">Hace 1 hora</span>
                                </div>
                            </div>
                            <div class="noti-item">
                                <div class="noti-icon" style="background: rgba(var(--color-danger-rgb),0.1); color: var(--color-danger)"><i class="fa-solid fa-lock"></i></div>
                                <div>
                                    <p class="text-sm">Subasta <strong>#SUB-039</strong> cerrada</p>
                                    <span class="text-xs text-muted">Hace 3 horas</span>
                                </div>
                            </div>
                        </div>
                    </div>

                    <div class="user-profile">
                        <div class="avatar">CM</div>
                        <div style="display: flex; flex-direction: column;">
                            <span class="text-sm" style="font-weight: 600;">Carlos Mendoza</span>
                            <span class="text-xs text-muted">Comprador Pro</span>
                        </div>
                    </div>
                </div>
            </header>

            <div class="content-scroll">
                
                <!-- ================= SECTION 2: DASHBOARD ================= -->
                <div id="view-dashboard" class="view-section active">
                    <div class="dashboard-header animate-fade-in-up stagger-1">
                        <div>
                            <h2>Hola, Carlos 👋</h2>
                            <p class="text-muted">Resumen de tu actividad en la plataforma</p>
                        </div>
                        <div class="flex gap-2">
                            <button class="btn btn-outline" style="border-color: #a855f7; color: #d8b4fe;" onclick="openAIModal()"><i class="fa-solid fa-wand-magic-sparkles"></i> ✨ Asistente IA</button>
                            <button class="btn btn-primary" onclick="nav('catalog')"><i class="fa-solid fa-plus"></i> Nueva Solicitud</button>
                        </div>
                    </div>

                    <div class="kpi-grid animate-fade-in-up stagger-2">
                        <div class="card card-active">
                            <div class="kpi-icon" style="background: rgba(var(--color-success-rgb), 0.1); color: var(--color-success);">
                                <i class="fa-solid fa-satellite-dish anim-pulse-success"></i>
                            </div>
                            <div class="kpi-info">
                                <h4>Subastas Activas</h4>
                                <div class="value">3</div>
                            </div>
                        </div>
                        <div class="card">
                            <div class="kpi-icon" style="background: rgba(var(--color-accent-rgb), 0.1); color: var(--color-accent);">
                                <i class="fa-solid fa-piggy-bank"></i>
                            </div>
                            <div class="kpi-info">
                                <h4>Mejor Ahorro</h4>
                                <div class="value">S/ 1,240</div>
                            </div>
                        </div>
                        <div class="card">
                            <div class="kpi-icon" style="background: rgba(234, 179, 8, 0.1); color: var(--color-warning);">
                                <i class="fa-solid fa-clock"></i>
                            </div>
                            <div class="kpi-info">
                                <h4>Pedidos Pendientes</h4>
                                <div class="value">2</div>
                            </div>
                        </div>
                        <div class="card">
                            <div class="kpi-icon" style="background: rgba(59, 130, 246, 0.1); color: var(--color-accent-2);">
                                <i class="fa-solid fa-box-check"></i>
                                <i class="fa-solid fa-check-double"></i>
                            </div>
                            <div class="kpi-info">
                                <h4>Pedidos Pagados</h4>
                                <div class="value">8</div>
                            </div>
                        </div>
                    </div>

                    <div class="dashboard-main-grid animate-fade-in-up stagger-3">
                        <div class="card">
                            <h3 class="mb-4" style="margin-bottom: 1rem;">Mis subastas activas</h3>
                            <div class="table-container">
                                <table>
                                    <thead>
                                        <tr>
                                            <th>ID</th>
                                            <th>Descripción</th>
                                            <th># Pujas</th>
                                            <th>Mejor Puja</th>
                                            <th>Estado</th>
                                            <th>Acción</th>
                                        </tr>
                                    </thead>
                                    <tbody>
                                        <tr>
                                            <td><strong>#SUB-047</strong></td>
                                            <td>Materiales eléctricos</td>
                                            <td><span class="badge badge-accent">34</span></td>
                                            <td class="text-success font-bold">S/ 1,890</td>
                                            <td><span class="badge badge-success anim-pulse-success">🟢 Abierta</span></td>
                                            <td><button class="btn btn-outline text-xs" onclick="openAuction('047')">Ver detalle</button></td>
                                        </tr>
                                        <tr>
                                            <td><strong>#SUB-046</strong></td>
                                            <td>Herramientas de corte</td>
                                            <td><span class="badge badge-accent">52</span></td>
                                            <td class="text-success font-bold">S/ 3,240</td>
                                            <td><span class="badge badge-success anim-pulse-success">🟢 Abierta</span></td>
                                            <td><button class="btn btn-outline text-xs" onclick="openAuction('046')">Ver detalle</button></td>
                                        </tr>
                                        <tr>
                                            <td><strong>#SUB-045</strong></td>
                                            <td>Cemento y agregados</td>
                                            <td><span class="badge badge-accent">27</span></td>
                                            <td class="text-success font-bold">S/ 8,650</td>
                                            <td><span class="badge badge-danger">🔴 Cerrada</span></td>
                                            <td><button class="btn btn-outline text-xs" disabled>Finalizada</button></td>
                                        </tr>
                                    </tbody>
                                </table>
                            </div>
                        </div>

                        <div class="card">
                            <h3 class="mb-4" style="margin-bottom: 1rem;">Actividad (6 meses)</h3>
                            <div class="chart-container">
                                <canvas id="activityChart"></canvas>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- ================= SECTION 3: LIVE AUCTION ================= -->
                <div id="view-auction" class="view-section">
                    <button class="btn btn-outline mb-4" onclick="nav('dashboard')" style="margin-bottom: 1rem;"><i class="fa-solid fa-arrow-left"></i> Volver</button>
                    
                    <div class="card auction-header-card animate-fade-in-up stagger-1">
                        <div>
                            <div class="flex items-center gap-2 mb-2" style="margin-bottom: 0.5rem;">
                                <span class="badge badge-success anim-pulse-success">🟢 ABIERTA</span>
                                <span class="text-muted">| Creada: Hoy 08:30 AM</span>
                            </div>
                            <h2>Solicitud #SUB-2024-047 — Materiales eléctricos</h2>
                            <p class="text-muted mt-1">Suministro para obra residencial "Los Pinos"</p>
                        </div>
                        <div style="text-align: right;">
                            <div class="text-sm text-muted mb-1">Tiempo restante</div>
                            <div class="timer-display" id="auction-countdown">02:14:33</div>
                            <button class="btn btn-danger mt-2 w-full" style="margin-top: 1rem; background: var(--color-danger); color: white; width: 100%;">Cerrar subasta ahora</button>
                        </div>
                    </div>

                    <div class="auction-grid animate-fade-in-up stagger-2">
                        <!-- Lista de items -->
                        <div class="card">
                            <h3 style="margin-bottom: 1rem;"><i class="fa-solid fa-list-check text-accent"></i> Ítems Solicitados</h3>
                            <div class="table-container">
                                <table>
                                    <thead>
                                        <tr>
                                            <th>Producto</th>
                                            <th>Cant.</th>
                                            <th>Unidad</th>
                                        </tr>
                                    </thead>
                                    <tbody>
                                        <tr><td>Cable NYY 4mm² marca Indeco o sim.</td><td>200</td><td>Metros</td></tr>
                                        <tr><td>Tubo conduit 3/4" PVC pesado</td><td>50</td><td>Unidad</td></tr>
                                        <tr><td>Interruptor termomagnético 32A</td><td>10</td><td>Unidad</td></tr>
                                        <tr><td>Cinta aislante 3M Super 33+</td><td>20</td><td>Rollos</td></tr>
                                    </tbody>
                                </table>
                            </div>
                        </div>

                        <!-- Panel de Pujas -->
                        <div class="card bids-panel">
                            <div class="flex justify-between items-center mb-4" style="margin-bottom: 1rem;">
                                <h3><i class="fa-solid fa-bolt text-accent"></i> Ofertas en vivo</h3>
                                <div class="flex items-center gap-4">
                                    <button class="btn btn-outline text-xs" style="border-color: #a855f7; color: #d8b4fe; padding: 0.4rem 0.8rem;" onclick="analyzeLiveBids()" id="btn-analyze-bids">✨ Analizar Pujas con IA</button>
                                    <div class="live-indicator anim-pulse-success">
                                        <span class="dot anim-pulse-success"></span> Conectado
                                    </div>
                                </div>
                            </div>
                            <p class="text-sm text-muted" style="margin-bottom: 1rem;">Se muestran las mejores ofertas. <span id="bids-counter">4</span> recibidas.</p>
                            
                            <div id="ai-analysis-result" style="display: none; margin-bottom: 1rem;"></div>

                            <div class="table-container">
                                <table>
                                    <thead>
                                        <tr>
                                            <th>Proveedor</th>
                                            <th>Monto Total</th>
                                            <th>Hora</th>
                                            <th>Estado</th>
                                        </tr>
                                    </thead>
                                    <tbody id="bids-table-body">
                                        <!-- Initial Data -->
                                        <tr>
                                            <td>Ferretería El Maestro</td>
                                            <td>S/ 2,100.00</td>
                                            <td class="text-muted text-sm">14:23:10</td>
                                            <td>-</td>
                                        </tr>
                                        <tr>
                                            <td>Materiales del Sur EIRL</td>
                                            <td>S/ 2,050.00</td>
                                            <td class="text-muted text-sm">13:55:05</td>
                                            <td>-</td>
                                        </tr>
                                        <tr>
                                            <td>Distribuidora Lima Norte</td>
                                            <td>S/ 1,980.00</td>
                                            <td class="text-muted text-sm">14:18:42</td>
                                            <td>-</td>
                                        </tr>
                                        <tr class="best-offer-row" id="current-best-bid">
                                            <td><strong>Ferretería Industrial SAC</strong></td>
                                            <td class="text-accent" style="font-weight: 800; font-size: 1.1rem;">S/ 1,890.00</td>
                                            <td class="text-muted text-sm">14:10:22</td>
                                            <td><span class="badge badge-accent">MEJOR OFERTA ★</span></td>
                                        </tr>
                                    </tbody>
                                </table>
                            </div>
                            
                            <!-- Formulario Proveedor (Simulado colapsado) -->
                            <div style="margin-top: 2rem; border-top: 1px solid var(--color-border); padding-top: 1rem;">
                                <button class="btn btn-outline w-full justify-between" onclick="document.getElementById('provider-form').classList.toggle('active')" style="display: flex; justify-content: space-between;">
                                    <span>¿Eres proveedor? Enviar Puja</span>
                                    <i class="fa-solid fa-chevron-down"></i>
                                </button>
                                <div id="provider-form" class="view-section" style="margin-top: 1rem; padding: 1rem; background: rgba(0,0,0,0.2); border-radius: 8px;">
                                    <div class="form-group">
                                        <label>Monto ofertado (S/)</label>
                                        <input type="number" placeholder="Ej. 1850.00">
                                    </div>
                                    <div class="form-group">
                                        <label>Notas / Condiciones</label>
                                        <textarea rows="2" placeholder="Ej. Entrega inmediata, incluye IGV"></textarea>
                                    </div>
                                    <button class="btn btn-primary w-full btn-lg">Enviar Oferta Definitiva</button>
                                </div>
                            </div>

                        </div>
                    </div>
                </div>

                <!-- ================= SECTION 4: CATALOG ================= -->
                <div id="view-catalog" class="view-section">
                    <div class="dashboard-header animate-fade-in-up stagger-1">
                        <h2>Catálogo de Productos</h2>
                        <p class="text-muted">Añade ítems para crear una nueva solicitud de subasta</p>
                    </div>

                    <div class="catalog-filters animate-fade-in-up stagger-2">
                        <div class="search-bar">
                            <i class="fa-solid fa-search"></i>
                            <input type="text" placeholder="Buscar herramientas, materiales...">
                        </div>
                        <select style="width: auto;">
                            <option>Todas las categorías</option>
                            <option>Eléctrico</option>
                            <option>Construcción</option>
                            <option>Herramientas</option>
                            <option>Plomería</option>
                        </select>
                    </div>

                    <div class="catalog-grid animate-fade-in-up stagger-3">
                        <!-- Generando 8 cards -->
                        <div class="card product-card">
                            <div class="product-img-placeholder"><i class="fa-solid fa-plug"></i></div>
                            <div class="product-info">
                                <span class="badge badge-warning" style="align-self: flex-start; margin-bottom: 0.5rem;">Eléctrico</span>
                                <h4>Cable THW 12 AWG</h4>
                                <p class="text-sm text-muted mb-4" style="margin-bottom: 1rem;">Rollo x 100m. Cobre 99.9%</p>
                                <button class="btn btn-outline product-btn"><i class="fa-solid fa-plus"></i> Añadir a solicitud</button>
                            </div>
                        </div>
                        <div class="card product-card">
                            <div class="product-img-placeholder"><i class="fa-solid fa-hammer"></i></div>
                            <div class="product-info">
                                <span class="badge badge-danger" style="align-self: flex-start; margin-bottom: 0.5rem;">Herramientas</span>
                                <h4>Taladro Percutor 800W</h4>
                                <p class="text-sm text-muted mb-4" style="margin-bottom: 1rem;">Industrial, mandril 1/2"</p>
                                <button class="btn btn-outline product-btn"><i class="fa-solid fa-plus"></i> Añadir a solicitud</button>
                            </div>
                        </div>
                        <div class="card product-card">
                            <div class="product-img-placeholder"><i class="fa-solid fa-trowel-bricks"></i></div>
                            <div class="product-info">
                                <span class="badge badge-success" style="align-self: flex-start; margin-bottom: 0.5rem;">Construcción</span>
                                <h4>Cemento Portland Tipo I</h4>
                                <p class="text-sm text-muted mb-4" style="margin-bottom: 1rem;">Bolsa x 42.5 Kg</p>
                                <button class="btn btn-outline product-btn"><i class="fa-solid fa-plus"></i> Añadir a solicitud</button>
                            </div>
                        </div>
                        <div class="card product-card">
                            <div class="product-img-placeholder"><i class="fa-solid fa-faucet-drip"></i></div>
                            <div class="product-info">
                                <span class="badge badge-accent" style="align-self: flex-start; margin-bottom: 0.5rem;">Plomería</span>
                                <h4>Tubo PVC SAP 2"</h4>
                                <p class="text-sm text-muted mb-4" style="margin-bottom: 1rem;">Clase 10, largo 5m</p>
                                <button class="btn btn-outline product-btn"><i class="fa-solid fa-plus"></i> Añadir a solicitud</button>
                            </div>
                        </div>
                        <div class="card product-card">
                            <div class="product-img-placeholder"><i class="fa-solid fa-helmet-safety"></i></div>
                            <div class="product-info">
                                <span class="badge badge-warning" style="align-self: flex-start; margin-bottom: 0.5rem;">EPP</span>
                                <h4>Casco de Seguridad</h4>
                                <p class="text-sm text-muted mb-4" style="margin-bottom: 1rem;">Tipo I Clase E</p>
                                <button class="btn btn-outline product-btn"><i class="fa-solid fa-plus"></i> Añadir a solicitud</button>
                            </div>
                        </div>
                        <div class="card product-card">
                            <div class="product-img-placeholder"><i class="fa-solid fa-paint-roller"></i></div>
                            <div class="product-info">
                                <span class="badge badge-success" style="align-self: flex-start; margin-bottom: 0.5rem;">Acabados</span>
                                <h4>Pintura Látex Lavable</h4>
                                <p class="text-sm text-muted mb-4" style="margin-bottom: 1rem;">Balde x 5 Galones, Blanco</p>
                                <button class="btn btn-outline product-btn"><i class="fa-solid fa-plus"></i> Añadir a solicitud</button>
                            </div>
                        </div>
                        <div class="card product-card">
                            <div class="product-img-placeholder"><i class="fa-solid fa-screwdriver-wrench"></i></div>
                            <div class="product-info">
                                <span class="badge badge-danger" style="align-self: flex-start; margin-bottom: 0.5rem;">Herramientas</span>
                                <h4>Juego de Llaves Mixtas</h4>
                                <p class="text-sm text-muted mb-4" style="margin-bottom: 1rem;">12 piezas, Cromo Vanadio</p>
                                <button class="btn btn-outline product-btn"><i class="fa-solid fa-plus"></i> Añadir a solicitud</button>
                            </div>
                        </div>
                        <div class="card product-card">
                            <div class="product-img-placeholder"><i class="fa-solid fa-lightbulb"></i></div>
                            <div class="product-info">
                                <span class="badge badge-warning" style="align-self: flex-start; margin-bottom: 0.5rem;">Eléctrico</span>
                                <h4>Reflector LED 100W</h4>
                                <p class="text-sm text-muted mb-4" style="margin-bottom: 1rem;">IP65 Exteriores</p>
                                <button class="btn btn-outline product-btn"><i class="fa-solid fa-plus"></i> Añadir a solicitud</button>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- ================= SECTION 5: ORDERS ================= -->
                <div id="view-orders" class="view-section">
                    <div class="dashboard-header animate-fade-in-up stagger-1">
                        <h2>Historial de Pedidos</h2>
                        <p class="text-muted">Gestión de pagos y entregas de subastas ganadas</p>
                    </div>

                    <div class="card animate-fade-in-up stagger-2">
                        <div class="table-container">
                            <table>
                                <thead>
                                    <tr>
                                        <th># Orden</th>
                                        <th>Subasta Orig.</th>
                                        <th>Proveedor Ganador</th>
                                        <th>Monto Final</th>
                                        <th>Estado</th>
                                        <th>Fecha</th>
                                        <th>Acción</th>
                                    </tr>
                                </thead>
                                <tbody>
                                    <tr>
                                        <td><strong>ORD-092</strong></td>
                                        <td>#SUB-045</td>
                                        <td>Constructoras Andinas SAC</td>
                                        <td>S/ 8,650.00</td>
                                        <td><span class="badge badge-warning">🟡 Pendiente</span></td>
                                        <td>23 May 2026</td>
                                        <td><button class="btn btn-primary text-xs py-1" onclick="openPaymentModal()">Pagar ahora</button></td>
                                    </tr>
                                    <tr>
                                        <td><strong>ORD-089</strong></td>
                                        <td>#SUB-040</td>
                                        <td>Ferretería El Maestro</td>
                                        <td>S/ 2,450.00</td>
                                        <td><span class="badge badge-success">🟢 Pagado</span></td>
                                        <td>20 May 2026</td>
                                        <td><button class="btn btn-outline text-xs py-1"><i class="fa-solid fa-download"></i> Factura</button></td>
                                    </tr>
                                    <tr>
                                        <td><strong>ORD-075</strong></td>
                                        <td>#SUB-032</td>
                                        <td>Materiales del Sur EIRL</td>
                                        <td>S/ 5,120.00</td>
                                        <td><span class="badge" style="background: rgba(59,130,246,0.1); color: #3b82f6; border: 1px solid #3b82f6">🔵 Entregado</span></td>
                                        <td>15 May 2026</td>
                                        <td><button class="btn btn-outline text-xs py-1"><i class="fa-solid fa-eye"></i> Ver</button></td>
                                    </tr>
                                    <tr>
                                        <td><strong>ORD-060</strong></td>
                                        <td>#SUB-021</td>
                                        <td>Distribuidora Lima Norte</td>
                                        <td>S/ 980.00</td>
                                        <td><span class="badge badge-danger">🔴 Cancelado</span></td>
                                        <td>02 May 2026</td>
                                        <td>-</td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>

            </div>
        </main>
    </div>
</div>

<!-- ================= MODALS & TOASTS ================= -->

<!-- Modal Pago -->
<div class="modal-overlay" id="payment-modal">
    <div class="modal-content">
        <div class="flex justify-between items-center mb-4">
            <h3>Completar Pago</h3>
            <button onclick="closePaymentModal()" class="text-muted hover:text-white"><i class="fa-solid fa-xmark fa-lg"></i></button>
        </div>
        
        <div class="card" style="background: var(--color-bg); margin-bottom: 1.5rem; padding: 1rem;">
            <p class="text-sm text-muted">Resumen de Orden ORD-092</p>
            <div class="flex justify-between mt-2">
                <span>Total a pagar:</span>
                <span class="text-accent" style="font-weight: 700; font-size: 1.2rem;">S/ 8,650.00</span>
            </div>
            <div class="text-xs text-muted mt-1">Proveedor: Constructoras Andinas SAC</div>
        </div>

        <form onsubmit="processPayment(event)">
            <div class="form-group">
                <label>Número de Tarjeta</label>
                <input type="text" placeholder="#### #### #### ####" required pattern="\d{4}\s?\d{4}\s?\d{4}\s?\d{4}">
            </div>
            <div class="flex gap-4">
                <div class="form-group w-full">
                    <label>Vencimiento</label>
                    <input type="text" placeholder="MM/YY" required>
                </div>
                <div class="form-group w-full">
                    <label>CVV</label>
                    <input type="password" placeholder="***" required>
                </div>
            </div>
            <div class="form-group">
                <label>Nombre en la tarjeta</label>
                <input type="text" placeholder="EJ. CARLOS MENDOZA" required>
            </div>
            <button type="submit" class="btn btn-primary w-full btn-lg mt-2"><i class="fa-solid fa-lock"></i> Confirmar pago S/ 8,650</button>
        </form>
    </div>
</div>

<!-- Modal Asistente IA -->
<div class="modal-overlay" id="ai-generator-modal">
    <div class="modal-content" style="max-width: 600px;">
        <div class="flex justify-between items-center mb-4">
            <h3 style="color: #d8b4fe;"><i class="fa-solid fa-wand-magic-sparkles"></i> ✨ Generador de Solicitudes IA</h3>
            <button onclick="closeAIModal()" class="text-muted hover:text-white"><i class="fa-solid fa-xmark fa-lg"></i></button>
        </div>
        
        <p class="text-sm text-muted mb-4">Describe tu proyecto en lenguaje natural y Gemini armará la lista de materiales técnica requerida para la subasta.</p>
        
        <div class="form-group">
            <textarea id="ai-prompt-input" rows="3" placeholder="Ej. Voy a instalar el sistema eléctrico de una casa de 2 pisos, necesito el tablero, cables principales, tomacorrientes e interruptores para 5 habitaciones..."></textarea>
        </div>
        
        <button id="btn-generate-ai" class="btn btn-outline w-full" style="border-color: #a855f7; color: #d8b4fe; margin-bottom: 1rem;" onclick="generateAIList()">
            ✨ Generar Lista de Materiales
        </button>

        <div id="ai-generator-output" style="display: none;">
            <div class="ai-result-box mb-4">
                <p id="ai-explanation" class="text-sm text-muted"></p>
            </div>
            <div class="table-container">
                <table style="font-size: 0.85rem;">
                    <thead>
                        <tr>
                            <th>Producto Sugerido</th>
                            <th>Cant.</th>
                            <th>Unidad</th>
                        </tr>
                    </thead>
                    <tbody id="ai-items-table">
                        <!-- AI items inserted here -->
                    </tbody>
                </table>
            </div>
            <button class="btn btn-primary w-full mt-4" onclick="addAIItemsToDraft()">Añadir todo al borrador de subasta</button>
        </div>
    </div>
</div>

<!-- Contenedor Toasts -->
<div id="toast-container"></div>

<!-- WhatsApp Flotante -->
<a href="https://wa.me/51999999999?text=Hola,%20necesito%20ayuda" target="_blank" class="wsp-floating" title="¿Necesitas ayuda? Escríbenos">
    <i class="fa-brands fa-whatsapp"></i>
    <span class="wsp-badge">1</span>
</a>

<!-- ================= JAVASCRIPT ================= -->
<script>
    // --- Utils & State ---
    const qs = (selector) => document.querySelector(selector);
    const qsa = (selector) => document.querySelectorAll(selector);
    
    // Set Current Date
    const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' };
    qs('#current-date').textContent = new Date().toLocaleDateString('es-ES', options);

    // --- Navigation Logic (SPA Simulation) ---
    function login() {
        qs('#view-landing').classList.remove('active');
        qs('#dashboard-layout').style.display = 'flex';
        nav('dashboard');
        
        // Welcome Toast Delay
        setTimeout(() => {
            showToast("👋 Bienvenido de vuelta, Carlos. Tienes 3 subastas activas.", "info");
        }, 1000);

        initChart(); // init chart when entering dashboard
    }

    function logout() {
        qs('#dashboard-layout').style.display = 'none';
        qs('#view-landing').classList.add('active');
    }

    function nav(viewId) {
        // Hide all views
        qsa('.view-section').forEach(el => el.classList.remove('active'));
        // Show target
        qs(`#view-${viewId}`).classList.add('active');
        
        // Update sidebar links
        qsa('.sidebar-link').forEach(el => el.classList.remove('active'));
        // Find link that calls this view
        const links = Array.from(qsa('.sidebar-link'));
        const activeLink = links.find(l => l.getAttribute('onclick') === `nav('${viewId}')`);
        if(activeLink) activeLink.classList.add('active');

        // close mobile menu if open
        if(window.innerWidth <= 768) {
            qs('#sidebar').classList.remove('open');
        }
    }

    function openAuction(id) {
        nav('auction');
        // Reset timer just for effect
        countdownTime = 2 * 3600 + 14 * 60 + 33; // 02:14:33
    }

    // --- UI Interactions ---
    qs('#sidebar-toggle').addEventListener('click', () => {
        qs('#sidebar').classList.toggle('collapsed');
        if(qs('#sidebar').classList.contains('collapsed')){
            qs('.sidebar-logo-text').style.display = 'none';
            qs('#logo-icon-only').style.display = 'block';
        } else {
            qs('.sidebar-logo-text').style.display = 'flex';
            qs('#logo-icon-only').style.display = 'none';
        }
    });

    function toggleMobileMenu() {
        qs('#sidebar').classList.toggle('open');
    }

    function toggleNotifications(e) {
        e.stopPropagation();
        qs('#noti-dropdown').classList.toggle('show');
    }

    document.addEventListener('click', (e) => {
        if(!e.target.closest('.notification-wrapper')) {
            qs('#noti-dropdown').classList.remove('show');
        }
    });

    // --- Number Animation (Landing) ---
    function animateValue(id, start, end, duration) {
        let obj = document.getElementById(id);
        let startTimestamp = null;
        const step = (timestamp) => {
            if (!startTimestamp) startTimestamp = timestamp;
            const progress = Math.min((timestamp - startTimestamp) / duration, 1);
            let current = Math.floor(progress * (end - start) + start);
            obj.innerHTML = current.toLocaleString('en-US') + (id==='stat3' ? '.4' : '');
            if (progress < 1) {
                window.requestAnimationFrame(step);
            }
        };
        window.requestAnimationFrame(step);
    }
    
    // Trigger animations when in view
    setTimeout(() => {
        animateValue("stat1", 0, 1240, 2000);
        animateValue("stat2", 0, 380, 2000);
        animateValue("stat3", 0, 2, 2000); // 2.4 handled via append
        animateValue("stat4", 0, 98, 2000);
    }, 500);


    // --- Chart.js Initialization ---
    let chartInstance = null;
    function initChart() {
        if(chartInstance) return; // Prevent recreation
        const ctx = document.getElementById('activityChart').getContext('2d');
        
        Chart.defaults.color = '#64748b';
        Chart.defaults.font.family = "'DM Sans', sans-serif";

        chartInstance = new Chart(ctx, {
            type: 'bar',
            data: {
                labels: ['Dic', 'Ene', 'Feb', 'Mar', 'Abr', 'May'],
                datasets: [{
                    label: 'Subastas Completadas',
                    data: [12, 19, 15, 25, 22, 30],
                    backgroundColor: '#f97316',
                    borderRadius: 6,
                    barPercentage: 0.6
                }]
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                plugins: {
                    legend: { display: false }
                },
                scales: {
                    y: {
                        beginAtZero: true,
                        grid: { color: '#1e2d40', drawBorder: false }
                    },
                    x: {
                        grid: { display: false, drawBorder: false }
                    }
                }
            }
        });
    }

    // --- Live Auction Simulation ---
    // Countdown
    let countdownTime = 2 * 3600 + 14 * 60 + 33; // initial seconds
    const timerDisplay = qs('#auction-countdown');
    
    setInterval(() => {
        if(qs('#view-auction').classList.contains('active') && countdownTime > 0) {
            countdownTime--;
            let h = Math.floor(countdownTime / 3600).toString().padStart(2, '0');
            let m = Math.floor((countdownTime % 3600) / 60).toString().padStart(2, '0');
            let s = (countdownTime % 60).toString().padStart(2, '0');
            timerDisplay.textContent = `${h}:${m}:${s}`;

            if(countdownTime <= 300) { // last 5 mins
                timerDisplay.classList.add('timer-danger');
            } else {
                timerDisplay.classList.remove('timer-danger');
            }
        }
    }, 1000);

    // Live Bids Simulation
    const providers = [
        "Constructora e Inversiones R&M", 
        "Ferretería La Solución", 
        "Distribuidora de Materiales Perú", 
        "Global Tools SAC", 
        "ElectroMateriales S.A."
    ];
    let currentLowestBid = 1890.00;
    let bidCount = 4;

    setInterval(() => {
        if(!qs('#view-auction').classList.contains('active')) return;

        // Simulate new bid 30% of the time every 8s
        if(Math.random() > 0.7) {
            bidCount++;
            qs('#bids-counter').textContent = bidCount;
            
            const providerName = providers[Math.floor(Math.random() * providers.length)];
            // Generate a bid slightly lower or higher
            let newBidAmount = currentLowestBid - (Math.random() * 50);
            if(newBidAmount < 1500) newBidAmount = 1500; // floor

            const isNewBest = newBidAmount < currentLowestBid;
            if(isNewBest) currentLowestBid = newBidAmount;

            const timeStr = new Date().toLocaleTimeString('es-PE', {hour12: false});
            
            // Format currency
            const formattedMoney = `S/ ${newBidAmount.toLocaleString('en-US', {minimumFractionDigits: 2, maximumFractionDigits: 2})}`;

            // Create row
            const tr = document.createElement('tr');
            tr.style.animation = "fadeInDown 0.5s ease forwards";
            
            if(isNewBest) {
                // remove previous best styles
                const oldBestRow = qs('.best-offer-row');
                if(oldBestRow) {
                    oldBestRow.classList.remove('best-offer-row');
                    oldBestRow.querySelector('td:nth-child(2)').classList.remove('text-accent');
                    oldBestRow.querySelector('td:nth-child(2)').style.fontWeight = 'normal';
                    oldBestRow.querySelector('td:nth-child(2)').style.fontSize = '0.9rem';
                    oldBestRow.querySelector('td:nth-child(4)').innerHTML = '-';
                    oldBestRow.querySelector('strong').replaceWith(oldBestRow.querySelector('strong').textContent);
                }

                tr.className = 'best-offer-row';
                tr.innerHTML = `
                    <td><strong>${providerName}</strong></td>
                    <td class="text-accent" style="font-weight: 800; font-size: 1.1rem;">${formattedMoney}</td>
                    <td class="text-muted text-sm">${timeStr}</td>
                    <td><span class="badge badge-accent">MEJOR OFERTA ★</span></td>
                `;
            } else {
                tr.innerHTML = `
                    <td>${providerName}</td>
                    <td>${formattedMoney}</td>
                    <td class="text-muted text-sm">${timeStr}</td>
                    <td>-</td>
                `;
            }

            // Insert at top of tbody
            const tbody = qs('#bids-table-body');
            tbody.insertBefore(tr, tbody.firstChild);

            // Toast
            showToast(`⚡ Nueva puja de ${providerName}`, "success");
        }
    }, 8000); // Check every 8 seconds

    // --- Toast System ---
    function showToast(message, type = 'info') {
        const container = qs('#toast-container');
        const toast = document.createElement('div');
        toast.className = 'toast';
        
        let icon = '<i class="fa-solid fa-info-circle text-accent"></i>';
        if(type === 'success') icon = '<i class="fa-solid fa-bolt" style="color: var(--color-success)"></i>';
        
        toast.innerHTML = `${icon} <div>${message}</div>`;
        container.appendChild(toast);

        // Remove after 4 seconds
        setTimeout(() => {
            toast.style.animation = "fadeInDown 0.3s ease reverse forwards";
            setTimeout(() => toast.remove(), 300);
        }, 4000);
    }

    // --- Modals ---
    function openPaymentModal() {
        qs('#payment-modal').classList.add('show');
    }

    function closePaymentModal() {
        qs('#payment-modal').classList.remove('show');
    }

    function processPayment(e) {
        e.preventDefault();
        
        // Simular carga
        const btn = e.target.querySelector('button');
        const originalText = btn.innerHTML;
        btn.innerHTML = '<i class="fa-solid fa-circle-notch fa-spin"></i> Procesando...';
        btn.disabled = true;

        setTimeout(() => {
            btn.innerHTML = '<i class="fa-solid fa-check"></i> ¡Pago procesado exitosamente!';
            btn.classList.remove('btn-primary');
            btn.style.backgroundColor = 'var(--color-success)';
            
            showToast("✅ Pago completado y notificado al proveedor.", "success");
            
            setTimeout(() => {
                closePaymentModal();
                btn.innerHTML = originalText;
                btn.disabled = false;
                btn.style.backgroundColor = '';
                btn.classList.add('btn-primary');
                // Update table visually
                const row = qs('#view-orders tbody tr:first-child');
                row.querySelector('.badge').className = 'badge badge-success';
                row.querySelector('.badge').textContent = '🟢 Pagado';
                row.querySelector('button').outerHTML = '<button class="btn btn-outline text-xs py-1"><i class="fa-solid fa-download"></i> Factura</button>';
            }, 1500);

        }, 2000);
    }

    // --- GEMINI API INTEGRATION ---
    const apiKey = ""; // API Key provista por el entorno en tiempo de ejecución

    async function callGeminiAPI(prompt, systemPrompt, jsonSchema = null) {
        const url = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`;

        const payload = {
            contents: [{ parts: [{ text: prompt }] }],
            systemInstruction: { parts: [{ text: systemPrompt }] }
        };

        if (jsonSchema) {
            payload.generationConfig = {
                responseMimeType: "application/json",
                responseSchema: jsonSchema
            };
        }

        const delays = [1000, 2000, 4000, 8000, 16000];
        for (let i = 0; i < delays.length + 1; i++) {
            try {
                const response = await fetch(url, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });
                if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
                const data = await response.json();
                return data.candidates[0].content.parts[0].text;
            } catch (e) {
                if (i === delays.length) {
                    showToast("Error de conexión con IA. Revisa consola.", "danger");
                    console.error("Gemini API Error:", e);
                    throw e;
                }
                await new Promise(r => setTimeout(r, delays[i]));
            }
        }
    }

    // IA Feature 1: Generador de Solicitudes
    function openAIModal() {
        qs('#ai-generator-modal').classList.add('show');
        qs('#ai-prompt-input').value = "";
        qs('#ai-generator-output').style.display = 'none';
        qs('#ai-items-table').innerHTML = '';
    }

    function closeAIModal() {
        qs('#ai-generator-modal').classList.remove('show');
    }

    async function generateAIList() {
        const input = qs('#ai-prompt-input').value.trim();
        if (!input) {
            showToast("Por favor, describe tu proyecto primero.", "warning");
            return;
        }

        const btn = qs('#btn-generate-ai');
        const originalText = btn.innerHTML;
        btn.innerHTML = '<i class="fa-solid fa-circle-notch fa-spin"></i> Generando lista experta...';
        btn.disabled = true;
        btn.classList.add('ai-shimmer');

        const systemPrompt = "Eres un maestro de obra y comprador experto para ferreterías B2B. A partir de la descripción del usuario, genera una lista de materiales precisa, profesional y con medidas estándar en Perú.";
        
        const schema = {
            type: "OBJECT",
            properties: {
                explicacion: { type: "STRING", description: "Breve explicación de la selección de materiales en 1 línea" },
                items: {
                    type: "ARRAY",
                    items: {
                        type: "OBJECT",
                        properties: {
                            producto: { type: "STRING", description: "Nombre técnico completo del material" },
                            cantidad: { type: "NUMBER" },
                            unidad: { type: "STRING", description: "Ej: Unidad, Metros, Rollos, Cajas" }
                        }
                    }
                }
            }
        };

        try {
            const responseText = await callGeminiAPI(`Necesito materiales para: ${input}`, systemPrompt, schema);
            const data = JSON.parse(responseText);
            
            qs('#ai-explanation').innerHTML = `<strong>Análisis IA:</strong> ${data.explicacion}`;
            
            const tbody = qs('#ai-items-table');
            tbody.innerHTML = '';
            
            data.items.forEach(item => {
                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td>${item.producto}</td>
                    <td>${item.cantidad}</td>
                    <td>${item.unidad}</td>
                `;
                tbody.appendChild(tr);
            });

            qs('#ai-generator-output').style.display = 'block';
            showToast("✨ Lista generada con éxito.", "success");
        } catch (error) {
            showToast("No se pudo generar la lista en este momento.", "danger");
        } finally {
            btn.innerHTML = originalText;
            btn.disabled = false;
            btn.classList.remove('ai-shimmer');
        }
    }

    function addAIItemsToDraft() {
        closeAIModal();
        showToast("✅ Materiales añadidos a la nueva solicitud.", "success");
        nav('catalog');
    }

    // IA Feature 2: Analizador de Pujas
    async function analyzeLiveBids() {
        const btn = qs('#btn-analyze-bids');
        const originalText = btn.innerHTML;
        btn.innerHTML = '<i class="fa-solid fa-circle-notch fa-spin"></i> Analizando mercado...';
        btn.disabled = true;

        const resultBox = qs('#ai-analysis-result');
        resultBox.style.display = 'block';
        resultBox.className = 'ai-result-box ai-shimmer';
        resultBox.innerHTML = '<span class="text-sm text-muted">Consultando IA sobre la competitividad de las ofertas...</span>';

        // Recolectar datos actuales de la tabla simulada
        const rows = document.querySelectorAll('#bids-table-body tr');
        let bidsData = "";
        rows.forEach((row) => {
            const prov = row.cells[0].innerText.replace('MEJOR OFERTA ★', '').trim();
            const monto = row.cells[1].innerText.trim();
            bidsData += `- ${prov}: ${monto}\n`;
        });

        const systemPrompt = "Eres un analista de adquisiciones industriales. Evalúa las ofertas actuales de la subasta inversa. Sé conciso (máximo 2 oraciones). Identifica si hay un claro ganador o si el usuario debe esperar más tiempo. El presupuesto referencial inicial de mercado era S/ 2500.";
        const prompt = `Analiza estas pujas en vivo y dame un consejo estratégico rápido:\n${bidsData}`;

        try {
            const advice = await callGeminiAPI(prompt, systemPrompt);
            resultBox.classList.remove('ai-shimmer');
            resultBox.innerHTML = `<strong>✨ Insight IA:</strong> <span class="text-sm">${advice}</span>`;
            showToast("Análisis completado.", "success");
        } catch (error) {
            resultBox.style.display = 'none';
        } finally {
            btn.innerHTML = originalText;
            btn.disabled = false;
        }
    }

</script>
</body>
</html>
