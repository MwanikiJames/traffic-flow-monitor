<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Traffic Jam - Real-Time Road Monitoring</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <style>
        * {
            font-family: 'Inter', sans-serif;
        }
        .map-container {
            height: 500px;
            border-radius: 12px;
            overflow: hidden;
        }
        .subscription-card {
            transition: all 0.3s ease;
            border: 2px solid transparent;
        }
        .subscription-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
        }
        .subscription-card.popular {
            border-color: #3b82f6;
            position: relative;
            overflow: visible;
        }
        .subscription-card.popular::before {
            content: 'POPULAR';
            position: absolute;
            top: -12px;
            left: 50%;
            transform: translateX(-50%);
            background: #3b82f6;
            color: white;
            padding: 4px 16px;
            border-radius: 20px;
            font-size: 12px;
            font-weight: 600;
        }
        .camera-feed {
            transition: all 0.3s ease;
            border: 2px solid transparent;
        }
        .camera-feed.active {
            border-color: #10b981;
            box-shadow: 0 0 0 3px rgba(16, 185, 129, 0.2);
        }
        .video-library-item:hover {
            background-color: #f9fafb;
            transform: translateX(5px);
            transition: all 0.2s ease;
        }
        .road-highlight {
            animation: pulse 2s infinite;
        }
        @keyframes pulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.7; }
        }
        .jam-indicator {
            width: 12px;
            height: 12px;
            border-radius: 50%;
            display: inline-block;
            margin-right: 8px;
        }
        .jam-low { background-color: #10b981; }
        .jam-medium { background-color: #f59e0b; }
        .jam-high { background-color: #ef4444; }
        .jam-severe { background-color: #7c3aed; }
    </style>
</head>
<body class="bg-gray-50">
    <!-- Login Screen -->
    <div id="loginScreen" class="min-h-screen flex items-center justify-center p-4">
        <div class="max-w-md w-full bg-white rounded-2xl shadow-xl p-8">
            <div class="text-center mb-8">
                <div class="flex items-center justify-center mb-4">
                    <div class="w-12 h-12 bg-blue-600 rounded-xl flex items-center justify-center">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-8 w-8 text-white" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                            <path d="M10 9H6L2 5l4-4h4v4h8v2h-8v4zM14 15h4l4 4-4 4h-4v-4H6v-2h8v-4z"/>
                        </svg>
                    </div>
                    <h1 class="text-3xl font-bold ml-3 text-gray-800">Traffic Jam</h1>
                </div>
                <p class="text-gray-600">Real-time road monitoring and traffic management</p>
            </div>
            
            <div id="loginForm">
                <h2 class="text-xl font-semibold mb-6 text-gray-800">Sign In to Your Account</h2>
                <div class="space-y-4">
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">Email Address</label>
                        <input type="email" id="loginEmail" class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 outline-none transition" placeholder="you@example.com">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">Password</label>
                        <input type="password" id="loginPassword" class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 outline-none transition" placeholder="••••••••">
                    </div>
                    <div class="flex items-center justify-between">
                        <label class="flex items-center">
                            <input type="checkbox" class="h-4 w-4 text-blue-600 rounded focus:ring-blue-500">
                            <span class="ml-2 text-sm text-gray-600">Remember me</span>
                        </label>
                        <a href="#" class="text-sm text-blue-600 hover:text-blue-800">Forgot password?</a>
                    </div>
                    <button onclick="handleLogin()" class="w-full bg-blue-600 text-white py-3 rounded-lg font-medium hover:bg-blue-700 transition duration-300">
                        Sign In
                    </button>
                </div>
                <p class="text-center mt-6 text-gray-600">
                    Don't have an account? 
                    <button onclick="showSubscriptionSelection()" class="text-blue-600 hover:text-blue-800 font-medium">Choose a plan</button>
                </p>
            </div>

            <div id="subscriptionSelection" class="hidden">
                <h2 class="text-2xl font-bold mb-2 text-center text-gray-800">Choose Your Plan</h2>
                <p class="text-gray-600 text-center mb-8">Select the perfect plan for your traffic monitoring needs</p>
                
                <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
                    <!-- Normal Plan -->
                    <div class="subscription-card bg-white rounded-xl border border-gray-200 p-6">
                        <div class="text-center mb-6">
                            <h3 class="text-xl font-bold text-gray-800 mb-2">Normal</h3>
                            <div class="flex items-center justify-center">
                                <span class="text-3xl font-bold text-gray-800">KSh 100</span>
                                <span class="text-gray-600 ml-2">/month</span>
                            </div>
                        </div>
                        <ul class="space-y-3 mb-6">
                            <li class="flex items-center">
                                <svg class="h-5 w-5 text-green-500 mr-3" fill="currentColor" viewBox="0 0 20 20">
                                    <path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd"/>
                                </svg>
                                <span>Real-time traffic jam alerts</span>
                            </li>
                            <li class="flex items-center">
                                <svg class="h-5 w-5 text-green-500 mr-3" fill="currentColor" viewBox="0 0 20 20">
                                    <path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd"/>
                                </svg>
                                <span>Interactive road maps</span>
                            </li>
                            <li class="flex items-center">
                                <svg class="h-5 w-5 text-green-500 mr-3" fill="currentColor" viewBox="0 0 20 20">
                                    <path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd"/>
                                </svg>
                                <span>1 device at a time</span>
                            </li>
                            <li class="flex items-center opacity-50">
                                <svg class="h-5 w-5 text-gray-400 mr-3" fill="currentColor" viewBox="0 0 20 20">
                                    <path fill-rule="evenodd" d="M4.293 4.293a1 1 0 011.414 0L10 8.586l4.293-4.293a1 1 0 111.414 1.414L11.414 10l4.293 4.293a1 1 0 01-1.414 1.414L10 11.414l-4.293 4.293a1 1 0 01-1.414-1.414L8.586 10 4.293 5.707a1 1 0 010-1.414z" clip-rule="evenodd"/>
                                </svg>
                                <span>Live CCTV access</span>
                            </li>
                            <li class="flex items-center opacity-50">
                                <svg class="h-5 w-5 text-gray-400 mr-3" fill="currentColor" viewBox="0 0 20 20">
                                    <path fill-rule="evenodd" d="M4.293 4.293a1 1 0 011.414 0L10 8.586l4.293-4.293a1 1 0 111.414 1.414L11.414 10l4.293 4.293a1 1 0 01-1.414 1.414L10 11.414l-4.293 4.293a1 1 0 01-1.414-1.414L8.586 10 4.293 5.707a1 1 0 010-1.414z" clip-rule="evenodd"/>
                                </svg>
                                <span>Video library</span>
                            </li>
                        </ul>
                        <button onclick="selectPlan('normal')" class="w-full py-3 border-2 border-blue-600 text-blue-600 rounded-lg font-medium hover:bg-blue-50 transition">
                            Select Normal Plan
                        </button>
                    </div>

                    <!-- Premium Plan -->
                    <div class="subscription-card popular bg-white rounded-xl border-2 border-blue-500 p-6">
                        <div class="text-center mb-6">
                            <h3 class="text-xl font-bold text-gray-800 mb-2">Premium</h3>
                            <div class="flex items-center justify-center">
                                <span class="text-3xl font-bold text-gray-800">KSh 500</span>
                                <span class="text-gray-600 ml-2">/month</span>
                            </div>
                        </div>
                        <ul class="space-y-3 mb-6">
                            <li class="flex items-center">
                                <svg class="h-5 w-5 text-green-500 mr-3" fill="currentColor" viewBox="0 0 20 20">
                                    <path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd"/>
                                </svg>
                                <span>All Normal features</span>
                            </li>
                            <li class="flex items-center">
                                <svg class="h-5 w-5 text-green-500 mr-3" fill="currentColor" viewBox="0 0 20 20">
                                    <path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd"/>
                                </svg>
                                <span>Live CCTV camera access</span>
                            </li>
                            <li class="flex items-center">
                                <svg class="h-5 w-5 text-green-500 mr-3" fill="currentColor" viewBox="0 0 20 20">
                                    <path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd"/>
                                </svg>
                                <span>Auto-camera switching</span>
                            </li>
                            <li class="flex items-center">
                                <svg class="h-5 w-5 text-green-500 mr-3" fill="currentColor" viewBox="0 0 20 20">
                                    <path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd"/>
                                </svg>
                                <span>1 device at a time</span>
                            </li>
                            <li class="flex items-center opacity-50">
                                <svg class="h-5 w-5 text-gray-400 mr-3" fill="currentColor" viewBox="0 0 20 20">
                                    <path fill-rule="evenodd" d="M4.293 4.293a1 1 0 011.414 0L10 8.586l4.293-4.293a1 1 0 111.414 1.414L11.414 10l4.293 4.293a1 1 0 01-1.414 1.414L10 11.414l-4.293 4.293a1 1 0 01-1.414-1.414L8.586 10 4.293 5.707a1 1 0 010-1.414z" clip-rule="evenodd"/>
                                </svg>
                                <span>Video save & replay</span>
                            </li>
                        </ul>
                        <button onclick="selectPlan('premium')" class="w-full py-3 bg-blue-600 text-white rounded-lg font-medium hover:bg-blue-700 transition">
                            Select Premium Plan
                        </button>
                    </div>

                    <!-- Platinum Plan -->
                    <div class="subscription-card bg-white rounded-xl border border-gray-200 p-6">
                        <div class="text-center mb-6">
                            <h3 class="text-xl font-bold text-gray-800 mb-2">Platinum</h3>
                            <div class="flex items-center justify-center">
                                <span class="text-3xl font-bold text-gray-800">KSh 1000</span>
                                <span class="text-gray-600 ml-2">/month</span>
                            </div>
                        </div>
                        <ul class="space-y-3 mb-6">
                            <li class="flex items-center">
                                <svg class="h-5 w-5 text-green-500 mr-3" fill="currentColor" viewBox="0 0 20 20">
                                    <path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd"/>
                                </svg>
                                <span>All Premium features</span>
                            </li>
                            <li class="flex items-center">
                                <svg class="h-5 w-5 text-green-500 mr-3" fill="currentColor" viewBox="0 0 20 20">
                                    <path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd"/>
                                </svg>
                                <span>Save & replay videos</span>
                            </li>
                            <li class="flex items-center">
                                <svg class="h-5 w-5 text-green-500 mr-3" fill="currentColor" viewBox="0 0 20 20">
                                    <path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd"/>
                                </svg>
                                <span>Request past videos</span>
                            </li>
                            <li class="flex items-center">
                                <svg class="h-5 w-5 text-green-500 mr-3" fill="currentColor" viewBox="0 0 20 20">
                                    <path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd"/>
                                </svg>
                                <span>Video library access</span>
                            </li>
                            <li class="flex items-center">
                                <svg class="h-5 w-5 text-green-500 mr-3" fill="currentColor" viewBox="0 0 20 20">
                                    <path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd"/>
                                </svg>
                                <span>Up to 5 devices at once</span>
                            </li>
                        </ul>
                        <button onclick="selectPlan('platinum')" class="w-full py-3 border-2 border-purple-600 text-purple-600 rounded-lg font-medium hover:bg-purple-50 transition">
                            Select Platinum Plan
                        </button>
                    </div>
                </div>
                
                <div class="text-center">
                    <button onclick="showLoginForm()" class="text-blue-600 hover:text-blue-800 font-medium">
                        ← Back to login
                    </button>
                </div>
            </div>
        </div>
    </div>

    <!-- Main App Interface -->
    <div id="mainApp" class="hidden min-h-screen">
        <!-- Top Navigation -->
        <nav class="bg-white shadow-md">
            <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
                <div class="flex justify-between items-center h-16">
                    <div class="flex items-center">
                        <div class="flex items-center">
                            <div class="w-10 h-10 bg-blue-600 rounded-lg flex items-center justify-center">
                                <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-white" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                                    <path d="M10 9H6L2 5l4-4h4v4h8v2h-8v4zM14 15h4l4 4-4 4h-4v-4H6v-2h8v-4z"/>
                                </svg>
                            </div>
                            <h1 class="ml-3 text-xl font-bold text-gray-800">Traffic Jam</h1>
                            <span id="userPlanBadge" class="ml-3 px-3 py-1 text-xs font-semibold rounded-full"></span>
                        </div>
                    </div>
                    
                    <div class="flex items-center space-x-4">
                        <!-- Search Bar -->
                        <div class="relative">
                            <input type="text" id="roadSearch" placeholder="Search road/highway..." class="pl-10 pr-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 outline-none w-64">
                            <svg class="h-5 w-5 text-gray-400 absolute left-3 top-2.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"/>
                            </svg>
                        </div>
                        
                        <div class="flex items-center space-x-2">
                            <span id="userEmail" class="text-sm text-gray-600"></span>
                            <button onclick="logout()" class="ml-4 px-4 py-2 bg-gray-100 text-gray-700 rounded-lg hover:bg-gray-200 transition">
                                Logout
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </nav>

        <!-- Main Content -->
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
            <div class="flex flex-col lg:flex-row gap-8">
                <!-- Left Sidebar -->
                <div class="lg:w-1/4">
                    <div class="bg-white rounded-xl shadow-sm p-6 mb-6">
                        <h2 class="text-lg font-semibold text-gray-800 mb-4">Navigation</h2>
                        <div class="space-y-3">
                            <button onclick="showMapView()" class="w-full flex items-center px-4 py-3 text-gray-700 hover:bg-blue-50 hover:text-blue-600 rounded-lg transition">
                                <svg class="h-5 w-5 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 20l-5.447-2.724A1 1 0 013 16.382V5.618a1 1 0 011.447-.894L9 7m0 13l6-3m-6 3V7m6 10l4.553 2.276A1 1 0 0021 18.382V7.618a1 1 0 00-.553-.894L15 4m0 13V4m0 0L9 7"/>
                                </svg>
                                Road Map & Traffic
                            </button>
                            
                            <button id="cameraModeBtn" onclick="startCameraMode()" class="w-full flex items-center px-4 py-3 text-gray-700 hover:bg-blue-50 hover:text-blue-600 rounded-lg transition">
                                <svg class="h-5 w-5 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 10l4.553-2.276A1 1 0 0121 8.618v6.764a1 1 0 01-1.447.894L15 14M5 18h8a2 2 0 002-2V8a2 2 0 00-2-2H5a2 2 0 00-2 2v8a2 2 0 002 2z"/>
                                </svg>
                                Start Camera/Live Mode
                            </button>
                            
                            <button id="videoLibraryBtn" onclick="showVideoLibrary()" class="w-full flex items-center px-4 py-3 text-gray-700 hover:bg-blue-50 hover:text-blue-600 rounded-lg transition">
                                <svg class="h-5 w-5 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10"/>
                                </svg>
                                Video Library
                            </button>
                            
                            <button onclick="showDriverReports()" class="w-full flex items-center px-4 py-3 text-gray-700 hover:bg-blue-50 hover:text-blue-600 rounded-lg transition">
                                <svg class="h-5 w-5 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"/>
                                </svg>
                                Driver Reports
                            </button>
                        </div>
                    </div>
                    
                    <!-- Active Devices -->
                    <div class="bg-white rounded-xl shadow-sm p-6">
                        <h2 class="text-lg font-semibold text-gray-800 mb-4">Active Devices</h2>
                        <div id="activeDevices" class="space-y-3">
                            <!-- Devices will be populated here -->
                        </div>
                    </div>
                </div>

                <!-- Main Content Area -->
                <div class="lg:w-3/4">
                    <!-- Map View (Default) -->
                    <div id="mapView" class="space-y-6">
                        <div class="flex justify-between items-center">
                            <h2 class="text-2xl font-bold text-gray-800">Traffic Map Overview</h2>
                            <div class="flex items-center space-x-4">
                                <div class="flex items-center">
                                    <span class="jam-indicator jam-low"></span>
                                    <span class="text-sm text-gray-600">Low</span>
                                </div>
                                <div class="flex items-center">
                                    <span class="jam-indicator jam-medium"></span>
                                    <span class="text-sm text-gray-600">Medium</span>
                                </div>
                                <div class="flex items-center">
                                    <span class="jam-indicator jam-high"></span>
                                    <span class="text-sm text-gray-600">High</span>
                                </div>
                                <div class="flex items-center">
                                    <span class="jam-indicator jam-severe"></span>
                                    <span class="text-sm text-gray-600">Severe</span>
                                </div>
                            </div>
                        </div>
                        
                        <div class="map-container" id="map">
                            <!-- Map will be initialized here -->
                        </div>
                        
                        <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                            <div class="bg-white rounded-xl shadow-sm p-6">
                                <h3 class="font-semibold text-gray-800 mb-3">Active Jams</h3>
                                <p class="text-3xl font-bold text-red-600">12</p>
                                <p class="text-sm text-gray-600 mt-2">On major roads</p>
                            </div>
                            <div class="bg-white rounded-xl shadow-sm p-6">
                                <h3 class="font-semibold text-gray-800 mb-3">Clear Roads</h3>
                                <p class="text-3xl font-bold text-green-600">48</p>
                                <p class="text-sm text-gray-600 mt-2">Free flowing traffic</p>
                            </div>
                            <div class="bg-white rounded-xl shadow-sm p-6">
                                <h3 class="font-semibold text-gray-800 mb-3">Cameras Active</h3>
                                <p class="text-3xl font-bold text-blue-600">156</p>
                                <p class="text-sm text-gray-600 mt-2">Out of 160 total</p>
                            </div>
                        </div>
                    </div>

                    <!-- Camera/Live Mode View -->
                    <div id="cameraView" class="hidden space-y-6">
                        <div class="flex justify-between items-center">
                            <h2 class="text-2xl font-bold text-gray-800">Live Camera Mode</h2>
                            <div class="flex items-center space-x-3">
                                <button onclick="skipCamera('previous')" class="px-4 py-2 border border-gray-300 rounded-lg hover:bg-gray-50 transition">
                                    <svg class="h-5 w-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7"/>
                                    </svg>
                                </button>
                                <button onclick="skipCamera('next')" class="px-4 py-2 border border-gray-300 rounded-lg hover:bg-gray-50 transition">
                                    <svg class="h-5 w-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7"/>
                                    </svg>
                                </button>
                                <button onclick="exitCameraMode()" class="px-4 py-2 bg-red-100 text-red-600 rounded-lg font-medium hover:bg-red-200 transition">
                                    Cancel Live Feed
                                </button>
                            </div>
                        </div>
                        
                        <div class="bg-white rounded-xl shadow-sm p-6">
                            <div class="flex items-center justify-between mb-4">
                                <div>
                                    <h3 class="text-lg font-semibold text-gray-800">Camera <span id="currentCameraName">M1-001</span></h3>
                                    <p class="text-gray-600">Thika Road, 100m from Exit 5</p>
                                </div>
                                <div class="flex items-center space-x-2">
                                    <span class="px-3 py-1 bg-green-100 text-green-800 rounded-full text-sm font-medium">LIVE</span>
                                    <button id="saveVideoBtn" class="hidden px-4 py-2 bg-purple-600 text-white rounded-lg hover:bg-purple-700 transition">
                                        Save This Video
                                    </button>
                                </div>
                            </div>
                            
                            <div class="aspect-video bg-gray-900 rounded-lg overflow-hidden mb-6">
                                <!-- Live video feed placeholder -->
                                <div class="w-full h-full flex items-center justify-center">
                                    <div class="text-center">
                                        <div class="w-16 h-16 bg-red-500 rounded-full animate-pulse mx-auto mb-4"></div>
                                        <p class="text-white text-lg">Live CCTV Feed</p>
                                        <p class="text-gray-400">Camera M1-001 • Thika Road</p>
                                    </div>
                                </div>
                            </div>
                            
                            <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
                                <div class="camera-feed bg-gray-100 rounded-lg p-4 cursor-pointer" onclick="switchCamera('M1-001')">
                                    <div class="aspect-video bg-gray-300 rounded mb-2"></div>
                                    <p class="font-medium text-gray-800">M1-001</p>
                                    <p class="text-sm text-gray-600">Thika Road</p>
                                </div>
                                <div class="camera-feed bg-gray-100 rounded-lg p-4 cursor-pointer" onclick="switchCamera('M1-002')">
                                    <div class="aspect-video bg-gray-300 rounded mb-2"></div>
                                    <p class="font-medium text-gray-800">M1-002</p>
                                    <p class="text-sm text-gray-600">Thika Road</p>
                                </div>
                                <div class="camera-feed bg-gray-100 rounded-lg p-4 cursor-pointer" onclick="switchCamera('M1-003')">
                                    <div class="aspect-video bg-gray-300 rounded mb-2"></div>
                                    <p class="font-medium text-gray-800">M1-003</p>
                                    <p class="text-sm text-gray-600">Thika Road</p>
                                </div>
                                <div class="camera-feed bg-gray-100 rounded-lg p-4 cursor-pointer" onclick="switchCamera('M1-004')">
                                    <div class="aspect-video bg-gray-300 rounded mb-2"></div>
                                    <p class="font-medium text-gray-800">M1-004</p>
                                    <p class="text-sm text-gray-600">Thika Road</p>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- Video Library View -->
                    <div id="videoLibraryView" class="hidden space-y-6">
                        <div class="flex justify-between items-center">
                            <h2 class="text-2xl font-bold text-gray-800">Video Library</h2>
                            <button onclick="showRequestForm()" id="requestVideoBtn" class="hidden px-4 py-2 bg-purple-600 text-white rounded-lg hover:bg-purple-700 transition">
                                Request Past Video
                            </button>
                        </div>
                        
                        <div id="videoLibraryList" class="space-y-4">
                            <!-- Videos will be populated here -->
                        </div>
                        
                        <!-- Request Video Form -->
                        <div id="requestVideoForm" class="hidden bg-white rounded-xl shadow-sm p-6">
                            <h3 class="text-lg font-semibold text-gray-800 mb-4">Request Past Video</h3>
                            <div class="space-y-4">
                                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                                    <div>
                                        <label class="block text-sm font-medium text-gray-700 mb-2">Date</label>
                                        <input type="date" id="requestDate" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 outline-none">
                                    </div>
                                    <div>
                                        <label class="block text-sm font-medium text-gray-700 mb-2">Start Time</label>
                                        <input type="time" id="requestTime" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 outline-none">
                                    </div>
                                </div>
                                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                                    <div>
                                        <label class="block text-sm font-medium text-gray-700 mb-2">Camera Name</label>
                                        <input type="text" id="cameraName" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 outline-none" placeholder="e.g., M1-001">
                                    </div>
                                    <div>
                                        <label class="block text-sm font-medium text-gray-700 mb-2">Camera Number</label>
                                        <input type="text" id="cameraNumber" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 outline-none" placeholder="e.g., 001">
                                    </div>
                                </div>
                                <div class="bg-blue-50 border border-blue-200 rounded-lg p-4">
                                    <p class="text-sm text-blue-800">
                                        <strong>Note:</strong> Videos are limited to 10 minutes duration. End time is automatically calculated from start time. Requested videos will appear in your library within 24 hours.
                                    </p>
                                </div>
                                <div class="flex justify-end space-x-3">
                                    <button onclick="hideRequestForm()" class="px-4 py-2 border border-gray-300 text-gray-700 rounded-lg hover:bg-gray-50 transition">
                                        Cancel
                                    </button>
                                    <button onclick="submitVideoRequest()" class="px-4 py-2 bg-purple-600 text-white rounded-lg hover:bg-purple-700 transition">
                                        Submit Request
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- Driver Reports View -->
                    <div id="driverReportsView" class="hidden space-y-6">
                        <h2 class="text-2xl font-bold text-gray-800">Driver Reported Traffic Areas</h2>
                        
                        <div class="bg-white rounded-xl shadow-sm p-6">
                            <div class="overflow-x-auto">
                                <table class="min-w-full divide-y divide-gray-200">
                                    <thead>
                                        <tr>
                                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Location</th>
                                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Severity</th>
                                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Reported By</th>
                                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Time</th>
                                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Status</th>
                                        </tr>
                                    </thead>
                                    <tbody class="divide-y divide-gray-200">
                                        <tr>
                                            <td class="px-6 py-4 whitespace-nowrap">Thika Road near Exit 7</td>
                                            <td class="px-6 py-4 whitespace-nowrap">
                                                <span class="px-3 py-1 bg-red-100 text-red-800 rounded-full text-sm font-medium">Severe</span>
                                            </td>
                                            <td class="px-6 py-4 whitespace-nowrap">Driver #A457</td>
                                            <td class="px-6 py-4 whitespace-nowrap">10:45 AM</td>
                                            <td class="px-6 py-4 whitespace-nowrap">
                                                <span class="px-3 py-1 bg-yellow-100 text-yellow-800 rounded-full text-sm font-medium">Active</span>
                                            </td>
                                        </tr>
                                        <tr>
                                            <td class="px-6 py-4 whitespace-nowrap">Mombasa Road Toll Station</td>
                                            <td class="px-6 py-4 whitespace-nowrap">
                                                <span class="px-3 py-1 bg-orange-100 text-orange-800 rounded-full text-sm font-medium">High</span>
                                            </td>
                                            <td class="px-6 py-4 whitespace-nowrap">Driver #B892</td>
                                            <td class="px-6 py-4 whitespace-nowrap">10:30 AM</td>
                                            <td class="px-6 py-4 whitespace-nowrap">
                                                <span class="px-3 py-1 bg-yellow-100 text-yellow-800 rounded-full text-sm font-medium">Active</span>
                                            </td>
                                        </tr>
                                        <tr>
                                            <td class="px-6 py-4 whitespace-nowrap">Waiyaki Way near Westlands</td>
                                            <td class="px-6 py-4 whitespace-nowrap">
                                                <span class="px-3 py-1 bg-blue-100 text-blue-800 rounded-full text-sm font-medium">Medium</span>
                                            </td>
                                            <td class="px-6 py-4 whitespace-nowrap">Driver #C341</td>
                                            <td class="px-6 py-4 whitespace-nowrap">10:15 AM</td>
                                            <td class="px-6 py-4 whitespace-nowrap">
                                                <span class="px-3 py-1 bg-green-100 text-green-800 rounded-full text-sm font-medium">Cleared</span>
                                            </td>
                                        </tr>
                                    </tbody>
                                </table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script src="script.js"></script>
</body>
</html>
