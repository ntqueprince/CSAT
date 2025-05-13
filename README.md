
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CSAT Performance Tracker</title>
    <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@fortawesome/fontawesome-free@6.4.0/css/all.min.css">
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #f8f9fa;
        }
        .card {
            border-radius: 15px;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
            background-color: white;
        }
        .input-good {
            background-color: #e6ffe6;
        }
        .input-bad {
            background-color: #ffe6e6;
        }
        .results-box {
            background-color: #e6ffe6;
            border-radius: 10px;
        }
        .target-achieved {
            color: #28a745;
            font-weight: bold;
        }
        select, input {
            border-radius: 8px;
            padding: 12px 15px;
            border: 1px solid #ced4da;
            width: 100%;
            font-size: 16px;
            transition: all 0.3s;
        }
        select:focus, input:focus {
            outline: none;
            box-shadow: 0 0 0 3px rgba(66, 153, 225, 0.5);
        }
        .header-icon {
            background-color: #4a7aff;
            color: white;
            width: 50px;
            height: 50px;
            border-radius: 12px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 24px;
        }
        .footer {
            background-color: #f5f5f5;
            padding: 15px;
            border-radius: 0 0 15px 15px;
            text-align: center;
            margin-top: 20px;
        }
    </style>
</head>
<body class="min-h-screen p-4 flex flex-col items-center justify-center">
    <div class="card max-w-md w-full px-6 py-8">
        <div class="flex items-center gap-4 mb-6">
            <div class="header-icon">
                <i class="fas fa-chart-line"></i>
            </div>
            <h1 class="text-2xl font-bold">Tracker</h1>
        </div>

        <div class="mb-5">
            <label for="goodCount" class="block text-gray-700 font-medium mb-2">Good Count</label>
            <div class="relative">
                <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
                    <i class="fas fa-thumbs-up text-gray-500"></i>
                </div>
                <input type="number" id="goodCount" class="pl-10 input-good" value="110" min="0">
            </div>
        </div>

        <div class="mb-5">
            <label for="badCount" class="block text-gray-700 font-medium mb-2">Bad Count</label>
            <div class="relative">
                <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
                    <i class="fas fa-thumbs-down text-gray-500"></i>
                </div>
                <input type="number" id="badCount" class="pl-10 input-bad" value="15" min="0">
            </div>
        </div>

        <div class="mb-6">
            <label for="targetCSAT" class="block text-gray-700 font-medium mb-2">Target CSAT %</label>
            <div class="relative">
                <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
                    <i class="fas fa-bullseye text-gray-500"></i>
                </div>
                <select id="targetCSAT" class="pl-10 border border-blue-300">
                    <!-- Options will be added via JavaScript -->
                </select>
            </div>
        </div>

        <div class="results-box p-5 mb-5">
            <h2 class="text-xl font-bold mb-4">Performance Results</h2>
            
            <div class="flex justify-between mb-3">
                <span class="text-gray-700">Current CSAT %:</span>
                <span id="currentCSAT" class="font-semibold">88.0%</span>
            </div>
            
            <div class="flex justify-between">
                <span class="text-gray-700">Required Good Responses:</span>
                <span id="requiredResponses" class="font-semibold">0</span>
            </div>
            
            <div id="targetAchievedMsg" class="target-achieved text-center mt-4 hidden">
                Target Achieved!
            </div>
        </div>
    </div>
    
    <div class="max-w-md w-full mt-2">
        <div class="bg-gray-800 text-white text-center py-2 rounded-md text-sm">
            <i class="far fa-image"></i> Made with Genspark
        </div>
        
        <div class="footer">
            <h3 class="text-lg font-bold mb-2">How It Works</h3>
            <p class="text-gray-600 text-sm">pcbdqunj.gensparkspace.com</p>
        </div>
    </div>

    <script>
        // Populate dropdown with .1 increments
        const targetCSATSelect = document.getElementById('targetCSAT');
        for (let i = 70.1; i <= 100.1; i += 1) {
            const option = document.createElement('option');
            option.value = i.toFixed(1);
            option.textContent = i.toFixed(1) + '%';
            if (i.toFixed(1) === '95.0') {
                option.selected = true;
            }
            targetCSATSelect.appendChild(option);
        }

        // Input fields
        const goodCountInput = document.getElementById('goodCount');
        const badCountInput = document.getElementById('badCount');

        // Output elements
        const currentCSATDisplay = document.getElementById('currentCSAT');
        const requiredResponsesDisplay = document.getElementById('requiredResponses');
        const targetAchievedMsg = document.getElementById('targetAchievedMsg');

        // Calculate CSAT and required responses
        function calculateCSAT() {
            const goodCount = parseInt(goodCountInput.value) || 0;
            const badCount = parseInt(badCountInput.value) || 0;
            const targetCSAT = parseFloat(targetCSATSelect.value);
            
            // Calculate current CSAT
            let currentCSAT = 0;
            if (goodCount + badCount > 0) {
                currentCSAT = (goodCount / (goodCount + badCount)) * 100;
            }
            
            // Update current CSAT display
            currentCSATDisplay.textContent = currentCSAT.toFixed(1) + '%';
            
            // Calculate required good responses
            let requiredResponses = 0;
            
            if (currentCSAT < targetCSAT && badCount > 0) {
                // Formula: good_needed = (target_csat * bad_count) / (100 - target_csat)
                requiredResponses = Math.ceil((targetCSAT * badCount) / (100 - targetCSAT)) - goodCount;
                if (requiredResponses < 0) requiredResponses = 0;
            }
            
            // Update required responses display
            requiredResponsesDisplay.textContent = requiredResponses;
            
            // Show "Target Achieved!" message if applicable
            if (currentCSAT >= targetCSAT && (goodCount > 0 || badCount > 0)) {
                targetAchievedMsg.classList.remove('hidden');
            } else {
                targetAchievedMsg.classList.add('hidden');
            }
        }

        // Add event listeners
        goodCountInput.addEventListener('input', calculateCSAT);
        badCountInput.addEventListener('input', calculateCSAT);
        targetCSATSelect.addEventListener('change', calculateCSAT);

        // Initialize calculations
        calculateCSAT();
    </script>
</body>
</html>
