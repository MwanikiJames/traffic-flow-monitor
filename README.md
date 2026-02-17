<html lang="en" class="scroll-smooth">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Traffic Flow Monitor - Beat The Traffic Jam</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                    },
                    colors: {
                        traffic: {
                            dark: '#0f172a',
                            card: '#1e293b',
                            accent: '#3b82f6',
                            success: '#10b981',
                            warning: '#f59e0b',
                            danger: '#ef4444',
                        }
                    },
                    animation: {
                        'pulse-slow': 'pulse 3s cubic-bezier(0.4, 0, 0.6, 1) infinite',
                        'float': 'float 6s ease-in-out infinite',
                        'slide-up': 'slideUp 0.5s ease-out',
                    },
                    keyframes: {
                        float: {
                            '0%, 100%': { transform: 'translateY(0)' },
                            '50%': { transform: 'translateY(-20px)' },
                        },
                        slideUp: {
                            '0%': { transform: 'translateY(20px)', opacity: '0' },
                            '100%': { transform: 'translateY(0)', opacity: '1' },
                        }
                    }
                }
            }
        }
    </script>
    <style>
        .glass-panel {
            background: rgba(30, 41, 59, 0.7);
            backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.1);
        }
        .gradient-text {
            background: linear-gradient(135deg, #3b82f6 0%, #8b5cf6 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }
        .live-indicator {
            box-shadow: 0 0 0 0 rgba(239, 68, 68, 0.7);
            animation: pulse-red 2s infinite;
        }
        @keyframes pulse-red {
            0% { box-shadow: 0 0 0 0 rgba(239, 68, 68, 0.7); }
            70% { box-shadow: 0 0 0 10px rgba(239, 68, 68, 0); }
            100% { box-shadow: 0 0 0 0 rgba(239, 68, 68, 0); }
        }
        .cctv-grid {
            background-image: 
                linear-gradient(rgba(59, 130, 246, 0.1) 1px, transparent 1px),
                linear-gradient(90deg, rgba(59, 130, 246, 0.1) 1px, transparent 1px);
            background-size: 20px 20px;
        }
    </style>
</head>
<body class="bg-traffic-dark text-white font-sans antialiased overflow-x-hidden">

    <!-- Navigation -->
    <nav class="fixed w-full z-50 glass-panel border-b border-slate-700/50">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex justify-between items-center h-16">
                <div class="flex items-center space-x-3">
                    <div class="relative">
                        <i data-lucide="activity" class="w-8 h-8 text-blue-500"></i>
                        <div class="absolute -top-1 -right-1 w-3 h-3 bg-green-500 rounded-full live-indicator"></div>
                    </div>
                    <span class="text-xl font-bold tracking-tight">Traffic<span class="text-blue-500">Flow</span></span>
                </div>
                
                <div class="hidden md:flex items-center space-x-8">
                    <a href="#features" class="text-sm text-slate-300 hover:text-white transition-colors">Features</a>
                    <a href="#live-monitor" class="text-sm text-slate-300 hover:text-white transition-colors">Live Monitor</a>
                    <a href="#analytics" class="text-sm text-slate-300 hover:text-white transition-colors">Analytics</a>
                </div>

                <div class="flex items-center space-x-4">
                    <button onclick="toggleLoginModal()" class="bg-blue-600 hover:bg-blue-700 text-white px-6 py-2 rounded-full text-sm font-medium transition-all transform hover:scale-105 shadow-lg shadow-blue-600/30">
                        Sign In
                    </button>
                </div>
            </div>
        </div>
    </nav>

    <!-- Hero Section -->
    <section class="relative pt-32 pb-20 lg:pt-48 lg:pb-32 overflow-hidden">
        <div class="absolute inset-0 z-0">
            <div class="absolute inset-0 bg-gradient-to-b from-blue-900/20 to-traffic-dark"></div>
            <img src="https://images.unsplash.com/photo-1449824913935-59a10b8d2000?w=1920&q=80" alt="City Traffic" class="w-full h-full object-cover opacity-40">
            <div class="absolute inset-0 cctv-grid opacity-30"></div>
        </div>

        <div class="relative z-10 max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 text-center">
            <div class="inline-flex items-center space-x-2 px-4 py-2 rounded-full bg-slate-800/50 border border-slate-700 mb-8 animate-slide-up">
                <span class="w-2 h-2 bg-green-500 rounded-full animate-pulse"></span>
                <span class="text-xs font-medium text-slate-300">System Operational • 24/7 Monitoring</span>
            </div>
            
            <h1 class="text-5xl md:text-7xl font-bold mb-6 leading-tight animate-slide-up" style="animation-delay: 0.1s;">
                BEAT THE <br>
                <span class="gradient-text">TRAFFIC JAM</span>
            </h1>
            
            <p class="max-w-2xl mx-auto text-lg md:text-xl text-slate-400 mb-10 animate-slide-up" style="animation-delay: 0.2s;">
                The next-generation traffic monitoring system. Live CCTV feeds, smart re-routing, and predictive analytics for the modern driver.
            </p>
            
            <div class="flex flex-col sm:flex-row items-center justify-center gap-4 animate-slide-up" style="animation-delay: 0.3s;">
                <button onclick="scrollToMonitor()" class="w-full sm:w-auto bg-blue-600 hover:bg-blue-700 text-white px-8 py-4 rounded-full font-semibold transition-all transform hover:scale-105 shadow-xl shadow-blue-600/30 flex items-center justify-center space-x-2">
                    <i data-lucide="video" class="w-5 h-5"></i>
                    <span>View Live Feeds</span>
                </button>
                <button class="w-full sm:w-auto glass-panel hover:bg-slate-800 text-white px-8 py-4 rounded-full font-semibold transition-all flex items-center justify-center space-x-2">
                    <i data-lucide="map" class="w-5 h-5"></i>
                    <span>Smart Routes</span>
                </button>
            </div>

            <!-- Stats -->
            <div class="mt-20 grid grid-cols-2 md:grid-cols-4 gap-8 max-w-4xl mx-auto animate-slide-up" style="animation-delay: 0.4s;">
                <div class="text-center">
                    <div class="text-3xl font-bold text-white">150+</div>
                    <div class="text-sm text-slate-400 mt-1">Live Cameras</div>
                </div>
                <div class="text-center">
                    <div class="text-3xl font-bold text-green-400">98%</div>
                    <div class="text-sm text-slate-400 mt-1">Uptime</div>
                </div>
                <div class="text-center">
                    <div class="text-3xl font-bold text-blue-400">24/7</div>
                    <div class="text-sm text-slate-400 mt-1">Monitoring</div>
                </div>
                <div class="text-center">
                    <div class="text-3xl font-bold text-purple-400">50K+</div>
                    <div class="text-sm text-slate-400 mt-1">Daily Users</div>
                </div>
            </div>
        </div>
    </section>

    <!-- Features Section -->
    <section id="features" class="py-20 bg-slate-900/50 relative">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="text-center mb-16">
                <h2 class="text-3xl md:text-4xl font-bold mb-4">Advanced Traffic Solutions</h2>
                <p class="text-slate-400 max-w-2xl mx-auto">Cutting-edge technology to keep you moving through the city efficiently and safely.</p>
            </div>

            <div class="grid md:grid-cols-2 gap-8">
                <!-- Feature 1: Live CCTV -->
                <div class="glass-panel rounded-2xl p-8 hover:transform hover:scale-[1.02] transition-all duration-300 group">
                    <div class="w-14 h-14 bg-blue-500/10 rounded-xl flex items-center justify-center mb-6 group-hover:bg-blue-500/20 transition-colors">
                        <i data-lucide="cctv" class="w-7 h-7 text-blue-500"></i>
                    </div>
                    <h3 class="text-2xl font-bold mb-3">Live CCTV Streams</h3>
                    <p class="text-slate-400 mb-6">Real-time surveillance across major highways and intersections. HD quality feeds with night vision capability.</p>
                    
                    <div class="relative rounded-xl overflow-hidden bg-slate-800 aspect-video">
                        <img src="https://images.unsplash.com/photo-1558618666-fcd25c85cd64?w=800&q=80" alt="CCTV Camera" class="w-full h-full object-cover opacity-80">
                        <div class="absolute top-4 left-4 flex items-center space-x-2 bg-black/50 px-3 py-1 rounded-full">
                            <div class="w-2 h-2 bg-red-500 rounded-full animate-pulse"></div>
                            <span class="text-xs font-medium">LIVE</span>
                        </div>
                        <div class="absolute bottom-4 right-4 text-xs text-white/70 font-mono">
                            CAM-04 • HWY-101
                        </div>
                    </div>
                </div>

                <!-- Feature 2: Smart Route Tracking -->
                <div class="glass-panel rounded-2xl p-8 hover:transform hover:scale-[1.02] transition-all duration-300 group">
                    <div class="w-14 h-14 bg-purple-500/10 rounded-xl flex items-center justify-center mb-6 group-hover:bg-purple-500/20 transition-colors">
                        <i data-lucide="route" class="w-7 h-7 text-purple-500"></i>
                    </div>
                    <h3 class="text-2xl font-bold mb-3">Smart Route Tracking</h3>
                    <p class="text-slate-400 mb-6">AI-powered route optimization based on real-time traffic conditions, accidents, and construction updates.</p>
                    
                    <div class="relative rounded-xl overflow-hidden bg-slate-800 aspect-video">
                        <img src="https://images.unsplash.com/photo-1524661135-423995f22d0b?w=800&q=80" alt="Traffic Map" class="w-full h-full object-cover opacity-80">
                        <div class="absolute inset-0 flex items-center justify-center">
                            <div class="bg-slate-900/90 px-4 py-2 rounded-lg border border-purple-500/30">
                                <div class="flex items-center space-x-2">
                                    <i data-lucide="navigation" class="w-5 h-5 text-purple-500"></i>
                                    <span class="text-sm font-medium">Optimal Route Found</span>
                                </div>
                                <div class="text-xs text-slate-400 mt-1">12 min faster than usual</div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Additional Features Grid -->
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mt-8">
                <div class="glass-panel rounded-xl p-6 text-center hover:bg-slate-800/50 transition-colors">
                    <i data-lucide="zap" class="w-8 h-8 text-yellow-500 mx-auto mb-3"></i>
                    <h4 class="font-semibold mb-2">Predictive Analytics</h4>
                    <p class="text-sm text-slate-400">ML algorithms predict congestion before it happens</p>
                </div>
                <div class="glass-panel rounded-xl p-6 text-center hover:bg-slate-800/50 transition-colors">
                    <i data-lucide="bell" class="w-8 h-8 text-red-500 mx-auto mb-3"></i>
                    <h4 class="font-semibold mb-2">Incident Alerts</h4>
                    <p class="text-sm text-slate-400">Instant notifications for accidents or road closures</p>
                </div>
                <div class="glass-panel rounded-xl p-6 text-center hover:bg-slate-800/50 transition-colors">
                    <i data-lucide="smartphone" class="w-8 h-8 text-green-500 mx-auto mb-3"></i>
                    <h4 class="font-semibold mb-2">Mobile App</h4>
                    <p class="text-sm text-slate-400">Full dashboard access on iOS and Android</p>
                </div>
            </div>
        </div>
    </section>

    <!-- Live Monitor Section -->
    <section id="live-monitor" class="py-20 relative">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex flex-col md:flex-row justify-between items-end mb-8">
                <div>
                    <h2 class="text-3xl md:text-4xl font-bold mb-2">Live Traffic Monitor</h2>
                    <p class="text-slate-400">Real-time feeds from major corridors</p>
                </div>
                <div class="flex items-center space-x-2 mt-4 md:mt-0">
                    <div class="w-2 h-2 bg-green-500 rounded-full animate-pulse"></div>
                    <span class="text-sm text-slate-300">All Systems Online</span>
                </div>
            </div>

            <!-- CCTV Grid -->
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
                <!-- Camera 1 -->
                <div class="relative rounded-xl overflow-hidden bg-slate-800 aspect-video group cursor-pointer">
                    <img src="https://images.unsplash.com/photo-1494522358652-f30e61a60313?w=600&q=80" alt="Highway Traffic" class="w-full h-full object-cover group-hover:scale-110 transition-transform duration-500">
                    <div class="absolute inset-0 bg-gradient-to-t from-black/80 via-transparent to-transparent"></div>
                    <div class="absolute top-3 left-3 flex items-center space-x-2">
                        <div class="w-2 h-2 bg-red-500 rounded-full animate-pulse"></div>
                        <span class="text-xs font-bold bg-black/50 px-2 py-1 rounded">LIVE</span>
                    </div>
                    <div class="absolute bottom-3 left-3 right-3">
                        <div class="flex justify-between items-end">
                            <div>
                                <div class="text-sm font-semibold">Highway 101 North</div>
                                <div class="text-xs text-slate-400">Mile Marker 42 • San Francisco</div>
                            </div>
                            <div class="text-xs font-mono text-green-400">45 mph avg</div>
                        </div>
                    </div>
                </div>

                <!-- Camera 2 -->
                <div class="relative rounded-xl overflow-hidden bg-slate-800 aspect-video group cursor-pointer">
                    <img src="https://images.unsplash.com/photo-1577083552431-6e5fd01aa342?w=600&q=80" alt="City Intersection" class="w-full h-full object-cover group-hover:scale-110 transition-transform duration-500">
                    <div class="absolute inset-0 bg-gradient-to-t from-black/80 via-transparent to-transparent"></div>
                    <div class="absolute top-3 left-3 flex items-center space-x-2">
                        <div class="w-2 h-2 bg-red-500 rounded-full animate-pulse"></div>
                        <span class="text-xs font-bold bg-black/50 px-2 py-1 rounded">LIVE</span>
                    </div>
                    <div class="absolute bottom-3 left-3 right-3">
                        <div class="flex justify-between items-end">
                            <div>
                                <div class="text-sm font-semibold">Intersection 4th & Market</div>
                                <div class="text-xs text-slate-400">Downtown • Heavy Traffic</div>
                            </div>
                            <div class="text-xs font-mono text-yellow-400">12 mph avg</div>
                        </div>
                    </div>
                </div>

                <!-- Camera 3 -->
                <div class="relative rounded-xl overflow-hidden bg-slate-800 aspect-video group cursor-pointer">
                    <img src="https://images.unsplash.com/photo-1566243052021-d745ace581b9?w=600&q=80" alt="Bridge Traffic" class="w-full h-full object-cover group-hover:scale-110 transition-transform duration-500">
                    <div class="absolute inset-0 bg-gradient-to-t from-black/80 via-transparent to-transparent"></div>
                    <div class="absolute top-3 left-3 flex items-center space-x-2">
                        <div class="w-2 h-2 bg-red-500 rounded-full animate-pulse"></div>
                        <span class="text-xs font-bold bg-black/50 px-2 py-1 rounded">LIVE</span>
                    </div>
                    <div class="absolute bottom-3 left-3 right-3">
                        <div class="flex justify-between items-end">
                            <div>
                                <div class="text-sm font-semibold">Bay Bridge Approach</div>
                                <div class="text-xs text-slate-400">Westbound • Clear</div>
                            </div>
                            <div class="text-xs font-mono text-green-400">55 mph avg</div>
                        </div>
                    </div>
                </div>

                <!-- Camera 4 -->
                <div class="relative rounded-xl overflow-hidden bg-slate-800 aspect-video group cursor-pointer">
                    <img src="https://images.unsplash.com/photo-1542282088-fe8426682b8f?w=600&q=80" alt="Night Traffic" class="w-full h-full object-cover group-hover:scale-110 transition-transform duration-500">
                    <div class="absolute inset-0 bg-gradient-to-t from-black/80 via-transparent to-transparent"></div>
                    <div class="absolute top-3 left-3 flex items-center space-x-2">
                        <div class="w-2 h-2 bg-red-500 rounded-full animate-pulse"></div>
                        <span class="text-xs font-bold bg-black/50 px-2 py-1 rounded">LIVE</span>
                    </div>
                    <div class="absolute bottom-3 left-3 right-3">
                        <div class="flex justify-between items-end">
                            <div>
                                <div class="text-sm font-semibold">I-280 South</div>
                                <div class="text-xs text-slate-400">Night Vision Active</div>
                            </div>
                            <div class="text-xs font-mono text-green-400">60 mph avg</div>
                        </div>
                    </div>
                </div>

                <!-- Camera 5 -->
                <div class="relative rounded-xl overflow-hidden bg-slate-800 aspect-video group cursor-pointer">
                    <img src="https://images.unsplash.com/photo-1605218427368-35b0f997c987?w=600&q=80" alt="Traffic Jam" class="w-full h-full object-cover group-hover:scale-110 transition-transform duration-500">
                    <div class="absolute inset-0 bg-gradient-to-t from-black/80 via-transparent to-transparent"></div>
                    <div class="absolute top-3 left-3 flex items-center space-x-2">
                        <div class="w-2 h-2 bg-red-500 rounded-full animate-pulse"></div>
                        <span class="text-xs font-bold bg-black/50 px-2 py-1 rounded">LIVE</span>
                    </div>
                    <div class="absolute bottom-3 left-3 right-3">
                        <div class="flex justify-between items-end">
                            <div>
                                <div class="text-sm font-semibold">Downtown Connector</div>
                                <div class="text-xs text-slate-400">Congestion Detected</div>
                            </div>
                            <div class="text-xs font-mono text-red-400">5 mph avg</div>
                        </div>
                    </div>
                </div>

                <!-- Add Camera Placeholder -->
                <div class="relative rounded-xl overflow-hidden bg-slate-800/50 aspect-video border-2 border-dashed border-slate-700 flex flex-col items-center justify-center cursor-pointer hover:border-blue-500/50 hover:bg-slate-800/30 transition-all group">
                    <i data-lucide="plus" class="w-12 h-12 text-slate-600 group-hover:text-blue-500 transition-colors mb-2"></i>
                    <span class="text-sm text-slate-500 group-hover:text-slate-300">Add Custom Feed</span>
                </div>
            </div>
        </div>
    </section>

    <!-- Analytics Dashboard Preview -->
    <section id="analytics" class="py-20 bg-slate-900/50">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="glass-panel rounded-3xl p-8 md:p-12 border border-slate-700/50">
                <div class="flex flex-col md:flex-row justify-between items-start md:items-center mb-10">
                    <div>
                        <h2 class="text-3xl font-bold mb-2">Traffic Analytics</h2>
                        <p class="text-slate-400">Real-time data insights and predictions</p>
                    </div>
                    <div class="flex space-x-2 mt-4 md:mt-0">
                        <button class="px-4 py-2 rounded-lg bg-blue-600 text-sm font-medium">Today</button>
                        <button class="px-4 py-2 rounded-lg bg-slate-800 text-sm font-medium text-slate-400 hover:text-white">Week</button>
                        <button class="px-4 py-2 rounded-lg bg-slate-800 text-sm font-medium text-slate-400 hover:text-white">Month</button>
                    </div>
                </div>

                <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
                    <!-- Chart Placeholder -->
                    <div class="lg:col-span-2 glass-panel rounded-2xl p-6">
                        <h3 class="text-lg font-semibold mb-6">Traffic Volume Trends</h3>
                        <div class="h-64 flex items-end justify-between space-x-2">
                            <div class="w-full bg-blue-500/20 rounded-t-lg relative group" style="height: 40%">
                                <div class="absolute -top-8 left-1/2 transform -translate-x-1/2 bg-slate-800 px-2 py-1 rounded text-xs opacity-0 group-hover:opacity-100 transition-opacity">12k</div>
                            </div>
                            <div class="w-full bg-blue-500/30 rounded-t-lg relative group" style="height: 65%">
                                <div class="absolute -top-8 left-1/2 transform -translate-x-1/2 bg-slate-800 px-2 py-1 rounded text-xs opacity-0 group-hover:opacity-100 transition-opacity">18k</div>
                            </div>
                            <div class="w-full bg-blue-500/40 rounded-t-lg relative group" style="height: 45%">
                                <div class="absolute -top-8 left-1/2 transform -translate-x-1/2 bg-slate-800 px-2 py-1 rounded text-xs opacity-0 group-hover:opacity-100 transition-opacity">14k</div>
                            </div>
                            <div class="w-full bg-blue-500/50 rounded-t-lg relative group" style="height: 80%">
                                <div class="absolute -top-8 left-1/2 transform -translate-x-1/2 bg-slate-800 px-2 py-1 rounded text-xs opacity-0 group-hover:opacity-100 transition-opacity">22k</div>
                            </div>
                            <div class="w-full bg-blue-500/60 rounded-t-lg relative group" style="height: 55%">
                                <div class="absolute -top-8 left-1/2 transform -translate-x-1/2 bg-slate-800 px-2 py-1 rounded text-xs opacity-0 group-hover:opacity-100 transition-opacity">16k</div>
                            </div>
                            <div class="w-full bg-blue-500/70 rounded-t-lg relative group" style="height: 90%">
                                <div class="absolute -top-8 left-1/2 transform -translate-x-1/2 bg-slate-800 px-2 py-1 rounded text-xs opacity-0 group-hover:opacity-100 transition-opacity">25k</div>
                            </div>
                            <div class="w-full bg-blue-500 rounded-t-lg relative group" style="height: 75%">
                                <div class="absolute -top-8 left-1/2 transform -translate-x-1/2 bg-slate-800 px-2 py-1 rounded text-xs opacity-0 group-hover:opacity-100 transition-opacity">20k</div>
                            </div>
                        </div>
                        <div class="flex justify-between mt-4 text-xs text-slate-400">
                            <span>6AM</span>
                            <span>9AM</span>
                            <span>12PM</span>
                            <span>3PM</span>
                            <span>6PM</span>
                            <span>9PM</span>
                            <span>12AM</span>
                        </div>
                    </div>

                    <!-- Stats Cards -->
                    <div class="space-y-4">
                        <div class="glass-panel rounded-xl p-6 border-l-4 border-green-500">
                            <div class="flex items-center justify-between mb-2">
                                <span class="text-slate-400 text-sm">Average Speed</span>
                                <i data-lucide="trending-up" class="w-4 h-4 text-green-500"></i>
                            </div>
                            <div class="text-2xl font-bold">42 mph</div>
                            <div class="text-xs text-green-400 mt-1">+5% from yesterday</div>
                        </div>

                        <div class="glass-panel rounded-xl p-6 border-l-4 border-yellow-500">
                            <div class="flex items-center justify-between mb-2">
                                <span class="text-slate-400 text-sm">Congestion Index</span>
                                <i data-lucide="alert-triangle" class="w-4 h-4 text-yellow-500"></i>
                            </div>
                            <div class="text-2xl font-bold">Medium</div>
                            <div class="text-xs text-yellow-400 mt-1">3 incidents reported</div>
                        </div>

                        <div class="glass-panel rounded-xl p-6 border-l-4 border-blue-500">
                            <div class="flex items-center justify-between mb-2">
                                <span class="text-slate-400 text-sm">Active Cameras</span>
                                <i data-lucide="camera" class="w-4 h-4 text-blue-500"></i>
                            </div>
                            <div class="text-2xl font-bold">147/150</div>
                            <div class="text-xs text-blue-400 mt-1">98% operational</div>
                        </div>

                        <div class="glass-panel rounded-xl p-6 border-l-4 border-purple-500">
                            <div class="flex items-center justify-between mb-2">
                                <span class="text-slate-400 text-sm">ETA Accuracy</span>
                                <i data-lucide="target" class="w-4 h-4 text-purple-500"></i>
                            </div>
                            <div class="text-2xl font-bold">94.2%</div>
                            <div class="text-xs text-purple-400 mt-1">ML model v2.4</div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <!-- Footer -->
    <footer class="bg-slate-950 border-t border-slate-800 pt-16 pb-8">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="grid grid-cols-1 md:grid-cols-4 gap-8 mb-12">
                <div class="col-span-1 md:col-span-2">
                    <div class="flex items-center space-x-3 mb-4">
                        <i data-lucide="activity" class="w-6 h-6 text-blue-500"></i>
                        <span class="text-lg font-bold">TrafficFlow</span>
                    </div>
                    <p class="text-slate-400 text-sm max-w-md">
                        Next-generation traffic monitoring and analytics platform. Keeping cities moving through intelligent infrastructure and real-time data.
                    </p>
                </div>
                
                <div>
                    <h4 class="font-semibold mb-4">Product</h4>
                    <ul class="space-y-2 text-sm text-slate-400">
                        <li><a href="#" class="hover:text-white transition-colors">Features</a></li>
                        <li><a href="#" class="hover:text-white transition-colors">API Access</a></li>
                        <li><a href="#" class="hover:text-white transition-colors">Mobile Apps</a></li>
                        <li><a href="#" class="hover:text-white transition-colors">Pricing</a></li>
                    </ul>
                </div>
                
                <div>
                    <h4 class="font-semibold mb-4">Legal</h4>
                    <ul class="space-y-2 text-sm text-slate-400">
                        <li><a href="#" class="hover:text-white transition-colors">Terms of Service</a></li>
                        <li><a href="#" class="hover:text-white transition-colors">Privacy Policy</a></li>
                        <li><a href="#" class="hover:text-white transition-colors">Cookie Policy</a></li>
                        <li><a href="#" class="hover:text-white transition-colors">Contact</a></li>
                    </ul>
                </div>
            </div>
            
            <div class="border-t border-slate-800 pt-8 flex flex-col md:flex-row justify-between items-center">
                <p class="text-slate-500 text-sm">© 2026 Traffic Flow Monitor. All rights reserved.</p>
                <div class="flex space-x-4 mt-4 md:mt-0">
                    <a href="#" class="text-slate-400 hover:text-white transition-colors">
                        <i data-lucide="twitter" class="w-5 h-5"></i>
                    </a>
                    <a href="#" class="text-slate-400 hover:text-white transition-colors">
                        <i data-lucide="github" class="w-5 h-5"></i>
                    </a>
                    <a href="#" class="text-slate-400 hover:text-white transition-colors">
                        <i data-lucide="linkedin" class="w-5 h-5"></i>
                    </a>
                </div>
            </div>
        </div>
    </footer>

    <!-- Login Modal -->
    <div id="loginModal" class="fixed inset-0 z-[100] hidden">
        <div class="absolute inset-0 bg-black/80 backdrop-blur-sm" onclick="toggleLoginModal()"></div>
        <div class="absolute inset-0 flex items-center justify-center p-4">
            <div class="glass-panel rounded-2xl p-8 w-full max-w-md transform transition-all scale-100 border border-slate-700 shadow-2xl">
                <div class="flex justify-between items-center mb-6">
                    <div>
                        <h3 class="text-2xl font-bold">Welcome Back</h3>
                        <p class="text-slate-400 text-sm mt-1">Sign in to access your dashboard</p>
                    </div>
                    <button onclick="toggleLoginModal()" class="text-slate-400 hover:text-white transition-colors">
                        <i data-lucide="x" class="w-6 h-6"></i>
                    </button>
                </div>
                
                <form onsubmit="handleLogin(event)" class="space-y-4">
                    <div>
                        <label class="block text-sm font-medium text-slate-300 mb-1">Email</label>
                        <input type="email" placeholder="you@example.com" class="w-full px-4 py-3 rounded-lg bg-slate-800/50 border border-slate-700 text-white placeholder-slate-500 focus:outline-none focus:border-blue-500 focus:ring-1 focus:ring-blue-500 transition-all" required>
                    </div>
                    
                    <div>
                        <label class="block text-sm font-medium text-slate-300 mb-1">Password</label>
                        <input type="password" placeholder="••••••••" class="w-full px-4 py-3 rounded-lg bg-slate-800/50 border border-slate-700 text-white placeholder-slate-500 focus:outline-none focus:border-blue-500 focus:ring-1 focus:ring-blue-500 transition-all" required>
                    </div>
                    
                    <div class="flex items-center justify-between text-sm">
                        <label class="flex items-center space-x-2 cursor-pointer">
                            <input type="checkbox" class="rounded bg-slate-800 border-slate-700 text-blue-600 focus:ring-blue-500">
                            <span class="text-slate-400">Remember me</span>
                        </label>
                        <a href="#" class="text-blue-500 hover:text-blue-400">Forgot password?</a>
                    </div>
                    
                    <button type="submit" class="w-full bg-blue-600 hover:bg-blue-700 text-white py-3 rounded-lg font-semibold transition-all transform hover:scale-[1.02] shadow-lg shadow-blue-600/20">
                        Sign In
                    </button>
                </form>
                
                <div class="mt-6 pt-6 border-t border-slate-700 text-center">
                    <p class="text-xs text-slate-500">
                        By continuing, you agree to our <a href="#" class="text-blue-500 hover:underline">Terms of Service</a> and <a href="#" class="text-blue-500 hover:underline">Privacy Policy</a>.
                    </p>
                </div>
                
                <div class="mt-4 text-center">
                    <span class="text-sm text-slate-400">Don't have an account? </span>
                    <a href="#" class="text-sm text-blue-500 hover:text-blue-400 font-medium">Get Access</a>
                </div>
            </div>
        </div>
    </div>

    <script>
        // Initialize Lucide icons
        lucide.createIcons();

        // Modal Toggle
        function toggleLoginModal() {
            const modal = document.getElementById('loginModal');
            if (modal.classList.contains('hidden')) {
                modal.classList.remove('hidden');
                document.body.style.overflow = 'hidden';
            } else {
                modal.classList.add('hidden');
                document.body.style.overflow = 'auto';
            }
        }

        // Handle Login
        function handleLogin(e) {
            e.preventDefault();
            const btn = e.target.querySelector('button[type="submit"]');
            const originalText = btn.innerText;
            btn.innerText = 'Signing in...';
            btn.disabled = true;
            
            setTimeout(() => {
                btn.innerText = 'Welcome! 🚦';
                btn.classList.remove('bg-blue-600');
                btn.classList.add('bg-green-600');
                
                setTimeout(() => {
                    toggleLoginModal();
                    btn.innerText = originalText;
                    btn.disabled = false;
                    btn.classList.add('bg-blue-600');
                    btn.classList.remove('bg-green-600');
                }, 1000);
            }, 1500);
        }

        // Scroll to Monitor
        function scrollToMonitor() {
            document.getElementById('live-monitor').scrollIntoView({ behavior: 'smooth' });
        }

        // Animate elements on scroll
        const observerOptions = {
            threshold: 0.1,
            rootMargin: '0px 0px -50px 0px'
        };

        const observer = new IntersectionObserver((entries) => {
            entries.forEach(entry => {
                if (entry.isIntersecting) {
                    entry.target.classList.add('animate-slide-up');
                    entry.target.style.opacity = '1';
                }
            });
        }, observerOptions);

        document.querySelectorAll('.glass-panel').forEach((el) => {
            el.style.opacity = '0';
            observer.observe(el);
        });

        // Live clock update
        function updateTime() {
            const now = new Date();
            const timeString = now.toLocaleTimeString('en-US', { 
                hour12: false, 
                hour: '2-digit', 
                minute: '2-digit',
                second: '2-digit'
            });
            
            // Update any time displays if needed
            document.querySelectorAll('.live-time').forEach(el => {
                el.textContent = timeString;
            });
        }

        setInterval(updateTime, 1000);
        updateTime();

        // Simulate live traffic data updates
        setInterval(() => {
            const speeds = document.querySelectorAll('.font-mono');
            speeds.forEach(speed => {
                if (speed.textContent.includes('mph')) {
                    const currentSpeed = parseInt(speed.textContent);
                    const variation = Math.floor(Math.random() * 5) - 2;
                    const newSpeed = Math.max(0, currentSpeed + variation);
                    speed.textContent = newSpeed + ' mph avg';
                    
                    // Update color based on speed
                    speed.classList.remove('text-green-400', 'text-yellow-400', 'text-red-400');
                    if (newSpeed > 40) speed.classList.add('text-green-400');
                    else if (newSpeed > 20) speed.classList.add('text-yellow-400');
                    else speed.classList.add('text-red-400');
                }
            });
        }, 5000);
    </script>
</body>
</html>
