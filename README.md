<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>SegurApp Recorridos</title>
    <link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
    <style>
        :root {
            --bg-principal: #000000;
            --bg-tarjeta: #121212;
            --bg-input: #1a1a1a;
            --neon-verde: #ff1e27; /* Rojo deportivo principal */
            --neon-cian: #ffffff;    /* Blanco puro para contraste de realces */
            --neon-rosa: #e50914;    /* Rojo secundario/alerta oscuro */
            --texto-principal: #ffffff;
            --texto-secundario: #a0a0a0; /* Gris plata */
            --borde-sutil: rgba(255, 255, 255, 0.1);
            --fuente: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            -webkit-tap-highlight-color: transparent;
            font-family: var(--fuente);
        }

        body {
            background-color: var(--bg-principal);
            color: var(--texto-principal);
            overflow-x: hidden;
            position: relative;
        }

        /* Fondo temático limpio sin overlays pesados */
        body::before {
            content: '';
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: linear-gradient(180deg, rgba(0, 0, 0, 0.9) 0%, rgba(18, 18, 18, 0.98) 100%),
                        url('https://imgs.search.brave.com/4EBMHUrFzUMmziphOMTPiPrRGPM74GSSptcyBcla7gU/rs:fit:860:0:0:0/g:ce/aHR0cHM6Ly9kZWxj/YXIuY29tLnV5L3dw/LWNvbnRlbnQvdXBs/b2Fkcy8yMDIzLzEw/L1JBSURFUi03LXNj/YWxlZC5qcGc') no-repeat center center;
            background-size: cover;
            z-index: -1;
            transform: translateZ(0);
        }

        /* Contenedor de Pantallas */
        .pantalla {
            display: none;
            min-height: 100vh;
            padding: 24px;
            padding-bottom: 110px;
            opacity: 0;
            transform: translateY(8px);
            transition: opacity 0.35s ease, transform 0.35s ease;
            will-change: transform, opacity;
        }

        .pantalla.activa {
            display: flex;
            flex-direction: column;
            opacity: 1;
            transform: translateY(0);
        }

        /* Animaciones refinadas y sutiles */
        @keyframes pulsoLogo {
            0%, 100% { border-color: rgba(255, 30, 39, 0.4); box-shadow: 0 0 10px rgba(255, 30, 39, 0.1); }
            50% { border-color: var(--neon-verde); box-shadow: 0 0 20px rgba(255, 30, 39, 0.4); }
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        /* Animación para el reflejo metálico */
        @keyframes barridoMetalico {
            0% { left: -150%; }
            50% { left: -150%; }
            100% { left: 150%; }
        }

        /* Encabezados Tipográficos Modernos */
        .titulo-app {
            text-align: center;
            font-size: 2rem;
            font-weight: 800;
            text-transform: uppercase;
            letter-spacing: 2px;
            color: #fff;
        }

        .subtitulo-app {
            text-align: center;
            font-size: 0.8rem;
            color: var(--texto-secundario);
            margin-bottom: 30px;
            line-height: 1.4;
            max-width: 320px;
            margin-left: auto;
            margin-right: auto;
        }

        #pantalla-home .subtitulo-app {
            color: var(--neon-verde);
            font-size: 0.75rem;
            text-transform: uppercase;
            letter-spacing: 1px;
            margin-bottom: 12px;
        }

        /* Tarjetas Estilo "Glow Plano" Mate con Reflejo Metálico */
        .tarjeta-cyber {
            background: var(--bg-tarjeta);
            border: 1px solid var(--borde-sutil);
            border-radius: 24px;
            padding: 24px;
            margin-bottom: 20px;
            position: relative;
            overflow: hidden;
            transition: border-color 0.25s ease;
        }

        .tarjeta-cyber::after {
            content: '';
            position: absolute;
            top: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(
                90deg,
                rgba(255, 255, 255, 0) 0%,
                rgba(255, 255, 255, 0.03) 20%,
                rgba(255, 255, 255, 0.12) 50%,
                rgba(255, 255, 255, 0.03) 80%,
                rgba(255, 255, 255, 0) 100%
            );
            transform: skewX(-25deg);
            animation: barridoMetalico 6s infinite ease-in-out;
            pointer-events: none;
        }

        .grupo-input {
            margin-bottom: 20px;
        }

        .grupo-input label {
            display: block;
            font-size: 0.7rem;
            text-transform: uppercase;
            color: var(--texto-secundario);
            margin-bottom: 8px;
            letter-spacing: 1px;
            font-weight: 600;
        }

        /* Inputs Sólidos Modernos */
        .input-cyber {
            width: 100%;
            background: var(--bg-input);
            border: 1px solid transparent;
            border-radius: 14px;
            padding: 16px;
            color: #fff;
            font-size: 0.95rem;
            transition: all 0.2s ease;
        }

        .input-cyber:focus {
            outline: none;
            border-color: rgba(255, 30, 39, 0.5);
            background: #222222;
        }

        /* Botones Planos de Alto Contraste */
        .btn-neon {
            width: 100%;
            background: var(--neon-verde);
            color: #ffffff;
            border: none;
            padding: 16px;
            border-radius: 14px;
            font-size: 0.95rem;
            font-weight: 700;
            text-transform: uppercase;
            letter-spacing: 0.5px;
            cursor: pointer;
            position: relative;
            overflow: hidden;
            transition: transform 0.1s ease, background-color 0.2s ease;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
        }

        /* Animaciones del Botón "Solicitar Servicio" */
        @keyframes motorRalenti {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.02); }
        }

        @keyframes pulsoMagnetico {
            0% {
                box-shadow: 0 0 0 0 rgba(255, 30, 39, 0.7), 
                            0 0 0 0 rgba(255, 30, 39, 0.4);
            }
            70% {
                box-shadow: 0 0 0 12px rgba(255, 30, 39, 0), 
                            0 0 0 24px rgba(255, 30, 39, 0);
            }
            100% {
                box-shadow: 0 0 0 0 rgba(255, 30, 39, 0), 
                            0 0 0 0 rgba(255, 30, 39, 0);
            }
        }

        @keyframes destelloCritico {
            0% { left: -150%; }
            30% { left: 150%; }
            100% { left: 150%; }
        }

        #pantalla-home .btn-neon-solicitar {
            background: linear-gradient(135deg, #ff1e27 0%, #e50914 100%) !important;
            color: #ffffff !important;
            font-size: 1.05rem !important;
            letter-spacing: 1px !important;
            box-shadow: 0 4px 20px rgba(255, 30, 39, 0.4);
            border: 1px solid rgba(255, 255, 255, 0.2) !important;
            animation: motorRalenti 1.8s infinite ease-in-out, 
                       pulsoMagnetico 2s infinite ease-in-out !important;
            transition: all 0.2s ease-in-out !important;
        }

        #pantalla-home .btn-neon-solicitar::after {
            content: '';
            position: absolute;
            top: 0;
            width: 120%;
            height: 100%;
            background: linear-gradient(
                90deg,
                rgba(255, 255, 255, 0) 0%,
                rgba(255, 255, 255, 0.2) 20%,
                rgba(255, 255, 255, 0.6) 50%,
                rgba(255, 255, 255, 0.2) 80%,
                rgba(255, 255, 255, 0) 100%
            ) !important;
            transform: skewX(-20deg);
            animation: destelloCritico 3.5s infinite ease-in-out !important;
            pointer-events: none;
        }

        #pantalla-home .btn-neon-solicitar:active {
            transform: scale(0.96) !important;
            background: #990005 !important;
            box-shadow: 0 2px 10px rgba(255, 30, 39, 0.2);
        }

        #pantalla-home .btn-neon-solicitar .material-icons {
            font-size: 24px;
            filter: drop-shadow(0 0 2px rgba(255,255,255,0.6));
        }

        /* Menú de contacto flotante */
        .barra-contacto {
            position: fixed;
            bottom: 24px; left: 24px; right: 24px;
            display: flex;
            gap: 12px;
            z-index: 100;
        }

        .btn-flotante {
            flex: 1;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
            padding: 16px;
            border-radius: 16px;
            font-weight: 700;
            text-transform: uppercase;
            font-size: 0.8rem;
            text-decoration: none;
            transition: transform 0.1s ease;
        }

        .btn-flotante:active {
            transform: scale(0.96);
        }

        .btn-llamada {
            background: #1a1a1a;
            color: var(--neon-verde);
            border: 1px solid rgba(255, 30, 39, 0.2);
        }

        .status-ubicacion {
            display: flex;
            align-items: center;
            gap: 8px;
            font-size: 0.8rem;
            margin-top: 5px;
            color: var(--neon-rosa);
        }
        .status-ubicacion.listo {
            color: #ffffff;
        }
        
        .status-ubicacion .material-icons {
            animation: spin 2s infinite linear;
        }

        .copyright {
            text-align: center;
            font-size: 0.7rem;
            color: var(--texto-secundario);
            margin-top: auto;
            padding-top: 30px;
            line-height: 1.5;
        }
        .copyright span {
            color: #fff;
            font-weight: 600;
        }

        /* Estilos específicos para la sección informativa */
        .info-recorridos {
            margin-bottom: 20px;
            font-size: 0.9rem;
            line-height: 1.4;
        }
        .info-recorridos p {
            margin-bottom: 12px;
            color: var(--texto-principal);
        }
        .info-recorridos h5 {
            color: var(--neon-verde);
            font-size: 0.85rem;
            text-transform: uppercase;
            margin-bottom: 10px;
            letter-spacing: 0.5px;
        }
        .info-recorridos ul {
            list-style: none;
            padding-left: 0;
        }
        .info-recorridos li {
            margin-bottom: 8px;
            display: flex;
            align-items: flex-start;
            gap: 8px;
            color: var(--texto-secundario);
        }
        .info-recorridos li span {
            color: var(--texto-principal);
        }

        /* Estilos Tarjeta Conductor de Confianza */
        .tarjeta-conductor {
            display: flex;
            align-items: center;
            gap: 16px;
            border-left: 4px solid var(--neon-verde);
        }

        .foto-conductor {
            width: 70px;
            height: 70px;
            border-radius: 50%;
            object-fit: cover;
            border: 2px solid var(--neon-verde);
            box-shadow: 0 0 10px rgba(255, 30, 39, 0.3);
            flex-shrink: 0;
        }

        .info-conductor {
            display: flex;
            flex-direction: column;
            gap: 4px;
        }

        .badge-verificado {
            display: inline-flex;
            align-items: center;
            gap: 4px;
            font-size: 0.65rem;
            text-transform: uppercase;
            letter-spacing: 0.5px;
            color: #4cd964;
            font-weight: 700;
        }

        .nombre-conductor {
            font-size: 0.95rem;
            font-weight: 700;
            color: #ffffff;
            text-transform: capitalize;
        }

        .detalle-licencia {
            font-size: 0.75rem;
            color: var(--texto-secundario);
        }

        .detalle-licencia strong {
            color: #ffffff;
        }
    </style>
</head>
<body>

    <!-- Pantalla Principal Única -->
    <div id="pantalla-home" class="pantalla activa">
        <div class="titulo-app">SegurApp</div>
        <div class="subtitulo-app">Recorridos en la ciudad de Neiva</div>

        <!-- Tarjeta Conductor de Confianza -->
        <div class="tarjeta-cyber tarjeta-conductor">
            <img src="https://scontent.fnva2-1.fna.fbcdn.net/v/t39.30808-6/699116233_989437980648237_9201268186456313724_n.jpg?stp=dst-jpg_tt6&cstp=mx944x1135&ctp=s944x1135&_nc_cat=104&ccb=1-7&_nc_sid=6ee11a&_nc_eui2=AeF20nckf-W9D3ZFO7cEU1xSlECitdgceRmUQKK12Bx5GWvzlgUUxyauRT7-iPD92RZ-aTX-rvfv8ZiXQ66KkWHV&_nc_ohc=ganR2FkYhr0Q7kNvwHTicbp&_nc_oc=AdrxaMxzFwKtYAsi8LIBm2DQYBLwpc5LUjsXOANMDkOQw85duwulwiV-JrKlk877gi0&_nc_zt=23&_nc_ht=scontent.fnva2-1.fna&_nc_gid=sz2j6utW2QrHMTlbo6H6gA&_nc_ss=7b2a8&oh=00_AQAobsTuhEl2RWIimscO7FLl5whp_msgKuN3TKJx7g4I7A&oe=6A66D12A" alt="Foto Sergio Alejandro Tapiero Chala" class="foto-conductor">
            <div class="info-conductor">
                <div class="badge-verificado">
                    <span class="material-icons" style="font-size: 14px;">verified</span> Conductor Asignado
                </div>
                <div class="nombre-conductor">Sergio Alejandro Tapiero Chala</div>
                <div class="detalle-licencia">
                    Licencia: <strong>1006056890</strong> | Moto <strong>Raider 125 negra</strong>
                </div>
                <div class="detalle-licencia">
                    Placa <strong>BWQ 69H</strong>
                </div>
            </div>
        </div>

        

        <div class="tarjeta-cyber">
            <div class="grupo-input">
                <span style="color:#ff0000; font-size: 13px; display:inline-block; margin-bottom:10px;">⏳ ¡Reserva con 10 minutos de anticipación!</span>

                <label>Notas de Ruta (Opcional)</label>
                <input type="text" id="reserva-notas" class="input-cyber" placeholder="Ej: Llevar casco extra, maleta pesada...">
            </div>

            <div class="status-ubicacion" id="geo-status">
                <span class="material-icons" style="font-size: 18px;">share_location</span>
                <span id="geo-texto">Sincronizando radar GPS...</span>
            </div>
            
            <div style="margin-top: 25px;">
                <button type="button" class="btn-neon btn-neon-solicitar" onclick="enviarReserva()">
                    <span class="material-icons">motorcycle</span> Solicitar Servicio Ahora
                </button>
            </div>
        </div>

        <div class="barra-contacto">
            <a href="tel:3189882787" class="btn-flotante btn-llamada">
                <span class="material-icons">phone_in_talk</span> Soporte Telefónico
            </a>
        </div>
        <!-- Sección Informativa del Servicio -->
        <div class="tarjeta-cyber info-recorridos">
            <p>Si buscas un servicio de transporte rápido, seguro y totalmente confiable, <strong>Recorridos</strong> es tu mejor opción. 📍</p>
            <p>Nos encargamos de que llegues a tiempo a tus citas, trabajo, compromisos o de regreso a casa, con la comodidad y la tranquilidad que te mereces.</p>
            
            <h5>¿Por qué elegirnos?</h5>
            <ul>
                <li>⏱️ <span><strong>Puntualidad garantizada:</strong> Respetamos tu tiempo.</span></li>
                <li>🔒 <span><strong>Viajes seguros:</strong> Conductores de confianza y rutas optimizadas.</span></li>
                <li>💬 <span><strong>Fácil de solicitar:</strong> Sin enredos ni esperas largas.</span></li>
            </ul>
            <p style="margin-top: 12px; margin-bottom: 0; font-weight: bold; color: var(--neon-verde);">¡Olvídate del estrés del tráfico! Déjanos el volante a nosotros.</p>
        </div>

        <footer class="copyright">
            © 2026 SegurApp Recorridos. Todos los derechos reservados.<br>
            Desarrollado por <span>Sergio Chala</span>
        </footer>
    </div>

    <script>
        let datosUbicacionActual = "Buscando satélites...";
        const WHATSAPP_DESTINO = "573189882787";

        document.addEventListener("DOMContentLoaded", () => {
            solicitarGeolocalizacion();
        });

        function solicitarGeolocalizacion() {
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(
                    (position) => {
                        const lat = position.coords.latitude;
                        const lon = position.coords.longitude;
                        datosUbicacionActual = `https://www.google.com/maps?q=${lat},${lon}`;
                        
                        const geoStatus = document.getElementById('geo-status');
                        geoStatus.classList.add('listo');
                        document.getElementById('geo-texto').innerText = "Ubicación de partida fijada por GPS.";
                        geoStatus.querySelector('.material-icons').style.animation = 'none';
                    },
                    (error) => {
                        document.getElementById('geo-texto').innerText = "Ubicación manual o imprecisa.";
                        datosUbicacionActual = "GPS Local (Verificar mapa al contactar)";
                        document.getElementById('geo-status').querySelector('.material-icons').style.animation = 'none';
                    },
                    { enableHighAccuracy: true, timeout: 10000 }
                );
            } else {
                document.getElementById('geo-texto').innerText = "GPS no soportado.";
            }
        }

        function enviarReserva() {
            const destino = "Origen fijado por GPS";
            const notas = document.getElementById('reserva-notas').value.trim();
            
            let mensaje = `*SOLICITUD DE RECORRIDO INMEDIATO* \n`;
            mensaje += `───────────────────────\n\n`;
            mensaje += ` *Hola! sergio chala, puedes pasar por mi?* \n`;
            mensaje += ` *Destino:* ${destino}\n`;
            if(notas) {
                mensaje += ` *Detalles:* ${notas}\n`;
            }
            mensaje += `\n *Coordenadas de Origen en Tiempo Real:* \n${datosUbicacionActual}\n\n`;
            mensaje += `───────────────────────\n\n`;
            mensaje += ` *Tiempo estimado de respuesta 10min*`;

            window.open(`https://wa.me/${WHATSAPP_DESTINO}?text=${encodeURIComponent(mensaje)}`, '_blank');
        }
    </script>
</body>
</html>
