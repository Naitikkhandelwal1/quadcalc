<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Quadratic Equation Solver</title>
    <!-- Use Tailwind CSS for a modern, responsive design -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Custom font for a clean look */
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap');
        body {
            font-family: 'Inter', sans-serif;
        }
        /* Style for the symbolic part of the result */
        .symbolic-fraction {
            font-family: monospace;
            display: block;
            margin-top: 5px;
            font-size: 0.9em;
            color: #555;
        }
    </style>
</head>
<body class="bg-gray-100 flex items-center justify-center min-h-screen p-4">
    <!-- Main container for the calculator with a clean, rounded design -->
    <div class="bg-white p-8 md:p-12 rounded-2xl shadow-xl w-full max-w-xl text-center">
        <h1 class="text-3xl font-bold text-gray-800 mb-2">Quadratic Equation Solver</h1>
        <p class="text-gray-600 mb-6">
            Enter the coefficients (a, b, and c) of the equation in the form of <span class="font-mono text-sm bg-gray-200 px-1 rounded">ax² + bx + c = 0</span>.
        </p>
        
        <!-- Input fields for a, b, and c -->
        <div class="flex flex-col gap-4">
            <div class="flex items-center justify-between">
                <label for="a" class="text-lg font-medium text-gray-700 w-12">a:</label>
                <input type="number" id="a" placeholder="e.g., 1" class="flex-1 p-3 border border-gray-300 rounded-lg text-lg focus:ring-2 focus:ring-blue-500 focus:outline-none transition-colors">
            </div>
            <div class="flex items-center justify-between">
                <label for="b" class="text-lg font-medium text-gray-700 w-12">b:</label>
                <input type="number" id="b" placeholder="e.g., -3" class="flex-1 p-3 border border-gray-300 rounded-lg text-lg focus:ring-2 focus:ring-blue-500 focus:outline-none transition-colors">
            </div>
            <div class="flex items-center justify-between">
                <label for="c" class="text-lg font-medium text-gray-700 w-12">c:</label>
                <input type="number" id="c" placeholder="e.g., 2" class="flex-1 p-3 border border-gray-300 rounded-lg text-lg focus:ring-2 focus:ring-blue-500 focus:outline-none transition-colors">
            </div>
        </div>
        
        <!-- Calculate button with hover effect -->
        <button id="calculate-btn" class="mt-8 w-full p-3 font-semibold text-white bg-blue-600 rounded-lg shadow-md hover:bg-blue-700 transition-colors">
            Calculate Roots
        </button>
        
        <!-- Result display area -->
        <div class="mt-8 pt-6 border-t border-gray-200">
            <h2 class="text-xl font-semibold text-gray-800 mb-2">Result</h2>
            <p id="result-text" class="text-lg font-medium text-gray-700 whitespace-pre-wrap"></p>
        </div>
    </div>

    <script>
        // --- Helper functions for fraction simplification ---

        // Function to find the greatest common divisor (GCD) using the Euclidean algorithm
        function findGCD(a, b) {
            return b === 0 ? a : findGCD(b, a % b);
        }

        // Function to simplify a fraction given a numerator and denominator
        function simplifyFraction(numerator, denominator) {
            // Handle edge cases
            if (denominator === 0) return { num: numerator, den: 0 };
            if (numerator === 0) return { num: 0, den: 1 };

            // Find the GCD to simplify the fraction
            const commonDivisor = findGCD(Math.abs(numerator), Math.abs(denominator));

            // Simplify and ensure the denominator is positive
            const num = numerator / commonDivisor;
            const den = denominator / commonDivisor;
            
            // Adjust signs so the denominator is always positive
            return den < 0 ? { num: -num, den: -den } : { num, den };
        }

        // --- Main script logic ---
        
        // Wait for the entire page to load before running the script
        document.addEventListener('DOMContentLoaded', () => {
            // Get references to the input and output elements
            const aInput = document.getElementById('a');
            const bInput = document.getElementById('b');
            const cInput = document.getElementById('c');
            const calculateBtn = document.getElementById('calculate-btn');
            const resultText = document.getElementById('result-text');

            // Add a click event listener to the button
            calculateBtn.addEventListener('click', () => {
                // Get values from input fields and convert them to floating-point numbers
                const a = parseFloat(aInput.value);
                const b = parseFloat(bInput.value);
                const c = parseFloat(cInput.value);

                // Basic validation: check if inputs are valid numbers
                if (isNaN(a) || isNaN(b) || isNaN(c)) {
                    resultText.textContent = "Please enter valid numbers for all coefficients.";
                    resultText.classList.remove('text-green-600', 'text-yellow-600');
                    resultText.classList.add('text-red-600');
                    return;
                }

                // Handle the case where 'a' is zero (not a quadratic equation)
                if (a === 0) {
                    resultText.textContent = "Coefficient 'a' cannot be zero for a quadratic equation.";
                    resultText.classList.remove('text-green-600', 'text-yellow-600');
                    resultText.classList.add('text-red-600');
                    return;
                }

                // Calculate the discriminant (delta)
                const discriminant = b * b - 4 * a * c;
                
                let result = '';
                // Clear previous text and styling
                resultText.textContent = "";
                resultText.classList.remove('text-red-600', 'text-yellow-600');

                // Check the discriminant to determine the nature of the roots
                if (discriminant > 0) {
                    // Case 1: Discriminant is positive
                    const sqrtD = Math.sqrt(discriminant);
                    if (sqrtD % 1 === 0) { 
                        // Perfect square, rational roots
                        const root1_num = -b + sqrtD;
                        const root1_den = 2 * a;
                        const root2_num = -b - sqrtD;
                        const root2_den = 2 * a;
                        
                        const simplifiedRoot1 = simplifyFraction(root1_num, root1_den);
                        const simplifiedRoot2 = simplifyFraction(root2_num, root2_den);

                        result = `Two distinct real roots:\nRoot 1 = ${simplifiedRoot1.num}/${simplifiedRoot1.den}\nRoot 2 = ${simplifiedRoot2.num}/${simplifiedRoot2.den}`;
                        resultText.classList.add('text-green-600');

                    } else { 
                        // Not a perfect square, irrational roots
                        const root1 = (-b + sqrtD) / (2 * a);
                        const root2 = (-b - sqrtD) / (2 * a);
                        result = `Two distinct irrational roots:\nRoot 1 ≈ ${root1.toFixed(4)}\nRoot 2 ≈ ${root2.toFixed(4)}\n\nSymbolic Form:\nRoot 1 = (-${b} + √${discriminant})/${2*a}\nRoot 2 = (-${b} - √${discriminant})/${2*a}`;
                        resultText.classList.add('text-yellow-600');
                    }
                } else if (discriminant === 0) {
                    // Case 2: Discriminant is zero (one real, repeated root)
                    const root_num = -b;
                    const root_den = 2 * a;
                    const simplifiedRoot = simplifyFraction(root_num, root_den);
                    result = `One real root (repeated):\nRoot = ${simplifiedRoot.num}/${simplifiedRoot.den}`;
                    resultText.classList.add('text-green-600');
                } else {
                    // Case 3: Discriminant is negative (two complex roots)
                    const realPartNum = -b;
                    const realPartDen = 2 * a;
                    const imagPartNum = Math.sqrt(-discriminant);
                    const imagPartDen = 2 * a;
                    
                    const simplifiedReal = simplifyFraction(realPartNum, realPartDen);

                    const root1 = `${simplifiedReal.num}/${simplifiedReal.den} + ${imagPartNum.toFixed(4)}/${imagPartDen}i`;
                    const root2 = `${simplifiedReal.num}/${simplifiedReal.den} - ${imagPartNum.toFixed(4)}/${imagPartDen}i`;
                    
                    result = `Two complex roots:\nRoot 1 = ${root1}\nRoot 2 = ${root2}\n\nSymbolic Form:\nRoot 1 = (-${b}/${2*a}) + (√${-discriminant}/${2*a})i\nRoot 2 = (-${b}/${2*a}) - (√${-discriminant}/${2*a})i`;
                    resultText.classList.add('text-yellow-600');
                }

                // Display the final result
                resultText.textContent = result;
            });
        });
    </script>
</body>
</html>
