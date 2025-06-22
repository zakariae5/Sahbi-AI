<!DOCTYPE html>
<html lang="ar">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1" />
  <title>Sahbi AI Chat (Mobile)</title>
  <style>
    /* Reset and base */
    * {
      box-sizing: border-box;
    }
    body {
      margin: 0;
      font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
      background: #f8f9fa;
      display: flex;
      flex-direction: column;
      height: 100vh;
      color: #222;
    }
    header {
      background-color: #673ab7;
      color: white;
      text-align: center;
      padding: 16px 12px;
      font-size: 1.4rem;
      font-weight: bold;
      flex-shrink: 0;
      user-select: none;
    }
    .controls {
      display: flex;
      gap: 12px;
      padding: 10px 12px;
      background-color: #ede7f6;
      flex-shrink: 0;
    }
    select {
      flex: 1;
      padding: 10px;
      font-size: 1rem;
      border-radius: 8px;
      border: 1px solid #b39ddb;
      background: white;
      -webkit-appearance: none;
      -moz-appearance: none;
      appearance: none;
      cursor: pointer;
    }
    #chatbox {
      flex-grow: 1;
      background: #fff;
      margin: 10px 12px;
      border-radius: 12px;
      box-shadow: 0 2px 8px rgba(0,0,0,0.1);
      overflow-y: auto;
      padding: 16px 12px;
      display: flex;
      flex-direction: column;
      gap: 10px;
    }
    .message {
      max-width: 75%;
      padding: 12px 16px;
      border-radius: 16px;
      font-size: 1rem;
      line-height: 1.3;
      word-break: break-word;
      user-select: text;
      box-shadow: 0 1px 4px rgba(0,0,0,0.1);
    }
    .message.user {
      background-color: #d1c4e9;
      align-self: flex-end;
      border-bottom-right-radius: 0;
    }
    .message.ai {
      background-color: #c8e6c9;
      align-self: flex-start;
      border-bottom-left-radius: 0;
    }
    form.input-area {
      display: flex;
      padding: 10px 12px;
      background-color: #ede7f6;
      flex-shrink: 0;
      gap: 10px;
      align-items: center;
    }
    form input[type="text"] {
      flex: 1;
      font-size: 1rem;
      padding: 12px 14px;
      border-radius: 8px;
      border: 1px solid #b39ddb;
      outline: none;
    }
    form input[type="text"]:focus {
      border-color: #673ab7;
      box-shadow: 0 0 5px #673ab7;
    }
    form button {
      background-color: #673ab7;
      border: none;
      color: white;
      font-weight: bold;
      padding: 12px 18px;
      border-radius: 8px;
      font-size: 1rem;
      cursor: pointer;
      user-select: none;
      transition: background-color 0.3s ease;
    }
    form button:hover {
      background-color: #512da8;
    }
    /* Scrollbar for mobile */
    #chatbox::-webkit-scrollbar {
      width: 6px;
    }
    #chatbox::-webkit-scrollbar-thumb {
      background-color: #673ab7;
      border-radius: 3px;
    }
  </style>
</head>
<body>

<header>🗨️ Sahbi AI Chat (Mobile)</header>

<div class="controls" role="region" aria-label="اختيار الجنس والمزاج">
  <select id="gender" aria-label="اختيار الجنس">
    <option value="ولد">ولد</option>
    <option value="بنت">بنت</option>
  </select>
  <select id="mood" aria-label="اختيار المزاج">
    <option value="صديق">صديق</option>
    <option value="رومانسي">رومانسي</option>
    <option value="غيور">غيور</option>
  </select>
</div>

<div id="chatbox" role="log" aria-live="polite" aria-relevant="additions"></div>

<form id="chat-form" class="input-area" autocomplete="off" aria-label="إرسال رسالة">
  <input
    type="text"
    id="userInput"
    placeholder="كتب شي حاجة..."
    aria-label="رسالتك"
    required
    autofocus
  />
  <button type="submit" aria-label="إرسال">إرسال</button>
</form>

<script>
  const chatbox = document.getElementById("chatbox");
  const form = document.getElementById("chat-form");
  const input = document.getElementById("userInput");
  const genderSelect = document.getElementById("gender");
  const moodSelect = document.getElementById("mood");

  function getAIResponse() {
    const gender = genderSelect.value;
    const mood = moodSelect.value;

    if (mood === "صديق") {
      return "😊 صاحبي العزيز، زوين هادشي لي كتبتي! شنو جديدك؟";
    } else if (mood === "رومانسي") {
      if (gender === "بنت") {
        return "❤️ حبيبي، توحشتك بزاف... فين غبرتي عليا؟";
      } else {
        return "❤️ حبيبتي، كنبغيك من قلبي... غير متغبرش عليا!";
      }
    } else if (mood === "غيور") {
      return "😠 فين كنت؟ معامن كنت كتكلّم؟ أنا غير بغيت نعرف صافي!";
    } else {
      return "🤖 مافهمتش، عاود من فضلك...";
    }
  }

  function appendMessage(sender, text) {
    const msgDiv = document.createElement("div");
    msgDiv.classList.add("message", sender);
    msgDiv.textContent = text;
    chatbox.appendChild(msgDiv);
    chatbox.scrollTop = chatbox.scrollHeight;
  }

  function speak(text) {
    if (!("speechSynthesis" in window)) return;

    const utterance = new SpeechSynthesisUtterance(text);
    utterance.lang = "ar-MA"; // دارجة مغربية
    window.speechSynthesis.cancel();
    window.speechSynthesis.speak(utterance);
  }

  form.addEventListener("submit", (e) => {
    e.preventDefault();
    const userText = input.value.trim();
    if (!userText) return;

    appendMessage("user", `👤: ${userText}`);

    const aiResponse = getAIResponse();

    setTimeout(() => {
      appendMessage("ai", `🤖: ${aiResponse}`);
      speak(aiResponse);
    }, 700);

    input.value = "";
    input.focus();
  });
</script>

</body>
</html>
