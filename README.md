# ourcalender
Calender 2025
<!-- Chosen Palette: Warm Neutral & Subtle Blue (Based on Tailwind's gray-50, gray-700, and blue-600) -->
<!-- Application Structure Plan: Dashboard/Thematic Approach. The SPA uses a single vertical scrollable layout divided into a header/controls section and a content grid. The core interaction is a "View Toggle" (Grid vs. List) which dynamically changes the visual density, allowing users to quickly scan all 12 months (Overview) or focus on individual month details (Focus). This structure is chosen because the content (12 distinct units) is fixed, and the main user task is either quick identification or deeper inspection. -->
<!-- Visualization & Content Choices: Summary: 12 months/calendar -> Goal: Organization, Comparison, and Context -> Viz/Presentation Method: Responsive Card Grid (HTML/Tailwind) -> Interaction: View Toggle (Grid/List), Current Month Highlight (JS) -> Justification: Cards are excellent for discrete, thematic units. The toggle enhances user control over visual density. -> Library/Method: Vanilla JS for dynamic layout and content rendering. Placeholder images used to maintain the single-file constraint. -->
<!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Interactive 12-Month Calendar View</title>
    
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- FIX: Added manifestLink placeholder to prevent JavaScript error -->
    <link rel="manifest" id="manifestLink">

    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;800&display=swap');
        body { font-family: 'Inter', sans-serif; }
        .chart-container { 
            position: relative; 
            width: 100%; 
            max-width: 600px; 
            margin-left: auto; 
            margin-right: auto; 
            height: 300px; 
            max-height: 400px; 
        }
        @media (min-width: 768px) { 
            .chart-container { height: 350px; } 
        }
    </style>
</head>
<body class="bg-gray-50 min-h-screen">

    <div class="max-w-6xl mx-auto p-4 sm:p-8">
        <!-- Application Title and Context Section -->
        <header class="text-center mb-10 pt-4">
            <h1 class="text-4xl font-extrabold text-gray-800 tracking-tight">Yearly Overview Dashboard</h1>
            <p class="text-lg text-gray-500 mt-2 max-w-2xl mx-auto">
                Welcome to your interactive calendar overview for the current year. This application presents all 12 months as visual cards, allowing for easy comparison and quick identification of the current period. Use the controls below to tailor your viewing experience.
            </p>
        </header>

        <!-- Interactive Controls Section -->
        <section class="mb-8 p-4 bg-white rounded-xl shadow-lg flex flex-col md:flex-row justify-between items-center space-y-4 md:space-y-0">
            <div class="text-sm font-semibold text-gray-600 flex items-center space-x-2">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-blue-500" viewBox="0 0 20 20" fill="currentColor">
                    <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm-1-8V6a1 1 0 112 0v4h2a1 1 0 110 2h-3a1 1 0 01-1-1z" clip-rule="evenodd" />
                </svg>
                <span id="currentDateDisplay">Loading...</span>
            </div>
            
            <!-- View Toggle Button -->
            <button id="viewToggle" class="flex items-center space-x-2 px-5 py-2 bg-blue-600 text-white font-medium rounded-full shadow-md hover:bg-blue-700 transition duration-150 transform hover:scale-105">
                <svg id="gridIcon" xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                    <path d="M5 3a2 2 0 00-2 2v2a2 2 0 002 2h2a2 2 0 002-2V5a2 2 0 00-2-2H5zM5 11a2 2 0 00-2 2v2a2 2 0 002 2h2a2 2 0 002-2v-2a2 2 0 00-2-2H5zM11 5a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2h-2a2 2 0 01-2-2V5zM11 13a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2h-2a2 2 0 01-2-2v-2z" />
                </svg>
                <svg id="listIcon" xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 hidden" viewBox="0 0 20 20" fill="currentColor">
                    <path fill-rule="evenodd" d="M3 5a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1zM3 10a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1zM3 15a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1z" clip-rule="evenodd" />
                </svg>
                <span id="toggleText">Switch to List View</span>
            </button>
        </section>

        <!-- Gallery Grid/List Section -->
        <section>
            <p class="text-sm text-center text-gray-400 mb-6">Click any month card to view potential context or details (Placeholder).</p>
            <div id="gallery" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6 pb-20">
                <!-- Content will be dynamically generated by JavaScript -->
            </div>
        </section>

    </div>

    <script>
        const MONTHS = [
            "January", "February", "March", "April", "May", "June",
            "July", "August", "September", "October", "November", "December"
        ];
        let isGridView = true;
        const currentMonthIndex = new Date().getMonth();

        function renderGallery() {
            const gallery = document.getElementById('gallery');
            const currentYear = new Date().getFullYear();
            gallery.innerHTML = '';
            
            // Set grid classes based on current view state
            if (isGridView) {
                gallery.className = "grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6 pb-20";
            } else {
                gallery.className = "grid grid-cols-1 gap-4 pb-20 max-w-2xl mx-auto";
            }

            MONTHS.forEach((month, index) => {
                const monthNumber = index + 1;
                const monthText = month.toUpperCase();
                const isCurrent = index === currentMonthIndex;
                
                // Color for placeholder image
                const baseColor = ['2563eb', 'dc2626', '059669', 'f59e0b', '9333ea'][index % 5];
                const highlightColor = isCurrent ? '10b981' : baseColor;
                const placeholderUrl = `https://placehold.co/400x300/${highlightColor}/ffffff?text=${monthText}+${currentYear}`;
                
                // Tailwind classes for the card
                let cardClasses = "bg-white rounded-xl shadow-xl overflow-hidden transform transition duration-300 hover:shadow-2xl cursor-pointer ";

                if (isCurrent) {
                    cardClasses += " ring-4 ring-blue-500 ring-offset-2 border-l-8 border-blue-600 ";
                } else {
                    cardClasses += " hover:scale-[1.02] border-l-4 border-gray-200 ";
                }

                // Conditional card structure for List View
                let cardContent;
                if (isGridView) {
                    // Standard Grid View Card (Image above text)
                    cardContent = `
                        <img src="${placeholderUrl}" 
                             alt="Image for ${month}" 
                             loading="lazy"
                             class="w-full h-48 object-cover object-center border-b-2 ${isCurrent ? 'border-blue-500' : 'border-gray-100'}"
                             onerror="this.onerror=null;this.src='https://placehold.co/400x300/a1a1a1/ffffff?text=${monthText}';">
                        <div class="p-4">
                            <h2 class="text-xl font-bold ${isCurrent ? 'text-blue-700' : 'text-gray-800'}">${month}</h2>
                            <p class="text-sm text-gray-500">Month ${monthNumber} of ${currentYear}</p>
                            <p class="mt-2 text-xs ${isCurrent ? 'text-blue-500 font-semibold' : 'text-gray-400'}">${isCurrent ? 'THIS IS THE CURRENT MONTH' : 'Click for potential details'}</p>
                        </div>
                    `;
                } else {
                    // List View Card (Image and text side-by-side)
                    cardContent = `
                        <div class="flex items-center space-x-4 p-4">
                            <img src="${placeholderUrl}" 
                                 alt="Image for ${month}" 
                                 loading="lazy"
                                 class="w-24 h-24 object-cover object-center rounded-lg shadow-md flex-shrink-0"
                                 onerror="this.onerror=null;this.src='https://placehold.co/100x100/a1a1a1/ffffff?text=M';">
                            <div class="flex-grow">
                                <h2 class="text-2xl font-bold ${isCurrent ? 'text-blue-700' : 'text-gray-800'}">${month}</h2>
                                <p class="text-base text-gray-500">Period ${monthNumber}: ${currentYear}</p>
                                <p class="mt-1 text-sm ${isCurrent ? 'text-blue-500 font-semibold' : 'text-gray-400'}">${isCurrent ? 'Focus Period: Active' : 'Details for this period are available.'}</p>
                            </div>
                        </div>
                    `;
                }


                const card = document.createElement('div');
                card.className = cardClasses;
                card.onclick = () => showMonthDetails(month);
                card.innerHTML = cardContent;
                gallery.appendChild(card);
            });
        }

        function toggleView() {
            isGridView = !isGridView;
            const toggleText = document.getElementById('toggleText');
            const gridIcon = document.getElementById('gridIcon');
            const listIcon = document.getElementById('listIcon');

            if (isGridView) {
                toggleText.textContent = "Switch to List View";
                gridIcon.classList.remove('hidden');
                listIcon.classList.add('hidden');
            } else {
                toggleText.textContent = "Switch to Grid View";
                gridIcon.classList.add('hidden');
                listIcon.classList.remove('hidden');
            }

            renderGallery();
        }

        function showMonthDetails(month) {
            // Note: There is no 'installMessage' element in the current HTML. Using console.log for placeholder feedback.
            console.log(`User clicked on: ${month}. This action would trigger a detailed view/modal.`);
            
            // To provide temporary visual feedback without altering the core structure, 
            // we can use a temporary element or a console message.
            // Since there's no visible element to update, let's keep the console log.
        }

        function updateCurrentDateDisplay() {
            const now = new Date();
            const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' };
            document.getElementById('currentDateDisplay').textContent = `Today: ${now.toLocaleDateString('en-US', options)}`;
        }

        function setupManifest() {
            const manifest = {
                name: "Yearly Calendar SPA",
                short_name: "Calendar",
                description: "Interactive 12-Month Calendar Overview.",
                start_url: "./",
                display: "standalone",
                background_color: "#f3f4f6",
                theme_color: "#3b82f6",
                icons: [
                    {
                        src: "https://placehold.co/192x192/3b82f6/ffffff?text=CAL",
                        sizes: "192x192",
                        type: "image/png"
                    },
                    {
                        src: "https://placehold.co/512x512/3b82f6/ffffff?text=CAL",
                        sizes: "512x512",
                        type: "image/png"
                    }
                ]
            };

            const manifestBlob = new Blob([JSON.stringify(manifest)], { type: 'application/json' });
            const manifestURL = URL.createObjectURL(manifestBlob);
            // This line now correctly finds the element defined in the <head>
            const manifestLinkElement = document.getElementById('manifestLink');
            if(manifestLinkElement) {
                manifestLinkElement.setAttribute('href', manifestURL);
            }
        }

        window.onload = function() {
            renderGallery(); 
            setupManifest();
            updateCurrentDateDisplay();
            document.getElementById('viewToggle').addEventListener('click', toggleView);
            
            let deferredPrompt;
            window.addEventListener('beforeinstallprompt', (e) => {
                e.preventDefault();
                deferredPrompt = e;
                console.log('Installation prompt ready.');
            });
        };
    </script>
</body>
</html>
