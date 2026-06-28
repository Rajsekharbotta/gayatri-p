<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>LocuLingua - Global Access Portal</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap');
        
        :root {
            --primary: #0f766e;
        }
        
        body {
            font-family: 'Inter', system-ui, sans-serif;
        }
        
        .hero-bg {
            background: linear-gradient(rgba(15, 118, 110, 0.85), rgba(15, 118, 110, 0.9)), url('https://picsum.photos/id/1015/2000/1200');
            background-size: cover;
            background-position: center;
        }
        
        .nav-link {
            transition: all 0.3s ease;
        }
        
        .nav-link:hover {
            color: #14b8a6;
            transform: translateY(-2px);
        }
        
        .card-hover {
            transition: all 0.3s ease;
        }
        
        .card-hover:hover {
            transform: translateY(-8px);
            box-shadow: 0 20px 25px -5px rgb(0 0 0 / 0.1), 0 8px 10px -6px rgb(0 0 0 / 0.1);
        }
        
        #map {
            height: 450px;
            border-radius: 12px;
        }
        
        .chat-bubble {
            max-width: 75%;
        }
    </style>
</head>
<body class="bg-gray-50 text-gray-800">
    <!-- Navbar -->
    <nav class="bg-white shadow-lg sticky top-0 z-50">
        <div class="max-w-7xl mx-auto px-6 py-4 flex items-center justify-between">
            <div class="flex items-center space-x-3">
                <div class="w-10 h-10 bg-teal-600 rounded-2xl flex items-center justify-center text-white text-2xl font-bold">
                    🌍
                </div>
                <div>
                    <span class="text-2xl font-bold text-teal-700">LocuLingua</span>
                    <p class="text-xs text-teal-600 -mt-1">Travel Without Barriers</p>
                </div>
            </div>
            
            <div class="hidden md:flex items-center space-x-8 text-sm font-medium">
                <a href="#" onclick="navigateTo('home')" class="nav-link text-gray-700 hover:text-teal-600">Home</a>
                <a href="#" onclick="navigateTo('translate')" class="nav-link text-gray-700 hover:text-teal-600">Live Translate</a>
                <a href="#" onclick="navigateTo('places')" class="nav-link text-gray-700 hover:text-teal-600">Nearby Places</a>
                <a href="#" onclick="navigateTo('map')" class="nav-link text-gray-700 hover:text-teal-600">Live Map</a>
            </div>
            
            <div class="flex items-center gap-4">
                <div class="relative">
                    <select id="lang-select" onchange="changeLanguage()" 
                            class="bg-white border border-gray-300 rounded-xl px-4 py-2 text-sm focus:outline-none focus:border-teal-500">
                        <option value="en">English</option>
                        <option value="hi">हिन्दी</option>
                        <option value="es">Español</option>
                        <option value="fr">Français</option>
                        <option value="ar">العربية</option>
                        <option value="zh">中文</option>
                    </select>
                </div>
                <button onclick="getCurrentLocation()" 
                        class="bg-teal-600 hover:bg-teal-700 text-white px-5 py-2.5 rounded-2xl flex items-center gap-2 text-sm font-medium transition">
                    <i class="fas fa-location-dot"></i>
                    <span id="loc-btn-text">Detect Location</span>
                </button>
            </div>
        </div>
    </nav>

    <!-- Hero Section -->
    <section id="home" class="hero-bg text-white py-24">
        <div class="max-w-7xl mx-auto px-6 text-center">
            <div class="max-w-3xl mx-auto">
                <h1 class="text-6xl font-bold mb-6 leading-tight">
                    Your World, <span class="text-teal-200">In Your Language</span>
                </h1>
                <p class="text-xl mb-10 text-teal-100">
                    Free real-time location services, instant translation, and local discovery. 
                    No borders. No barriers.
                </p>
                
                <div class="bg-white/10 backdrop-blur-lg rounded-3xl p-2 max-w-xl mx-auto">
                    <div class="flex items-center bg-white rounded-3xl text-gray-900">
                        <input id="search-input" 
                               onkeypress="if(event.key === 'Enter') searchPlaces()"
                               type="text" 
                               placeholder="Search restaurants, lodges, markets..." 
                               class="flex-1 bg-transparent px-8 py-5 outline-none text-lg rounded-3xl">
                        <button onclick="searchPlaces()" 
                                class="bg-teal-600 hover:bg-teal-700 m-1.5 px-10 py-4 rounded-3xl font-medium flex items-center gap-2">
                            <i class="fas fa-search"></i>
                            <span>Search</span>
                        </button>
                    </div>
                </div>
                
                <div class="flex justify-center gap-8 mt-10 text-sm">
                    <div class="flex items-center gap-2">
                        <i class="fas fa-globe text-teal-300"></i>
                        <span>150+ Countries</span>
                    </div>
                    <div class="flex items-center gap-2">
                        <i class="fas fa-language text-teal-300"></i>
                        <span>100+ Languages</span>
                    </div>
                    <div class="flex items-center gap-2">
                        <i class="fas fa-wallet text-teal-300"></i>
                        <span>Budget Filters</span>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <!-- Current Location Status -->
    <div class="bg-white border-b py-4">
        <div class="max-w-7xl mx-auto px-6 flex items-center justify-between text-sm">
            <div class="flex items-center gap-3">
                <div id="current-location" class="flex items-center gap-2 text-teal-700 font-medium">
                    <i class="fas fa-map-marker-alt"></i>
                    <span>Detecting your location...</span>
                </div>
            </div>
            <div id="current-lang" class="text-gray-500 flex items-center gap-2">
                <span class="font-medium">Current Language:</span> 
                <span class="px-3 py-1 bg-teal-100 text-teal-700 rounded-full text-xs font-medium">English (EN)</span>
            </div>
        </div>
    </div>

    <!-- Live Translation Section -->
    <section id="translate" class="py-16 bg-white">
        <div class="max-w-7xl mx-auto px-6">
            <div class="flex flex-col md:flex-row gap-12">
                <div class="flex-1">
                    <h2 class="text-4xl font-semibold mb-6 text-teal-800">Instant Voice Translation</h2>
                    <p class="text-gray-600 mb-8 text-lg">
                        Speak in your language. Hear and read everything in your preferred language.
                    </p>
                    
                    <div class="bg-gray-100 rounded-3xl p-8">
                        <div class="grid grid-cols-2 gap-6">
                            <div>
                                <label class="block text-xs uppercase tracking-widest mb-2 text-gray-500">You Speak</label>
                                <select id="input-lang" class="w-full p-4 rounded-2xl border">
                                    <option value="en">English</option>
                                    <option value="hi">हिन्दी</option>
                                    <option value="es">Español</option>
                                </select>
                            </div>
                            <div>
                                <label class="block text-xs uppercase tracking-widest mb-2 text-gray-500">Translate To</label>
                                <select id="output-lang" class="w-full p-4 rounded-2xl border">
                                    <option value="en">English</option>
                                    <option value="hi">हिन्दी</option>
                                    <option value="es">Español</option>
                                    <option value="fr">Français</option>
                                </select>
                            </div>
                        </div>
                        
                        <div class="mt-8">
                            <button onclick="startVoiceTranslation()" 
                                    class="w-full bg-gradient-to-r from-teal-600 to-cyan-600 hover:from-teal-700 hover:to-cyan-700 text-white py-6 rounded-3xl text-xl font-semibold flex items-center justify-center gap-4 shadow-lg">
                                <i class="fas fa-microphone text-3xl"></i>
                                <span>Start Speaking • Live Translate</span>
                            </button>
                        </div>
                        
                        <div id="translation-result" class="mt-6 min-h-[120px] bg-white p-6 rounded-2xl border border-dashed border-gray-300 hidden">
                            <!-- Filled by JS -->
                        </div>
                    </div>
                </div>
                
                <div class="flex-1">
                    <div class="bg-teal-50 border border-teal-100 rounded-3xl p-8 h-full">
                        <h3 class="font-semibold text-xl mb-6">How it works</h3>
                        <div class="space-y-8">
                            <div class="flex gap-5">
                                <div class="w-10 h-10 bg-white rounded-2xl flex items-center justify-center text-2xl shadow">🌍</div>
                                <div>
                                    <div class="font-medium">1. Detect Location</div>
                                    <div class="text-sm text-gray-600">Automatically detects local language around you</div>
                                </div>
                            </div>
                            <div class="flex gap-5">
                                <div class="w-10 h-10 bg-white rounded-2xl flex items-center justify-center text-2xl shadow">🗣️</div>
                                <div>
                                    <div class="font-medium">2. Speak Freely</div>
                                    <div class="text-sm text-gray-600">Talk in your native tongue</div>
                                </div>
                            </div>
                            <div class="flex gap-5">
                                <div class="w-10 h-10 bg-white rounded-2xl flex items-center justify-center text-2xl shadow">📢</div>
                                <div>
                                    <div class="font-medium">3. Instant Output</div>
                                    <div class="text-sm text-gray-600">Listen + read in target language</div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <!-- Nearby Places -->
    <section id="places" class="py-16 bg-gray-100">
        <div class="max-w-7xl mx-auto px-6">
            <div class="flex justify-between items-end mb-10">
                <div>
                    <h2 class="text-4xl font-semibold text-teal-800">Discover Nearby</h2>
                    <p class="text-gray-600 mt-2">Restaurants • Street Food • Budget Lodges • Markets</p>
                </div>
                
                <div class="flex gap-3">
                    <select id="budget-filter" onchange="filterPlaces()" class="px-6 py-3 rounded-2xl border bg-white">
                        <option value="">All Budgets</option>
                        <option value="low">₹0 - ₹500</option>
                        <option value="mid">₹500 - ₹1500</option>
                        <option value="high">₹1500+</option>
                    </select>
                    <select id="type-filter" onchange="filterPlaces()" class="px-6 py-3 rounded-2xl border bg-white">
                        <option value="">All Types</option>
                        <option value="restaurant">Restaurants</option>
                        <option value="stall">Street Stalls</option>
                        <option value="lodge">Lodges</option>
                    </select>
                </div>
            </div>
            
            <div id="places-grid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                <!-- Populated by JS -->
            </div>
        </div>
    </section>

    <!-- Interactive Map -->
    <section id="map" class="py-16 bg-white">
        <div class="max-w-7xl mx-auto px-6">
            <h2 class="text-4xl font-semibold text-teal-800 mb-8">Live Location Map</h2>
            <div id="map-container" class="bg-gray-200 rounded-3xl overflow-hidden shadow-xl">
                <div id="map"></div>
            </div>
        </div>
    </section>

    <!-- AI Assistant -->
    <div onclick="toggleAssistant()" id="assistant-btn"
         class="fixed bottom-8 right-8 w-16 h-16 bg-gradient-to-br from-teal-500 to-cyan-600 rounded-full flex items-center justify-center text-white shadow-2xl cursor-pointer hover:scale-110 transition z-50">
        <i class="fas fa-robot text-3xl"></i>
    </div>

    <!-- AI Chat Window -->
    <div id="assistant-window" class="hidden fixed bottom-28 right-8 w-96 bg-white rounded-3xl shadow-2xl overflow-hidden border border-gray-100 z-50">
        <div class="bg-teal-700 text-white p-4 flex justify-between items-center">
            <div class="flex items-center gap-3">
                <div class="w-8 h-8 bg-white/20 rounded-2xl flex items-center justify-center">🤖</div>
                <div>
                    <div class="font-semibold">LocuLingua AI Assistant</div>
                    <div class="text-xs opacity-75">Multilingual • Voice Enabled</div>
                </div>
            </div>
            <button onclick="toggleAssistant()" class="text-white/70 hover:text-white">✕</button>
        </div>
        
        <div id="chat-messages" class="h-96 overflow-y-auto p-5 space-y-4 bg-gray-50">
            <div class="chat-bubble bg-teal-100 p-4 rounded-3xl rounded-tl-none">
                Hello! How can I help you today? Ask me anything about local places, translations, or directions.
            </div>
        </div>
        
        <div class="p-4 border-t bg-white">
            <div class="flex gap-2">
                <input id="chat-input" type="text" 
                       onkeypress="if(event.key === 'Enter') sendMessage()" 
                       placeholder="Type or speak..." 
                       class="flex-1 border border-gray-300 focus:border-teal-400 rounded-3xl px-6 py-4 outline-none">
                <button onclick="sendMessage()" 
                        class="bg-teal-600 hover:bg-teal-700 text-white w-14 h-14 rounded-3xl flex items-center justify-center">
                    <i class="fas fa-paper-plane"></i>
                </button>
            </div>
            <button onclick="startVoiceInput()" 
                    class="text-xs text-teal-600 hover:text-teal-700 mt-3 flex items-center gap-1">
                <i class="fas fa-microphone"></i> Voice Input
            </button>
        </div>
    </div>

    <footer class="bg-teal-900 text-teal-100 py-12">
        <div class="max-w-7xl mx-auto px-6 text-center">
            <div class="flex justify-center gap-8 text-2xl mb-6">
                <i class="fab fa-x-twitter"></i>
                <i class="fab fa-facebook"></i>
                <i class="fab fa-instagram"></i>
            </div>
            <p class="text-teal-400">© 2026 LocuLingua • Free Public Service Portal</p>
            <p class="text-xs text-teal-500 mt-2">Making travel accessible for everyone, everywhere.</p>
        </div>
    </footer>

    <script>
        // Tailwind script already loaded
        
        let map;
        let userMarker;
        let currentLat = 28.6139;
        let currentLng = 77.2090;
        let currentPlace = "New Delhi, India";
        
        // Sample places data
        let placesData = [
            {
                id: 1,
                name: "Mohan Dhaba",
                type: "stall",
                price: 120,
                rating: 4.6,
                distance: "0.8 km",
                desc: "Famous for butter paratha & chai",
                lat: 28.6200,
                lng: 77.2150
            },
            {
                id: 2,
                name: "The Grand Lodge",
                type: "lodge",
                price: 850,
                rating: 4.2,
                distance: "1.2 km",
                desc: "Clean & affordable stay",
                lat: 28.6080,
                lng: 77.2020
            },
            {
                id: 3,
                name: "Spice Garden Restaurant",
                type: "restaurant",
                price: 650,
                rating: 4.8,
                distance: "2.1 km",
                desc: "North Indian & Continental",
                lat: 28.6300,
                lng: 77.2200
            },
            {
                id: 4,
                name: "Street Chaat Corner",
                type: "stall",
                price: 80,
                rating: 4.5,
                distance: "0.4 km",
                desc: "Pani puri & golgappe",
                lat: 28.6150,
                lng: 77.2120
            }
        ];
        
        function init() {
            // Initialize map
            map = L.map('map').setView([currentLat, currentLng], 15);
            
            L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
                attribution: '&copy; OpenStreetMap contributors'
            }).addTo(map);
            
            userMarker = L.marker([currentLat, currentLng]).addTo(map)
                .bindPopup("You are here").openPopup();
            
            // Render places
            renderPlaces(placesData);
            
            // Fake location after 1.5s
            setTimeout(() => {
                document.getElementById('current-location').innerHTML = `
                    <i class="fas fa-map-marker-alt"></i>
                    <span>New Delhi, India • Hindi + English detected</span>
                `;
            }, 1500);
        }
        
        function getCurrentLocation() {
            const btn = document.getElementById('loc-btn-text');
            btn.innerHTML = `<i class="fas fa-spinner fa-spin"></i>`;
            
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition((position) => {
                    currentLat = position.coords.latitude;
                    currentLng = position.coords.longitude;
                    
                    if (map && userMarker) {
                        map.setView([currentLat, currentLng], 16);
                        userMarker.setLatLng([currentLat, currentLng]);
                    }
                    
                    document.getElementById('current-location').innerHTML = `
                        <i class="fas fa-map-marker-alt"></i>
                        <span>Your live location detected</span>
                    `;
                    btn.textContent = "Location Updated";
                }, () => {
                    alert("Location access denied. Using default location.");
                    btn.textContent = "Detect Location";
                });
            } else {
                alert("Geolocation not supported by your browser.");
                btn.textContent = "Detect Location";
            }
        }
        
        function renderPlaces(places) {
            const container = document.getElementById('places-grid');
            container.innerHTML = '';
            
            places.forEach(place => {
                const priceSymbol = place.price < 300 ? '₹' : place.price < 800 ? '₹₹' : '₹₹₹';
                
                const card = document.createElement('div');
                card.className = `bg-white rounded-3xl overflow-hidden card-hover border border-gray-100`;
                card.innerHTML = `
                    <div class="h-48 bg-gradient-to-br from-teal-500 to-cyan-600 flex items-center justify-center text-6xl">
                        ${place.type === 'stall' ? '🍲' : place.type === 'restaurant' ? '🍽️' : '🛏️'}
                    </div>
                    <div class="p-6">
                        <div class="flex justify-between items-start">
                            <div>
                                <h4 class="font-semibold text-xl">${place.name}</h4>
                                <p class="text-teal-600 text-sm">${place.distance}</p>
                            </div>
                            <div class="text-right">
                                <span class="text-amber-500 text-xl">${place.rating} ★</span>
                            </div>
                        </div>
                        <p class="text-gray-600 text-sm my-3">${place.desc}</p>
                        <div class="flex items-center justify-between text-sm">
                            <span class="font-medium text-teal-700">${priceSymbol}</span>
                            <button onclick="showPlaceDetail(${place.id})" 
                                    class="text-teal-600 hover:text-teal-700 font-medium flex items-center gap-1">
                                Details <i class="fas fa-arrow-right"></i>
                            </button>
                        </div>
                    </div>
                `;
                container.appendChild(card);
            });
        }
        
        function filterPlaces() {
            const budget = document.getElementById('budget-filter').value;
            const type = document.getElementById('type-filter').value;
            
            let filtered = placesData;
            
            if (budget) {
                if (budget === 'low') filtered = filtered.filter(p => p.price < 500);
                else if (budget === 'mid') filtered = filtered.filter(p => p.price >= 500 && p.price <= 1500);
                else filtered = filtered.filter(p => p.price > 1500);
            }
            
            if (type) {
                filtered = filtered.filter(p => p.type === type);
            }
            
            renderPlaces(filtered);
        }
        
        function searchPlaces() {
            const query = document.getElementById('search-input').value.toLowerCase().trim();
            if (!query) {
                renderPlaces(placesData);
                return;
            }
            
            const filtered = placesData.filter(place => 
                place.name.toLowerCase().includes(query) || 
                place.desc.toLowerCase().includes(query)
            );
            
            renderPlaces(filtered);
            
            // Scroll to places
            document.getElementById('places').scrollIntoView({ behavior: 'smooth' });
        }
        
        function showPlaceDetail(id) {
            const place = placesData.find(p => p.id === id);
            if (!place) return;
            
            alert(`\n${place.name}\n\n${place.desc}\nDistance: ${place.distance}\nPrice range: ₹${place.price}\nRating: ${place.rating} stars\n\nDirections would open here in the full app.`);
        }
        
        // Translation simulation
        let recognition;
        
        function startVoiceTranslation() {
            const inputLang = document.getElementById('input-lang').value;
            const outputLang = document.getElementById('output-lang').value;
            
            const resultDiv = document.getElementById('translation-result');
            resultDiv.classList.remove('hidden');
            resultDiv.innerHTML = `
                <div class="text-xs uppercase mb-2 text-teal-500">Detected Speech → Translation</div>
                <div class="italic">"Namaste, aapka swagat hai"</div>
                <div class="mt-6 text-teal-700 font-medium">→</div>
                <div class="mt-2">"Hello, welcome to our city!"</div>
                <div class="text-[10px] text-gray-500 mt-8">Powered by browser speech synthesis</div>
            `;
            
            // Speak the translation
            speak("Hello, welcome to our city!", outputLang);
        }
        
        function speak(text, lang = 'en') {
            if ('speechSynthesis' in window) {
                const utterance = new SpeechSynthesisUtterance(text);
                utterance.lang = lang === 'hi' ? 'hi-IN' : 'en-US';
                speechSynthesis.speak(utterance);
            }
        }
        
        function changeLanguage() {
            const lang = document.getElementById('lang-select').value;
            const display = document.getElementById('current-lang');
            
            const langNames = {
                'en': 'English (EN)',
                'hi': 'हिन्दी (HI)',
                'es': 'Español (ES)',
                'fr': 'Français (FR)',
                'ar': 'العربية (AR)',
                'zh': '中文 (ZH)'
            };
            
            display.innerHTML = `
                <span class="font-medium">Current Language:</span> 
                <span class="px-3 py-1 bg-teal-100 text-teal-700 rounded-full text-xs font-medium">${langNames[lang]}</span>
            `;
        }
        
        // AI Assistant
        function toggleAssistant() {
            const win = document.getElementById('assistant-window');
            win.classList.toggle('hidden');
        }
        
        function sendMessage() {
            const input = document.getElementById('chat-input');
            const msg = input.value.trim();
            if (!msg) return;
            
            const chat = document.getElementById('chat-messages');
            
            // User message
            const userMsg = document.createElement('div');
            userMsg.className = 'chat-bubble bg-teal-600 text-white p-4 rounded-3xl rounded-tr-none ml-auto';
            userMsg.textContent = msg;
            chat.appendChild(userMsg);
            
            input.value = '';
            chat.scrollTop = chat.scrollHeight;
            
            // Simulate AI response
            setTimeout(() => {
                const responses = [
                    "The nearest cheap lodge is about 800 meters away. Would you like directions?",
                    "I can help translate your sentence into Hindi or Spanish. Try speaking to me!",
                    "There is a popular street food stall 400m from your location with excellent reviews.",
                    "Budget filter applied. Showing only options under ₹700."
                ];
                
                const aiMsg = document.createElement('div');
                aiMsg.className = 'chat-bubble bg-white border p-4 rounded-3xl rounded-tl-none';
                aiMsg.textContent = responses[Math.floor(Math.random() * responses.length)];
                chat.appendChild(aiMsg);
                chat.scrollTop = chat.scrollHeight;
            }, 900);
        }
        
        function startVoiceInput() {
            if ('SpeechRecognition' in window || 'webkitSpeechRecognition' in window) {
                const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
                recognition = new SpeechRecognition();
                recognition.lang = 'en-US';
                
                recognition.onresult = function(event) {
                    const transcript = event.results[0][0].transcript;
                    document.getElementById('chat-input').value = transcript;
                    sendMessage();
                };
                
                recognition.start();
            } else {
                alert("Voice input not supported in your browser.");
            }
        }
        
        function navigateTo(section) {
            document.getElementById(section).scrollIntoView({ behavior: 'smooth' });
        }
        
        // Keyboard shortcuts
        document.addEventListener('keydown', function(e) {
            if (e.key === "/" && document.getElementById('search-input') !== document.activeElement) {
                e.preventDefault();
                document.getElementById('search-input').focus();
            }
        });
        
        // Initialize everything
        window.onload = function() {
            init();
        };
    </script>
</body>
</html>
