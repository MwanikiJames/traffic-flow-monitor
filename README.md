<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Traffic Jam - Smart Traffic Monitoring</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');
        
        body { font-family: 'Inter', sans-serif; }
        
        .glass-panel {
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.2);
        }
        
        .dark .glass-panel {
            background: rgba(30, 41, 59, 0.95);
            border: 1px solid rgba(255, 255, 255, 0.1);
        }
        
        #map { height: 100vh; width: 100%; z-index: 0; }
        
        .camera-feed {
            aspect-ratio: 16/9;
            background: #000;
            position: relative;
            overflow: hidden;
        }
        
        .camera-feed img {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }
        
        .traffic-indicator {
            animation: pulse 2s infinite;
        }
        
        @keyframes pulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.7; }
        }
        
        .subscription-card {
            transition: transform 0.3s ease, box-shadow 0.3s ease;
        }
        
        .subscription-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04);
        }
        
        .premium-badge {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
        }
        
        .platinum-badge {
            background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
        }
        
        .camera-switch-btn {
            transition: all 0.3s ease;
        }
        
        .camera-switch-btn:active {
            transform: scale(0.95);
        }
        
        .video-controls {
            background: linear-gradient(to top, rgba(0,0,0,0.8), transparent);
        }
        
        /* Custom scrollbar */
        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        
        ::-webkit-scrollbar-track {
            background: #f1f1f1;
        }
        
        ::-webkit-scrollbar-thumb {
            background: #888;
            border-radius: 3px;
        }
        
        ::-webkit-scrollbar-thumb:hover {
            background: #555;
        }
    </style>
</head>
<body class="bg-gray-50 dark:bg-slate-900 text-gray-900 dark:text-white overflow-hidden">

    <!-- Authentication / Subscription Screen -->
    <div id="authScreen" class="fixed inset-0 z-50 bg-white dark:bg-slate-900 overflow-y-auto">
        <div class="min-h-screen flex flex-col">
            <!-- Header -->
            <div class="p-6 border-b dark:border-slate-700">
                <div class="flex items-center justify-between max-w-6xl mx-auto">
                    <div class="flex items-center gap-3">
                        <div class="w-12 h-12 bg-blue-600 rounded-xl flex items-center justify-center">
                            <i data-lucide="traffic-cone" class="text-white w-6 h-6"></i>
                        </div>
                        <div>
                            <h1 class="text-2xl font-bold text-blue-600">Traffic Jam</h1>
                            <p class="text-sm text-gray-500 dark:text-gray-400">Smart Road Monitoring</p>
                        </div>
                    </div>
                    <button onclick="toggleDarkMode()" class="p-2 rounded-lg hover:bg-gray-100 dark:hover:bg-slate-800">
                        <i data-lucide="moon" class="w-5 h-5 dark:hidden"></i>
                        <i data-lucide="sun" class="w-5 h-5 hidden dark:block"></i>
                    </button>
                </div>
            </div>

            <!-- Subscription Plans -->
            <div class="flex-1 p-6 max-w-6xl mx-auto w-full">
                <div class="text-center mb-12">
                    <h2 class="text-3xl font-bold mb-4">Choose Your Plan</h2>
                    <p class="text-gray-600 dark:text-gray-400">Stay ahead of traffic with real-time road monitoring</p>
                </div>

                <div class="grid md:grid-cols-3 gap-6 mb-12">
                    <!-- Normal Plan -->
                    <div class="subscription-card rounded-2xl border-2 border-gray-200 dark:border-slate-700 p-6 relative bg-white dark:bg-slate-800">
                        <div class="mb-4">
                            <span class="inline-block px-3 py-1 rounded-full text-sm font-semibold bg-gray-100 dark:bg-slate-700 text-gray-700 dark:text-gray-300">Normal</span>
                        </div>
                        <div class="mb-6">
                            <span class="text-4xl font-bold">100<span class="text-lg font-normal text-gray-500">sh/month</span></span>
                        </div>
                        <ul class="space-y-3 mb-6 text-sm">
                            <li class="flex items-center gap-2">
                                <i data-lucide="check" class="w-4 h-4 text-green-500"></i>
                                <span>Real-time traffic updates</span>
                            </li>
                            <li class="flex items-center gap-2">
                                <i data-lucide="check" class="w-4 h-4 text-green-500"></i>
                                <span>Road congestion status</span>
                            </li>
                            <li class="flex items-center gap-2">
                                <i data-lucide="check" class="w-4 h-4 text-green-500"></i>
                                <span>Alternative route suggestions</span>
                            </li>
                            <li class="flex items-center gap-2 opacity-50">
                                <i data-lucide="x" class="w-4 h-4 text-red-400"></i>
                                <span>Live CCTV access</span>
                            </li>
                            <li class="flex items-center gap-2 opacity-50">
                                <i data-lucide="x" class="w-4 h-4 text-red-400"></i>
                                <span>Video recording</span>
                            </li>
                        </ul>
                        <button onclick="selectPlan('normal')" class="w-full py-3 rounded-xl border-2 border-blue-600 text-blue-600 font-semibold hover:bg-blue-50 dark:hover:bg-blue-900/20 transition">
                            Select Normal
                        </button>
                    </div>

                    <!-- Premium Plan -->
                    <div class="subscription-card rounded-2xl border-2 border-blue-500 p-6 relative bg-white dark:bg-slate-800 shadow-xl">
                        <div class="absolute -top-3 left-1/2 transform -translate-x-1/2">
                            <span class="premium-badge text-white px-4 py-1 rounded-full text-sm font-bold shadow-lg">Most Popular</span>
                        </div>
                        <div class="mb-4 mt-2">
                            <span class="inline-block px-3 py-1 rounded-full text-sm font-semibold bg-blue-100 dark:bg-blue-900/30 text-blue-700 dark:text-blue-300">Premium</span>
                        </div>
                        <div class="mb-6">
                            <span class="text-4xl font-bold text-blue-600">500<span class="text-lg font-normal text-gray-500">sh/month</span></span>
                        </div>
                        <ul class="space-y-3 mb-6 text-sm">
                            <li class="flex items-center gap-2">
                                <i data-lucide="check" class="w-4 h-4 text-green-500"></i>
                                <span>Everything in Normal</span>
                            </li>
                            <li class="flex items-center gap-2">
                                <i data-lucide="check" class="w-4 h-4 text-green-500"></i>
                                <span>Live CCTV streaming</span>
                            </li>
                            <li class="flex items-center gap-2">
                                <i data-lucide="check" class="w-4 h-4 text-green-500"></i>
                                <span>Auto camera switching</span>
                            </li>
                            <li class="flex items-center gap-2">
                                <i data-lucide="check" class="w-4 h-4 text-green-500"></i>
                                <span>HD quality video</span>
                            </li>
                            <li class="flex items-center gap-2 opacity-50">
                                <i data-lucide="x" class="w-4 h-4 text-red-400"></i>
                                <span>Save & replay videos</span>
                            </li>
                        </ul>
                        <button onclick="selectPlan('premium')" class="w-full py-3 rounded-xl bg-blue-600 text-white font-semibold hover:bg-blue-700 transition shadow-lg">
                            Select Premium
                        </button>
                    </div>

                    <!-- Platinum Plan -->
                    <div class="subscription-card rounded-2xl border-2 border-purple-500 p-6 relative bg-white dark:bg-slate-800">
                        <div class="absolute -top-3 left-1/2 transform -translate-x-1/2">
                            <span class="platinum-badge text-white px-4 py-1 rounded-full text-sm font-bold shadow-lg">Best Value</span>
                        </div>
                        <div class="mb-4 mt-2">
                            <span class="inline-block px-3 py-1 rounded-full text-sm font-semibold bg-purple-100 dark:bg-purple-900/30 text-purple-700 dark:text-purple-300">Platinum</span>
                        </div>
                        <div class="mb-6">
                            <span class="text-4xl font-bold text-purple-600">1000<span class="text-lg font-normal text-gray-500">sh/month</span></span>
                        </div>
                        <ul class="space-y-3 mb-6 text-sm">
                            <li class="flex items-center gap-2">
                                <i data-lucide="check" class="w-4 h-4 text-green-500"></i>
                                <span>Everything in Premium</span>
                            </li>
                            <li class="flex items-center gap-2">
                                <i data-lucide="check" class="w-4 h-4 text-green-500"></i>
                                <span>Save videos to device</span>
                            </li>
                            <li class="flex items-center gap-2">
                                <i data-lucide="check" class="w-4 h-4 text-green-500"></i>
                                <span>Replay past 24h footage</span>
                            </li>
                            <li class="flex items-center gap-2">
                                <i data-lucide="check" class="w-4 h-4 text-green-500"></i>
                                <span>4K Ultra HD quality</span>
                            </li>
                            <li class="flex items-center gap-2">
                                <i data-lucide="check" class="w-4 h-4 text-green-500"></i>
                                <span>Priority support</span>
                            </li>
                        </ul>
                        <button onclick="selectPlan('platinum')" class="w-full py-3 rounded-xl bg-purple-600 text-white font-semibold hover:bg-purple-700 transition shadow-lg">
                            Select Platinum
                        </button>
                    </div>
                </div>

                <!-- Demo Mode -->
                <div class="text-center">
                    <button onclick="selectPlan('demo')" class="text-gray-500 hover:text-gray-700 dark:hover:text-gray-300 underline">
                        Continue in Demo Mode
                    </button>
                </div>
            </div>
        </div>
    </div>

    <!-- Main App Interface -->
    <div id="mainApp" class="hidden h-screen flex flex-col">
        <!-- Top Navigation -->
        <div class="glass-panel z-20 px-4 py-3 flex items-center justify-between shadow-sm">
            <div class="flex items-center gap-3">
                <div class="w-10 h-10 bg-blue-600 rounded-lg flex items-center justify-center">
                    <i data-lucide="traffic-cone" class="text-white w-5 h-5"></i>
                </div>
                <div>
                    <h1 class="font-bold text-lg leading-tight">Traffic Jam</h1>
                    <span id="currentPlanBadge" class="text-xs px-2 py-0.5 rounded-full bg-gray-100 dark:bg-slate-700">Normal</span>
                </div>
            </div>
            
            <div class="flex items-center gap-2">
                <button onclick="toggleTrafficLayer()" class="p-2 rounded-lg hover:bg-gray-100 dark:hover:bg-slate-800 relative" title="Traffic Status">
                    <i data-lucide="activity" class="w-5 h-5"></i>
                    <span id="trafficStatusIndicator" class="absolute top-1 right-1 w-2 h-2 bg-green-500 rounded-full traffic-indicator"></span>
                </button>
                <button onclick="toggleCameraPanel()" id="cameraBtn" class="p-2 rounded-lg hover:bg-gray-100 dark:hover:bg-slate-800 hidden" title="Live Cameras">
                    <i data-lucide="video" class="w-5 h-5"></i>
                </button>
                <button onclick="showSubscriptionModal()" class="p-2 rounded-lg hover:bg-gray-100 dark:hover:bg-slate-800" title="Subscription">
                    <i data-lucide="crown" class="w-5 h-5 text-yellow-500"></i>
                </button>
                <button onclick="toggleSettings()" class="p-2 rounded-lg hover:bg-gray-100 dark:hover:bg-slate-800">
                    <i data-lucide="settings" class="w-5 h-5"></i>
                </button>
            </div>
        </div>

        <!-- Map Container -->
        <div class="flex-1 relative">
            <div id="map"></div>
            
            <!-- Traffic Legend -->
            <div class="absolute bottom-4 left-4 glass-panel rounded-lg p-3 shadow-lg z-[1000] text-xs space-y-2">
                <div class="font-semibold mb-1">Traffic Status</div>
                <div class="flex items-center gap-2">
                    <span class="w-3 h-3 rounded-full bg-green-500"></span>
                    <span>Clear (0-30%)</span>
                </div>
                <div class="flex items-center gap-2">
                    <span class="w-3 h-3 rounded-full bg-yellow-500"></span>
                    <span>Moderate (30-60%)</span>
                </div>
                <div class="flex items-center gap-2">
                    <span class="w-3 h-3 rounded-full bg-red-500"></span>
                    <span>Heavy (60%+)</span>
                </div>
                <div class="flex items-center gap-2">
                    <span class="w-3 h-3 rounded-full bg-black"></span>
                    <span>Blocked</span>
                </div>
            </div>

            <!-- GPS Toggle -->
            <button onclick="toggleGPS()" id="gpsBtn" class="absolute bottom-4 right-4 w-12 h-12 bg-blue-600 text-white rounded-full shadow-lg flex items-center justify-center z-[1000] hover:bg-blue-700 transition">
                <i data-lucide="navigation" class="w-5 h-5"></i>
            </button>
        </div>

        <!-- Camera Panel (Premium/Platinum only) -->
        <div id="cameraPanel" class="hidden fixed inset-x-0 bottom-0 z-30 bg-white dark:bg-slate-900 rounded-t-3xl shadow-2xl transform transition-transform duration-300 max-h-[70vh] overflow-hidden flex flex-col">
            <!-- Drag Handle -->
            <div class="w-full pt-2 pb-1 flex justify-center cursor-pointer" onclick="toggleCameraPanel()">
                <div class="w-12 h-1 bg-gray-300 dark:bg-slate-600 rounded-full"></div>
            </div>

            <!-- Camera Header -->
            <div class="px-4 py-3 border-b dark:border-slate-700 flex items-center justify-between">
                <div>
                    <h3 class="font-bold text-lg">Live Traffic Cameras</h3>
                    <p class="text-sm text-gray-500 dark:text-gray-400" id="currentCameraLocation">Mombasa Road - Camera #12</p>
                </div>
                <div class="flex items-center gap-2">
                    <span id="liveIndicator" class="flex items-center gap-1 text-xs font-semibold text-red-500">
                        <span class="w-2 h-2 bg-red-500 rounded-full animate-pulse"></span>
                        LIVE
                    </span>
                    <button onclick="toggleCameraPanel()" class="p-1">
                        <i data-lucide="x" class="w-5 h-5"></i>
                    </button>
                </div>
            </div>

            <!-- Main Camera Feed -->
            <div class="relative bg-black">
                <div class="camera-feed">
                    <img id="mainCameraFeed" src="http://static.photos/technology/640x360/101" alt="Live Camera Feed" class="w-full h-full object-cover">
                    
                    <!-- Platinum Controls -->
                    <div id="platinumControls" class="hidden absolute top-4 right-4 flex gap-2">
                        <button onclick="saveCurrentVideo()" class="bg-black/50 backdrop-blur text-white px-3 py-1 rounded-lg text-sm flex items-center gap-1 hover:bg-black/70">
                            <i data-lucide="download" class="w-4 h-4"></i>
                            Save
                        </button>
                        <button onclick="showReplayModal()" class="bg-black/50 backdrop-blur text-white px-3 py-1 rounded-lg text-sm flex items-center gap-1 hover:bg-black/70">
                            <i data-lucide="history" class="w-4 h-4"></i>
                            Replay
                        </button>
                    </div>

                    <!-- Video Controls Overlay -->
                    <div class="video-controls absolute bottom-0 left-0 right-0 p-4 flex items-center justify-between text-white">
                        <div class="flex items-center gap-3">
                            <button onclick="togglePlay()" class="w-10 h-10 rounded-full bg-white/20 flex items-center justify-center hover:bg-white/30">
                                <i data-lucide="pause" class="w-5 h-5" id="playPauseIcon"></i>
                            </button>
                            <span class="text-sm font-mono" id="videoTime">00:00 / LIVE</span>
                        </div>
                        <div class="flex items-center gap-2">
                            <button onclick="toggleFullscreen()" class="p-2 hover:bg-white/20 rounded-lg">
                                <i data-lucide="maximize" class="w-5 h-5"></i>
                            </button>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Camera Navigation -->
            <div class="p-4 bg-gray-50 dark:bg-slate-800">
                <div class="flex items-center justify-between mb-3">
                    <span class="text-sm font-semibold text-gray-600 dark:text-gray-400">Auto-switching enabled</span>
                    <label class="relative inline-flex items-center cursor-pointer">
                        <input type="checkbox" id="autoSwitchToggle" class="sr-only peer" checked onchange="toggleAutoSwitch()">
                        <div class="w-11 h-6 bg-gray-200 peer-focus:outline-none rounded-full peer dark:bg-gray-700 peer-checked:after:translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-white after:border-gray-300 after:border after:rounded-full after:h-5 after:w-5 after:transition-all peer-checked:bg-blue-600"></div>
                    </label>
                </div>

                <!-- Camera Strip -->
                <div class="flex gap-3 overflow-x-auto pb-2" id="cameraStrip">
                    <!-- Camera thumbnails generated by JS -->
                </div>

                <!-- Jump Ahead Controls -->
                <div class="mt-4 flex items-center justify-between">
                    <span class="text-sm text-gray-600 dark:text-gray-400">Manual Override:</span>
                    <div class="flex gap-2">
                        <button onclick="jumpCamera(-2)" class="camera-switch-btn px-4 py-2 bg-gray-200 dark:bg-slate-700 rounded-lg text-sm font-medium hover:bg-gray-300 dark:hover:bg-slate-600">
                            <i data-lucide="skip-back" class="w-4 h-4 inline mr-1"></i>
                            -200m
                        </button>
                        <button onclick="jumpCamera(-1)" class="camera-switch-btn px-4 py-2 bg-gray-200 dark:bg-slate-700 rounded-lg text-sm font-medium hover:bg-gray-300 dark:hover:bg-slate-600">
                            <i data-lucide="chevron-left" class="w-4 h-4 inline mr-1"></i>
                            -100m
                        </button>
                        <button onclick="jumpCamera(1)" class="camera-switch-btn px-4 py-2 bg-blue-600 text-white rounded-lg text-sm font-medium hover:bg-blue-700">
                            +100m
                            <i data-lucide="chevron-right" class="w-4 h-4 inline ml-1"></i>
                        </button>
                        <button onclick="jumpCamera(2)" class="camera-switch-btn px-4 py-2 bg-blue-600 text-white rounded-lg text-sm font-medium hover:bg-blue-700">
                            +200m
                            <i data-lucide="skip-forward" class="w-4 h-4 inline ml-1"></i>
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <!-- Replay Modal (Platinum only) -->
        <div id="replayModal" class="hidden fixed inset-0 z-50 bg-black/80 flex items-center justify-center p-4">
            <div class="bg-white dark:bg-slate-900 rounded-2xl max-w-2xl w-full max-h-[80vh] overflow-hidden flex flex-col">
                <div class="p-4 border-b dark:border-slate-700 flex items-center justify-between">
                    <h3 class="font-bold text-lg">Replay Past 24 Hours</h3>
                    <button onclick="closeReplayModal()" class="p-1 hover:bg-gray-100 dark:hover:bg-slate-800 rounded">
                        <i data-lucide="x" class="w-5 h-5"></i>
                    </button>
                </div>
                <div class="p-4 flex-1 overflow-y-auto">
                    <div class="mb-4">
                        <label class="block text-sm font-medium mb-2">Select Time</label>
                        <input type="datetime-local" id="replayTime" class="w-full p-2 border rounded-lg dark:bg-slate-800 dark:border-slate-700">
                    </div>
                    <div id="replayVideoContainer" class="aspect-video bg-black rounded-lg flex items-center justify-center">
                        <p class="text-white/70">Select a time to view historical footage</p>
                    </div>
                </div>
            </div>
        </div>

        <!-- Upgrade Modal -->
        <div id="upgradeModal" class="hidden fixed inset-0 z-50 bg-black/50 flex items-center justify-center p-4 backdrop-blur-sm">
            <div class="bg-white dark:bg-slate-900 rounded-2xl max-w-md w-full p-6 shadow-2xl">
                <div class="text-center mb-6">
                    <div class="w-16 h-16 bg-yellow-100 dark:bg-yellow-900/30 rounded-full flex items-center justify-center mx-auto mb-4">
                        <i data-lucide="crown" class="w-8 h-8 text-yellow-600"></i>
                    </div>
                    <h3 class="text-xl font-bold mb-2">Upgrade Required</h3>
                    <p class="text-gray-600 dark:text-gray-400">This feature is not available in your current plan.</p>
                </div>
                <div class="space-y-3">
                    <button onclick="showSubscriptionModal()" class="w-full py-3 bg-blue-600 text-white rounded-xl font-semibold hover:bg-blue-700">
                        View Plans
                    </button>
                    <button onclick="closeUpgradeModal()" class="w-full py-3 border border-gray-300 dark:border-slate-700 rounded-xl font-semibold hover:bg-gray-50 dark:hover:bg-slate-800">
                        Maybe Later
                    </button>
                </div>
            </div>
        </div>
    </div>

    <script>
        // Global Variables
        let currentPlan = 'normal';
        let map;
        let userMarker;
        let watchId;
        let currentCameraIndex = 0;
        let autoSwitchEnabled = true;
        let cameras = [];
        let isPlaying = true;
        let trafficLayer = true;
        let trafficLayersGroup;

        // Initialize
        document.addEventListener('DOMContentLoaded', () => {
            // Check if libraries loaded
            if (typeof lucide === 'undefined') {
                console.error('Lucide icons failed to load');
                return;
            }
            if (typeof L === 'undefined') {
                console.error('Leaflet failed to load');
                return;
            }
            
            try {
                lucide.createIcons();
                initMap();
                generateMockCameras();
            } catch (error) {
                console.error('Initialization error:', error);
                alert('Failed to initialize app. Please check console for details.');
            }
        });

        // Plan Selection
        function selectPlan(plan) {
            currentPlan = plan;
            
            // Update UI based on plan
            const badge = document.getElementById('currentPlanBadge');
            const cameraBtn = document.getElementById('cameraBtn');
            const platinumControls = document.getElementById('platinumControls');
            
            if (plan === 'demo') {
                badge.textContent = 'Demo';
                badge.className = 'text-xs px-2 py-0.5 rounded-full bg-gray-100 dark:bg-slate-700';
                cameraBtn.classList.remove('hidden');
                // Demo gets premium features for 5 minutes
                setTimeout(() => {
                    alert('Demo period ended. Please subscribe to continue using camera features.');
                    cameraBtn.classList.add('hidden');
                }, 300000);
            } else if (plan === 'normal') {
                badge.textContent = 'Normal';
                badge.className = 'text-xs px-2 py-0.5 rounded-full bg-gray-100 dark:bg-slate-700';
                cameraBtn.classList.add('hidden');
            } else if (plan === 'premium') {
                badge.textContent = 'Premium';
                badge.className = 'text-xs px-2 py-0.5 rounded-full bg-blue-100 dark:bg-blue-900/30 text-blue-700 dark:text-blue-300';
                cameraBtn.classList.remove('hidden');
                platinumControls.classList.add('hidden');
            } else if (plan === 'platinum') {
                badge.textContent = 'Platinum';
                badge.className = 'text-xs px-2 py-0.5 rounded-full bg-purple-100 dark:bg-purple-900/30 text-purple-700 dark:text-purple-300';
                cameraBtn.classList.remove('hidden');
                platinumControls.classList.remove('hidden');
            }
            
            // Hide auth screen, show main app
            document.getElementById('authScreen').classList.add('hidden');
            document.getElementById('mainApp').classList.remove('hidden');
            
            // Refresh icons
            lucide.createIcons();
        }

        // Map Initialization
        function initMap() {
            // Default to Nairobi
            map = L.map('map').setView([-1.2921, 36.8219], 13);
            
            L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
                attribution: '© OpenStreetMap contributors'
            }).addTo(map);
            
            // Add mock traffic sensors
            addTrafficSensors();
        }

        function addTrafficSensors() {
            const roads = [
                { name: 'Mombasa Road', coords: [[-1.2921, 36.8219], [-1.3000, 36.8500]], status: 'moderate' },
                { name: 'Uhuru Highway', coords: [[-1.2850, 36.8200], [-1.2900, 36.8300]], status: 'heavy' },
                { name: 'Jogoo Road', coords: [[-1.2800, 36.8600], [-1.2750, 36.8800]], status: 'clear' },
                { name: 'Langata Road', coords: [[-1.3300, 36.7900], [-1.3500, 36.8100]], status: 'blocked' }
            ];
            
            roads.forEach(road => {
                const color = road.status === 'clear' ? '#22c55e' : 
                             road.status === 'moderate' ? '#eab308' : 
                             road.status === 'heavy' ? '#ef4444' : '#000000';
                
                const weight = road.status === 'blocked' ? 6 : 4;
                
                const polyline = L.polyline(road.coords, {
                    color: color,
                    weight: weight,
                    opacity: 0.8
                }).bindPopup(`
                    <div class="font-semibold">${road.name}</div>
                    <div class="text-sm capitalize text-gray-600">Status: ${road.status}</div>
                    <div class="text-xs text-gray-500">Updated 2 mins ago</div>
                `);
                
                trafficLayersGroup.addLayer(polyline);
                
                // Add sensor markers every 100m (simulated)
                for (let i = 0; i < road.coords.length - 1; i++) {
                    const start = road.coords[i];
                    const end = road.coords[i+1];
                    const mid = [(start[0] + end[0])/2, (start[1] + end[1])/2];
                    
                    const marker = L.circleMarker(mid, {
                        radius: 6,
                        fillColor: color,
                        color: '#fff',
                        weight: 2,
                        opacity: 1,
                        fillOpacity: 0.8
                    }).bindPopup(`
                        <div class="text-xs font-semibold">Sensor ID: ${Math.random().toString(36).substr(2, 6).toUpperCase()}</div>
                        <div class="text-xs">Road: ${road.name}</div>
                        <div class="text-xs">Distance: ${(i+1)*100}m</div>
                    `);
                    
                    trafficLayersGroup.addLayer(marker);
                }
            });
            
            trafficLayersGroup.addTo(map);
        }

        // Generate Mock Cameras
        function generateMockCameras() {
            const baseCoords = [-1.2921, 36.8219];
            for (let i = 0; i < 10; i++) {
                cameras.push({
                    id: i,
                    name: `Camera #${i + 1}`,
                    location: `Mombasa Road - ${i * 100}m`,
                    lat: baseCoords[0] + (i * 0.001),
                    lng: baseCoords[1] + (i * 0.001),
                    image: `http://static.photos/technology/640x360/${100 + i}`,
                    thumbnail: `http://static.photos/technology/320x240/${100 + i}`
                });
            }
            renderCameraStrip();
        }

        function renderCameraStrip() {
            const strip = document.getElementById('cameraStrip');
            strip.innerHTML = cameras.map((cam, idx) => `
                <div onclick="selectCamera(${idx})" class="flex-shrink-0 w-32 cursor-pointer ${idx === currentCameraIndex ? 'ring-2 ring-blue-500 rounded-lg' : ''}">
                    <div class="relative rounded-lg overflow-hidden aspect-video bg-gray-200">
                        <img src="${cam.thumbnail}" class="w-full h-full object-cover ${idx === currentCameraIndex ? 'opacity-100' : 'opacity-70'}">
                        ${idx === currentCameraIndex ? '<div class="absolute top-1 left-1 w-2 h-2 bg-red-500 rounded-full animate-pulse"></div>' : ''}
                        <div class="absolute bottom-0 left-0 right-0 bg-black/50 text-white text-xs p-1 truncate">
                            ${cam.name}
                        </div>
                    </div>
                </div>
            `).join('');
        }

        function selectCamera(index) {
            currentCameraIndex = index;
            const cam = cameras[index];
            document.getElementById('mainCameraFeed').src = cam.image;
            document.getElementById('currentCameraLocation').textContent = cam.location;
            renderCameraStrip();
            
            // Center map on camera
            map.setView([cam.lat, cam.lng], 16);
        }

        // GPS and Auto-switching
        function toggleGPS() {
            const btn = document.getElementById('gpsBtn');
            
            if (watchId) {
                navigator.geolocation.clearWatch(watchId);
                watchId = null;
                btn.classList.remove('bg-red-600');
                btn.classList.add('bg-blue-600');
                alert('GPS tracking stopped');
            } else {
                if ("geolocation" in navigator) {
                    btn.classList.remove('bg-blue-600');
                    btn.classList.add('bg-green-600');
                    
                    watchId = navigator.geolocation.watchPosition(
                        (position) => {
                            const lat = position.coords.latitude;
                            const lng = position.coords.longitude;
                            
                            if (userMarker) {
                                map.removeLayer(userMarker);
                            }
                            
                            userMarker = L.marker([lat, lng], {
                                icon: L.divIcon({
                                    className: 'custom-div-icon',
                                    html: "<div style='background-color:#3b82f6;width:16px;height:16px;border-radius:50%;border:3px solid white;box-shadow:0 2px 4px rgba(0,0,0,0.3);'></div>",
                                    iconSize: [16, 16],
                                    iconAnchor: [8, 8]
                                })
                            }).addTo(map).bindPopup('You are here');
                            
                            map.setView([lat, lng], 16);
                            
                            // Auto-switch camera if enabled
                            if (autoSwitchEnabled && currentPlan !== 'normal') {
                                autoSwitchCamera(lat, lng);
                            }
                        },
                        (error) => {
                            console.error('GPS Error:', error);
                            alert('Unable to access GPS. Please enable location services.');
                        },
                        { enableHighAccuracy: true, maximumAge: 10000, timeout: 5000 }
                    );
                } else {
                    alert('Geolocation is not supported by your browser');
                }
            }
        }

        function autoSwitchCamera(userLat, userLng) {
            // Find nearest camera
            let nearestIndex = 0;
            let minDistance = Infinity;
            
            cameras.forEach((cam, idx) => {
                const distance = Math.sqrt(
                    Math.pow(cam.lat - userLat, 2) + 
                    Math.pow(cam.lng - userLng, 2)
                );
                if (distance < minDistance) {
                    minDistance = distance;
                    nearestIndex = idx;
                }
            });
            
            // Switch if different and within reasonable distance (100m approx)
            if (nearestIndex !== currentCameraIndex && minDistance < 0.001) {
                selectCamera(nearestIndex);
            }
        }

        function toggleAutoSwitch() {
            autoSwitchEnabled = document.getElementById('autoSwitchToggle').checked;
        }

        function jumpCamera(steps) {
            const newIndex = currentCameraIndex + steps;
            if (newIndex >= 0 && newIndex < cameras.length) {
                selectCamera(newIndex);
            } else {
                alert('No cameras available in that direction');
            }
        }

        // UI Toggles
        function toggleCameraPanel() {
            const panel = document.getElementById('cameraPanel');
            if (currentPlan === 'normal') {
                showUpgradeModal();
                return;
            }
            panel.classList.toggle('hidden');
        }

        let trafficLayersGroup = L.layerGroup();

        function toggleTrafficLayer() {
            trafficLayer = !trafficLayer;
            const indicator = document.getElementById('trafficStatusIndicator');
            if (trafficLayer) {
                indicator.classList.remove('hidden');
                indicator.style.display = 'flex';
                // Re-add traffic sensors if they were removed
                if (!map.hasLayer(trafficLayersGroup)) {
                    map.addLayer(trafficLayersGroup);
                }
            } else {
                indicator.classList.add('hidden');
                indicator.style.display = 'none';
                if (map.hasLayer(trafficLayersGroup)) {
                    map.removeLayer(trafficLayersGroup);
                }
            }
        }

        function toggleDarkMode() {
            document.documentElement.classList.toggle('dark');
        }

        function toggleSettings() {
            alert('Settings: Notifications, Language, Help & Support');
        }

        // Platinum Features
        function saveCurrentVideo() {
            if (currentPlan !== 'platinum') {
                showUpgradeModal();
                return;
            }
            
            // Simulate download
            const link = document.createElement('a');
            link.href = cameras[currentCameraIndex].image;
            link.download = `traffic-cam-${cameras[currentCameraIndex].name}-${Date.now()}.jpg`;
            link.click();
            
            alert('Video saved to your device!');
        }

        function showReplayModal() {
            if (currentPlan !== 'platinum') {
                showUpgradeModal();
                return;
            }
            document.getElementById('replayModal').classList.remove('hidden');
        }

        function closeReplayModal() {
            document.getElementById('replayModal').classList.add('hidden');
        }

        function showSubscriptionModal() {
            document.getElementById('authScreen').classList.remove('hidden');
            document.getElementById('mainApp').classList.add('hidden');
        }

        function showUpgradeModal() {
            document.getElementById('upgradeModal').classList.remove('hidden');
        }

        function closeUpgradeModal() {
            document.getElementById('upgradeModal').classList.add('hidden');
        }

        // Video Controls
        function togglePlay() {
            isPlaying = !isPlaying;
            const icon = document.getElementById('playPauseIcon');
            if (isPlaying) {
                icon.setAttribute('data-lucide', 'pause');
                document.getElementById('liveIndicator').classList.remove('hidden');
            } else {
                icon.setAttribute('data-lucide', 'play');
                document.getElementById('liveIndicator').classList.add('hidden');
            }
            lucide.createIcons();
        }

        function toggleFullscreen() {
            const feed = document.querySelector('.camera-feed');
            if (!document.fullscreenElement) {
                feed.requestFullscreen();
            } else {
                document.exitFullscreen();
            }
        }

        // Replay functionality
        document.getElementById('replayTime')?.addEventListener('change', function(e) {
            const container = document.getElementById('replayVideoContainer');
            container.innerHTML = `
                <img src="${cameras[currentCameraIndex].image}?t=${Date.now()}" class="w-full h-full object-cover opacity-70">
                <div class="absolute inset-0 flex items-center justify-center">
                    <div class="bg-black/70 text-white px-4 py-2 rounded-lg">
                        <i data-lucide="history" class="w-6 h-6 inline mr-2"></i>
                        Historical Footage: ${new Date(e.target.value).toLocaleString()}
                    </div>
                </div>
            `;
            lucide.createIcons();
        });

        // Close modals on outside click
        window.onclick = function(event) {
            const upgradeModal = document.getElementById('upgradeModal');
            const replayModal = document.getElementById('replayModal');
            
            if (event.target === upgradeModal) {
                closeUpgradeModal();
            }
            if (event.target === replayModal) {
                closeReplayModal();
            }
        }
    </script>
</body>
</html>
