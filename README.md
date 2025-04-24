<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Ask AI (Google-powered)</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f7f9fb;
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 50px 20px;
    }

    h1 {
      color: #333;
      margin-bottom: 20px;
    }

    input {
      width: 100%;
      max-width: 500px;
      padding: 12px;
      font-size: 16px;
      border-radius: 8px;
      border: 1px solid #ccc;
    }

    button {
      margin-top: 12px;
      padding: 10px 24px;
      font-size: 16px;
      background-color: #007bff;
      color: white;
      border: none;
      border-radius: 8px;
      cursor: pointer;
    }

    button:hover {
      background-color: #0056b3;
    }

    #answer {
      margin-top: 30px;
      background: #fff;
      padding: 20px;
      border-radius: 10px;
      width: 100%;
      max-width: 600px;
      box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
      white-space: pre-wrap;
    }
  </style>
</head>
<body>

  <h1>  GPI (Google-powered) </h1>
  <input id="questionInput" placeholder="Type your question..." />
  <button onclick="askQuestion()">Ask</button>
  <div id="answer"></div>

  <script>
    async function askQuestion() {
      const query = document.getElementById("questionInput").value.trim();
      const answerDiv = document.getElementById("answer");
      const apiKey = "YOUR_SERPAPI_KEY"; // ← Replace this with your SerpAPI key

      if (!query) {
        answerDiv.textContent = "Please enter a question.";
        return;
      }

      answerDiv.textContent = "Searching Google...";

      const proxyUrl = "https://corsproxy.io/?";
      const searchUrl = `https://serpapi.com/search.json?q=${encodeURIComponent(query)}&api_key=${apiKey}`;

      try {
        const response = await fetch(proxyUrl + searchUrl);
        const data = await response.json();
        console.log("Returned Data:", data); // for debugging

        let answerText = "";

        if (data.organic_results && data.organic_results.length > 0) {
          data.organic_results.slice(0, 3).forEach((result, i) => {
            const title = result.title || "No title";
            const snippet = result.snippet || "No description available.";
            answerText += `🔹 ${title}\n${snippet}\n\n`;
          });
        } else {
          answerText = "its on testing!!!"
        }

        answerDiv.textContent = answerText;
      } catch (error) {
        console.error("Error:", error);
        answerDiv.textContent = "❗ Error fetching results. Please check your internet or API key.";
      }
    }
  </script>
</body>
</html>

