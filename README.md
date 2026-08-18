    <!DOCTYPE html>
    <html lang="es">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
        <title>SegurApp Recorridos - Control de Transporte y Domicilios</title>
        <link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
        
    <script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/12.17.1/firebase-app.js";
    import { getAnalytics } from "https://www.gstatic.com/firebasejs/12.17.1/firebase-analytics.js";
    import { 
        getAuth, 
        signInWithEmailAndPassword, 
        createUserWithEmailAndPassword,
        signInWithPopup, 
        GoogleAuthProvider, 
        FacebookAuthProvider, 
        OAuthProvider, 
        RecaptchaVerifier, 
        signInWithPhoneNumber,
        signOut,
        onAuthStateChanged 
    } from "https://www.gstatic.com/firebasejs/12.17.1/firebase-auth.js";
    import { getDatabase, ref, set, get, child } from "https://www.gstatic.com/firebasejs/12.17.1/firebase-database.js";

    const firebaseConfig = {
        apiKey: "AIzaSyDTNh_JZfAPDdW1PDuhse3H3kSeuzd3V-4",
        authDomain: "mi-app-a88d7.firebaseapp.com",
        databaseURL: "https://mi-app-a88d7-default-rtdb.firebaseio.com", /*[cite: 1] Integrada correctamente */
        projectId: "mi-app-a88d7",
        storageBucket: "mi-app-a88d7.firebasestorage.app",
        messagingSenderId: "372012990915",
        appId: "1:372012990915:web:552c23fa6d01351a92fd76",
        measurementId: "G-0X076JP1LP"
    };

    const app = initializeApp(firebaseConfig);
    const analytics = getAnalytics(app);
    const auth = getAuth(app);
    const db = getDatabase(app);

    window.firebaseAuth = auth;
    window.firebaseDb = db;
    window.authProviders = {
        Google: new GoogleAuthProvider(),
        Facebook: new FacebookAuthProvider(),
        Apple: new OAuthProvider('apple.com')
    };
    window.firebaseFns = {
        signInWithEmailAndPassword,
        createUserWithEmailAndPassword,
        signInWithPopup,
        RecaptchaVerifier,
        signInWithPhoneNumber,
        signOut,
        onAuthStateChanged,
        ref,
        set,
        get,
        child
    };
    </script>
        
        <style>
            :root {
                --bg-principal: #d4af37;
                --bg-tarjeta: #000000;
                --bg-input: #000000;
                --dorado-brillante: #d4af37;
                --dorado-oscuro: #997a15;
                --glow-dorado: rgba(212, 175, 55, 0.35);    
                --verde-verificado: #00ff88;
                --glow-verde: rgba(0, 255, 136, 0.6);
                --rojo-alerta: #ff4444;
                --glow-rojo: rgba(255, 68, 68, 0.5);
                --texto-principal: #ffffff;
                --texto-secundario: #e1e7f0;
                --borde-sutil: rgba(212, 175, 55, 0.4);
                --fuente: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
                --whatsapp-green: #25d366;
                --call-blue: #0088cc;
                --nequi-purple: #5200ff;
            }

            * {
                box-sizing: border-box;
                margin: 0;
                padding: 0;
                font-family: var(--fuente);
                -webkit-tap-highlight-color: transparent;
            }

            body {
                background-color: var(--bg-principal);
                background-image: radial-gradient(circle at center, rgba(212, 175, 55, 0.08) 0%, rgba(8, 8, 10, 0.98) 80%);
                color: var(--texto-principal);
                min-height: 100vh;
                display: flex;
                flex-direction: column;
                align-items: center;
                padding: 15px;
            }

            .container {
                width: 100%;
                max-width: 500px;
                display: flex;
                flex-direction: column;
                gap: 15px;
            }

            header {
                text-align: center;
                padding: 10px 0;
                border-bottom: 1px solid var(--borde-sutil);
                display: flex;
                justify-content: center;
                align-items: center;
                position: relative;
            }

            .btn-dev-img {
                width: 90px;
                height: 90px;
                object-fit: cover;
                border-radius: 50%;
                border: 2px solid var(--dorado-brillante);
                box-shadow: 0 0 10px var(--glow-dorado);
                cursor: pointer;
                transition: transform 0.2s ease, box-shadow 0.2s ease;
            }

            .btn-dev-img:hover {
                transform: scale(1.08);
                box-shadow: 0 0 18px var(--glow-dorado);
            }

            .btn-dev-logout {
                background: rgba(255, 68, 68, 0.15);
                border: 1px solid var(--rojo-alerta);
                color: var(--rojo-alerta);
                padding: 6px 12px;
                border-radius: 20px;
                font-size: 0.8rem;
                font-weight: 700;
                cursor: pointer;
                display: flex;
                align-items: center;
                gap: 5px;
            }

            .card {
                background: var(--bg-tarjeta);
                border: 1px solid var(--borde-sutil);
                border-radius: 16px;
                padding: 18px;
                box-shadow: 0 10px 25px rgba(0,0,0,0.5);
            }

            .card-title {
                font-size: 1rem;
                color: var(--dorado-brillante);
                margin-bottom: 12px;
                display: flex;
                align-items: center;
                justify-content: space-between;
                text-transform: uppercase;
                font-weight: 800;
            }

            .card-title-text {
                display: flex;
                align-items: center;
                gap: 8px;
            }

            .input-custom {
                width: 100%;
                padding: 12px 14px;
                background: var(--bg-input);
                border: 1px solid var(--borde-sutil);
                border-radius: 10px;
                color: #fff;
                font-size: 0.95rem;
                margin-bottom: 12px;
                outline: none;
            }

            .input-custom:focus {
                border-color: var(--dorado-brillante);
                box-shadow: 0 0 8px var(--glow-dorado);
            }

            select.input-custom {
                appearance: none;
                background-image: url('data:image/svg+xml;utf8,<svg fill="%23d4af37" height="24" viewBox="0 0 24 24" width="24" xmlns="http://www.w3.org/2000/svg"><path d="M7 10l5 5 5-5z"/></svg>');
                background-repeat: no-repeat;
                background-position: right 10px center;
            }

            .btn-action {
                width: 100%;
                padding: 14px;
                border-radius: 12px;
                border: none;
                font-weight: 800;
                font-size: 0.95rem;
                cursor: pointer;
                display: flex;
                align-items: center;
                justify-content: center;
                gap: 8px;
                text-transform: uppercase;
                transition: all 0.2s ease;
                text-decoration: none;
            }

            .btn-solicitar {
                background: linear-gradient(135deg, var(--verde-verificado) 0%, #056232 100%);
                color: #000;
                box-shadow: 0 0 15px var(--glow-verde);
            }

            .btn-cancelar {
                background: linear-gradient(135deg, #ff4444 0%, #aa0000 100%);
                color: #fff;
                margin-top: 8px;
            }

            .btn-aceptar {
                background: linear-gradient(135deg, var(--dorado-brillante) 0%, var(--dorado-oscuro) 100%);
                color: #000;
                box-shadow: 0 0 15px var(--glow-dorado);
            }

            .btn-whatsapp {
                background: linear-gradient(135deg, #25d366 0%, #128c7e 100%);
                color: #fff;
                box-shadow: 0 0 12px rgba(37, 211, 102, 0.4);
            }

            .btn-llamar {
                background: linear-gradient(135deg, #0088cc 0%, #005580 100%);
                color: #fff;
                box-shadow: 0 0 12px rgba(0, 136, 204, 0.4);
            }

            .btn-pago-nequi {
                background: linear-gradient(135deg, #5200ff 0%, #2b0080 100%);
                color: #fff;
                box-shadow: 0 0 12px rgba(82, 0, 255, 0.4);
                margin-top: 10px;
            }

            .btn-pago-qr {
                background: linear-gradient(135deg, #333333 0%, #111111 100%);
                border: 1px solid var(--dorado-brillante);
                color: var(--dorado-brillante);
                box-shadow: 0 0 10px var(--glow-dorado);
                margin-top: 8px;
            }

            .btn-terminos {
                background: transparent;
                border: 1px dashed var(--dorado-brillante);
                color: var(--dorado-brillante);
                margin-top: 8px;
                font-size: 0.85rem;
            }

            .social-login-container {
                display: flex;
                flex-direction: column;
                gap: 10px;
                margin-top: 15px;
                border-top: 1px solid var(--borde-sutil);
                padding-top: 15px;
            }

            .social-buttons-grid {
                display: grid;
                grid-template-columns: repeat(4, 1fr);
                gap: 8px;
            }

            .btn-social-compact {
                padding: 10px 0;
                border-radius: 10px;
                border: 1px solid var(--borde-sutil);
                background: #111;
                color: #fff;
                cursor: pointer;
                display: flex;
                align-items: center;
                justify-content: center;
                transition: all 0.2s;
            }

            .btn-social-compact:hover {
                border-color: var(--dorado-brillante);
                box-shadow: 0 0 10px var(--glow-dorado);
                transform: translateY(-2px);
            }

            .btn-google { border-color: rgba(234, 67, 53, 0.5); color: #ea4335; }
            .btn-facebook { border-color: rgba(24, 119, 242, 0.5); color: #1877f2; }
            .btn-apple { border-color: rgba(255, 255, 255, 0.4); color: #fff; }
            .btn-phone { border-color: rgba(0, 255, 136, 0.5); color: var(--verde-verificado); }

            .payment-group {
                border-top: 1px solid var(--borde-sutil);
                padding-top: 12px;
                margin-top: 15px;
            }

            .contact-group {
                display: grid;
                grid-template-columns: 1fr 1fr;
                gap: 10px;
                margin-bottom: 10px;
            }

            .status-container-interactive {
                margin-top: 15px;
                padding: 16px;
                border-radius: 16px;
                display: flex;
                flex-direction: column;
                align-items: center;
                gap: 10px;
                text-align: center;
                transition: all 0.4s ease;
            }

            .status-espera-animated {
                background: rgba(212, 175, 55, 0.1);
                border: 2px solid var(--dorado-brillante);
                box-shadow: 0 0 20px var(--glow-dorado);
                animation: pulse-border-dorado 2s infinite alternate;
            }

            .status-aceptado-animated {
                background: rgba(0, 255, 136, 0.12);
                border: 2px solid var(--verde-verificado);
                box-shadow: 0 0 25px var(--glow-verde);
                animation: pulse-border-verde 1.5s infinite alternate;
            }

            .status-oferta-animated {
                background: rgba(255, 170, 0, 0.15);
                border: 2px solid #ffaa00;
                box-shadow: 0 0 25px rgba(255, 170, 0, 0.5);
                animation: pulse-border-oferta 1.2s infinite alternate;
            }

            .precio-destacado-box {
                background: rgba(0, 0, 0, 0.7);
                border: 2px solid var(--dorado-brillante);
                border-radius: 12px;
                padding: 10px 16px;
                margin: 8px 0;
                display: inline-flex;
                flex-direction: column;
                align-items: center;
                justify-content: center;
                box-shadow: 0 0 15px var(--glow-dorado);
                transition: all 0.3s ease;
                position: relative;
                overflow: hidden;
            }

            .precio-destacado-box.precio-cambiado {
                border-color: var(--verde-verificado);
                box-shadow: 0 0 25px var(--glow-verde);
                animation: pulse-precio 0.8s ease-in-out 2;
            }

            .precio-label {
                font-size: 0.75rem;
                color: var(--texto-secundario);
                text-transform: uppercase;
                letter-spacing: 1px;
                font-weight: 700;
            }

            .precio-val-num {
                font-size: 1.5rem;
                font-weight: 900;
                color: var(--verde-verificado);
                text-shadow: 0 0 10px var(--glow-verde);
                display: flex;
                align-items: center;
                gap: 4px;
            }

            .badge-actualizado {
                position: absolute;
                top: 0;
                right: 0;
                background: var(--verde-verificado);
                color: #000;
                font-size: 0.65rem;
                font-weight: 900;
                padding: 2px 6px;
                border-bottom-left-radius: 6px;
                text-transform: uppercase;
                animation: fadeIn 0.3s ease;
            }

            @keyframes pulse-precio {
                0% { transform: scale(1); box-shadow: 0 0 10px var(--glow-verde); }
                50% { transform: scale(1.08); box-shadow: 0 0 35px var(--verde-verificado); }
                100% { transform: scale(1); box-shadow: 0 0 15px var(--glow-verde); }
            }

            .status-icon-box {
                width: 50px;
                height: 50px;
                border-radius: 50%;
                display: flex;
                align-items: center;
                justify-content: center;
                font-size: 28px;
            }

            .status-espera-animated .status-icon-box {
                background: rgba(212, 175, 55, 0.2);
                color: var(--dorado-brillante);
            }

            .status-aceptado-animated .status-icon-box {
                background: rgba(0, 255, 136, 0.25);
                color: var(--verde-verificado);
            }

            .status-oferta-animated .status-icon-box {
                background: rgba(255, 170, 0, 0.25);
                color: #ffaa00;
            }

            .codigo-badge-box {
                width: 100%;
                background: rgba(212, 175, 55, 0.15);
                border: 2px dashed var(--dorado-brillante);
                border-radius: 12px;
                padding: 12px;
                margin-bottom: 12px;
                text-align: center;
            }

            .codigo-num {
                font-size: 1.8rem;
                font-weight: 900;
                color: var(--dorado-brillante);
                letter-spacing: 6px;
                text-shadow: 0 0 10px var(--glow-dorado);
                margin: 4px 0;
            }

            .spin-icon { animation: spin 2.5s linear infinite; }
            .bounce-icon { animation: bounce 1.2s ease infinite; }

            @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
            @keyframes bounce { 0%, 100% { transform: translateY(0); } 50% { transform: translateY(-7px); } }
            @keyframes pulse-border-dorado { 0% { box-shadow: 0 0 10px rgba(212, 175, 55, 0.2); } 100% { box-shadow: 0 0 25px rgba(212, 175, 55, 0.6); } }
            @keyframes pulse-border-verde { 0% { box-shadow: 0 0 10px rgba(0, 255, 136, 0.3); } 100% { box-shadow: 0 0 30px rgba(0, 255, 136, 0.8); } }
            @keyframes pulse-border-oferta { 0% { box-shadow: 0 0 10px rgba(255, 170, 0, 0.3); } 100% { box-shadow: 0 0 30px rgba(255, 170, 0, 0.8); } }

            .finance-wrapper {
                display: flex;
                flex-direction: column;
                gap: 15px;
                margin-top: 15px;
            }

            .container-ingresos-neon {
                position: relative;
                background: linear-gradient(135deg, rgba(0, 255, 136, 0.12) 0%, rgba(5, 98, 50, 0.25) 50%, rgba(0, 0, 0, 0.9) 100%);
                border: 2px solid var(--verde-verificado);
                border-radius: 18px;
                padding: 16px;
                box-shadow: 0 0 25px var(--glow-verde), inset 0 0 15px rgba(0, 255, 136, 0.2);
                overflow: hidden;
                animation: pulse-ingresos-bg 3s infinite alternate;
            }

            .container-ingresos-neon::before {
                content: '';
                position: absolute;
                top: -50%;
                left: -50%;
                width: 200%;
                height: 200%;
                background: radial-gradient(circle, rgba(0, 255, 136, 0.15) 10%, transparent 60%);
                animation: rotate-glow 8s linear infinite;
                pointer-events: none;
            }

            @keyframes rotate-glow {
                0% { transform: rotate(0deg); }
                100% { transform: rotate(360deg); }
            }

            @keyframes pulse-ingresos-bg {
                0% { border-color: #00ff88; box-shadow: 0 0 20px rgba(0, 255, 136, 0.4); }
                50% { border-color: #00ffbb; box-shadow: 0 0 35px rgba(0, 255, 187, 0.8); }
                100% { border-color: #00ff88; box-shadow: 0 0 20px rgba(0, 255, 136, 0.4); }
            }

            .ingresos-header {
                display: flex;
                align-items: center;
                justify-content: space-between;
                margin-bottom: 12px;
                position: relative;
                z-index: 2;
            }

            .ingresos-title {
                color: var(--verde-verificado);
                font-size: 0.95rem;
                font-weight: 900;
                letter-spacing: 1px;
                text-transform: uppercase;
                display: flex;
                align-items: center;
                gap: 6px;
                text-shadow: 0 0 10px var(--glow-verde);
            }

            .container-gastos-box {
                background: linear-gradient(135deg, rgba(255, 68, 68, 0.08) 0%, rgba(20, 5, 5, 0.85) 100%);
                border: 1px solid var(--rojo-alerta);
                border-radius: 18px;
                padding: 16px;
                box-shadow: 0 0 15px rgba(255, 68, 68, 0.2);
            }

            .gastos-header {
                display: flex;
                align-items: center;
                justify-content: space-between;
                margin-bottom: 12px;
            }

            .gastos-title {
                color: var(--rojo-alerta);
                font-size: 0.95rem;
                font-weight: 900;
                letter-spacing: 1px;
                text-transform: uppercase;
                display: flex;
                align-items: center;
                gap: 6px;
                text-shadow: 0 0 8px var(--glow-rojo);
            }

            .period-grid {
                display: grid;
                grid-template-columns: 1fr 1fr 1fr;
                gap: 8px;
                position: relative;
                z-index: 2;
            }

            .period-card {
                background: rgba(0, 0, 0, 0.6);
                border: 1px solid rgba(255, 255, 255, 0.1);
                border-radius: 12px;
                padding: 10px 6px;
                text-align: center;
                backdrop-filter: blur(4px);
            }

            .period-card.ingresos-card { border-color: rgba(0, 255, 136, 0.3); }
            .period-card.gastos-card { border-color: rgba(255, 68, 68, 0.3); }
            .period-card.ganancia-card { border-color: rgba(212, 175, 55, 0.35); }

            .period-label {
                font-size: 0.68rem;
                color: var(--texto-secundario);
                font-weight: 800;
                text-transform: uppercase;
                margin-bottom: 4px;
            }

            .period-value {
                font-size: 0.95rem;
                font-weight: 900;
            }

            .period-value.ingreso { color: var(--verde-verificado); text-shadow: 0 0 8px var(--glow-verde); }
            .period-value.gasto { color: var(--rojo-alerta); text-shadow: 0 0 8px var(--glow-rojo); }
            .period-value.ganancia { color: var(--dorado-brillante); text-shadow: 0 0 10px var(--glow-dorado); }

            .anim-incremento { animation: bump-scale 0.5s ease-in-out; }

            @keyframes bump-scale {
                0% { transform: scale(1); }
                50% { transform: scale(1.18); filter: brightness(1.4); }
                100% { transform: scale(1); }
            }

            .sub-gastos-form {
                display: flex;
                gap: 6px;
                margin-top: 10px;
            }

            .btn-add-gasto {
                background: linear-gradient(135deg, var(--rojo-alerta) 0%, #880000 100%);
                color: #fff;
                border: none;
                border-radius: 8px;
                padding: 0 12px;
                font-weight: 800;
                cursor: pointer;
                display: flex;
                align-items: center;
                justify-content: center;
            }

            .gastos-lista-box {
                margin-top: 10px;
                max-height: 120px;
                overflow-y: auto;
                display: flex;
                flex-direction: column;
                gap: 6px;
                position: relative;
                z-index: 2;
            }

            .gasto-item-row {
                display: flex;
                justify-content: space-between;
                align-items: center;
                background: rgba(255,255,255,0.04);
                border-left: 3px solid var(--rojo-alerta);
                padding: 6px 10px;
                border-radius: 6px;
                font-size: 0.78rem;
            }

            .card-utilidad-neta {
                position: relative;
                background: linear-gradient(135deg, rgba(212, 175, 55, 0.18) 0%, rgba(153, 122, 21, 0.25) 50%, rgba(0, 0, 0, 0.95) 100%);
                border: 2px solid var(--dorado-brillante);
                border-radius: 18px;
                padding: 16px;
                margin-top: 10px;
                text-align: center;
                box-shadow: 0 0 25px var(--glow-dorado), inset 0 0 15px rgba(212, 175, 55, 0.2);
                overflow: hidden;
                animation: pulse-ganancia-bg 3s infinite alternate;
            }

            .card-utilidad-neta::before {
                content: '';
                position: absolute;
                top: -50%;
                left: -50%;
                width: 200%;
                height: 200%;
                background: radial-gradient(circle, rgba(212, 175, 55, 0.2) 10%, transparent 60%);
                animation: rotate-glow 8s linear infinite;
                pointer-events: none;
            }

            @keyframes pulse-ganancia-bg {
                0% { border-color: #d4af37; box-shadow: 0 0 20px rgba(212, 175, 55, 0.4); }
                50% { border-color: #fff1a8; box-shadow: 0 0 35px rgba(255, 241, 168, 0.8); }
                100% { border-color: #d4af37; box-shadow: 0 0 20px rgba(212, 175, 55, 0.4); }
            }

            .ganancia-header {
                display: flex;
                align-items: center;
                justify-content: space-between;
                margin-bottom: 12px;
                position: relative;
                z-index: 2;
            }

            .ganancia-title {
                color: var(--dorado-brillante);
                font-size: 0.95rem;
                font-weight: 900;
                letter-spacing: 1px;
                text-transform: uppercase;
                display: flex;
                align-items: center;
                gap: 6px;
                text-shadow: 0 0 10px var(--glow-dorado);
            }

            .modal-overlay {
                position: fixed;
                top: 0; left: 0; width: 100%; height: 100%;
                background: rgba(0,0,0,0.85);
                backdrop-filter: blur(5px);
                display: flex;
                align-items: center;
                justify-content: center;
                z-index: 1000;
                opacity: 0;
                pointer-events: none;
                transition: opacity 0.3s ease;
                padding: 20px;
            }

            .modal-overlay.active {
                opacity: 1;
                pointer-events: auto;
            }

            .modal-content {
                background: var(--bg-tarjeta);
                border: 1px solid var(--borde-sutil);
                border-radius: 20px;
                width: 100%;
                max-width: 400px;
                padding: 20px;
                text-align: center;
                max-height: 85vh;
                overflow-y: auto;
            }

            .input-code {
                width: 100%;
                padding: 12px;
                background: var(--bg-input);
                border: 1px solid var(--borde-sutil);
                border-radius: 10px;
                color: #fff;
                font-size: 1.2rem;
                text-align: center;
                letter-spacing: 4px;
                margin: 15px 0;
                outline: none;
            }

            .info-box {
                background: var(--bg-input);
                border: 1px solid var(--borde-sutil);
                border-radius: 10px;
                padding: 12px;
                margin-bottom: 12px;
                font-size: 0.85rem;
                color: var(--texto-secundario);
                text-align: left;
                line-height: 1.4;
            }

            .info-box strong { color: #fff; }

            .user-badge {
                background: rgba(0, 255, 136, 0.1);
                border: 1px solid var(--verde-verificado);
                padding: 12px 14px;
                border-radius: 12px;
                margin-bottom: 15px;
                display: flex;
                align-items: center;
                justify-content: space-between;
                font-size: 0.85rem;
            }

            .user-avatar-preview {
                width: 45px;
                height: 45px;
                border-radius: 50%;
                object-fit: cover;
                border: 2px solid var(--verde-verificado);
            }

            .qr-image-container {
                width: 100%;
                max-width: 250px;
                margin: 15px auto;
                border-radius: 12px;
                overflow: hidden;
                border: 2px solid var(--dorado-brillante);
                box-shadow: 0 0 15px var(--glow-dorado);
            }

            .qr-image-container img {
                width: 100%;
                height: auto;
                display: block;
            }

            /* Nuevos estilos estéticos con animación para los botones del panel de cliente */
.client-action-buttons {
    display: flex;
    gap: 8px;
    align-items: center;
    justify-content: flex-end;
    margin-bottom: 12px;
}

.btn-modern-logout {
    background: linear-gradient(135deg, rgba(255, 68, 68, 0.2) 0%, rgba(136, 0, 0, 0.4) 100%);
    border: 1px solid var(--rojo-alerta);
    color: var(--rojo-alerta);
    padding: 8px 14px;
    border-radius: 10px;
    cursor: pointer;
    display: flex;
    align-items: center;
    gap: 6px;
    font-size: 0.78rem;
    font-weight: 800;
    text-transform: uppercase;
    box-shadow: 0 0 10px rgba(255, 68, 68, 0.2);
    transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.btn-modern-logout:hover {
    background: var(--rojo-alerta);
    color: #fff;
    box-shadow: 0 0 20px var(--glow-rojo);
    transform: translateY(-2px);
}

.btn-modern-edit {
    background: linear-gradient(135deg, rgba(212, 175, 55, 0.2) 0%, rgba(153, 122, 21, 0.4) 100%);
    border: 1px solid var(--dorado-brillante);
    color: var(--dorado-brillante);
    padding: 8px 14px;
    border-radius: 10px;
    cursor: pointer;
    display: flex;
    align-items: center;
    gap: 6px;
    font-size: 0.78rem;
    font-weight: 800;
    text-transform: uppercase;
    box-shadow: 0 0 10px var(--glow-dorado);
    transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.btn-modern-edit:hover {
    background: var(--dorado-brillante);
    color: #000;
    box-shadow: 0 0 20px var(--glow-dorado);
    transform: translateY(-2px);
}   
        </style>
    </head>
    <body>

        <div class="container">
            <header>
                <img src="https://scontent.fnva2-1.fna.fbcdn.net/v/t39.30808-6/770562384_986398197754607_2505745652231744957_n.jpg?stp=dst-jpg_tt6&cstp=mx2048x2048&ctp=s2048x2048&_nc_cat=100&ccb=1-7&_nc_sid=6ee11a&_nc_ohc=gd7NfdbVzPkQ7kNvwHGK7H5&_nc_oc=AdodzRLiDqI2QJ0eyibdjtPC62g-B7mwzSfhZwPT5YSVgRFiUc-SFX9VU8vyvPyh_0s&_nc_zt=23&_nc_ht=scontent.fnva2-1.fna&_nc_gid=k8J_OO78tWO4R0LQjIh65g&_nc_ss=7b2a8&oh=00_AQFSWRM0EvWIG-KzYiSuC3Z-MUjtZjPzdzkgr0a-xHl3og&oe=6A897443" alt="Modo Desarrollador" class="btn-dev-img" id="btnDevHeader" onclick="abrirModalDev()" title="Panel Desarrollador">   
            </header>

            <div class="card" id="panelRegistro">
                <div class="card-title">
                    <div class="card-title-text">
                        <span class="material-icons">person_add</span> Iniciar Sesión / Registro
                    </div>
                </div>
                
                <p style="font-size: 0.85rem; color: var(--texto-secundario); margin-bottom: 15px;">
                    Accede con tu correo, teléfono o redes sociales para solicitar servicios de transporte y domicilios seguros.
                </p>

                <input type="email" id="authEmail" class="input-custom" placeholder="✉️ Correo Electrónico" autocomplete="email">
                <input type="password" id="authPassword" class="input-custom" placeholder="🔒 Contraseña" autocomplete="current-password">

                <div style="display: flex; gap: 8px; margin-bottom: 12px;">
                    <button class="btn-action btn-aceptar" onclick="iniciarSesionCorreo()">
                        <span class="material-icons">login</span> Ingresar
                    </button>
                    <button class="btn-action btn-solicitar" onclick="abrirModalRegistroCompleto()" style="font-size:0.85rem;">
                        <span class="material-icons">person_add</span> Registrarse
                    </button>
                </div>

                <div class="social-login-container">
                    <div style="font-size: 0.75rem; color: var(--texto-secundario); text-transform: uppercase; text-align: center; font-weight: 700; margin-bottom: 6px;">
                        O accede rápidamente con:
                    </div>
                    
                    <div class="social-buttons-grid">
                        <button class="btn-social-compact btn-google" onclick="loginSocial('Google')" title="Continuar con Google">
                            <span class="material-icons" style="font-size: 20px;">g_translate</span>
                        </button>
                        
                        <button class="btn-social-compact btn-facebook" onclick="loginSocial('Facebook')" title="Continuar con Facebook">
                            <span class="material-icons" style="font-size: 20px;">facebook</span>
                        </button>

                        <button class="btn-social-compact btn-apple" onclick="loginSocial('Apple')" title="Continuar with Apple">
                            <span class="material-icons" style="font-size: 20px;">phone_iphone</span>
                        </button>

                        <button class="btn-social-compact btn-phone" onclick="abrirModalTelefono()" title="Iniciar sesión con Teléfono">
                            <span class="material-icons" style="font-size: 20px;">phone</span>
                        </button>
                    </div>
                </div>
            </div>
            
<div class="card" id="panelCliente" style="display: none;">
    <div class="card-title">
        <div class="card-title-text">
            <span class="material-icons">local_shipping</span> Solicitar Servicio / Domicilio
        </div>
    </div>  

    <!-- Botones estéticos y animados de Cerrar Sesión y Editar Perfil -->
    <div class="client-action-buttons">
        <button onclick="cerrarSesionUsuario()" class="btn-modern-logout" title="Cerrar sesión de usuario">
            <span class="material-icons" style="font-size: 16px;">logout</span> Salir
        </button>
        
        <button onclick="abrirModalPerfil()" class="btn-modern-edit" title="Editar perfil de usuario">
            <span class="material-icons" style="font-size: 16px;">edit</span> Editar Perfil
        </button>
    </div>

                <div class="user-badge" id="userBadgeInfo">
                    <div style="display: flex; align-items: center; gap: 10px;">
                        <img id="badgeAvatarImg" src="https://via.placeholder.com/150" alt="Avatar" class="user-avatar-preview">
                        <div>
                            <strong id="badgeNombreUser" style="color: #fff;">-</strong><br>
                            <span id="badgeDocUser" style="color: var(--texto-secundario); font-size: 0.78rem;">-</span>
                        </div>
                    </div>
                </div>

                <!-- SECCIÓN: SELECTOR DE TIPO DE SERVICIO -->
                <label style="font-size: 0.8rem; color: var(--dorado-brillante); font-weight: 700; display: block; margin-bottom: 6px; text-transform: uppercase;">
                    🎯 Tipo de Servicio
                </label>
                <select id="selectTipoServicio" class="input-custom" onchange="cambiarTipoServicio()">
                    <option value="transporte">🛵 Transporte de Pasajeros</option>
                    <option value="domicilio">📦 Servicio de Domicilios / Envíos</option>
                </select>

                <!-- CAMPOS PARA TRANSPORTE -->
                <div id="grupoTransporte">
                    <input type="text" id="inputOrigen" class="input-custom" placeholder=" ¿Dónde te recogemos? (Dirección de Origen)" autocomplete="off">
                    <input type="text" id="inputDestino" class="input-custom" placeholder=" ¿A dónde deseas ir? (Dirección de Destino)" autocomplete="off">
                </div>

                <!-- CAMPOS PARA DOMICILIOS -->
                <div id="grupoDomicilio" style="display: none;">
                    <input type="text" id="inputEstablecimiento" class="input-custom" placeholder=" Establecimiento o Negocio (Ej: Restaurante, Tienda...)" autocomplete="off">
                    <input type="text" id="inputDetallePedido" class="input-custom" placeholder=" Detalle del pedido / Productos a reclamar" autocomplete="off">
                    <input type="text" id="inputDirRecogidaDom" class="input-custom" placeholder=" Dirección de Recogida (Negocio / Origen)" autocomplete="off">
                    <input type="text" id="inputDirEntregaDom" class="input-custom" placeholder=" Dirección de Entrega Final (Cliente)" autocomplete="off">
                </div>
                
                <div style="position: relative;">
                    <input type="number" id="inputCosto" class="input-custom" placeholder=" Valor Ofrecido ($)" value="4500" min="4500" step="500" autocomplete="off">
                    <span style="font-size: 0.90rem; color: var(--dorado-brillante); display: block; margin-top: -8px; margin-bottom: 12px; padding-left: 8px;">
                        * La tarifa mínima del servicio es de $4.500
                    </span>
                </div>

                <div class="info-box" id="infoUbicacion">
                    💡 Ingresa los datos completos del servicio y el valor ofrecido para coordinar con un domiciliario.
                </div>

                <button class="btn-action btn-solicitar" id="btnSolicitar" onclick="solicitarServicio()">
                    <span class="material-icons">local_shipping</span> Solicitar Servicio
                </button>
                
                <button class="btn-action btn-cancelar" id="btnCancelarCliente" onclick="cancelarServicio()" style="display: none;">
                    <span class="material-icons">cancel</span> Cancelar Solicitud
                </button>

                <button class="btn-action btn-solicitar" id="btnConductorLlego" onclick="conductorLlegoServicio()" style="display: none; margin-top: 8px; background: linear-gradient(135deg, #00d2ff 0%, #3a7bd5 100%); color: #fff;">
                    <span class="material-icons">check_circle_outline</span> El Conductor/Domiciliario Ya Llegó
                </button>

                <div id="panelOpcionesContraoferta" style="display: none; margin-top: 10px; gap: 10px; flex-direction: column;">
                    <button class="btn-action btn-solicitar" onclick="aceptarPrecioPropuestoCliente()">
                        <span class="material-icons">check_circle</span> Aceptar Nuevo Precio ($<span id="txtNuevoPrecioBoton">0</span>)
                    </button>
                    <button class="btn-action btn-cancelar" onclick="cancelarServicio()" style="margin-top: 0;">
                        <span class="material-icons">cancel</span> Rechazar y Cancelar Servicio
                    </button>
                </div>

                <div id="statusClienteContainer" style="display: none;">
                    <div class="codigo-badge-box">
                        <div style="font-size: 0.75rem; color: var(--texto-secundario); font-weight: 700; text-transform: uppercase;">
                            🔒 Código de Verificación
                        </div>
                        <div class="codigo-num" id="clienteCodigoDisplay">----</div>
                        <div style="font-size: 0.72rem; color: var(--dorado-brillante);">
                            Muestra este código al domiciliario al recibir tu pedido.
                        </div>
                    </div>

                    <div id="statusInteractiveBox" class="status-container-interactive status-espera-animated">
                        <div class="status-icon-box">
                            <span class="material-icons spin-icon" id="statusIcon">hourglass_top</span>
                        </div>
                        <div>
                            <div style="font-size: 1.05rem; font-weight: 800;" id="statusTitle">Esperando confirmación del domiciliario...</div>
                            
                            <div class="precio-destacado-box" id="precioDestacadoBox">
                                <span class="precio-label" id="precioLabelText">VALOR PROPUESTO</span>
                                <div class="precio-val-num">
                                    <span class="material-icons" style="font-size:22px;">payments</span>
                                    <span id="statusCostoDisplay">$4.500</span>
                                </div>
                                <div id="badgePrecioUpdate" class="badge-actualizado" style="display:none;">¡Actualizado!</div>
                            </div>

                            <div style="font-size: 0.82rem; color: var(--texto-secundario); margin-top: 2px;" id="statusSubtext">Música de espera activa. Buscando unidad de domicilio cercana.</div>
                        </div>
                    </div>
                </div>

                <div class="payment-group">
                    <div style="font-size: 0.8rem; color: var(--texto-secundario); margin-bottom: 8px; font-weight: 700; text-transform: uppercase;">
                        💳 Métodos de Pago Digital
                    </div>
                    <a href="https://checkout.nequi.wompi.co/l/b2z8AH" target="_blank" class="btn-action btn-pago-nequi">
                        <span class="material-icons">account_balance_wallet</span> Nequi
                    </a>

                    <button class="btn-action btn-pago-qr" onclick="abrirModalQR()">
                        <span class="material-icons">qr_code_scanner</span> Pago por Código QR
                    </button>
                        
                    <div style="font-size: 0.8rem; color: var(--texto-secundario); margin-top: 15px; margin-bottom: 8px; font-weight: 700; text-transform: uppercase;">
                        🎧 Atención y Soporte
                    </div>

                    <div class="contact-group">
                        <a href="tel:3153903131" class="btn-action btn-llamar">
                            <span class="material-icons">headset_mic</span> Atención
                        </a>
                        <button class="btn-action btn-whatsapp" onclick="enviarSoportePagoWhatsApp()">
                            <span class="material-icons">receipt_long</span> Soporte
                        </button>
                    </div>

                    <button class="btn-action btn-terminos" onclick="abrirModalTerminos()">
                        <span class="material-icons">gavel</span> Términos y Condiciones
                    </button>
                </div>
            </div>

            <div class="card" id="panelDev" style="display: none;">
                <div class="card-title">
                    <div class="card-title-text">
                        <span class="material-icons">admin_panel_settings</span> Control Desarrollador
                    </div>
                    <button class="btn-dev-logout" onclick="salirDevMode()">
                        <span class="material-icons" style="font-size: 16px;">logout</span> Salir
                    </button>
                </div>
                
                <div class="info-box">
                    <strong>Clave de Acceso:</strong> Validada (0408)<br>
                    <strong>Estado del Sistema:</strong> Monitoreando Solicitudes y Domicilios
                </div>

                <div id="solicitudDevContainer">
                    <p style="color: var(--texto-secundario); font-size: 0.9rem; text-align: center; padding: 20px;">
                        No hay solicitudes pendientes en este momento.
                    </p>
                </div>

                <div class="finance-wrapper">
                    <div class="container-ingresos-neon">
                        <div class="ingresos-header">
                            <span class="ingresos-title">
                                <span class="material-icons" style="font-size:22px;">trending_up</span> Ingresos Confirmados
                            </span>
                            <span style="font-size: 0.65rem; background: var(--verde-verificado); color: #000; padding: 2px 6px; border-radius: 4px; font-weight: 900;">AUTOMÁTICO</span>
                        </div>

                        <div class="period-grid">
                            <div class="period-card ingresos-card">
                                <div class="period-label">Semana</div>
                                <div class="period-value ingreso" id="ingresoSemana">$0</div>
                            </div>
                            <div class="period-card ingresos-card">
                                <div class="period-label">Mes</div>
                                <div class="period-value ingreso" id="ingresoMes">$0</div>
                            </div>
                            <div class="period-card ingresos-card">
                                <div class="period-label">Año</div>
                                <div class="period-value ingreso" id="ingresoAno">$0</div>
                            </div>
                        </div>

                        <div class="gastos-lista-box" id="ingresosListaContainer"></div>
                    </div>

                    <div class="container-gastos-box">
                        <div class="gastos-header">
                            <span class="gastos-title">
                                <span class="material-icons" style="font-size:20px;">trending_down</span> Control de Gastos
                            </span>
                        </div>

                        <div class="period-grid">
                            <div class="period-card gastos-card">
                                <div class="period-label">Semana</div>
                                <div class="period-value gasto" id="gastoSemana">$0</div>
                            </div>
                            <div class="period-card gastos-card">
                                <div class="period-label">Mes</div>
                                <div class="period-value gasto" id="gastoMes">$0</div>
                            </div>
                            <div class="period-card gastos-card">
                                <div class="period-label">Año</div>
                                <div class="period-value gasto" id="gastoAno">$0</div>
                            </div>
                        </div>

                        <div class="sub-gastos-form">
                            <input type="text" id="gastoConcepto" class="input-custom" placeholder="Ej: Gasolina, Mantenimiento..." style="margin-bottom:0; font-size:0.8rem; padding:8px;">
                            <input type="number" id="gastoMonto" class="input-custom" placeholder="Valor ($)" style="margin-bottom:0; font-size:0.8rem; padding:8px; width:110px;">
                            <button class="btn-add-gasto" onclick="agregarGastoDev()">
                                <span class="material-icons" style="font-size:18px;">add</span>
                            </button>
                        </div>

                        <div class="gastos-lista-box" id="gastosListaContainer"></div>
                    </div>

                    <div class="card-utilidad-neta">
                        <div class="ganancia-header">
                            <span class="ganancia-title">
                                <span class="material-icons" style="font-size:22px;">diamond</span> GANANCIA NETA REAL
                            </span>
                            <span style="font-size: 0.65rem; background: var(--dorado-brillante); color: #000; padding: 2px 6px; border-radius: 4px; font-weight: 900;">BALANCE</span>
                        </div>

                        <div class="period-grid">
                            <div class="period-card ganancia-card">
                                <div class="period-label">Semana</div>
                                <div class="period-value ganancia" id="gananciaSemana">$0</div>
                            </div>
                            <div class="period-card ganancia-card">
                                <div class="period-label">Mes</div>
                                <div class="period-value ganancia" id="gananciaMes">$0</div>
                            </div>
                            <div class="period-card ganancia-card">
                                <div class="period-label">Año</div>
                                <div class="period-value ganancia" id="gananciaAno">$0</div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- MODAL REGISTRO (ID, EDAD, TELÉFONO OPCIONALES) -->
        <div class="modal-overlay" id="modalRegistroCompleto">
            <div class="modal-content" style="text-align: left;">
                <div style="text-align: center;">
                    <span class="material-icons" style="font-size: 40px; color: var(--verde-verificado);">how_to_reg</span>
                    <h3 style="color: var(--dorado-brillante); margin-top: 5px;">Registro de Cuenta</h3>
                    <p style="font-size: 0.8rem; color: var(--texto-secundario);">Ingresa tus datos para completar la creación de tu cuenta</p>
                </div>

                <input type="email" id="regEmail" class="input-custom" placeholder="✉️ Correo Electrónico" autocomplete="email" style="margin-top: 12px;">
                <input type="password" id="regPassword" class="input-custom" placeholder="🔒 Contraseña" autocomplete="new-password">

                <label style="font-size: 0.75rem; color: var(--dorado-brillante); font-weight: 700; display: block; margin-bottom: 4px;">👤 Nombre Completo:</label>
                <input type="text" id="regNombre" class="input-custom" placeholder="Tu nombre y apellido">

                <label style="font-size: 0.75rem; color: var(--dorado-brillante); font-weight: 700; display: block; margin-bottom: 4px;">🆔 Número de ID / Documento (Opcional):</label>
                <input type="text" id="regIdDoc" class="input-custom" placeholder="Ej: C.C. 12345678">

                <label style="font-size: 0.75rem; color: var(--dorado-brillante); font-weight: 700; display: block; margin-bottom: 4px;">🎂 Edad (Opcional):</label>
                <input type="number" id="regEdad" class="input-custom" placeholder="Ej: 25">

                <label style="font-size: 0.75rem; color: var(--dorado-brillante); font-weight: 700; display: block; margin-bottom: 4px;">📱 Número de Teléfono (Opcional):</label>
                <input type="tel" id="regTelefono" class="input-custom" placeholder="Ej: 3101234567">

                <div style="display: flex; gap: 10px; margin-top: 10px;">
                    <button class="btn-action btn-solicitar" onclick="ejecutarRegistroCompleto()">
                        <span class="material-icons">person_add</span> Registrar Cuenta
                    </button>
                    <button class="btn-action btn-cancelar" onclick="cerrarModalRegistroCompleto()" style="margin-top:0;">
                        <span class="material-icons">close</span> Cancelar
                    </button>
                </div>
            </div>
        </div>

        <!-- MODAL EDITAR PERFIL -->
        <div class="modal-overlay" id="modalPerfil">
            <div class="modal-content" style="text-align: left;">
                <div style="text-align: center;">
                    <span class="material-icons" style="font-size: 40px; color: var(--dorado-brillante);">manage_accounts</span>
                    <h3 style="color: var(--dorado-brillante); margin-top: 5px;">Editar Perfil de Usuario</h3>
                    <p style="font-size: 0.8rem; color: var(--texto-secundario);">Actualiza tu información personal y foto de perfil</p>
                </div>

                <div style="display: flex; flex-direction: column; align-items: center; margin: 12px 0;">
                    <img id="previewAvatarModal" src="https://via.placeholder.com/150" alt="Vista previa avatar" style="width: 70px; height: 70px; border-radius: 50%; object-fit: cover; border: 2px solid var(--dorado-brillante); margin-bottom: 8px;">
                    <label style="font-size: 0.78rem; color: var(--dorado-brillante); font-weight: 700; cursor: pointer; background: rgba(212,175,55,0.15); padding: 6px 12px; border-radius: 8px; border: 1px dashed var(--dorado-brillante);">
                        📷 Subir Foto de Usuario
                        <input type="file" id="inputFotoPerfil" accept="image/*" style="display: none;" onchange="procesarFotoPerfil(this)">
                    </label>
                </div>

                <label style="font-size: 0.75rem; color: var(--dorado-brillante); font-weight: 700; display: block; margin-bottom: 4px;">👤 Nombre de Usuario:</label>
                <input type="text" id="editNombre" class="input-custom" placeholder="Tu nombre completo">

                <label style="font-size: 0.75rem; color: var(--dorado-brillante); font-weight: 700; display: block; margin-bottom: 4px;">🆔 Número de ID / Documento (Opcional):</label>
                <input type="text" id="editIdDoc" class="input-custom" placeholder="Ej: C.C. 12345678">

                <label style="font-size: 0.75rem; color: var(--dorado-brillante); font-weight: 700; display: block; margin-bottom: 4px;">📱 Teléfono (Opcional):</label>
                <input type="tel" id="editTelefono" class="input-custom" placeholder="Tu número celular">

                <label style="font-size: 0.75rem; color: var(--dorado-brillante); font-weight: 700; display: block; margin-bottom: 4px;">🎂 Edad (Opcional):</label>
                <input type="number" id="editEdad" class="input-custom" placeholder="Ej: 25">

                <label style="font-size: 0.75rem; color: var(--dorado-brillante); font-weight: 700; display: block; margin-bottom: 4px;">🏠 Dirección de Vivienda (Opcional):</label>
                <input type="text" id="editDireccion" class="input-custom" placeholder="Ej: Calle 10 # 5-20">

                <label style="font-size: 0.75rem; color: var(--dorado-brillante); font-weight: 700; display: block; margin-bottom: 4px;">🚨 Contacto de Emergencia (Opcional):</label>
                <input type="text" id="editEmergencia" class="input-custom" placeholder="Nombre y Teléfono de contacto">

                <div style="display: flex; gap: 10px; margin-top: 10px;">
                    <button class="btn-action btn-aceptar" onclick="guardarPerfilEditado()">
                        <span class="material-icons">save</span> Guardar Cambios
                    </button>
                    <button class="btn-action btn-cancelar" onclick="cerrarModalPerfil()" style="margin-top:0;">
                        <span class="material-icons">close</span> Cancelar
                    </button>
                </div>
            </div>
        </div>

        <div class="modal-overlay" id="modalDev">
            <div class="modal-content">
                <span class="material-icons" style="font-size: 48px; color: var(--dorado-brillante);">lock</span>
                <h3 style="color: var(--dorado-brillante); margin-top: 10px;">Acceso Desarrollador</h3>
                <p style="font-size: 0.85rem; color: var(--texto-secundario); margin-top: 5px;">Ingresa la clave de desarrollador:</p>
                
                <input type="password" id="inputClaveDev" class="input-code" placeholder="****" maxlength="4">
                
                <div style="display: flex; gap: 10px;">
                    <button class="btn-action btn-aceptar" onclick="validarClaveDev()">Ingresar</button>
                    <button class="btn-action btn-cancelar" onclick="cerrarModalDev()">Cerrar</button>
                </div>
            </div>
        </div>

        <div class="modal-overlay" id="modalTelefono">
            <div class="modal-content">
                <span class="material-icons" style="font-size: 48px; color: var(--verde-verificado);">phone</span>
                <h3 style="color: var(--dorado-brillante); margin-top: 10px;">Iniciar con Teléfono</h3>
                <p style="font-size: 0.85rem; color: var(--texto-secundario); margin-top: 5px;">Ingresa tu número con código de país (ej: +57...):</p>
                
                <input type="tel" id="inputTelefonoAuth" class="input-custom" placeholder="+57 3000000000" style="margin: 10px 0;">
                <div id="recaptcha-container"></div>

                <div id="panelCodigoSMS" style="display:none; margin-top:10px;">
                    <input type="text" id="inputCodigoSMS" class="input-custom" placeholder="Código de 6 dígitos" style="text-align:center; letter-spacing:3px;">
                    <button class="btn-action btn-aceptar" onclick="verificarCodigoSMS()">Verificar Código</button>
                </div>

                <div style="display: flex; gap: 10px; margin-top: 10px;" id="panelBtnEnviarSMS">
                    <button class="btn-action btn-aceptar" onclick="enviarCodigoSMS()">Enviar Código</button>
                    <button class="btn-action btn-cancelar" onclick="cerrarModalTelefono()">Cancelar</button>
                </div>
            </div>
        </div>

        <div class="modal-overlay" id="modalQR">
            <div class="modal-content">
                <span class="material-icons" style="font-size: 48px; color: var(--dorado-brillante);">qr_code_2</span>
                <h3 style="color: var(--dorado-brillante); margin-top: 5px;">Escanea para Pagar</h3>
                <p style="font-size: 0.85rem; color: var(--texto-secundario); margin-top: 5px;">
                    Escanea este código desde la app de tu banco o Nequi para realizar el pago:
                </p>
                
                <div class="qr-image-container">
     <img src="https://scontent.fnva2-1.fna.fbcdn.net/v/t39.30808-6/770562384_986398197754607_2505745652231744957_n.jpg?stp=dst-jpg_tt6&cstp=mx2048x2048&ctp=s2048x2048&_nc_cat=100&ccb=1-7&_nc_sid=6ee11a&_nc_ohc=gd7NfdbVzPkQ7kNvwHGK7H5&_nc_oc=AdodzRLiDqI2QJ0eyibdjtPC62g-B7mwzSfhZwPT5YSVgRFiUc-SFX9VU8vyvPyh_0s&_nc_zt=23&_nc_ht=scontent.fnva2-1.fna&_nc_gid=k8J_OO78tWO4R0LQjIh65g&_nc_ss=7b2a8&oh=00_AQFSWRM0EvWIG-KzYiSuC3Z-MUjtZjPzdzkgr0a-xHl3og&oe=6A897443" 
     alt="Modo Desarrollador" 
     class="btn-dev-img" 
     id="btnDevHeader" 
     title="Mantén presionado por 10 segundos para acceder">
                </div>

                <button class="btn-action btn-cancelar" onclick="cerrarModalQR()">
                    <span class="material-icons">close</span> Cerrar
                </button>
            </div>
        </div>  

        <div class="modal-overlay" id="modalTerminos">
            <div class="modal-content" style="text-align: left;">
                <div style="text-align: center;">
                    <span class="material-icons" style="font-size: 48px; color: var(--dorado-brillante);">description</span>
                    <h3 style="color: var(--dorado-brillante); margin-top: 5px;">Términos y Condiciones</h3>
                </div>
                
                <div class="info-box" style="margin-top: 15px; font-size: 0.8rem;">
                    <p style="margin-bottom: 8px;"><strong>1. Uso de la Plataforma:</strong> SegurApp Recorridos y Domicilios facilita el enlace directo entre usuarios y transportadores/domiciliarios.</p>
                    <p style="margin-bottom: 8px;"><strong>2. Tarifas Mínimas:</strong> La tarifa base mínima establecida para cualquier servicio o envío es de $4.500 COP.</p>
                    <p style="margin-bottom: 8px;"><strong>3. Código de Verificación:</strong> El usuario debe suministrar al domiciliario el código asignado al recibir su pedido o encomienda.</p>
                    <p style="margin-bottom: 8px;"><strong>4. Pagos Digitales:</strong> Los comprobantes de transferencias o transacciones Nequi deben enviarse al soporte asignado para validar la transacción.</p>
                </div>

                <button class="btn-action btn-aceptar" onclick="cerrarModalTerminos()" style="margin-top: 10px;">
                    <span class="material-icons">check</span> Entendido
                </button>
            </div>
        </div>

        <script>
            const DEV_KEY = "0408";
            const TARIFA_MINIMA = 4500;
            let tipoServicioActual = "transporte";
            let direccionOrigen = "";
            let barrioOrigen = "";
            let destinoTexto = "";
            let establecimientoDom = "";
            let detallePedidoDom = "";
            let costoRecorrido = 4500;
            let costoSugeridoCliente = 4500;
            let codigoServicio = "";

            let usuarioActual = null;
            let confirmationResultPhone = null;
            let avatarTempBase64 = "";

            let audioContext = null;
            let intervaloSonidoDev = null;
            let intervaloMusicaEspera = null;

            let estadoSolicitud = 'IDLE'; 
            let esModoDev = false;
            let yaContabilizadoActual = false;

            const canalServicio = new BroadcastChannel('segurapp_channel');

            function cambiarTipoServicio() {
                tipoServicioActual = document.getElementById("selectTipoServicio").value;
                const grupoTransporte = document.getElementById("grupoTransporte");
                const grupoDomicilio = document.getElementById("grupoDomicilio");
                const btnSolicitar = document.getElementById("btnSolicitar");

                if (tipoServicioActual === "domicilio") {
                    grupoTransporte.style.display = "none";
                    grupoDomicilio.style.display = "block";
                    btnSolicitar.innerHTML = `<span class="material-icons">local_shipping</span> Solicitar Domicilio`;
                } else {
                    grupoTransporte.style.display = "block";
                    grupoDomicilio.style.display = "none";
                    btnSolicitar.innerHTML = `<span class="material-icons">local_taxi</span> Solicitar Servicio`;
                }
            }

            function iniciarSesionCorreo() {
                const email = document.getElementById("authEmail").value.trim();
                const password = document.getElementById("authPassword").value;
                if(!email || !password) { alert("Ingresa correo y contraseña"); return; }

                window.firebaseFns.signInWithEmailAndPassword(window.firebaseAuth, email, password)
                    .then((userCredential) => {
                        manejarUsuarioAutenticado(userCredential.user);
                    })
                    .catch((error) => {
                        alert("Error al iniciar sesión: " + error.message);
                    });
            }

            function abrirModalRegistroCompleto() {
                document.getElementById("modalRegistroCompleto").classList.add("active");
            }

            function cerrarModalRegistroCompleto() {
                document.getElementById("modalRegistroCompleto").classList.remove("active");
            }

            function ejecutarRegistroCompleto() {
                const email = document.getElementById("regEmail").value.trim();
                const password = document.getElementById("regPassword").value;
                const nombre = document.getElementById("regNombre").value.trim();
                const numDoc = document.getElementById("regIdDoc").value.trim();
                const edad = document.getElementById("regEdad").value.trim();
                const telefono = document.getElementById("regTelefono").value.trim();

                if (!email || !password || !nombre) {
                    alert("⚠️ Por favor ingresa al menos el correo, la contraseña y tu nombre.");
                    return;
                }

                window.firebaseFns.createUserWithEmailAndPassword(window.firebaseAuth, email, password)
                    .then((userCredential) => {
                        const user = userCredential.user;
                        
                        // Recuperar o inicializar datos guardados localmente asociados a este email si existen, o crear nuevos
                        let perfilGuardado = JSON.parse(localStorage.getItem("segurapp_user_" + email)) || {};
                        
                        usuarioActual = {
                            email: email,
                            nombre: nombre || perfilGuardado.nombre || "Usuario",
                            tipoDoc: "C.C.",
                            numDoc: numDoc || perfilGuardado.numDoc || "No registrado",
                            telefono: telefono || perfilGuardado.telefono || "No registrado",
                            edad: edad || perfilGuardado.edad || "No especificada",
                            direccion: perfilGuardado.direccion || "",
                            emergencia: perfilGuardado.emergencia || "",
                            avatar: perfilGuardado.avatar || "https://via.placeholder.com/150"
                        };
                        
                        // Guardado local persistente vinculado a la sesión/correo y general
                        localStorage.setItem("segurapp_user_" + email, JSON.stringify(usuarioActual));
                        localStorage.setItem("segurapp_user", JSON.stringify(usuarioActual));

                        cerrarModalRegistroCompleto();
                        alert("¡Cuenta creada y datos guardados localmente con éxito!");
                        cargarUIUsuario();
                    })
                    .catch((error) => {
                        alert("Error al registrar: " + error.message);
                    });
            }

            function loginSocial(providerName) {
                const provider = window.authProviders[providerName];
                if(!provider) return;

                window.firebaseFns.signInWithPopup(window.firebaseAuth, provider)
                    .then((result) => {
                        const user = result.user;
                        manejarUsuarioAutenticado(user);
                    }).catch((error) => {
                        alert(`Error con inicio de sesión ${providerName}: ` + error.message);
                    });
            }

            function abrirModalTelefono() {
                document.getElementById("modalTelefono").classList.add("active");
                if (!window.recaptchaVerifier) {
                    try {
                        window.recaptchaVerifier = new window.firebaseFns.RecaptchaVerifier(window.firebaseAuth, 'recaptcha-container', {
                            'size': 'invisible'
                        });
                    } catch(e) {}
                }
            }

            function cerrarModalTelefono() {
                document.getElementById("modalTelefono").classList.remove("active");
            }

            function enviarCodigoSMS() {
                const telefono = document.getElementById("inputTelefonoAuth").value.trim();
                if(!telefono) { alert("Ingresa un número de teléfono válido"); return; }

                const appVerifier = window.recaptchaVerifier;
                window.firebaseFns.signInWithPhoneNumber(window.firebaseAuth, telefono, appVerifier)
                    .then((confirmationResult) => {
                        confirmationResultPhone = confirmationResult;
                        document.getElementById("panelCodigoSMS").style.display = "block";
                        document.getElementById("panelBtnEnviarSMS").style.display = "none";
                        alert("¡Código SMS enviado con éxito!");
                    }).catch((error) => {
                        alert("Error al enviar código SMS: " + error.message);
                    });
            }

            function verificarCodigoSMS() {
                const codigo = document.getElementById("inputCodigoSMS").value.trim();
                if(!codigo) { alert("Ingresa el código recibido"); return; }

                confirmationResultPhone.confirm(codigo).then((result) => {
                    cerrarModalTelefono();
                    const user = result.user;
                    manejarUsuarioAutenticado(user);
                }).catch((error) => {
                    alert("Código incorrecto o expirado: " + error.message);
                });
            }

            function manejarUsuarioAutenticado(user) {
                const identificadorKey = user.email || user.phoneNumber || user.uid;
                const userSavedStr = localStorage.getItem("segurapp_user_" + identificadorKey) || localStorage.getItem("segurapp_user");
                
                if (userSavedStr) {
                    usuarioActual = JSON.parse(userSavedStr);
                } else {
                    usuarioActual = {
                        email: user.email || user.phoneNumber || "No registrado",
                        nombre: user.displayName || user.email || user.phoneNumber || "Usuario Verificado",
                        tipoDoc: "C.C.",
                        numDoc: "No registrado",
                        telefono: user.phoneNumber || user.email || "No registrado",
                        edad: "No especificada",
                        direccion: "",
                        emergencia: "",
                        avatar: user.photoURL || "https://via.placeholder.com/150"
                    };
                }
                
                // Persistir de forma local intacta
                localStorage.setItem("segurapp_user_" + identificadorKey, JSON.stringify(usuarioActual));
                localStorage.setItem("segurapp_user", JSON.stringify(usuarioActual));
                
                cargarUIUsuario();
            }

            function abrirModalPerfil() {
                if (!usuarioActual) return;
                document.getElementById("editNombre").value = usuarioActual.nombre || "";
                document.getElementById("editIdDoc").value = usuarioActual.numDoc === "No registrado" ? "" : (usuarioActual.numDoc || "");
                document.getElementById("editTelefono").value = usuarioActual.telefono === "No registrado" ? "" : (usuarioActual.telefono || "");
                document.getElementById("editEdad").value = usuarioActual.edad === "No especificada" ? "" : (usuarioActual.edad || "");
                document.getElementById("editDireccion").value = usuarioActual.direccion || "";
                document.getElementById("editEmergencia").value = usuarioActual.emergencia || "";
                
                avatarTempBase64 = usuarioActual.avatar || "https://via.placeholder.com/150";
                document.getElementById("previewAvatarModal").src = avatarTempBase64;

                document.getElementById("modalPerfil").classList.add("active");
            }

            function cerrarModalPerfil() {
                document.getElementById("modalPerfil").classList.remove("active");
            }

            function procesarFotoPerfil(input) {
                if (input.files && input.files[0]) {
                    const reader = new FileReader();
                    reader.onload = function(e) {
                        avatarTempBase64 = e.target.result;
                        document.getElementById("previewAvatarModal").src = avatarTempBase64;
                    }
                    reader.readAsDataURL(input.files[0]);
                }
            }

            function guardarPerfilEditado() {
                const nombre = document.getElementById("editNombre").value.trim();
                const idDoc = document.getElementById("editIdDoc").value.trim();
                const telefono = document.getElementById("editTelefono").value.trim();
                const edad = document.getElementById("editEdad").value.trim();
                const direccion = document.getElementById("editDireccion").value.trim();
                const emergencia = document.getElementById("editEmergencia").value.trim();

                if (!nombre) {
                    alert("⚠️ El nombre de usuario es obligatorio.");
                    return;
                }

                usuarioActual.nombre = nombre;
                usuarioActual.numDoc = idDoc || "No registrado";
                usuarioActual.telefono = telefono || "No registrado";
                usuarioActual.edad = edad || "No especificada";
                usuarioActual.direccion = direccion;
                usuarioActual.emergencia = emergencia;
                if (avatarTempBase64) {
                    usuarioActual.avatar = avatarTempBase64;
                }

                // Guardado local permanente e intacto
                const identificadorKey = usuarioActual.email || usuarioActual.telefono;
                if (identificadorKey) {
                    localStorage.setItem("segurapp_user_" + identificadorKey, JSON.stringify(usuarioActual));
                }
                localStorage.setItem("segurapp_user", JSON.stringify(usuarioActual));

                cerrarModalPerfil();
                cargarUIUsuario();
                alert("¡Información de perfil guardada localmente con éxito!");
            }

            function obtenerEstadisticas() {
                const stats = localStorage.getItem("segurapp_stats");
                if (stats) {
                    return JSON.parse(stats);
                }
                return { confirmadas: 0, rechazadas: 0, historial: [], gastos: [] };
            }

            function guardarEstadisticas(stats) {
                localStorage.setItem("segurapp_stats", JSON.stringify(stats));
                calcularYRenderizarFinanzas();
            }

            function registrarResultadoSolicitud(resultado) {
                if (yaContabilizadoActual) return;

                const stats = obtenerEstadisticas();
                const fechaActual = new Date();

                if (resultado === 'CONFIRMADA') {
                    stats.confirmadas++;
                } else if (resultado === 'RECHAZADA') {
                    stats.rechazadas++;
                }

                stats.historial.push({
                    id: Date.now(),
                    codigo: codigoServicio,
                    tipo: tipoServicioActual,
                    usuario: usuarioActual ? usuarioActual.nombre : "Anónimo",
                    origen: direccionOrigen,
                    destino: destinoTexto,
                    establecimiento: establecimientoDom,
                    detalle: detallePedidoDom,
                    valor: costoRecorrido,
                    resultado: resultado,
                    fecha: fechaActual.toISOString()
                });

                yaContabilizadoActual = true;
                guardarEstadisticas(stats);

                if (resultado === 'CONFIRMADA') {
                    animarIncrementoIngreso();
                }
            }

            function calcularYRenderizarFinanzas() {
                const stats = obtenerEstadisticas();
                const ahora = new Date();

                let ingSem = 0, ingMes = 0, ingAno = 0;
                let gasSem = 0, gasMes = 0, gasAno = 0;

                if (stats.historial) {
                    stats.historial.forEach(item => {
                        if (item.resultado === 'CONFIRMADA') {
                            const d = new Date(item.fecha);
                            const diffDias = (ahora - d) / (1000 * 60 * 60 * 24);

                            if (diffDias <= 7) ingSem += item.valor;
                            if (d.getMonth() === ahora.getMonth() && d.getFullYear() === ahora.getFullYear()) ingMes += item.valor;
                            if (d.getFullYear() === ahora.getFullYear()) ingAno += item.valor;
                        }
                    });
                }

                if (stats.gastos) {
                    stats.gastos.forEach(g => {
                        const d = new Date(g.fecha);
                        const diffDias = (ahora - d) / (1000 * 60 * 60 * 24);

                        if (diffDias <= 7) gasSem += g.monto;
                        if (d.getMonth() === ahora.getMonth() && d.getFullYear() === ahora.getFullYear()) gasMes += g.monto;
                        if (d.getFullYear() === ahora.getFullYear()) gasAno += g.monto;
                    });
                }

                document.getElementById("ingresoSemana").innerText = `$${ingSem.toLocaleString('es-CO')}`;
                document.getElementById("ingresoMes").innerText = `$${ingMes.toLocaleString('es-CO')}`;
                document.getElementById("ingresoAno").innerText = `$${ingAno.toLocaleString('es-CO')}`;

                document.getElementById("gastoSemana").innerText = `$${gasSem.toLocaleString('es-CO')}`;
                document.getElementById("gastoMes").innerText = `$${gasMes.toLocaleString('es-CO')}`;
                document.getElementById("gastoAno").innerText = `$${gasAno.toLocaleString('es-CO')}`;

                document.getElementById("gananciaSemana").innerText = `$${(ingSem - gasSem).toLocaleString('es-CO')}`;
                document.getElementById("gananciaMes").innerText = `$${(ingMes - gasMes).toLocaleString('es-CO')}`;
                document.getElementById("gananciaAno").innerText = `$${(ingAno - gasAno).toLocaleString('es-CO')}`;

                renderizarListaGastos(stats.gastos || []);
                renderizarListaIngresos(stats.historial || []);
            }

            function agregarGastoDev() {
                const concepto = document.getElementById("gastoConcepto").value.trim();
                const monto = parseFloat(document.getElementById("gastoMonto").value);

                if (!concepto || isNaN(monto) || monto <= 0) {
                    alert("Por favor ingresa un concepto y un valor válido de gasto.");
                    return;
                }

                const stats = obtenerEstadisticas();
                if (!stats.gastos) stats.gastos = [];

                stats.gastos.push({
                    id: Date.now(),
                    concepto: concepto,
                    monto: monto,
                    fecha: new Date().toISOString()
                });

                document.getElementById("gastoConcepto").value = "";
                document.getElementById("gastoMonto").value = "";

                guardarEstadisticas(stats);
            }

            function eliminarGastoDev(id) {
                const stats = obtenerEstadisticas();
                if (stats.gastos) {
                    stats.gastos = stats.gastos.filter(g => g.id !== id);
                    guardarEstadisticas(stats);
                }
            }

            function eliminarIngresoDev(id) {
                const stats = obtenerEstadisticas();
                if (stats.historial) {
                    const index = stats.historial.findIndex(item => item.id === id);
                    if (index !== -1) {
                        if (stats.historial[index].resultado === 'CONFIRMADA' && stats.confirmadas > 0) {
                            stats.confirmadas--;
                        }
                        stats.historial.splice(index, 1);
                        guardarEstadisticas(stats);
                    }
                }
            }

            function renderizarListaGastos(gastos) {
                const container = document.getElementById("gastosListaContainer");
                if (!gastos || gastos.length === 0) {
                    container.innerHTML = `<p style="font-size: 0.72rem; color: var(--texto-secundario); text-align: center; padding: 4px;">Sin gastos registrados.</p>`;
                    return;
                }

                let html = "";
                [...gastos].reverse().forEach(g => {
                    html += `
                        <div class="gasto-item-row">
                            <div>
                                <strong style="color:#fff;">${g.concepto}</strong>
                                <span style="color:var(--rojo-alerta); font-weight:800; margin-left:6px;">-$${g.monto.toLocaleString('es-CO')}</span>
                            </div>
                            <button onclick="eliminarGastoDev(${g.id})" style="background:none; border:none; color:var(--rojo-alerta); cursor:pointer;" title="Eliminar gasto">
                                <span class="material-icons" style="font-size:16px;">delete</span>
                            </button>
                        </div>
                    `;
                });
                container.innerHTML = html;
            }

            function renderizarListaIngresos(historial) {
                const container = document.getElementById("ingresosListaContainer");
                const confirmados = historial.filter(item => item.resultado === 'CONFIRMADA');
                
                if (confirmados.length === 0) {
                    container.innerHTML = `<p style="font-size: 0.72rem; color: var(--texto-secundario); text-align: center; padding: 4px;">Sin ingresos confirmados registrados.</p>`;
                    return;
                }

                let html = "";
                [...confirmados].reverse().forEach(item => {
                    const fechaFormateada = new Date(item.fecha).toLocaleDateString('es-CO', {month:'short', day:'numeric', hour:'2-digit', minute:'2-digit'});
                    const etiquetaTipo = item.tipo === 'domicilio' ? '📦 [DOMICILIO]' : '🛵 [TRANSPORTE]';
                    const detalleRuta = item.tipo === 'domicilio' ? `${item.establecimiento} (${item.detalle}) ➔ ${item.origen}` : `${item.origen} ➔ ${item.destino}`;
                    
                    html += `
                        <div class="gasto-item-row" style="border-left-color: var(--verde-verificado);">
                            <div>
                                <strong style="color:#fff;">${etiquetaTipo} [${item.codigo}] ${detalleRuta}</strong>
                                <div style="font-size:0.7rem; color:var(--texto-secundario);">${item.usuario} • ${fechaFormateada}</div>
                                <span style="color:var(--verde-verificado); font-weight:800;">+$${item.valor.toLocaleString('es-CO')}</span>
                            </div>
                            <button onclick="eliminarIngresoDev(${item.id})" style="background:none; border:none; color:var(--rojo-alerta); cursor:pointer;" title="Eliminar ingreso">
                                <span class="material-icons" style="font-size:16px;">delete</span>
                            </button>
                        </div>
                    `;
                });
                container.innerHTML = html;
            }

            function animarIncrementoIngreso() {
                const els = [
                    document.getElementById("ingresoSemana"), document.getElementById("ingresoMes"), document.getElementById("ingresoAno"),
                    document.getElementById("gananciaSemana"), document.getElementById("gananciaMes"), document.getElementById("gananciaAno")
                ];
                els.forEach(el => {
                    if(el) {
                        el.classList.remove("anim-incremento");
                        void el.offsetWidth;
                        el.classList.add("anim-incremento");
                    }
                });
            }

            canalServicio.onmessage = (event) => {
                if (event.data.tipo === 'CAMBIO_ESTADO') {
                    const previoCosto = costoRecorrido;
                    estadoSolicitud = event.data.estado;

                    if (event.data.datos) {
                        tipoServicioActual = event.data.datos.tipoServicioActual || tipoServicioActual;
                        barrioOrigen = event.data.datos.barrioOrigen || barrioOrigen;
                        direccionOrigen = event.data.datos.direccionOrigen || direccionOrigen;
                        destinoTexto = event.data.datos.destinoTexto || destinoTexto;
                        establecimientoDom = event.data.datos.establecimientoDom || establecimientoDom;
                        detallePedidoDom = event.data.datos.detallePedidoDom || detallePedidoDom;
                        costoRecorrido = event.data.datos.costoRecorrido || costoRecorrido;
                        costoSugeridoCliente = event.data.datos.costoSugeridoCliente || costoSugeridoCliente;
                        codigoServicio = event.data.datos.codigoServicio || codigoServicio;
                        if (event.data.datos.usuario) {
                            usuarioActual = event.data.datos.usuario;
                        }
                    }

                    if (estadoSolicitud === 'SOLICITADO') {
                        if (!esModoDev) {
                            mostrarUIEsperaCliente();
                            iniciarMusicaEsperaCliente();
                            if (previoCosto !== costoRecorrido) animarCambioPrecioCliente(true);
                        } else {
                            actualizarVistaDev();
                            iniciarSonidoAlertaDev();
                        }
                    } else if (estadoSolicitud === 'OFERTA_DESARROLLADOR') {
                        detenerTodosLosSonidos();
                        if (!esModoDev) {
                            mostrarUIOfertaDesarrollador();
                            reproducirSonidoExitoCliente();
                            animarCambioPrecioCliente(true);
                        } else {
                            actualizarVistaDev();
                        }
                    } else if (estadoSolicitud === 'IDLE') {
                        detenerTodosLosSonidos();
                        if (!esModoDev) {
                            document.getElementById("btnSolicitar").style.display = "flex";
                            document.getElementById("btnCancelarCliente").style.display = "none";
                            document.getElementById("btnConductorLlego").style.display = "none";
                            document.getElementById("panelOpcionesContraoferta").style.display = "none";
                            document.getElementById("statusClienteContainer").style.display = "none";
                        } else {
                            actualizarVistaDev();
                        }
                    } else if (estadoSolicitud === 'ACEPTADO') {
                        detenerTodosLosSonidos();
                        if (!esModoDev) {
                            mostrarUIAceptadoCliente();
                            reproducirSonidoExitoCliente();
                            if (previoCosto !== costoRecorrido) animarCambioPrecioCliente(true);
                        } else {
                            actualizarVistaDev();
                        }
                    } else if (estadoSolicitud === 'LLEGADO') {
                        detenerTodosLosSonidos();
                        if (!esModoDev) {
                            mostrarUILlegadoCliente();
                        } else {
                            actualizarVistaDev();
                        }
                    }
                }
            };

            function notificarCambioEstado() {
                canalServicio.postMessage({
                    tipo: 'CAMBIO_ESTADO',
                    estado: estadoSolicitud,
                    datos: { tipoServicioActual, barrioOrigen, direccionOrigen, destinoTexto, establecimientoDom, detallePedidoDom, costoRecorrido, costoSugeridoCliente, codigoServicio, usuario: usuarioActual }
                });
            }

            function generarCodigoAleatorio() {
                return Math.floor(1000 + Math.random() * 9000).toString();
            }

            function cargarUIUsuario() {
                const userSaved = localStorage.getItem("segurapp_user");
                if (userSaved) {
                    usuarioActual = JSON.parse(userSaved);
                    document.getElementById("panelRegistro").style.display = "none";
                    document.getElementById("panelCliente").style.display = "block";
                    document.getElementById("badgeNombreUser").innerText = usuarioActual.nombre;
                    document.getElementById("badgeDocUser").innerText = `ID: ${usuarioActual.numDoc} | Edad: ${usuarioActual.edad} | Tel: ${usuarioActual.telefono}`;
                    
                    if (usuarioActual.avatar) {
                        document.getElementById("badgeAvatarImg").src = usuarioActual.avatar;
                    }
                } else {
                    document.getElementById("panelRegistro").style.display = "block";
                    document.getElementById("panelCliente").style.display = "none";
                }
            }

            function cerrarSesionUsuario() {
                if(window.firebaseAuth) {
                    window.firebaseFns.signOut(window.firebaseAuth).catch(() => {});
                }
                // Mantenemos la data local en localStorage para que al reingresar quede intacta como se solicitó
                usuarioActual = null;
                document.getElementById("panelCliente").style.display = "none";
                document.getElementById("panelRegistro").style.display = "block";
            }

            function abrirModalQR() {
                document.getElementById("modalQR").classList.add("active");
            }

            function cerrarModalQR() {
                document.getElementById("modalQR").classList.remove("active");
            }

            function abrirModalTerminos() {
                document.getElementById("modalTerminos").classList.add("active");
            }

            function cerrarModalTerminos() {
                document.getElementById("modalTerminos").classList.remove("active");
            }

            function enviarSoportePagoWhatsApp() {
                const usr = usuarioActual || { nombre: "Cliente", numDoc: "-" };
                const mensaje = `*SOPORTE DE PAGO - SEGURAPP*\n\n` +
                    ` *Cliente:* ${usr.nombre}\n` +
                    ` *Documento/ID:* ${usr.numDoc}\n` +
                    ` *Código Servicio:* ${codigoServicio || "Sin código activo"}\n\n` +
                    `Adjunto mi comprobante de pago para verificación.`;
                
                const url = `https://wa.me/573153903131?text=${encodeURIComponent(mensaje)}`;
                window.open(url, '_blank');
            }

            function iniciarMusicaEsperaCliente() {
                if (esModoDev) return;
                detenerTodosLosSonidos();

                try { audioContext = new (window.AudioContext || window.webkitAudioContext)(); } catch(e) { return; }

                const acorde = [261.63, 329.63, 392.00, 493.88];
                let notaIdx = 0;

                intervaloMusicaEspera = setInterval(() => {
                    if (!audioContext) return;
                    const ahora = audioContext.currentTime;
                    const freq = acorde[notaIdx % acorde.length];
                    
                    try {
                        let osc = audioContext.createOscillator();
                        let gain = audioContext.createGain();

                        osc.type = 'sine';
                        osc.frequency.setValueAtTime(freq, ahora);
                        gain.gain.setValueAtTime(0.001, ahora);
                        gain.gain.linearRampToValueAtTime(0.08, ahora + 0.3);
                        gain.gain.exponentialRampToValueAtTime(0.0001, ahora + 1.2);

                        osc.connect(gain);
                        gain.connect(audioContext.destination);

                        osc.start(ahora);
                        osc.stop(ahora + 1.2);
                    } catch(e) {}
                    notaIdx++;
                }, 600);
            }

            function iniciarSonidoAlertaDev() {
                if (!esModoDev) return;
                detenerTodosLosSonidos();

                try { audioContext = new (window.AudioContext || window.webkitAudioContext)(); } catch(e) { return; }

                intervaloSonidoDev = setInterval(() => {
                    if (!audioContext) return;
                    const ahora = audioContext.currentTime;
                    reproducirNota(audioContext, 659.25, ahora, 0.1, 'square');
                    reproducirNota(audioContext, 880.00, ahora + 0.1, 0.15, 'square');
                }, 800);
            }

            function reproducirSonidoExitoCliente() {
                if (esModoDev) return;
                detenerTodosLosSonidos();

                try {
                    const ctx = new (window.AudioContext || window.webkitAudioContext)();
                    const ahora = ctx.currentTime;
                    reproducirNota(ctx, 523.25, ahora, 0.15, 'sine');
                    reproducirNota(ctx, 659.25, ahora + 0.15, 0.15, 'sine');
                    reproducirNota(ctx, 783.99, ahora + 0.30, 0.35, 'sine');
                } catch(e) {}
            }

            function reproducirNota(ctx, freq, startTime, duracion, waveType = 'sine') {
                try {
                    let osc = ctx.createOscillator();
                    let gain = ctx.createGain();
                    osc.type = waveType;
                    osc.frequency.setValueAtTime(freq, startTime);
                    gain.gain.setValueAtTime(0.01, startTime);
                    gain.gain.linearRampToValueAtTime(0.2, startTime + 0.02);
                    gain.gain.exponentialRampToValueAtTime(0.001, startTime + duracion);
                    osc.connect(gain);
                    gain.connect(ctx.destination);
                    osc.start(startTime);
                    osc.stop(startTime + duracion);
                } catch(e) {}
            }

            function detenerTodosLosSonidos() {
                if (intervaloSonidoDev) { clearInterval(intervaloSonidoDev); intervaloSonidoDev = null; }
                if (intervaloMusicaEspera) { clearInterval(intervaloMusicaEspera); intervaloMusicaEspera = null; }
                if (audioContext) { audioContext.close(); audioContext = null; }
            }

            function animarCambioPrecioCliente(esAjusteExterno = false) {
                const box = document.getElementById("precioDestacadoBox");
                const badge = document.getElementById("badgePrecioUpdate");
                
                box.classList.remove("precio-cambiado");
                void box.offsetWidth; 
                box.classList.add("precio-cambiado");

                if (esAjusteExterno) {
                    badge.style.display = "block";
                    setTimeout(() => { badge.style.display = "none"; }, 3500);
                }
            }

            function mostrarUIEsperaCliente() {
                document.getElementById("btnSolicitar").style.display = "none";
                document.getElementById("btnCancelarCliente").style.display = "flex";
                document.getElementById("btnConductorLlego").style.display = "none";
                document.getElementById("panelOpcionesContraoferta").style.display = "none";
                document.getElementById("statusClienteContainer").style.display = "block";
                
                document.getElementById("clienteCodigoDisplay").innerText = codigoServicio;
                document.getElementById("precioLabelText").innerText = "VALOR PROPUESTO";
                document.getElementById("statusCostoDisplay").innerText = `$${Number(costoRecorrido).toLocaleString('es-CO')}`;

                const box = document.getElementById("statusInteractiveBox");
                box.className = "status-container-interactive status-espera-animated";

                document.getElementById("statusIcon").className = "material-icons spin-icon";
                document.getElementById("statusIcon").innerText = "hourglass_top";
                const tituloEspera = tipoServicioActual === 'domicilio' ? "Esperando confirmación del domiciliario..." : "Esperando confirmación del conductor...";
                document.getElementById("statusTitle").innerText = tituloEspera;
                document.getElementById("statusSubtext").innerText = "Música de espera activa. Buscando unidad cercana.";
                
                animarCambioPrecioCliente();
            }

            function mostrarUIOfertaDesarrollador() {
                document.getElementById("btnSolicitar").style.display = "none";
                document.getElementById("btnCancelarCliente").style.display = "none";
                document.getElementById("btnConductorLlego").style.display = "none";
                document.getElementById("panelOpcionesContraoferta").style.display = "flex";
                document.getElementById("txtNuevoPrecioBoton").innerText = Number(costoRecorrido).toLocaleString('es-CO');
                document.getElementById("statusClienteContainer").style.display = "block";

                document.getElementById("clienteCodigoDisplay").innerText = codigoServicio;
                document.getElementById("precioLabelText").innerText = "NUEVO VALOR REVISADO";
                document.getElementById("statusCostoDisplay").innerText = `$${Number(costoRecorrido).toLocaleString('es-CO')}`;

                const box = document.getElementById("statusInteractiveBox");
                box.className = "status-container-interactive status-oferta-animated";

                document.getElementById("statusIcon").className = "material-icons bounce-icon";
                document.getElementById("statusIcon").innerText = "price_change";
                const tituloOferta = tipoServicioActual === 'domicilio' ? "¡El domiciliario propone una nueva tarifa!" : "¡El conductor propone una nueva tarifa!";
                document.getElementById("statusTitle").innerText = tituloOferta;
                document.getElementById("statusSubtext").innerText = `El valor inicial fue ajustado a $${Number(costoRecorrido).toLocaleString('es-CO')}. ¿Deseas aceptar este precio?`;

                animarCambioPrecioCliente();
            }

            function mostrarUIAceptadoCliente() {
                document.getElementById("btnSolicitar").style.display = "none";
                document.getElementById("btnCancelarCliente").style.display = "flex";
                document.getElementById("btnConductorLlego").style.display = "flex";
                document.getElementById("panelOpcionesContraoferta").style.display = "none";
                document.getElementById("statusClienteContainer").style.display = "block";

                document.getElementById("clienteCodigoDisplay").innerText = codigoServicio;
                document.getElementById("precioLabelText").innerText = "VALOR ACORDADO";
                document.getElementById("statusCostoDisplay").innerText = `$${Number(costoRecorrido).toLocaleString('es-CO')}`;

                const box = document.getElementById("statusInteractiveBox");
                box.className = "status-container-interactive status-aceptado-animated";

                document.getElementById("statusIcon").className = "material-icons bounce-icon";
                document.getElementById("statusIcon").innerText = tipoServicioActual === 'domicilio' ? "local_shipping" : "directions_car";
                const tituloAceptado = tipoServicioActual === 'domicilio' ? "¡Tu domicilio va en camino!" : "¡El conductor ya va por ti!";
                document.getElementById("statusTitle").innerText = tituloAceptado;
                document.getElementById("statusSubtext").innerText = "Tu servicio fue asignado exitosamente y está en curso.";
                
                animarCambioPrecioCliente();
            }

            function mostrarUILlegadoCliente() {
                document.getElementById("btnSolicitar").style.display = "none";
                document.getElementById("btnCancelarCliente").style.display = "none";
                document.getElementById("btnConductorLlego").style.display = "none";
                document.getElementById("panelOpcionesContraoferta").style.display = "none";
                document.getElementById("statusClienteContainer").style.display = "block";

                const box = document.getElementById("statusInteractiveBox");
                box.className = "status-container-interactive status-aceptado-animated";

                document.getElementById("statusIcon").className = "material-icons";
                document.getElementById("statusIcon").innerText = "task_alt";
                const tituloLlegada = tipoServicioActual === 'domicilio' ? "¡Domicilio entregado con éxito!" : "¡Conductor llegado a destino!";
                document.getElementById("statusTitle").innerText = tituloLlegada;
                document.getElementById("statusSubtext").innerText = "Las alertas y tonos han sido desactivados exitosamente.";
            }

            function solicitarServicio() {
                const costoVal = parseFloat(document.getElementById("inputCosto").value);

                if (tipoServicioActual === "transporte") {
                    const origenVal = document.getElementById("inputOrigen").value.trim();
                    const destinoVal = document.getElementById("inputDestino").value.trim();
                    if (!origenVal) { alert("Por favor ingresa la dirección de origen."); return; }
                    if (!destinoVal) { alert("Por favor ingresa el lugar de destino."); return; }
                    direccionOrigen = origenVal;
                    destinoTexto = destinoVal;
                } else {
                    const estabVal = document.getElementById("inputEstablecimiento").value.trim();
                    const detalleVal = document.getElementById("inputDetallePedido").value.trim();
                    const dirRecogida = document.getElementById("inputDirRecogidaDom").value.trim();
                    const dirEntrega = document.getElementById("inputDirEntregaDom").value.trim();

                    if (!estabVal) { alert("Por favor ingresa el establecimiento o negocio."); return; }
                    if (!detalleVal) { alert("Por favor ingresa el detalle del pedido."); return; }
                    if (!dirRecogida) { alert("Por favor ingresa la dirección de recogida del domicilio."); return; }
                    if (!dirEntrega) { alert("Por favor ingresa la dirección de entrega del domicilio."); return; }

                    establecimientoDom = estabVal;
                    detallePedidoDom = detalleVal;
                    direccionOrigen = dirRecogida;
                    destinoTexto = dirEntrega;
                }
                
                if (isNaN(costoVal) || costoVal < TARIFA_MINIMA) {
                    alert(`La tarifa mínima para el servicio es de $${TARIFA_MINIMA.toLocaleString('es-CO')}.`);
                    document.getElementById("inputCosto").value = TARIFA_MINIMA;
                    return;
                }

                barrioOrigen = "Centro / Zona Urbana";
                costoRecorrido = costoVal;
                costoSugeridoCliente = costoVal;
                codigoServicio = generarCodigoAleatorio();
                estadoSolicitud = 'SOLICITADO';
                yaContabilizadoActual = false;

                mostrarUIEsperaCliente();
                iniciarMusicaEsperaCliente();
                
                actualizarVistaDev();
                notificarCambioEstado();
            }

            function cancelarServicio() {
                if (estadoSolicitud === 'SOLICITADO' || estadoSolicitud === 'OFERTA_DESARROLLADOR' || estadoSolicitud === 'ACEPTADO') {
                    registrarResultadoSolicitud('RECHAZADA');
                }

                estadoSolicitud = 'IDLE';
                codigoServicio = "";
                detenerTodosLosSonidos();

                document.getElementById("btnSolicitar").style.display = "flex";
                document.getElementById("btnCancelarCliente").style.display = "none";
                document.getElementById("btnConductorLlego").style.display = "none";
                document.getElementById("panelOpcionesContraoferta").style.display = "none";
                document.getElementById("statusClienteContainer").style.display = "none";

                actualizarVistaDev();
                notificarCambioEstado();
            }

            function conductorLlegoServicio() {
                estadoSolicitud = 'LLEGADO';
                detenerTodosLosSonidos();
                mostrarUILlegadoCliente();
                actualizarVistaDev();
                notificarCambioEstado();
            }

            function aceptarPrecioPropuestoCliente() {
                estadoSolicitud = 'ACEPTADO';
                registrarResultadoSolicitud('CONFIRMADA');
                detenerTodosLosSonidos();

                mostrarUIAceptadoCliente();
                reproducirSonidoExitoCliente();

                actualizarVistaDev();
                notificarCambioEstado();
            }

            function aceptarServicioDev() {
                const inputDev = document.getElementById("inputCostoDev");
                const nuevoPrecio = inputDev ? parseFloat(inputDev.value) : costoRecorrido;

                if (!isNaN(nuevoPrecio) && nuevoPrecio !== costoSugeridoCliente) {
                    costoRecorrido = nuevoPrecio;
                    estadoSolicitud = 'OFERTA_DESARROLLADOR';
                    detenerTodosLosSonidos();

                    if (!esModoDev) {
                        mostrarUIOfertaDesarrollador();
                        reproducirSonidoExitoCliente();
                    } else {
                        actualizarVistaDev();
                    }
                } else {
                    estadoSolicitud = 'ACEPTADO';
                    registrarResultadoSolicitud('CONFIRMADA');
                    detenerTodosLosSonidos();

                    if (!esModoDev) {
                        mostrarUIAceptadoCliente();
                        reproducirSonidoExitoCliente();
                    } else {
                        actualizarVistaDev();
                    }
                }

                notificarCambioEstado();
            }

            function ajustarPrecioDev() {
                const nuevoPrecioInput = document.getElementById("inputCostoDev");
                const nuevoPrecio = parseFloat(nuevoPrecioInput.value);

                if (isNaN(nuevoPrecio) || nuevoPrecio < TARIFA_MINIMA) {
                    alert(`El valor debe ser mínimo $${TARIFA_MINIMA.toLocaleString('es-CO')}.`);
                    return;
                }

                costoRecorrido = nuevoPrecio;
                alert(`Tarifa actualizada a $${Number(costoRecorrido).toLocaleString('es-CO')}`);

                if (costoRecorrido !== costoSugeridoCliente) {
                    estadoSolicitud = 'OFERTA_DESARROLLADOR';
                    detenerTodosLosSonidos();

                    if (!esModoDev) {
                        mostrarUIOfertaDesarrollador();
                        reproducirSonidoExitoCliente();
                    }
                }
                
                notificarCambioEstado();
                actualizarVistaDev();
            }

            function abrirModalDev() {
                document.getElementById("modalDev").classList.add("active");
                document.getElementById("inputClaveDev").focus();
            }

            function cerrarModalDev() {
                document.getElementById("modalDev").classList.remove("active");
                document.getElementById("inputClaveDev").value = "";
            }

            function validarClaveDev() {
                const clave = document.getElementById("inputClaveDev").value;
                const usuarioAutenticadoFirebase = window.firebaseAuth && window.firebaseAuth.currentUser;

                if (!usuarioAutenticadoFirebase) {
                    alert("⚠️ Acceso denegado: Debes iniciar sesión con una cuenta de Firebase antes de acceder al panel de desarrollador.");
                    cerrarModalDev();
                    return;
                }

                if (clave === DEV_KEY) {
                    esModoDev = true;
                    detenerTodosLosSonidos();
                    cerrarModalDev();
                    document.getElementById("panelRegistro").style.display = "none";
                    document.getElementById("panelCliente").style.display = "none";
                    document.getElementById("btnDevHeader").style.display = "none";
                    document.getElementById("panelDev").style.display = "block";

                    calcularYRenderizarFinanzas();
                    actualizarVistaDev();

                    if (estadoSolicitud === 'SOLICITADO') {
                        iniciarSonidoAlertaDev();
                    }
                } else {
                    alert("Clave incorrecta.");
                }
            }

            function salirDevMode() {
                detenerTodosLosSonidos();
                esModoDev = false;
                document.getElementById("panelDev").style.display = "none";
                document.getElementById("btnDevHeader").style.display = "block";
                cargarUIUsuario();

                if (estadoSolicitud === 'SOLICITADO') {
                    mostrarUIEsperaCliente();
                    iniciarMusicaEsperaCliente();
                } else if (estadoSolicitud === 'OFERTA_DESARROLLADOR') {
                    mostrarUIOfertaDesarrollador();
                } else if (estadoSolicitud === 'ACEPTADO') {
                    mostrarUIAceptadoCliente();
                } else if (estadoSolicitud === 'LLEGADO') {
                    mostrarUILlegadoCliente();
                }
            }

            function enviarWhatsAppDev() {
                const usr = usuarioActual || { nombre: "Usuario Genérico", numDoc: "No registrado", telefono: "No registrado" };
                const telLimpio = (usr.telefono && usr.telefono !== "No registrado") ? usr.telefono.replace(/\D/g, '') : '3153903131';
                const tipoStr = tipoServicioActual === 'domicilio' ? 'DOMICILIO / ENVÍO' : 'RECORRIDO DE TRANSPORTE';
                
                let detalleExtra = `📍 *Origen:* ${direccionOrigen}\n🔴 *Destino:* ${destinoTexto}`;
                if (tipoServicioActual === 'domicilio') {
                    detalleExtra = `🏪 *Establecimiento:* ${establecimientoDom}\n📝 *Pedido:* ${detallePedidoDom}\n📍 *Recogida:* ${direccionOrigen}\n🏠 *Entrega:* ${destinoTexto}`;
                }

                const mensaje = `*SOLICITUD ${tipoStr} - SEGURAPP*\n\n` +
                    ` *CÓDIGO DE SEGURIDAD:* ${codigoServicio}\n\n` +
                    ` *Cliente:* ${usr.nombre}\n` +
                    ` *ID:* ${usr.numDoc} | 📱 *Tel:* ${usr.telefono}\n` +
                    ` *Edad:* ${usr.edad || 'No especificada'} | 🏠 *Dir:* ${usr.direccion || 'No especificada'}\n` +
                    ` *Emergencia:* ${usr.emergencia || 'No especificado'}\n\n` +
                    detalleExtra + `\n` +
                    `*Valor Acordado:* $${Number(costoRecorrido).toLocaleString('es-CO')}`;
                
                const url = `https://wa.me/57${telLimpio}?text=${encodeURIComponent(mensaje)}`;
                window.open(url, '_blank');
            }

            function actualizarVistaDev() {
                if (!esModoDev) return;

                calcularYRenderizarFinanzas();
                
                const container = document.getElementById("solicitudDevContainer");

                if (estadoSolicitud === 'SOLICITADO') {
                    const usr = usuarioActual || { nombre: "Usuario Genérico", numDoc: "No registrado", telefono: "No registrado", edad: "-", direccion: "-", emergencia: "-" };
                    const etiquetaServicio = tipoServicioActual === 'domicilio' ? '📦 ¡NUEVO DOMICILIO ENTRANTE!' : '🛵 ¡SOLICITUD DE TRANSPORTE!';

                    let htmlDetalles = `
                        <p style="font-size: 0.85rem; color: #fff; margin-bottom: 6px;">
                            <strong style="color: var(--verde-verificado);">📍 Recogida:</strong> ${direccionOrigen}
                        </p>
                        <p style="font-size: 0.85rem; color: #fff; margin-bottom: 6px;">
                            <strong style="color: var(--dorado-brillante);">🔴 Destino:</strong> ${destinoTexto}
                        </p>
                    `;

                    if (tipoServicioActual === 'domicilio') {
                        htmlDetalles = `
                            <p style="font-size: 0.85rem; color: #fff; margin-bottom: 6px;">
                                <strong style="color: var(--dorado-brillante);">🏪 Establecimiento:</strong> ${establecimientoDom}
                            </p>
                            <p style="font-size: 0.85rem; color: #fff; margin-bottom: 6px;">
                                <strong style="color: var(--verde-verificado);">📝 Pedido:</strong> ${detallePedidoDom}
                            </p>
                            <p style="font-size: 0.85rem; color: #fff; margin-bottom: 6px;">
                                <strong style="color: var(--dorado-brillante);">Recogida:</strong> ${direccionOrigen}
                            </p>
                            <p style="font-size: 0.85rem; color: #fff; margin-bottom: 6px;">
                                <strong style="color: var(--dorado-brillante);">Entrega:</strong> ${destinoTexto}
                            </p>
                        `;
                    }

                    container.innerHTML = `
                        <div style="background: rgba(212,175,55,0.1); border: 2px solid var(--dorado-brillante); border-radius: 12px; padding: 14px; margin-bottom: 15px;">
                            <div style="font-size: 0.85rem; font-weight: 900; color: var(--dorado-brillante); margin-bottom: 8px;">
                                ${etiquetaServicio}
                            </div>
                            <div class="codigo-badge-box" style="margin-bottom:8px;">
                                <div style="font-size: 0.68rem; color: var(--texto-secundario);">CÓDIGO DE VERIFICACIÓN</div>
                                <div class="codigo-num" style="font-size: 1.4rem;">${codigoServicio}</div>
                            </div>
                            <div style="background:rgba(0,0,0,0.5); padding:10px; border-radius:8px; margin-bottom:10px; font-size:0.8rem;">
                                <p style="color:#fff; margin-bottom:4px;"><strong>👤 Cliente:</strong> ${usr.nombre}</p>
                                <p style="color:var(--texto-secundario); margin-bottom:4px;"><strong>🆔 ID:</strong> ${usr.numDoc} | 🎂 <strong>Edad:</strong> ${usr.edad}</p>
                                <p style="color:var(--texto-secundario); margin-bottom:4px;"><strong>📱 Tel:</strong> ${usr.telefono} | 🏠 <strong>Dir:</strong> ${usr.direccion || 'No reg.'}</p>
                                <p style="color:var(--rojo-alerta); margin-bottom:6px;"><strong>🚨 Emergencia:</strong> ${usr.emergencia || 'No reg.'}</p>
                                <hr style="border-color:var(--borde-sutil); margin:6px 0;">
                                ${htmlDetalles}
                            </div>
                            <div style="margin-bottom: 10px;">
                                <label style="font-size:0.75rem; color:var(--dorado-brillante); font-weight:700; display:block; margin-bottom:4px;">Ajustar / Proponer Nueva Tarifa ($):</label>
                                <div style="display:flex; gap:6px;">
                                    <input type="number" id="inputCostoDev" class="input-custom" value="${costoRecorrido}" style="margin-bottom:0; font-size:0.9rem; padding:8px;">
                                    <button class="btn-action btn-aceptar" onclick="ajustarPrecioDev()" style="width:130px; font-size:0.75rem; padding:8px;">Contraoferta</button>
                                </div>
                            </div>
                            <div style="display: flex; gap: 8px;">
                                <button class="btn-action btn-solicitar" onclick="aceptarServicioDev()" style="font-size:0.75rem; padding:6px;">
                                    <span /span> Aceptar
                                </button>
                                <button class="btn-action btn-whatsapp" onclick="enviarWhatsAppDev()"  style="font-size:0.75rem; padding:6px;">
                                    <span /span> WhatsApp
                                </button>
                                <button class="btn-action btn-cancelar" onclick="cancelarServicio()" style="font-size:0.85rem; padding:10px; margin-top:0; width:100px;">
                                    <span /span> Rechazar
                                </button>
                            </div>
                        </div>
                    `;
                } else if (estadoSolicitud === 'OFERTA_DESARROLLADOR') {
                    container.innerHTML = `
                        <div style="background: rgba(255,170,0,0.1); border: 2px solid #ffaa00; border-radius: 12px; padding: 14px; text-align:center;">
                            <strong style="color:#ffaa00; font-size:0.9rem;">⏳ Contraoferta Enviada al Cliente</strong>
                            <p style="font-size:0.8rem; color:var(--texto-secundario); margin:8px 0;">Esperando respuesta del usuario sobre la nueva tarifa propuesta de $${Number(costoRecorrido).toLocaleString('es-CO')}.</p>
                            <button class="btn-action btn-cancelar" onclick="cancelarServicio()" style="font-size:0.8rem; padding:8px;">Cancelar Solicitud</button>
                        </div>
                    `;
                } else if (estadoSolicitud === 'ACEPTADO') {
                    const usr = usuarioActual || { nombre: "Cliente", numDoc: "No registrado", telefono: "No registrado" };
                    container.innerHTML = `
                        <div style="background: rgba(0,255,136,0.1); border: 2px solid var(--verde-verificado); border-radius: 12px; padding: 14px; text-align:center;">
                            <strong style="color:var(--verde-verificado); font-size:0.9rem;">🚀 Servicio en Curso / Aceptado</strong>
                            <p style="font-size:0.8rem; color:#fff; margin:6px 0;">Código de verificación: <strong>${codigoServicio}</strong></p>
                            <p style="font-size:0.8rem; color:var(--texto-secundario); margin-bottom:10px;">Cliente: ${usr.nombre} (${usr.telefono})</p>
                            <div style="display:flex; gap:8px;">
                                <button class="btn-action btn-whatsapp" onclick="enviarWhatsAppDev()" style="font-size:0.8rem; padding:8px;">
                                    <span class="material-icons" style="font-size:16px;">chat</span>Cliente
                                </button>
                                <button class="btn-action btn-cancelar" onclick="cancelarServicio()" style="font-size:0.8rem; padding:8px; margin-top:0;">Finalizar</button>
                            </div>
                        </div>
                    `;
                } else {
                    container.innerHTML = `<p style="color: var(--texto-secundario); font-size: 0.9rem; text-align: center; padding: 20px;">No hay solicitudes pendientes en este momento.</p>`;
                }
            }

            window.onload = function() {
                // Sincronizar estado de autenticación con Firebase para recargas y persistencia local automática
                if (window.firebaseAuth && window.firebaseFns) {
                    window.firebaseFns.onAuthStateChanged(window.firebaseAuth, (user) => {
                        if (user) {
                            manejarUsuarioAutenticado(user);
                        } else {
                            cargarUIUsuario();
                        }
                    });
                } else {
                    cargarUIUsuario();
                }
                calcularYRenderizarFinanzas();
            };

            
        </script>
    </body>
    </html>
