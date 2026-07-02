<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Domínio — Sistema de Perfil Cyber-Gótico</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
    
    <style>
        * {
            user-select: none;
            -webkit-tap-highlight-color: transparent;
        }
        body, html {
            background-color: #030303;
            overflow-x: hidden;
            margin: 0;
            padding: 0;
            height: 100%;
        }
        .clip-path-hexagon {
            clip-path: polygon(50% 0%, 100% 15%, 100% 85%, 50% 100%, 0% 85%, 0% 15%);
        }
        .custom-scroll::-webkit-scrollbar {
            width: 4px;
        }
        .custom-scroll::-webkit-scrollbar-thumb {
            background: var(--neon-base, #a200ff);
            border-radius: 10px;
        }
        #particles-canvas {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 1;
            pointer-events: none;
        }
        .app-interface {
            position: relative;
            z-index: 2;
        }
    </style>
</head>
<body class="text-neutral-200 font-sans selection:bg-purple-900/50">

    <canvas id="particles-canvas"></canvas>

    <div class="app-interface min-h-screen flex flex-col justify-between">

        <div id="intro-layer" class="fixed inset-0 bg-[#020202] z-[99999] flex flex-col items-center justify-center transition-opacity duration-1000 ease-out">
            <div id="intro-logo" class="font-serif tracking-[0.5em] text-3xl md:text-6xl text-center font-bold opacity-0 transition-all duration-700 uppercase" style="color: #a200ff; text-shadow: 0 0 20px #a200ff;">
                DOMÍNIO
            </div>
            <div class="w-24 h-[1px] bg-neutral-800 mt-4 relative overflow-hidden">
                <div class="absolute h-full w-1/2 bg-purple-600" style="animation: loadSlide 1.5s infinite linear;"></div>
            </div>
        </div>

        <div id="saving-screen" class="hidden fixed inset-0 bg-black/90 backdrop-blur-md z-[999999] flex flex-col items-center justify-center animate-fadeIn">
            <div class="w-14 h-14 border-4 border-neutral-900 border-t-purple-600 rounded-full animate-spin mb-4 shadow-[0_0_15px_rgba(162,0,255,0.4)]"></div>
            <h2 class="font-serif text-lg tracking-[0.2em] uppercase font-bold animate-pulse text-purple-400" style="text-shadow: 0 0 10px rgba(162, 0, 255, 0.6);">
                Sincronizando Domínio...
            </h2>
            <p class="text-[10px] text-neutral-500 uppercase mt-1 tracking-wider">Gravando mídias e atmosfera no servidor</p>
        </div>

        <style>
            @keyframes loadSlide {
                0% { left: -50%; }
                100% { left: 100%; }
            }
            @keyframes fadeIn {
                from { opacity: 0; }
                to { opacity: 1; }
            }
            .animate-fadeIn {
                animation: fadeIn 0.3s ease forwards;
            }
        </style>

        <main class="w-full flex-1">

            <div id="main-banner-container" class="w-full relative overflow-hidden bg-[#070707] border-b border-purple-900/30 flex items-center justify-center" style="height: 280px;">
                <div id="banner-media-render" class="w-full h-full absolute inset-0 pointer-events-none">
                </div>
                <div class="absolute inset-0 bg-gradient-to-b from-black/70 via-black/20 to-[#030303]"></div>

                <div class="absolute top-4 left-4 z-10 flex flex-col gap-1">
                    <h1 class="font-serif text-lg md:text-2xl font-bold uppercase tracking-widest text-purple-500 dynamic-glow">Domínio</h1>
                    <div id="badge-mode" class="text-[9px] uppercase font-bold tracking-wider px-2 py-0.5 rounded border border-blue-500/30 bg-blue-950/40 text-blue-400 w-max">
                        <i class="fa-solid fa-eye mr-1"></i> Modo Visitante
                    </div>
                </div>

                <div class="absolute bottom-4 right-4 z-20 flex flex-col items-center gap-1.5">
                    <div id="avatar-touch-zone" onclick="handleAvatarClick()" class="rounded-full border-2 bg-neutral-900 overflow-hidden cursor-pointer shadow-lg transition-transform duration-300 active:scale-95 flex items-center justify-center" style="width: 80px; height: 80px; border-color: #a200ff; box-shadow: 0 0 15px rgba(162,0,255,0.4);">
                        <div id="avatar-media-render" class="w-full h-full">
                        </div>
                    </div>
                    <span id="display-username" class="text-xs font-bold font-mono text-neutral-400 bg-black/60 px-2 py-0.5 rounded">@shadow_master</span>
                </div>
            </div>

            <div id="avatar-context-menu" class="hidden absolute right-4 z-50 bg-[#0a0a0a] border border-neutral-800 rounded-xl p-2 w-48 shadow-2xl animate-fadeIn">
                <button onclick="openPanel('perfil')" class="w-full text-left text-xs p-2.5 rounded-lg hover:bg-neutral-900 transition-colors flex items-center gap-2 text-neutral-300">
                    <i class="fa-solid fa-user-gear text-purple-500"></i> Editar Perfil
                </button>
                <button onclick="openPanel('dominio')" class="w-full text-left text-xs p-2.5 rounded-lg hover:bg-neutral-900 transition-colors flex items-center gap-2 text-neutral-300">
                    <i class="fa-solid fa-sliders text-cyan-500"></i> Configurar Domínio
                </button>
                <div class="border-t border-neutral-800 my-1"></div>
                <button onclick="logoutSession()" class="w-full text-left text-xs p-2.5 rounded-lg hover:bg-red-950/30 transition-colors flex items-center gap-2 text-red-400">
                    <i class="fa-solid fa-right-from-bracket"></i> Desconectar
                </button>
            </div>

            <div class="max-w-xl mx-4 sm:mx-auto -mt-4 p-4 bg-neutral-900/80 border border-neutral-800 rounded-xl text-center backdrop-blur-md relative z-10 shadow-xl">
                <p id="display-bio" class="text-xs md:text-sm text-neutral-300 leading-relaxed font-light">Abrace as sombras para alcançar a luz e dominar o ambiente digital.</p>
            </div>

            <div class="max-w-xl mx-4 sm:mx-auto my-6">
                <div class="rounded-xl overflow-hidden border border-neutral-800/60 bg-black/40 p-1 backdrop-blur-sm">
                    <iframe id="spotify-player" src="https://open.spotify.com/embed/playlist/37i9dQZF1DX1tzg9j4037i?utm_source=generator" width="100%" height="80" frameBorder="0" allow="autoplay; clipboard-write; encrypted-media; fullscreen; picture-in-picture" loading="lazy"></iframe>
                </div>
                <div class="editor-only hidden mt-2 flex gap-2">
                    <input type="text" id="spotify-link-input" placeholder="URL da música/playlist do Spotify..." class="flex-1 bg-neutral-950 border border-neutral-800 rounded-lg p-2 text-xs focus:outline-none focus:border-purple-500 text-white font-mono">
                    <button onclick="applySpotifyUrl()" class="px-3 bg-purple-900/40 border border-purple-700/50 text-purple-300 text-xs rounded-lg font-bold uppercase tracking-wider">Aplicar</button>
                </div>
            </div>

            <div class="max-w-xl mx-4 sm:mx-auto my-8">
                <div id="social-grid-container" class="grid grid-cols-4 gap-4 bg-neutral-950/40 p-4 border border-neutral-900 rounded-xl">
                </div>
                <div class="editor-only hidden mt-3 flex justify-end">
                    <button onclick="triggerAddSocialModal()" class="text-[11px] bg-neutral-900 border border-neutral-800 text-neutral-400 px-3 py-1.5 rounded-lg hover:border-purple-500 transition-colors">
                        <i class="fa-solid fa-plus mr-1"></i> Gerenciar Botões Sociais
                    </button>
                </div>
            </div>

            <div class="max-w-4xl mx-auto px-4 py-4 flex flex-col items-center gap-8">
                <h3 class="font-serif tracking-widest text-xs uppercase text-neutral-500 text-center border-b border-neutral-900 pb-2 w-full max-w-xs">Registros Visuais</h3>
                
                <div id="gallery-container" class="w-full flex flex-col items-center gap-8">
                </div>

                <div class="editor-only hidden w-full max-w-xs">
                    <button onclick="addNewGallerySlot()" class="w-full py-3 bg-neutral-950 border border-dashed border-neutral-800 hover:border-purple-500/50 rounded-xl font-bold uppercase tracking-widest text-[10px] text-neutral-400 transition-colors">
                        <i class="fa-solid fa-photo-film mr-2"></i> Inserir Novo Bloco de Mídia
                    </button>
                </div>
            </div>

            <div class="max-w-xl mx-4 sm:mx-auto my-12 flex flex-col gap-3">
                <h3 class="font-serif tracking-widest text-xs uppercase text-neutral-500 text-center mb-2">Conexões Externas</h3>
                <div id="custom-links-container" class="flex flex-col gap-3">
                </div>
                <div class="editor-only hidden mt-2">
                    <button onclick="addNewCustomLink()" class="w-full py-3 bg-neutral-900/40 border border-neutral-800 text-neutral-400 text-xs rounded-xl font-bold uppercase tracking-wider">
                        <i class="fa-solid fa-plus mr-1"></i> Adicionar Link de Texto
                    </button>
                </div>
            </div>

        </main>

        <footer class="w-full bg-neutral-950/80 border-t border-neutral-900/60 p-3 text-center backdrop-blur-md relative z-30">
            <div id="footer-login-zone">
                <button onclick="toggleAuthModal(true)" class="text-[10px] text-neutral-500 hover:text-purple-400 font-mono tracking-widest uppercase transition-colors">
                    <i class="fa-solid fa-lock-open mr-1"></i> Painel de Autenticação do Proprietário
                </button>
            </div>
        </footer>

    </div>

    <div id="auth-modal" class="hidden fixed inset-0 bg-black/80 backdrop-blur-md z-[60000] flex items-center justify-center p-4 animate-fadeIn">
        <div class="bg-[#090909] border border-neutral-800 w-full max-w-sm rounded-2xl p-6 shadow-2xl flex flex-col gap-4">
            <div class="flex justify-between items-center border-b border-neutral-800 pb-3">
                <h4 class="font-serif text-sm uppercase tracking-widest text-purple-400 font-bold">Acesso ao Domínio</h4>
                <button onclick="toggleAuthModal(false)" class="text-neutral-500 text-xs"><i class="fa-solid fa-xmark"></i></button>
            </div>
            <div class="flex flex-col gap-3">
                <input type="email" id="auth-email" placeholder="E-mail Cadastrado" class="w-full bg-neutral-950 border border-neutral-800 p-2.5 rounded-lg text-xs text-white focus:outline-none focus:border-purple-600">
                <input type="password" id="auth-password" placeholder="Senha do Servidor" class="w-full bg-neutral-950 border border-neutral-800 p-2.5 rounded-lg text-xs text-white focus:outline-none focus:border-purple-600">
            </div>
            <div class="flex flex-col gap-2 pt-2">
                <div class="flex gap-2">
                    <button onclick="attemptLogin()" class="flex-1 py-2.5 bg-purple-600 hover:bg-purple-700 text-black font-bold uppercase text-[11px] rounded-xl transition-colors">Entrar</button>
                    <button onclick="createAccount()" class="flex-1 py-2.5 bg-neutral-800 hover:bg-neutral-700 text-neutral-300 font-bold uppercase text-[11px] rounded-xl transition-colors">Criar Conta</button>
                </div>
                <div class="flex justify-between mt-3 border-t border-neutral-800 pt-3 px-1">
                    <button onclick="requestRecoveryToken('password')" class="text-[10px] uppercase font-bold tracking-wider text-neutral-500 hover:text-purple-400 transition-colors">Recuperar Senha</button>
                    <button onclick="requestRecoveryToken('account')" class="text-[10px] uppercase font-bold tracking-wider text-neutral-500 hover:text-purple-400 transition-colors">Recuperar Conta</button>
                </div>
            </div>
        </div>
    </div>
    <div id="master-config-panel" class="hidden fixed inset-0 bg-black/70 backdrop-blur-md z-[50000] flex items-end sm:items-center justify-center animate-fadeIn">
        <div class="bg-[#080808] border border-neutral-900 w-full sm:w-11/12 sm:max-w-md rounded-t-2xl sm:rounded-2xl overflow-hidden shadow-2xl flex flex-col max-h-[85vh]">
            
            <div class="flex border-b border-neutral-900 bg-neutral-950/60">
                <button id="tab-btn-perfil" onclick="switchTab('perfil')" class="flex-1 py-3.5 font-serif text-[11px] uppercase tracking-wider text-neutral-400 border-b-2 border-transparent font-bold">Perfil</button>
                <button id="tab-btn-dominio" onclick="switchTab('dominio')" class="flex-1 py-3.5 font-serif text-[11px] uppercase tracking-wider text-neutral-400 border-b-2 border-transparent font-bold">Domínio</button>
                <button id="tab-btn-animacao" onclick="switchTab('animacao')" class="flex-1 py-3.5 font-serif text-[11px] uppercase tracking-wider text-neutral-400 border-b-2 border-transparent font-bold text-purple-400"><i class="fa-solid fa-wand-magic-sparkles mr-1"></i>Efeitos</button>
            </div>

            <div class="p-5 overflow-y-auto flex-1 flex flex-col gap-4 text-xs custom-scroll">
                
                <div id="panel-tab-perfil" class="hidden flex flex-col gap-4">
                    <div class="flex flex-col gap-1.5">
                        <label class="text-[10px] text-neutral-400 uppercase tracking-wider font-bold">Username Identification</label>
                        <input type="text" id="config-username" class="w-full bg-neutral-950 border border-neutral-800 p-2.5 rounded-lg text-white font-mono">
                    </div>
                    <div class="flex flex-col gap-1.5">
                        <label class="text-[10px] text-neutral-400 uppercase tracking-wider font-bold">Biografia Textual</label>
                        <textarea id="config-bio" rows="3" class="w-full bg-neutral-950 border border-neutral-800 p-2.5 rounded-lg text-white resize-none leading-relaxed"></textarea>
                    </div>
                </div>

                <div id="panel-tab-dominio" class="hidden flex flex-col gap-4">
                    <div class="bg-neutral-950 p-3 rounded-xl border border-neutral-900">
                        <label class="text-[10px] text-purple-400 uppercase font-bold block mb-2">Coloração Energética (Neon)</label>
                        <div class="flex items-center gap-3">
                            <input type="color" id="config-neon-hex" value="#a200ff" class="w-12 h-8 bg-transparent border-0 cursor-pointer rounded-md">
                            <span class="text-neutral-400 font-mono text-[11px]">Selecione a cor base</span>
                        </div>
                    </div>

                    <div class="bg-neutral-950 p-3 rounded-xl border border-neutral-900 flex flex-col gap-2">
                        <label class="text-[10px] text-neutral-400 uppercase font-bold">Mídia do Banner (Foto/Vídeo)</label>
                        <input type="text" id="config-banner-url" oninput="updateBannerUrlLive(this.value)" placeholder="URL da imagem ou vídeo .mp4" class="w-full bg-neutral-900 border border-neutral-800 p-2 rounded-md text-[11px] font-mono">
                        <div class="flex items-center justify-between mt-1">
                            <span class="text-[10px] text-neutral-500">Ou arquivo do celular:</span>
                            <input type="file" id="file-banner-upload" accept="image/*,video/mp4,video/webm" onchange="uploadLocalMedia('banner')" class="text-[10px] text-purple-400 w-44">
                        </div>
                        <div class="mt-2">
                            <label class="text-[10px] text-neutral-500 block mb-1">Altura do Banner ($Y$): <span id="val-banner-y">280</span>px</label>
                            <input type="range" id="slider-banner-y" min="150" max="450" value="280" class="w-full accent-purple-600" oninput="liveUpdateBannerHeight(this.value)">
                        </div>
                    </div>

                    <div class="bg-neutral-950 p-3 rounded-xl border border-neutral-900 flex flex-col gap-2">
                        <label class="text-[10px] text-neutral-400 uppercase font-bold">Mídia do Avatar (Foto/Vídeo)</label>
                        <input type="text" id="config-avatar-url" oninput="updateAvatarUrlLive(this.value)" placeholder="URL da imagem ou vídeo .mp4" class="w-full bg-neutral-900 border border-neutral-800 p-2 rounded-md text-[11px] font-mono">
                        <div class="flex items-center justify-between mt-1">
                            <span class="text-[10px] text-neutral-500">Arquivo local:</span>
                            <input type="file" id="file-avatar-upload" accept="image/*,video/mp4,video/webm" onchange="uploadLocalMedia('avatar')" class="text-[10px] text-purple-400 w-44">
                        </div>
                        <div class="grid grid-cols-2 gap-2 mt-2">
                            <div>
                                <label class="text-[10px] text-neutral-500 block mb-1">Largura ($X$): <span id="val-avatar-x">80</span>px</label>
                                <input type="range" id="slider-avatar-x" min="60" max="150" value="80" class="w-full accent-purple-600" oninput="liveUpdateAvatarDim('x', this.value)">
                            </div>
                            <div>
                                <label class="text-[10px] text-neutral-500 block mb-1">Altura ($Y$): <span id="val-avatar-y">80</span>px</label>
                                <input type="range" id="slider-avatar-y" min="60" max="150" value="80" class="w-full accent-purple-600" oninput="liveUpdateAvatarDim('y', this.value)">
                            </div>
                        </div>
                    </div>
                </div>

                <div id="panel-tab-animacao" class="hidden flex flex-col gap-3">
                    <p class="text-[10px] text-neutral-400 uppercase tracking-widest border-b border-neutral-900 pb-1 mb-1">Ambiente de Fundo Atmospheric</p>
                    <div class="grid grid-cols-2 gap-2">
                        <button onclick="setAtmosphere('none')" class="p-3 rounded-xl bg-neutral-950 border border-neutral-800 text-left hover:border-purple-600 transition-all flex items-center gap-2">
                            <i class="fa-solid fa-ban text-neutral-600"></i> Estático
                        </button>
                        <button onclick="setAtmosphere('nuvens')" class="p-3 rounded-xl bg-neutral-950 border border-neutral-800 text-left hover:border-purple-600 transition-all flex items-center gap-2">
                            <i class="fa-solid fa-cloud text-slate-400"></i> Nuvens Sóbrias
                        </button>
                        <button onclick="setAtmosphere('flor')" class="p-3 rounded-xl bg-neutral-950 border border-neutral-800 text-left hover:border-purple-600 transition-all flex items-center gap-2">
                            <i class="fa-solid fa-seedling text-red-500"></i> Pétalas Negras
                        </button>
                        <button onclick="setAtmosphere('folhas')" class="p-3 rounded-xl bg-neutral-950 border border-neutral-800 text-left hover:border-purple-600 transition-all flex items-center gap-2">
                            <i class="fa-solid fa-leaf text-amber-600"></i> Folhas Secas
                        </button>
                        <button onclick="setAtmosphere('raios')" class="p-3 rounded-xl bg-neutral-950 border border-neutral-800 text-left hover:border-purple-600 transition-all flex items-center gap-2">
                            <i class="fa-solid fa-bolt text-yellow-400"></i> Tempestade Eletro
                        </button>
                        <button onclick="setAtmosphere('cruzes')" class="p-3 rounded-xl bg-neutral-950 border border-neutral-800 text-left hover:border-purple-600 transition-all flex items-center gap-2">
                            <i class="fa-solid fa-cross text-purple-400"></i> Cruzes Góticas
                        </button>
                        <button onclick="setAtmosphere('espadas')" class="p-3 rounded-xl bg-neutral-950 border border-neutral-800 text-left hover:border-purple-600 transition-all flex items-center gap-2">
                            <i class="fa-solid fa-gavel text-zinc-400"></i> Lâminas/Espadas
                        </button>
                        <button onclick="setAtmosphere('morcegos')" class="p-3 rounded-xl bg-neutral-950 border border-neutral-800 text-left hover:border-purple-600 transition-all flex items-center gap-2">
                            <i class="fa-solid fa-bat text-purple-600"></i> Vampírico/Morcegos
                        </button>
                    </div>
                </div>

            </div>

            <div class="p-4 bg-neutral-950 border-t border-neutral-900 flex gap-2 pb-6 sm:pb-4">
                <button onclick="closeMasterPanel()" class="flex-1 py-3 bg-neutral-900 hover:bg-neutral-800 text-[11px] font-bold uppercase rounded-xl text-neutral-400">Fechar</button>
                <button onclick="syncAllToSupabase()" class="flex-1 py-3 bg-purple-600 hover:bg-purple-700 text-black font-bold uppercase text-[11px] rounded-xl shadow-lg">Salvar Estado</button>
            </div>
        </div>
    </div>

    <div id="social-manager-modal" class="hidden fixed inset-0 bg-black/80 backdrop-blur-sm z-[60000] flex items-center justify-center p-4">
        <div class="bg-[#090909] border border-neutral-800 w-full max-w-sm rounded-xl p-5 flex flex-col gap-3 text-xs">
            <h4 class="font-serif text-xs uppercase tracking-wider text-neutral-400">Configurar Ícones Rápidos</h4>
            <div class="flex flex-col gap-2">
                <label class="text-[10px] text-neutral-500">Rede Destino</label>
                <select id="social-select-type" class="w-full bg-neutral-950 border border-neutral-800 p-2 rounded-md text-white">
                    <option value="spotify">Spotify</option>
                    <option value="soundcloud">Soundcloud</option>
                    <option value="tiktok">TikTok</option>
                    <option value="instagram">Instagram</option>
                    <option value="steam">Steam</option>
                </select>
            </div>
            <div class="flex flex-col gap-2">
                <label class="text-[10px] text-neutral-500">Link Completo (URL)</label>
                <input type="text" id="social-input-url" placeholder="https://..." class="w-full bg-neutral-950 border border-neutral-800 p-2 rounded-md text-white font-mono">
            </div>
            <div class="flex gap-2 pt-2">
                <button onclick="document.getElementById('social-manager-modal').classList.add('hidden')" class="flex-1 py-2 bg-neutral-900 rounded-lg text-neutral-400">Cancelar</button>
                <button onclick="saveSocialGridElement()" class="flex-1 py-2 bg-purple-600 text-black font-bold rounded-lg">Inserir/Fixar</button>
            </div>
        </div>
    </div>

    <script>
        // --- ESTADO GLOBAL CONFIGURÁVEL DO PERFIL ---
        let profileState = {
            username: "@shadow_master",
            bio: "Abrace as sombras para alcançar a luz e dominar o ambiente digital.",
            neonColor: "#a200ff",
            atmosphere: "morcegos",
            spotifyTrackUrl: "https://open.spotify.com/embed/playlist/37i9dQZF1DX1tzg9j4037i?utm_source=generator",
            banner: { url: "https://images.unsplash.com/photo-1550684848-fac1c5b4e853?q=80&w=1200", height: 280, isVideo: false },
            avatar: { url: "https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?q=80&w=200", width: 80, height: 80, isVideo: false },
            socials: [
                { type: 'instagram', url: '#' },
                { type: 'spotify', url: '#' },
                { type: 'tiktok', url: '#' },
                { type: 'steam', url: '#' }
            ],
            gallery: [
                { id: "g1", url: "https://images.unsplash.com/photo-1618005182384-a83a8bd57fbe?q=80&w=600", isVideo: false, noFrame: false, width: 280, height: 380 }
            ],
            textLinks: [
                { title: "Acesse Meu Portfólio Principal", url: "#" }
            ]
        };

        let isEditorAuthenticated = false; 
        let supabaseClient = null;

        // VARIÁVEIS ADICIONADAS PARA O NOVO SISTEMA DE AUTENTICAÇÃO
        let userDatabase = {}; 
        let activeRecoveryTokens = {}; 

        // --- INICIALIZAÇÃO SEGURA DO SUPABASE ---
        const SUPABASE_URL = "https://vjloltywvuyqtebvucux.supabase.co";
        const SUPABASE_ANON_KEY = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6InZqbG9sdHl3dnV5cXRlYnZ1Y3V4Iiwicm9sZSI6ImFub24iLCJpYXQiOjE3ODI5NTc4NjcsImV4cCI6MjA5ODUzMzg2N30.tw9-LrIhaoMp1JqVPDaFh7gNyVVDtmkoFePF5xD8gis";

        // Garante que o objeto supabase exista antes de criar o cliente
        if (window.supabase) {
            supabaseClient = window.supabase.createClient(SUPABASE_URL, SUPABASE_ANON_KEY);
            console.log("Supabase inicializado com sucesso.");
        } else {
            console.error("A biblioteca do Supabase não foi carregada!");
        }

        // --- ENGINE DE INICIALIZAÇÃO ---
        window.addEventListener('DOMContentLoaded', () => {
            initializeAtmosphereEngine();
            loadProfileData();
            
            setTimeout(() => {
                document.getElementById('intro-logo').classList.remove('opacity-0');
            }, 400);

            setTimeout(() => {
                document.getElementById('intro-layer').classList.add('opacity-0');
                setTimeout(() => {
                    document.getElementById('intro-layer').classList.add('hidden');
                }, 1000);
            }, 2200);
        });

        function checkInterfacePermissions() {
            const editorElements = document.querySelectorAll('.editor-only');
            const badge = document.getElementById('badge-mode');
            
            if (isEditorAuthenticated) {
                editorElements.forEach(el => el.classList.remove('hidden'));
                badge.className = "text-[9px] uppercase font-bold tracking-wider px-2 py-0.5 rounded border border-purple-500/30 bg-purple-950/40 text-purple-400 w-max";
                badge.innerHTML = `<i class="fa-solid fa-user-shield mr-1"></i> Modo Administrador`;
            } else {
                editorElements.forEach(el => el.classList.add('hidden'));
                badge.className = "text-[9px] uppercase font-bold tracking-wider px-2 py-0.5 rounded border border-blue-500/30 bg-blue-950/40 text-blue-400 w-max";
                badge.innerHTML = `<i class="fa-solid fa-eye mr-1"></i> Modo Visitante`;
            }
        }

        function handleAvatarClick() {
            if(isEditorAuthenticated) {
                const menu = document.getElementById('avatar-context-menu');
                menu.classList.toggle('hidden');
            } else {
                toggleAuthModal(true);
            }
        }

        function toggleAuthModal(show) {
            document.getElementById('auth-modal').classList.toggle('hidden', !show);
        }

        // --- INÍCIO DAS NOVAS FUNÇÕES DE AUTENTICAÇÃO INTEGRADAS ---
        function attemptLogin() {
            const email = document.getElementById('auth-email').value;
            const pass = document.getElementById('auth-password').value;

            if (email === 'xangai129@gmail.com' && pass === 'wgk123') {
                isEditorAuthenticated = true;
                toggleAuthModal(false);
                checkInterfacePermissions();
                renderAllDynamicElements();
                alert("Acesso de Desenvolvedor Autorizado. Bem-vindo.");
                return;
            }

            if (email && pass) {
                if (userDatabase[email] && userDatabase[email] === pass) {
                    isEditorAuthenticated = true;
                    toggleAuthModal(false);
                    checkInterfacePermissions();
                    renderAllDynamicElements();
                } else {
                    alert("Credenciais inválidas ou conta não encontrada.");
                }
            } else {
                alert("Por favor, preencha o e-mail e a senha para entrar.");
            }
        }

        function createAccount() {
            const email = document.getElementById('auth-email').value;
            const pass = document.getElementById('auth-password').value;

            if (email && pass) {
                if (userDatabase[email]) {
                    alert("Esta conta já está registrada no sistema!");
                } else {
                    userDatabase[email] = pass;
                    alert("Conta criada com sucesso! Você já pode fazer login no Domínio.");
                }
            } else {
                alert("Preencha um e-mail e uma senha para criar sua conta.");
            }
        }

        function requestRecoveryToken(type) {
            const email = prompt(`Digite seu e-mail cadastrado para recuperar ${type === 'password' ? 'sua senha' : 'sua conta'}:`);
            
            if (email) {
                const token = Math.floor(100000 + Math.random() * 900000).toString();
                activeRecoveryTokens[email] = token;
                
                alert(`[SIMULAÇÃO DE E-MAIL] O token de recuperação foi enviado para: ${email}\n\nSeu Token é: ${token}`);
                
                if (type === 'password') {
                    recoverPassword(email);
                } else {
                    recoverAccount(email);
                }
            }
        }

        function recoverPassword(email) {
            const token = prompt("Digite o token de 6 dígitos que foi enviado para o seu e-mail:");
            
            if (token && activeRecoveryTokens[email] === token) {
                const newPass = prompt("Token validado com sucesso! Digite sua NOVA senha:");
                if (newPass) {
                    userDatabase[email] = newPass;
                    delete activeRecoveryTokens[email];
                    alert("Senha alterada com sucesso! Use sua nova senha para entrar.");
                }
            } else {
                alert("Token inválido ou expirado. Operação cancelada.");
            }
        }

        function recoverAccount(email) {
            const token = prompt("Digite o token de 6 dígitos que foi enviado para o seu e-mail antigo:");
            
            if (token && activeRecoveryTokens[email] === token) {
                const newEmail = prompt("Token validado! Digite o NOVO E-MAIL que assumirá esta conta:");
                if (newEmail) {
                    userDatabase[newEmail] = userDatabase[email] || "senha_padrao_recuperada"; 
                    delete userDatabase[email];
                    delete activeRecoveryTokens[email];
                    alert("Conta recuperada e transferida para o novo e-mail com sucesso!");
                }
            } else {
                alert("Token inválido ou expirado. Operação cancelada.");
            }
        }
        // --- FIM DAS NOVAS FUNÇÕES DE AUTENTICAÇÃO ---

        function logoutSession() {
            isEditorAuthenticated = false;
            document.getElementById('avatar-context-menu').classList.add('hidden');
            checkInterfacePermissions();
            renderAllDynamicElements();
        }

        function renderSocialGrid() {
            const container = document.getElementById('social-grid-container');
            container.innerHTML = "";
            
            const iconMap = {
                spotify: 'fa-brands fa-spotify text-green-500',
                soundcloud: 'fa-brands fa-soundcloud text-orange-500',
                tiktok: 'fa-brands fa-tiktok text-pink-500',
                instagram: 'fa-brands fa-instagram text-rose-400',
                steam: 'fa-brands fa-steam text-blue-400'
            };

            profileState.socials.forEach((item, index) => {
                const itemBox = document.createElement('div');
                itemBox.className = "flex flex-col items-center justify-center text-center p-2 rounded-lg bg-neutral-900/60 relative group";
                
                let deleteBtn = isEditorAuthenticated ? `<button onclick="deleteSocial(${index})" class="absolute -top-1 -right-1 text-[8px] w-4 h-4 bg-red-600 rounded-full text-white flex items-center justify-center"><i class="fa-solid fa-xmark"></i></button>` : '';

                itemBox.innerHTML = `
                    ${deleteBtn}
                    <a href="${item.url}" target="_blank" class="text-xl mb-1.5 transition-transform active:scale-90">
                        <i class="${iconMap[item.type] || 'fa-solid fa-link'}"></i>
                    </a>
                    <span class="text-[9px] uppercase tracking-wider text-neutral-400 font-medium">${item.type}</span>
                `;
                container.appendChild(itemBox);
            });
        }

        function triggerAddSocialModal() {
            document.getElementById('social-manager-modal').classList.remove('hidden');
        }

        function saveSocialGridElement() {
            const type = document.getElementById('social-select-type').value;
            const url = document.getElementById('social-input-url').value || '#';
            profileState.socials.push({ type, url });
            document.getElementById('social-manager-modal').classList.add('hidden');
            renderSocialGrid();
        }

        function deleteSocial(index) {
            profileState.socials.splice(index, 1);
            renderSocialGrid();
        }

        function openPanel(tabDefault) {
            document.getElementById('avatar-context-menu').classList.add('hidden');
            document.getElementById('master-config-panel').classList.remove('hidden');
            
            document.getElementById('config-username').value = profileState.username;
            document.getElementById('config-bio').value = profileState.bio;
            document.getElementById('config-banner-url').value = profileState.banner.url.startsWith('blob:') ? '' : profileState.banner.url;
            document.getElementById('config-avatar-url').value = profileState.avatar.url.startsWith('blob:') ? '' : profileState.avatar.url;
            document.getElementById('config-neon-hex').value = profileState.neonColor;

            switchTab(tabDefault);
        }

        function closeMasterPanel() {
            document.getElementById('master-config-panel').classList.add('hidden');
        }

        function switchTab(tabName) {
            ['perfil', 'dominio', 'animacao'].forEach(t => {
                document.getElementById(`panel-tab-${t}`).classList.add('hidden');
                document.getElementById(`tab-btn-${t}`).classList.remove('border-b-2', 'text-white');
            });
            document.getElementById(`panel-tab-${tabName}`).classList.remove('hidden');
            document.getElementById(`tab-btn-${tabName}`).classList.add('border-b-2', 'text-white');
            document.getElementById(`tab-btn-${tabName}`).style.borderBottomColor = profileState.neonColor;
        }

        function liveUpdateBannerHeight(val) {
            document.getElementById('val-banner-y').innerText = val;
            document.getElementById('main-banner-container').style.height = `${val}px`;
            profileState.banner.height = parseInt(val);
        }

        function liveUpdateAvatarDim(axis, val) {
            const node = document.getElementById('avatar-touch-zone');
            if(axis === 'x') {
                document.getElementById('val-avatar-x').innerText = val;
                node.style.width = `${val}px`;
                profileState.avatar.width = parseInt(val);
            } else {
                document.getElementById('val-avatar-y').innerText = val;
                node.style.height = `${val}px`;
                profileState.avatar.height = parseInt(val);
            }
        }

        function applySpotifyUrl() {
            const val = document.getElementById('spotify-link-input').value;
            if(val) {
                let embedUrl = val;
                if(val.includes('spotify.com/track/')) {
                    let parts = val.split('spotify.com/track/')[1].split('?')[0];
                    embedUrl = `https://open.spotify.com/embed/track/${parts}?utm_source=generator`;
                }
                document.getElementById('spotify-player').src = embedUrl;
                profileState.spotifyTrackUrl = embedUrl;
            }
        }

        function updateBannerUrlLive(val) {
            profileState.banner.url = val;
            profileState.banner.isVideo = val.match(/\.(mp4|webm|ogg)/i) != null;
            renderBanner();
        }

        function updateAvatarUrlLive(val) {
            profileState.avatar.url = val;
            profileState.avatar.isVideo = val.match(/\.(mp4|webm|ogg)/i) != null;
            renderAvatar();
        }

        function updateVideoNode(container, src) {
            const existingVid = container.querySelector('video');
            if (existingVid && existingVid.src === src) return; 

            container.innerHTML = "";
            const video = document.createElement('video');
            video.src = src;
            
            video.volume = 0;
            video.muted = true;
            video.defaultMuted = true;
            
            video.setAttribute('autoplay', 'true');
            video.setAttribute('loop', 'true');
            video.setAttribute('muted', 'true');
            video.setAttribute('playsinline', 'true');
            video.style.width = '100%';
            video.style.height = '100%';
            video.style.objectFit = 'cover';
            
            container.appendChild(video);
            video.load();
            const playPromise = video.play();
            if (playPromise !== undefined) {
                playPromise.catch(e => console.log("Autoplay mitigado com sucesso."));
            }
        }

        function updateImageNode(container, src) {
            const existingDiv = container.querySelector('div.bg-cover');
            if (existingDiv && (existingDiv.style.backgroundImage === `url("${src}")` || existingDiv.style.backgroundImage === `url('${src}')`)) return;
            container.innerHTML = `<div class="w-full h-full bg-cover bg-center" style="background-image: url('${src}');"></div>`;
        }

        function uploadLocalMedia(target, slotId = null) {
            const fileInput = slotId ? document.getElementById(`file-upload-${slotId}`) : document.getElementById(`file-${target}-upload`);
            if (!fileInput || !fileInput.files[0]) return;

            const file = fileInput.files[0];
            const objectURL = URL.createObjectURL(file);
            const isVideo = file.type.includes('video');

            if (target === 'banner') {
                profileState.banner.url = objectURL;
                profileState.banner.isVideo = isVideo;
                const txtInput = document.getElementById('config-banner-url');
                if (txtInput) txtInput.value = ""; 
                renderBanner();
            } else if (target === 'avatar') {
                profileState.avatar.url = objectURL;
                profileState.avatar.isVideo = isVideo;
                const txtInput = document.getElementById('config-avatar-url');
                if (txtInput) txtInput.value = ""; 
                renderAvatar();
            } else if (target === 'gallery' && slotId) {
                const idx = profileState.gallery.findIndex(item => item.id === slotId);
                if(idx !== -1) {
                    profileState.gallery[idx].url = objectURL;
                    profileState.gallery[idx].isVideo = isVideo;
                    renderGallerySlot(slotId);
                }
            }
        }

        function renderBanner() {
            const bannerBox = document.getElementById('banner-media-render');
            if (profileState.banner.isVideo) {
                updateVideoNode(bannerBox, profileState.banner.url);
            } else {
                updateImageNode(bannerBox, profileState.banner.url);
            }
            document.getElementById('main-banner-container').style.height = `${profileState.banner.height}px`;
        }

        function renderAvatar() {
            const avatarBox = document.getElementById('avatar-media-render');
            if (profileState.avatar.isVideo) {
                updateVideoNode(avatarBox, profileState.avatar.url);
            } else {
                updateImageNode(avatarBox, profileState.avatar.url);
            }
            const zone = document.getElementById('avatar-touch-zone');
            zone.style.width = `${profileState.avatar.width}px`;
            zone.style.height = `${profileState.avatar.height}px`;
        }

        function renderGallerySlot(id) {
            const slot = profileState.gallery.find(item => item.id === id);
            if (!slot) return;
            const targetBox = document.getElementById(`media-box-${id}`);
            if (!targetBox) return;

            if (slot.url) {
                if (slot.isVideo) {
                    updateVideoNode(targetBox, slot.url);
                } else {
                    updateImageNode(targetBox, slot.url);
                }
            } else {
                targetBox.innerHTML = `<div class="text-neutral-700 text-xs text-center h-full flex flex-col items-center justify-center"><i class="fa-solid fa-clapperboard text-xl mb-1 block"></i>Vazio</div>`;
            }
        }

        function buildGallery() {
            const galleryContainer = document.getElementById('gallery-container');
            galleryContainer.innerHTML = "";

            profileState.gallery.forEach((slot) => {
                const element = document.createElement('div');
                element.className = "flex flex-col items-center gap-4 w-full max-w-[320px]";
                
                element.innerHTML = `
                    <div id="frame-${slot.id}" class="bg-neutral-950 border-2 transition-all duration-500 flex items-center justify-center relative overflow-hidden ${slot.noFrame ? 'rounded-2xl' : 'clip-path-hexagon'}" 
                         style="width: ${slot.width}px; height: ${slot.height}px; border-color: ${profileState.neonColor}; box-shadow: 0 0 15px rgba(0,0,0,0.5);">
                        <div id="media-box-${slot.id}" class="w-full h-full"></div>
                    </div>
                    
                    <div class="editor-only ${isEditorAuthenticated ? '' : 'hidden'} w-full bg-neutral-900/90 p-3 border border-neutral-800 rounded-xl flex flex-col gap-2">
                        <input type="text" placeholder="URL da Mídia (Imagem ou MP4)" class="w-full bg-neutral-950 border border-neutral-800 p-1.5 text-[11px] rounded-md text-white font-mono" value="${slot.url.startsWith('blob:') ? '' : slot.url}" onblur="updateGallerySlotUrl('${slot.id}', this.value)">
                        <div class="flex items-center justify-between text-[10px]">
                            <span class="text-neutral-500">Arquivo local:</span>
                            <input type="file" id="file-upload-${slot.id}" accept="image/*,video/mp4,video/webm" onchange="uploadLocalMedia('gallery', '${slot.id}')" class="w-32 text-purple-400">
                        </div>
                        <div class="grid grid-cols-2 gap-1.5 mt-1">
                            <div>
                                <label class="text-[9px] text-neutral-400 block">Eixo X</label>
                                <input type="range" min="150" max="320" value="${slot.width}" class="w-full accent-purple-600" oninput="liveUpdateSlotDim('${slot.id}', 'width', this.value)">
                            </div>
                            <div>
                                <label class="text-[9px] text-neutral-400 block">Eixo Y</label>
                                <input type="range" min="150" max="420" value="${slot.height}" class="w-full accent-purple-600" oninput="liveUpdateSlotDim('${slot.id}', 'height', this.value)">
                            </div>
                        </div>
                        <div class="flex gap-1.5 mt-1">
                            <button onclick="toggleGalleryFrame('${slot.id}')" class="flex-1 bg-neutral-800 py-1 rounded text-[10px] font-bold uppercase">Moldura</button>
                            <button onclick="deleteGallerySlot('${slot.id}')" class="bg-red-950/40 border border-red-900 text-red-400 px-2 rounded text-[10px]"><i class="fa-solid fa-trash"></i></button>
                        </div>
                    </div>
                `;
                galleryContainer.appendChild(element);
                renderGallerySlot(slot.id); 
            });
        }

        function renderTextLinks() {
            const customLinksContainer = document.getElementById('custom-links-container');
            customLinksContainer.innerHTML = "";
            profileState.textLinks.forEach((link, idx) => {
                const item = document.createElement('div');
                item.className = "w-full flex items-center gap-2";
                item.innerHTML = `
                    <a href="${link.url}" target="_blank" class="flex-1 text-center py-3 bg-neutral-900/60 border border-neutral-800 rounded-xl text-xs uppercase tracking-wider font-medium hover:border-purple-500 transition-colors">
                        ${link.title}
                    </a>
                    ${isEditorAuthenticated ? `<button onclick="deleteCustomLink(${idx})" class="p-3 bg-red-950/30 border border-red-900/40 text-red-400 rounded-xl text-xs"><i class="fa-solid fa-trash"></i></button>` : ''}
                `;
                customLinksContainer.appendChild(item);
            });
        }

        function renderAllDynamicElements() {
            document.getElementById('display-username').innerText = profileState.username;
            document.getElementById('display-bio').innerText = profileState.bio;
            
            const spotifyPlayer = document.getElementById('spotify-player');
            if (spotifyPlayer.src !== profileState.spotifyTrackUrl) {
                spotifyPlayer.src = profileState.spotifyTrackUrl;
            }

            renderBanner();
            renderAvatar();
            renderSocialGrid();
            buildGallery();
            renderTextLinks();
        }

        function liveUpdateSlotDim(id, dimension, value) {
            const idx = profileState.gallery.findIndex(item => item.id === id);
            if(idx !== -1) {
                profileState.gallery[idx][dimension] = parseInt(value);
                const node = document.getElementById(`frame-${id}`);
                if(dimension === 'width') node.style.width = `${value}px`;
                if(dimension === 'height') node.style.height = `${value}px`;
            }
        }

        function toggleGalleryFrame(id) {
            const idx = profileState.gallery.findIndex(item => item.id === id);
            if(idx !== -1) {
                profileState.gallery[idx].noFrame = !profileState.gallery[idx].noFrame;
                const frame = document.getElementById(`frame-${id}`);
                if (profileState.gallery[idx].noFrame) {
                    frame.classList.remove('clip-path-hexagon');
                    frame.classList.add('rounded-2xl');
                } else {
                    frame.classList.remove('rounded-2xl');
                    frame.classList.add('clip-path-hexagon');
                }
            }
        }

        function updateGallerySlotUrl(id, value) {
            const idx = profileState.gallery.findIndex(item => item.id === id);
            if(idx !== -1 && value) {
                profileState.gallery[idx].url = value;
                profileState.gallery[idx].isVideo = value.match(/\.(mp4|webm|ogg)$/i) != null;
                renderGallerySlot(id);
            }
        }

        function deleteGallerySlot(id) {
            profileState.gallery = profileState.gallery.filter(item => item.id !== id);
            buildGallery();
        }

        function addNewGallerySlot() {
            const newId = "g" + Date.now();
            profileState.gallery.push({ id: newId, url: "", isVideo: false, noFrame: false, width: 240, height: 320 });
            buildGallery();
        }

        function addNewCustomLink() {
            const title = prompt("Título do Link:") || "Nova Conexão";
            const url = prompt("Endereço Completo (URL):") || "#";
            profileState.textLinks.push({ title, url });
            renderTextLinks();
        }

        function deleteCustomLink(index) {
            profileState.textLinks.splice(index, 1);
            renderTextLinks();
        }

        let canvas, ctx, particles = [];
        let animationFrameId = null;

        function initializeAtmosphereEngine() {
            canvas = document.getElementById('particles-canvas');
            ctx = canvas.getContext('2d');
            window.addEventListener('resize', resizeAtmosphereCanvas);
            resizeAtmosphereCanvas();
            loopAtmosphere();
        }

        function resizeAtmosphereCanvas() {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
        }

        function setAtmosphere(theme) {
            profileState.atmosphere = theme;
            particles = [];
        }

        function loopAtmosphere() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            generateParticlesByTheme();
            updateAndDrawParticles();
            animationFrameId = requestAnimationFrame(loopAtmosphere);
        }

        function generateParticlesByTheme() {
            if (profileState.atmosphere === 'none') return;
            if (particles.length > 25) return;

            if (Math.random() < 0.03) {
                particles.push({
                    x: Math.random() * canvas.width,
                    y: -20,
                    size: Math.random() * 3 + 2,
                    speedY: Math.random() * 1 + 0.5,
                    speedX: Math.random() * 0.6 - 0.3,
                    alpha: 1,
                    angle: Math.random() * Math.PI,
                    spin: Math.random() * 0.02 - 0.01
                });
            }
        }

        function updateAndDrawParticles() {
            for (let i = particles.length - 1; i >= 0; i--) {
                let p = particles[i];
                p.y += p.speedY;
                p.x += p.speedX;
                p.angle += p.spin;

                ctx.save();
                ctx.globalAlpha = p.alpha;
                ctx.fillStyle = profileState.neonColor;
                ctx.strokeStyle = profileState.neonColor;
                ctx.translate(p.x, p.y);
                ctx.rotate(p.angle);

                if (profileState.atmosphere === 'morcegos') {
                    ctx.beginPath(); ctx.moveTo(0, 0);
                    ctx.quadraticCurveTo(-10, -12, -20, -2); ctx.quadraticCurveTo(-12, 5, 0, 2);
                    ctx.quadraticCurveTo(12, 5, 20, -2); ctx.quadraticCurveTo(10, -12, 0, 0); ctx.fill();
                } else if (profileState.atmosphere === 'cruzes') {
                    ctx.fillRect(-2, -8, 4, 16); ctx.fillRect(-6, -3, 12, 4);
                } else if (profileState.atmosphere === 'folhas' || profileState.atmosphere === 'flor') {
                    ctx.fillStyle = profileState.atmosphere === 'flor' ? '#550000' : '#d97706';
                    ctx.beginPath(); ctx.ellipse(0, 0, p.size * 2, p.size, 0, 0, 2 * Math.PI); ctx.fill();
                } else if (profileState.atmosphere === 'espadas') {
                    ctx.fillRect(-1, -10, 2, 20); ctx.fillRect(-4, 5, 8, 2);
                } else if (profileState.atmosphere === 'nuvens') {
                    ctx.fillStyle = '#111111'; ctx.beginPath(); ctx.arc(0, 0, p.size * 6, 0, Math.PI * 2); ctx.fill();
                } else if (profileState.atmosphere === 'raios') {
                    if(Math.random() < 0.005) {
                        ctx.fillStyle = '#ffffff'; ctx.fillRect(-canvas.width, -canvas.height, canvas.width * 2, canvas.height * 2);
                    }
                }

                ctx.restore();
                if (p.y > canvas.height + 30) particles.splice(i, 1);
            }
        }

        function loadProfileData() {
            if (!supabaseClient) {
                renderAllDynamicElements();
                checkInterfacePermissions();
                return;
            }

            supabaseClient
                .from('profiles')
                .select('*')
                .eq('id', 1)
                .single()
                .then(({ data, error }) => {
                    if (data) {
                        profileState = data.state_object;
                        renderAllDynamicElements();
                    }
                });
        }

        function syncAllToSupabase() {
            profileState.username = document.getElementById('config-username').value;
            profileState.bio = document.getElementById('config-bio').value;
            
            const bannerInp = document.getElementById('config-banner-url').value;
            if (bannerInp) {
                profileState.banner.url = bannerInp;
                profileState.banner.isVideo = bannerInp.match(/\.(mp4|webm|ogg)/i) != null;
            }
            
            const avatarInp = document.getElementById('config-avatar-url').value;
            if (avatarInp) {
                profileState.avatar.url = avatarInp;
                profileState.avatar.isVideo = avatarInp.match(/\.(mp4|webm|ogg)/i) != null;
            }
            
            profileState.neonColor = document.getElementById('config-neon-hex').value;
            document.documentElement.style.setProperty('--neon-base', profileState.neonColor);

            document.getElementById('saving-screen').classList.remove('hidden');

            if (!supabaseClient) {
                setTimeout(() => {
                    document.getElementById('saving-screen').classList.add('hidden');
                    closeMasterPanel();
                    renderAllDynamicElements();
                }, 1400);
                return;
            }

            supabaseClient
                .from('profiles')
                .upsert({ id: 1, state_object: profileState })
                .then(({ error }) => {
                    setTimeout(() => {
                        document.getElementById('saving-screen').classList.add('hidden');
                        if (!error) {
                            closeMasterPanel();
                            renderAllDynamicElements();
                        } else {
                            alert("Falha de persistência de dados no cluster Supabase.");
                        }
                    }, 800);
                });
        }
    </script>
</body>
</html>
