// Traffic Jam App - Core Functionality Module

class TrafficJamApp {
    constructor() {
        this.currentPlan = 'normal';
        this.gpsWatcher = null;
        this.currentCamera = 0;
        this.autoSwitch = true;
        this.cameras = [];
        this.userPosition = null;
        this.map = null;
        this.mockMode = true; // For demo purposes without real CCTV
        
        this.init();
    }
    
    init() {
        this.setupEventListeners();
        this.initializeMap();
        this.generateCameraNetwork();
    }
    
    setupEventListeners() {
        // Handle visibility change to pause/resume GPS
        document.addEventListener('visibilitychange', () => {
            if (document.hidden) {
                this.pauseGPS();
            } else {
                this.resumeGPS();
            }
        });
        
        // Handle window resize
        window.addEventListener('resize', () => {
            if (this.map) this.map.invalidateSize();
        });
    }
    
    initializeMap() {
        // Default coordinates (Nairobi, Kenya)
        const defaultCoords = [-1.2921, 36.8219];
        
        this.map = L.map('map', {
            zoomControl: false,
            attributionControl: false
        }).setView(defaultCoords, 13);
        
        // Add OpenStreetMap tiles
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            maxZoom: 19,
            attribution: '© OpenStreetMap'
        }).addTo(this.map);
        
        // Add zoom control to bottom right
        L.control.zoom({
            position: 'bottomright'
        }).addTo(this.map);
        
        this.renderTrafficOverlay();
    }
    
    generateCameraNetwork() {
        // Simulate CCTV network every 100m on major roads
        const roads = [
            { name: 'Mombasa Road', baseCoord: [-1.2921, 36.8219], bearing: 45 },
            { name: 'Uhuru Highway', baseCoord: [-1.2850, 36.8200], bearing: 90 },
            { name: 'Jogoo Road', baseCoord: [-1.2800, 36.8600], bearing: 135 }
        ];
        
        roads.forEach(road => {
            for (let i = 0; i < 20; i++) {
                const distance = i * 0.001; // Approx 100m in degrees
                const angle = road.bearing * (Math.PI / 180);
                
                this.cameras.push({
                    id: `${road.name.replace(/\s+/g, '')}_${i}`,
                    name: `${road.name} - Camera ${i + 1}`,
                    road: road.name,
                    distance: i * 100,
                    lat: road.baseCoord[0] + (distance * Math.sin(angle)),
                    lng: road.baseCoord[1] + (distance * Math.cos(angle)),
                    direction: road.bearing,
                    status: this.getRandomStatus(),
                    lastUpdate: new Date()
                });
            }
        });
        
        this.renderCameraMarkers();
    }
    
    getRandomStatus() {
        const statuses = ['clear', 'moderate', 'heavy', 'blocked'];
        const weights = [0.4, 0.3, 0.2, 0.1]; // Probability distribution
        const random = Math.random();
        let sum = 0;
        
        for (let i = 0; i < weights.length; i++) {
            sum += weights[i];
            if (random < sum) return statuses[i];
        }
        return 'clear';
    }
    
    renderCameraMarkers() {
        this.cameras.forEach((cam, index) => {
            const icon = L.divIcon({
                className: 'camera-marker',
                html: `<div class="w-3 h-3 rounded-full bg-blue-500 border-2 border-white shadow-md ${cam.status === 'blocked' ? 'bg-red-500 animate-pulse' : ''}"></div>`,
                iconSize: [12, 12],
                iconAnchor: [6, 6]
            });
            
            const marker = L.marker([cam.lat, cam.lng], { icon }).addTo(this.map);
            
            marker.bindPopup(`
                <div class="p-2">
                    <h4 class="font-bold text-sm">${cam.name}</h4>
                    <p class="text-xs text-gray-600">Status: ${cam.status}</p>
                    <p class="text-xs text-gray-500">Updated: ${cam.lastUpdate.toLocaleTimeString()}</p>
                    ${this.currentPlan !== 'normal' ? 
                        `<button onclick="app.viewCamera(${index})" class="mt-2 px-3 py-1 bg-blue-600 text-white text-xs rounded hover:bg-blue-700">View Live</button>` 
                        : '<p class="text-xs text-orange-500 mt-1">Premium required for live view</p>'}
                </div>
            `);
        });
    }
    
    renderTrafficOverlay() {
        // Simulate traffic data with polylines
        const trafficRoutes = [
            {
                coords: [[-1.2921, 36.8219], [-1.2950, 36.8250], [-1.2980, 36.8300]],
                congestion: 0.8,
                name: 'Mombasa Rd - CBD to South B'
            },
            {
                coords: [[-1.2850, 36.8200], [-1.2880, 36.8250], [-1.2900, 36.8300]],
                congestion: 0.3,
                name: 'Uhuru Hwy - Museum Hill'
            }
        ];
        
        trafficRoutes.forEach(route => {
            const color = route.congestion > 0.7 ? '#ef4444' : 
                         route.congestion > 0.4 ? '#eab308' : '#22c55e';
            
            const polyline = L.polyline(route.coords, {
                color: color,
                weight: 6,
                opacity: 0.7,
                lineCap: 'round',
                lineJoin: 'round'
            }).addTo(this.map);
            
            // Animate traffic flow
            if (route.congestion < 0.8) {
                this.animateTrafficFlow(polyline, route.congestion);
            }
        });
    }
    
    animateTrafficFlow(polyline, speed) {
        // CSS animation for traffic flow effect would go here
        // This is a placeholder for advanced SVG animation
    }
    
    startGPSTracking() {
        if (!navigator.geolocation) {
            this.showToast('GPS not supported on this device');
            return;
        }
        
        const options = {
            enableHighAccuracy: true,
            timeout: 5000,
            maximumAge: 0
        };
        
        this.gpsWatcher = navigator.geolocation.watchPosition(
            (position) => this.handlePositionUpdate(position),
            (error) => this.handleGPSError(error),
            options
        );
        
        this.showToast('GPS tracking started');
    }
    
    handlePositionUpdate(position) {
        const { latitude, longitude, speed, heading } = position.coords;
        this.userPosition = { lat: latitude, lng: longitude, speed, heading };
        
        // Update user marker
        this.updateUserMarker(latitude, longitude);
        
        // Auto-switch camera if enabled and user has premium
        if (this.autoSwitch && this.currentPlan !== 'normal') {
            this.checkCameraProximity();
        }
        
        // Update UI with speed if available
        if (speed) {
            this.updateSpeedDisplay(speed);
        }
    }
    
    updateUserMarker(lat, lng) {
        if (this.userMarker) {
            this.map.removeLayer(this.userMarker);
        }
        
        const userIcon = L.divIcon({
            className: 'user-location',
            html: `
                <div class="relative">
                    <div class="w-4 h-4 bg-blue-600 rounded-full border-2 border-white shadow-lg"></div>
                    <div class="absolute inset-0 w-4 h-4 bg-blue-600 rounded-full animate-ping opacity-75"></div>
                </div>
            `,
            iconSize: [16, 16],
            iconAnchor: [8, 8]
        });
        
        this.userMarker = L.marker([lat, lng], { icon: userIcon, zIndexOffset: 1000 }).addTo(this.map);
        this.map.panTo([lat, lng]);
    }
    
    checkCameraProximity() {
        if (!this.userPosition) return;
        
        let nearestCam = null;
        let minDistance = Infinity;
        let nearestIndex = -1;
        
        this.cameras.forEach((cam, index) => {
            const distance = this.calculateDistance(
                this.userPosition.lat, 
                this.userPosition.lng, 
                cam.lat, 
                cam.lng
            );
            
            if (distance < minDistance) {
                minDistance = distance;
                nearestCam = cam;
                nearestIndex = index;
            }
        });
        
        // Switch if within 150m and different from current
        if (minDistance < 0.15 && nearestIndex !== this.currentCamera) {
            this.switchToCamera(nearestIndex);
        }
    }
    
    calculateDistance(lat1, lng1, lat2, lng2) {
        // Haversine formula for accurate distance calculation
        const R = 6371; // Earth's radius in km
        const dLat = (lat2 - lat1) * Math.PI / 180;
        const dLng = (lng2 - lng1) * Math.PI / 180;
        const a = Math.sin(dLat/2) * Math.sin(dLat/2) +
                  Math.cos(lat1 * Math.PI / 180) * Math.cos(lat2 * Math.PI / 180) *
                  Math.sin(dLng/2) * Math.sin(dLng/2);
        const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
        return R * c; // Distance in km
    }
    
    switchToCamera(index) {
        this.currentCamera = index;
        const cam = this.cameras[index];
        
        // Update UI
        document.getElementById('currentCameraLocation').textContent = cam.name;
        document.getElementById('mainCameraFeed').src = 
            `http://static.photos/technology/640x360/${100 + index}?t=${Date.now()}`;
        
        // Highlight in strip
        this.highlightCameraInStrip(index);
        
        // Show notification
        this.showToast(`Switched to ${cam.name}`);
    }
    
    highlightCameraInStrip(index) {
        // Update camera strip UI to show active camera
        const strip = document.getElementById('cameraStrip');
        if (strip) {
            const items = strip.children;
            Array.from(items).forEach((item, i) => {
                if (i === index) {
                    item.classList.add('ring-2', 'ring-blue-500');
                } else {
                    item.classList.remove('ring-2', 'ring-blue-500');
                }
            });
        }
    }
    
    manualCameraJump(steps) {
        const newIndex = this.currentCamera + steps;
        if (newIndex >= 0 && newIndex < this.cameras.length) {
            this.switchToCamera(newIndex);
        } else {
            this.showToast('No cameras in that direction');
        }
    }
    
    pauseGPS() {
        if (this.gpsWatcher) {
            navigator.geolocation.clearWatch(this.gpsWatcher);
        }
    }
    
    resumeGPS() {
        if (this.gpsWatcher) {
            this.startGPSTracking();
        }
    }
    
    handleGPSError(error) {
        console.error('GPS Error:', error);
        let message = 'Unable to get location';
        switch(error.code) {
            case error.PERMISSION_DENIED:
                message = "Please enable location permissions";
                break;
            case error.POSITION_UNAVAILABLE:
                message = "Location information unavailable";
                break;
            case error.TIMEOUT:
                message = "Location request timed out";
                break;
        }
        this.showToast(message);
    }
    
    updateSpeedDisplay(speed) {
        // Convert m/s to km/h
        const kmh = Math.round(speed * 3.6);
        // Could update a speedometer UI element here
    }
    
    showToast(message) {
        // Create toast notification
        const toast = document.createElement('div');
        toast.className = 'toast';
        toast.textContent = message;
        document.body.appendChild(toast);
        
        setTimeout(() => toast.classList.add('show'), 100);
        setTimeout(() => {
            toast.classList.remove('show');
            setTimeout(() => toast.remove(), 300);
        }, 3000);
    }
    
    saveVideoFrame() {
        if (this.currentPlan !== 'platinum') {
            this.showToast('Platinum subscription required');
            return;
        }
        
        const video = document.getElementById('mainCameraFeed');
        const canvas = document.createElement('canvas');
        canvas.width = video.naturalWidth || 640;
        canvas.height = video.naturalHeight || 360;
        const ctx = canvas.getContext('2d');
        ctx.drawImage(video, 0, 0);
        
        // Download
        const link = document.createElement('a');
        link.download = `traffic-jam-${Date.now()}.jpg`;
        link.href = canvas.toDataURL('image/jpeg');
        link.click();
        
        this.showToast('Image saved successfully');
    }
    
    replayHistory(timestamp) {
        if (this.currentPlan !== 'platinum') return;
        
        // Simulate historical data retrieval
        const cam = this.cameras[this.currentCamera];
        const historicalUrl = `http://static.photos/technology/640x360/${100 + this.currentCamera}?date=${timestamp}`;
        
        document.getElementById('mainCameraFeed').src = historicalUrl;
        this.showToast(`Showing footage from ${new Date(timestamp).toLocaleString()}`);
    }
}

// Initialize app
const app = new TrafficJamApp();
