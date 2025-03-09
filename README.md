<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>IELTS Essay Generator</title>

  <!-- Google Font -->
  <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap">

  <!-- Tesseract.js (OCR) -->
  <script src="https://cdn.jsdelivr.net/npm/tesseract.js@4.0.3/dist/tesseract.min.js"></script>

  <!-- JSZip & FileSaver (DOCX export) -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jszip/3.10.1/jszip.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/FileSaver.js/2.0.5/FileSaver.min.js"></script>

  <style>
    :root {
      --bg-gradient-start: #f0f4f8;
      --bg-gradient-end: #a3bffa;
      --container-bg: rgba(255, 255, 255, 0.95);
      --text-primary: #2c3e50;
      --text-secondary: #34495e;
      --card-bg: white;
      --accent-color: #27ae60;
      --button-bg: #3498db;
      --button-hover: #2980b9;
      --shadow-color: rgba(0, 0, 0, 0.05);
      --border-color: #dfe6e9;
    }
    [data-theme="dark"] {
      --bg-gradient-start: #1a1a2e;
      --bg-gradient-end: #16213e;
      --container-bg: rgba(26, 26, 46, 0.95);
      --text-primary: #e0e0e0;
      --text-secondary: #b0b0c0;
      --card-bg: #2a2a44;
      --accent-color: #2ecc71;
      --button-bg: #3498db;
      --button-hover: #2980b9;
      --shadow-color: rgba(0, 0, 0, 0.1);
      --border-color: #44466b;
    }
    body {
      font-family: 'Poppins', sans-serif;
      margin: 0;
      padding: 0;
      background: linear-gradient(135deg, var(--bg-gradient-start), var(--bg-gradient-end));
      min-height: 100vh;
      display: flex;
      justify-content: center;
      align-items: flex-start;
      padding-top: 40px;
      transition: background 0.3s;
      text-align: center;
    }
    .hero {
      padding: 30px;
      max-width: 900px;
      color: var(--text-primary);
    }
    .hero h1 {
      font-size: 48px;
      font-weight: 600;
      margin: 0 0 25px;
      text-shadow: 1px 1px 4px var(--shadow-color);
    }
    .hero p {
      font-size: 20px;
      margin: 0 0 20px;
      color: var(--text-secondary);
    }
    .cta-button {
      background: linear-gradient(90deg, #ff6f61, #ff8a65);
      color: white;
      padding: 12px 35px;
      border: none;
      border-radius: 25px;
      font-size: 20px;
      cursor: pointer;
      transition: transform 0.3s ease, background 0.3s ease;
      box-shadow: 0 2px 6px var(--shadow-color);
    }
    .cta-button:hover {
      transform: scale(1.05);
      background: linear-gradient(90deg, #ff5e50, #ff7a55);
    }
    .container {
      display: none;
      flex-direction: column;
      align-items: center;
      padding: 25px;
      width: 90%;
      max-width: 1000px;
      background: var(--container-bg);
      border-radius: 12px;
      box-shadow: 0 4px 12px var(--shadow-color);
      margin-top: 40px;
      animation: fadeIn 0.5s ease forwards;
      transition: background 0.3s;
    }
    .main-section {
      padding: 20px;
      margin-bottom: 25px;
    }
    /* Essay Type Wrapper */
    .essay-type-wrapper {
      display: inline-flex;
      align-items: center;
      background: var(--card-bg);
      border: 1px solid var(--border-color);
      border-radius: 10px;
      box-shadow: 0 2px 4px var(--shadow-color);
      padding: 10px 20px;
      margin: 10px 0;
    }
    .essay-type-wrapper span {
      font-size: 18px;
      font-weight: 500;
      color: var(--text-primary);
      margin-right: 15px;
    }
    .change-type-btn {
      background: var(--button-bg);
      color: #fff;
      border: none;
      padding: 5px 12px;
      border-radius: 6px;
      cursor: pointer;
      font-size: 14px;
      transition: background 0.3s;
    }
    .change-type-btn:hover {
      background: var(--button-hover);
    }
    /* Essay Topic Wrapper */
    .essay-topic-wrapper {
      display: inline-flex;
      align-items: center;
      background: var(--card-bg);
      border: 1px solid var(--border-color);
      border-radius: 10px;
      box-shadow: 0 2px 4px var(--shadow-color);
      padding: 10px 20px;
      margin: 10px 0;
      max-width: 80%;
      word-wrap: break-word;
    }
    .essay-topic-wrapper span {
      font-size: 18px;
      font-weight: 500;
      color: var(--text-secondary);
      margin-right: 15px;
    }
    .change-topic-btn, .custom-topic-btn {
      background: var(--button-bg);
      color: #fff;
      border: none;
      padding: 5px 12px;
      border-radius: 6px;
      cursor: pointer;
      font-size: 14px;
      transition: background 0.3s;
      margin-right: 5px;
    }
    .change-topic-btn:hover, .custom-topic-btn:hover {
      background: var(--button-hover);
    }
    .custom-assignment-input {
      display: none;
      margin-top: 15px;
      text-align: center;
    }
    .custom-assignment-input input {
      padding: 10px;
      width: 70%;
      font-size: 16px;
      border: 1px solid var(--border-color);
      border-radius: 6px;
      box-shadow: inset 0 2px 3px var(--shadow-color);
      background: var(--card-bg);
      color: var(--text-primary);
    }
    .assignment-upload {
      display: none;
      margin-top: 15px;
      text-align: center;
    }
    .assignment-upload input[type="file"] {
      font-size: 16px;
    }
    .content-wrapper {
      display: flex;
      flex-direction: column;
      width: 100%;
      gap: 20px;
    }
    .top-section, .bottom-section {
      display: flex;
      justify-content: space-between;
      gap: 20px;
      width: 100%;
    }
    .suggestion-card, .writing-area, .sticky-note, .sticky-note-vocab {
      background: var(--card-bg);
      border-radius: 12px;
      padding: 20px;
      box-shadow: 0 3px 8px var(--shadow-color);
    }
    .suggestion-card {
      width: 48%;
      position: relative;
    }
    .suggestion-card h3 {
      font-size: 20px;
      font-weight: 600;
      color: var(--accent-color);
      margin: 0 0 15px;
    }
    .suggestion-card p {
      font-size: 16px;
      color: var(--text-secondary);
      line-height: 1.6;
      margin: 0;
    }
    .close-button {
      position: absolute;
      top: 10px;
      right: 10px;
      background: #e74c3c;
      color: white;
      width: 25px;
      height: 25px;
      border: none;
      border-radius: 50%;
      font-size: 14px;
      font-weight: 600;
      cursor: pointer;
      display: flex;
      align-items: center;
      justify-content: center;
      transition: background 0.3s;
    }
    .close-button:hover {
      background: #c0392b;
    }
    .writing-area {
      width: 48%;
      position: relative;
    }
    .writing-area textarea {
      width: 100%;
      height: 200px;
      background: repeating-linear-gradient(var(--card-bg), var(--card-bg) 28px, #f5f7fa 29px);
      border: 1px solid var(--border-color);
      border-radius: 10px;
      font-size: 16px;
      line-height: 28px;
      resize: vertical;
      padding: 12px;
      box-sizing: border-box;
      font-family: 'Poppins', sans-serif;
      outline: none;
      color: var(--text-primary);
    }
    .word-count {
      font-size: 14px;
      color: var(--text-secondary);
      text-align: right;
      margin: 8px 0;
    }
    .export-button {
      background: var(--button-bg);
      color: white;
      padding: 10px 20px;
      border: none;
      border-radius: 6px;
      cursor: pointer;
      margin-top: 10px;
      display: block;
      margin-left: auto;
      transition: background 0.3s;
      font-size: 16px;
    }
    .export-button:hover {
      background: var(--button-hover);
    }
    .sticky-note, .sticky-note-vocab {
      width: 48%;
      position: relative;
    }
    .sticky-note .timer {
      font-size: 18px;
      font-weight: 600;
      color: #e67e22;
      margin-bottom: 10px;
    }
    .sticky-note .start-button, .sticky-note .stop-button, .sticky-note .set-timer-button {
      background: #e67e22;
      color: white;
      border: none;
      padding: 8px 12px;
      border-radius: 6px;
      cursor: pointer;
      font-size: 14px;
      margin-left: 8px;
      transition: background 0.3s;
    }
    .sticky-note .start-button:hover, .sticky-note .stop-button:hover, .sticky-note .set-timer-button:hover {
      background: #d35400;
    }
    .sticky-note p, .sticky-note-vocab p {
      font-size: 15px;
      color: var(--text-secondary);
      line-height: 1.6;
      margin: 8px 0;
    }
    .sticky-note-vocab h3 {
      font-size: 20px;
      font-weight: 600;
      color: var(--accent-color);
      margin: 0 0 15px;
    }
    .timer-settings {
      display: none;
      margin-top: 10px;
    }
    .timer-settings input {
      padding: 8px;
      font-size: 14px;
      border: 1px solid var(--border-color);
      border-radius: 6px;
      background: var(--card-bg);
      color: var(--text-primary);
    }
    .dot, .dot-vocab, .dot-structure {
      display: none;
      position: fixed;
      width: 40px;
      height: 40px;
      background: var(--card-bg);
      border-radius: 50%;
      cursor: pointer;
      z-index: 10;
      text-align: center;
      line-height: 40px;
      font-size: 22px;
      box-shadow: 0 2px 4px var(--shadow-color);
      transition: transform 0.3s;
    }
    .dot:hover, .dot-vocab:hover, .dot-structure:hover {
      transform: scale(1.1);
    }
    .dot::before { content: "⏰"; }
    .dot-vocab::before { content: "📖"; }
    .dot-structure::before { content: "📋"; }
    .theme-toggle {
      position: fixed;
      top: 20px;
      left: 20px;
      background: var(--button-bg);
      color: white;
      border: none;
      padding: 8px 15px;
      border-radius: 6px;
      cursor: pointer;
      font-size: 16px;
      z-index: 100;
      transition: background 0.3s;
    }
    .theme-toggle:hover {
      background: var(--button-hover);
    }
    @keyframes fadeIn {
      from { opacity: 0; }
      to { opacity: 1; }
    }
    .hidden { display: none; }
  </style>
</head>
<body>
  <!-- HERO SECTION -->
  <div class="hero" id="heroSection">
    <h1>IELTS Essay Generator</h1>
    <p>Select your preferred topic:</p>
    <div id="preferenceForm">
      <label><input type="radio" name="preference" value="Education"> Education</label>
      <label><input type="radio" name="preference" value="Technology"> Technology</label>
      <label><input type="radio" name="preference" value="Environment"> Environment</label>
      <label><input type="radio" name="preference" value="Society"> Society</label>
      <label><input type="radio" name="preference" value="Health"> Health</label>
      <label><input type="radio" name="preference" value="Other" onchange="handleOtherPreference(this)"> Other</label>
      <div id="otherInput" class="custom-assignment-input hidden">
        <input type="text" id="customOtherTopic" placeholder="Enter your custom topic...">
        <button onclick="applyCustomOtherTopic()">Confirm</button>
      </div>
      <button onclick="savePreferences()">Confirm</button>
    </div>
    <button class="cta-button hidden" id="startButton" onclick="showContent()">Get Started</button>
  </div>

  <!-- CONTENT SECTION -->
  <div class="container" id="contentSection">
    <button class="theme-toggle" onclick="toggleTheme()">Toggle Dark Mode</button>

    <div class="main-section">
      <!-- Essay Type Wrapper -->
      <div class="essay-type-wrapper" id="essayTypeWrapper" style="display:none;">
        <span id="essayTypeText"></span>
        <button class="change-type-btn" onclick="toggleEditEssayType()">Change Type</button>
      </div>

      <!-- Essay Topic Wrapper -->
      <div class="essay-topic-wrapper" id="essayTopicWrapper" style="display:none;">
        <span id="essayTopicText"></span>
        <button class="change-topic-btn" id="randomTopicBtn" onclick="changeTopicRandom()">Random Topic</button>
        <button class="custom-topic-btn" onclick="showCustomTopicInput()">Custom Topic</button>
      </div>

      <!-- Custom Topic Input -->
      <div id="customAssignmentInput" class="custom-assignment-input">
        <input type="text" id="customAssignmentValue" placeholder="Enter your topic here...">
        <button onclick="applyCustomAssignment()">Confirm</button>
      </div>

      <!-- Upload Image (OCR) -->
      <div id="assignmentUpload" class="assignment-upload hidden">
        <input type="file" id="uploadImage" accept="image/*" onchange="handleImageUpload(this.files)">
      </div>
    </div>

    <div class="content-wrapper">
      <div class="top-section">
        <div class="suggestion-card" id="structureCard">
          <button class="close-button" onclick="hideStructure()">×</button>
          <h3>Writing Structure</h3>
          <p id="suggestedStructure">Select an essay type to see the structure!</p>
        </div>
        <div class="writing-area" id="writingArea">
          <div class="word-count" id="wordCount">0 words</div>
          <textarea id="essayInput" placeholder="Start writing your essay here..."></textarea>
          <button class="export-button" onclick="exportEssay()">Export Essay</button>
        </div>
      </div>
      <div class="bottom-section" id="bottomSection">
        <div class="sticky-note" id="stickyNote">
          <button class="close-button" onclick="hideNote('stickyNote', 'dot')">×</button>
          <div class="timer" id="timer">00:00</div>
          <p id="timeProverb" style="font-size:14px; color:#7f8c8d; margin:8px 0;">Time waits for no one.</p>
          <button class="start-button" onclick="startTimer()">Start</button>
          <button class="stop-button" onclick="stopTimer()">Stop</button>
          <button class="set-timer-button" onclick="showTimerSettings()">Set Timer</button>
          <div class="timer-settings" id="timerSettings">
            <input type="number" id="timerMinutes" min="1" placeholder="Minutes">
            <button onclick="setTimer()">Set</button>
          </div>
        </div>
        <div class="sticky-note-vocab" id="stickyNoteVocab">
          <button class="close-button" onclick="hideNote('stickyNoteVocab', 'dotVocab')">×</button>
          <h3>Vocabulary Suggestions</h3>
          <p id="suggestions">Suggestions will appear here.</p>
        </div>
      </div>
    </div>
  </div>

  <div class="dot" id="dot" onclick="showNote('stickyNote', 'dot')"></div>
  <div class="dot-vocab" id="dotVocab" onclick="showNote('stickyNoteVocab', 'dotVocab')"></div>
  <div class="dot-structure" id="dotStructure" onclick="showStructure()"></div>

  <script>
    /****************************************************************
     * DATA (Cập nhật theo FULL TEMPLATE TASK 2.pdf)
     ****************************************************************/
    const essayTypes = [
      {
        type: "To What Extent Do You Agree or Disagree",
        topics: [
          { topic: "The government should invest more in public education rather than healthcare. To what extent do you agree or disagree?", theme: "Education" },
          { topic: "Modern technology is reducing the quality of human communication. To what extent do you agree or disagree?", theme: "Technology" }
        ],
        "Template A": `<strong>Template A (Fully Agree or Fully Disagree)</strong><br>
<strong>Introduction</strong><br>
• General Statement/Context: In recent years, there has been considerable debate about (topic).<br>
• State Position: One school of thought holds that (paraphrase the question). From my perspective, I completely agree/disagree with this view.<br>
• Thesis: In this essay, I will elaborate on the reasons supporting my position.<br>
<strong>Body Paragraph 1</strong><br>
• Topic Sentence: The primary justification for my viewpoint is (main reason).<br>
• Explanation/Example: This is because (expand your idea), which can lead to (result).<br>
• Supporting Sentence: Hence, it becomes evident that (reinforce argument).<br>
<strong>Body Paragraph 2</strong><br>
• Topic Sentence: Another compelling argument is (second reason).<br>
• Explanation/Example: (Provide details/example) demonstrates that (explanation).<br>
• Consequence: Therefore, embracing/rejecting (idea) might yield (further explanation).<br>
<strong>Conclusion</strong><br>
• Restate Position: In conclusion, I firmly agree/disagree that (paraphrase the topic) because (key points).<br>
• Final Thought: It is hoped that stakeholders will (suggest action).`,
        "Template B": `<strong>Template B (Partially Agree/Partially Disagree)</strong><br>
<strong>Introduction</strong><br>
• General Statement/Context: There is an ongoing debate concerning (topic).<br>
• Balanced Position: While I acknowledge that (some aspects), I hold that (other aspects) are of equal importance.<br>
• Thesis: The following paragraphs will discuss both perspectives before concluding.<br>
<strong>Body Paragraph 1</strong><br>
• Topic Sentence: On one hand, supporters claim that (view).<br>
• Explanation/Example: Firstly, (reason/example) implies (explanation).<br>
• Linking: This suggests benefits such as (result).<br>
<strong>Body Paragraph 2</strong><br>
• Topic Sentence: On the other hand, I strongly believe that (counter-view).<br>
• Explanation/Example: For instance, (reason/example) indicates (explanation).<br>
<strong>Conclusion</strong><br>
• Summation: In conclusion, although (first view) appears advantageous, I am convinced that (second view) is more crucial due to (summary).<br>
• Final Remark: Looking ahead, (future implication).`
      },
      {
        type: "Discuss Both Views and Give Your Opinion",
        topics: [
          { topic: "Some people believe that children should learn a foreign language from primary school, while others think it should wait until secondary school. Discuss both views and give your opinion.", theme: "Education" },
          { topic: "Some argue that technology improves life, but others say it harms health. Discuss both views and give your opinion.", theme: "Technology" }
        ],
        structure: `<strong>Introduction</strong><br>
• General Statement/Context: It is often argued that (view A). However, others believe that (view B).<br>
• Position: Although both sides present compelling arguments, I tend to side with (view A or view B).<br>
<strong>Body Paragraph 1 (View A)</strong><br>
• Topic Sentence: Proponents of (view A) claim that (main argument).<br>
• Explanation: This is primarily because (detail).<br>
• Example: For example, (example) demonstrates (clarification).<br>
• Transition: Consequently, supporters highlight (reinforcement).<br>
<strong>Body Paragraph 2 (View B)</strong><br>
• Topic Sentence: Conversely, supporters of (view B) argue that (main argument).<br>
• Explanation: This stems from (rationale).<br>
• Example: For instance, (example) indicates (explanation).<br>
• Your Opinion: From my perspective, while (acknowledge positives), it is overshadowed by (reason).<br>
<strong>Conclusion</strong><br>
• Restate Discussion: In conclusion, although both (view A) and (view B) offer benefits/drawbacks, I am convinced that (your preferred view) holds more validity.<br>
• Final Thought: Further measures should be taken to maximize the advantages while minimizing the drawbacks.`
      },
      {
        type: "Advantages and Disadvantages",
        topics: [
          { topic: "Discuss the advantages and disadvantages of the growing trend of online education.", theme: "Education" },
          { topic: "Discuss the advantages and disadvantages of automation in modern industry.", theme: "Technology" }
        ],
        "Template A": `<strong>Template A (With Opinion: Do the Advantages Outweigh the Disadvantages?)</strong><br>
<strong>Introduction</strong><br>
• General Statement/Context: (Topic) has become increasingly common.<br>
• Position: While it has drawbacks, I believe its advantages prevail.<br>
• Thesis: This essay will explore why the benefits overshadow its downsides.<br>
<strong>Body Paragraph 1 (Advantages)</strong><br>
• Topic Sentence: One key advantage of (topic) is (advantage 1).<br>
• Explanation/Example: For example, (example) shows (explanation).<br>
• Additional Advantage: Another benefit is (advantage 2), leading to (result).<br>
<strong>Body Paragraph 2 (Disadvantages)</strong><br>
• Topic Sentence: However, there are also drawbacks.<br>
• Example 1: For instance, (disadvantage 1) can cause (negative effect).<br>
• Example 2: Additionally, (disadvantage 2) might lead to (further negative impact).<br>
<strong>Conclusion</strong><br>
• Restate Position: In conclusion, although (topic) has drawbacks, its advantages prevail.<br>
• Recommendation: Appropriate strategies should be implemented to maximize benefits and reduce downsides.`,
        "Template B": `<strong>Template B (Without Stating Which Side Outweighs)</strong><br>
<strong>Introduction</strong><br>
• General Statement/Context: (Topic) has transformed the way people (related action), with both benefits and drawbacks.<br>
• Overview: This essay will discuss the advantages and disadvantages.<br>
<strong>Body Paragraph 1 (Advantages)</strong><br>
• Topic Sentence: Firstly, (advantage 1) illustrates (explanation).<br>
• Example: (Example) reinforces this benefit.<br>
<strong>Body Paragraph 2 (Disadvantages)</strong><br>
• Topic Sentence: However, (disadvantage 1) presents challenges, such as (effect).<br>
<strong>Conclusion</strong><br>
• Summary: In conclusion, while (topic) offers advantages like (advantage), it also poses challenges such as (disadvantage).`
      },
      {
        type: "Causes, Problems and Solutions",
        topics: [
          { topic: "Discuss the causes, problems and solutions of urban pollution.", theme: "Environment" },
          { topic: "Discuss the causes, problems and solutions of rising obesity rates.", theme: "Health" }
        ],
        "Template A": `<strong>Template A (Problems – Solutions)</strong><br>
<strong>Introduction</strong><br>
• General Statement/Context: (Topic) has become a pressing issue worldwide.<br>
• Overview: This essay will discuss the principal problems and propose solutions.<br>
<strong>Body Paragraph 1 (Problems)</strong><br>
• Topic Sentence: One significant problem is (major problem).<br>
• Explanation: This is due to (cause), leading to (effect).<br>
• Additional Problem: Moreover, (secondary problem) results in (negative outcome).<br>
<strong>Body Paragraph 2 (Solutions)</strong><br>
• Topic Sentence: To address these issues, several solutions are proposed.<br>
• Solution 1: Firstly, (solution) can tackle (problem 1) because (reason).<br>
• Solution 2: Secondly, (solution) may reduce (problem 2) by (explanation).<br>
<strong>Conclusion</strong><br>
• Summary: In conclusion, (topic) presents challenges such as (problem), but viable solutions exist.<br>
• Recommendation: Stakeholders should collaborate to implement these solutions.`,
        "Template B": `<strong>Template B (Causes – Effects – Solutions)</strong><br>
<strong>Introduction</strong><br>
• General Statement: There is growing concern about (topic), which can have far-reaching consequences.<br>
• Overview: This essay examines the causes and effects, and proposes solutions.<br>
<strong>Body Paragraph 1 (Causes and Effects)</strong><br>
• Topic Sentence: A major cause is (cause 1), leading to (effect 1).<br>
• Additional Cause: Another factor is (cause 2), which causes (effect 2).<br>
<strong>Body Paragraph 2 (Solutions)</strong><br>
• Topic Sentence: To combat these challenges, remedies include (solution 1) and (solution 2).<br>
<strong>Conclusion</strong><br>
• Summary: In conclusion, (topic) results from (cause 1) and (cause 2) causing (effect 1) and (effect 2).<br>
• Call to Action: With concerted efforts, these negative impacts can be minimized.`
      },
      {
        type: "Two-Part Question",
        topics: [
          { topic: "Discuss the factors that lead to job satisfaction and explain how they affect employee productivity.", theme: "Society" },
          { topic: "What are the main reasons for the decline in traditional print media, and how can they be revived?", theme: "Technology" }
        ],
        structure: `<strong>Introduction:</strong><br>
• Introduce the topic and outline the two questions.<br>
<strong>Body Paragraph 1:</strong><br>
• Address the first question with detailed explanation and examples.<br>
<strong>Body Paragraph 2:</strong><br>
• Address the second question with supporting details and examples.<br>
<strong>Conclusion:</strong><br>
• Summarize both answers and provide a final thought.`
      },
      {
        type: "Positive or Negative Development",
        topics: [
          { topic: "Do you think the rise of social media is a positive or negative development?", theme: "Society" },
          { topic: "Is the rapid advancement of technology a positive or negative development for our future?", theme: "Technology" }
        ],
        structure: `<strong>Introduction:</strong><br>
• Introduce the topic and state your position.<br>
• Explain briefly that both positive and negative aspects exist.<br>
<strong>Body Paragraph 1:</strong><br>
• Discuss the positive aspects with examples.<br>
<strong>Body Paragraph 2:</strong><br>
• Discuss the negative aspects with examples.<br>
<strong>Conclusion:</strong><br>
• Summarize your stance and recommend that stakeholders address any drawbacks.`
      }
    ];

    /****************************************************************
     * GLOBAL VARIABLES
     ****************************************************************/
    const quotes = [
      "Education is the most powerful weapon which you can use to change the world. - Nelson Mandela",
      "The only way to do great work is to love what you do. - Steve Jobs",
      "The best way to predict the future is to create it. - Peter Drucker"
    ];

    let userPreferences = JSON.parse(localStorage.getItem("userPreferences")) || [];
    let defaultTimerMinutes = parseInt(localStorage.getItem("defaultTimerMinutes")) || 0;

    let currentEssayType = "";
    let currentEssayTopic = "";
    let currentTheme = "";
    let isOtherMode = false;
    let isEditingEssayType = false;

    let customAssignment = null;
    let timerInterval = null;
    let startTime = null;
    let elapsedTime = 0;

    /****************************************************************
     * SAVE USER PREFERENCES
     ****************************************************************/
    function savePreferences() {
      const radios = document.getElementsByName("preference");
      userPreferences = Array.from(radios)
        .filter(r => r.checked)
        .map(r => r.value);
      if (userPreferences.length === 0) {
        alert("Please select a topic!");
        return;
      }
      localStorage.setItem("userPreferences", JSON.stringify(userPreferences));
      document.getElementById("preferenceForm").style.display = "none";
      showContent();
    }

    /****************************************************************
     * SHOW MAIN CONTENT
     ****************************************************************/
    function showContent() {
      document.getElementById("heroSection").classList.add("hidden");
      document.getElementById("contentSection").style.display = "block";
      document.getElementById("essayTypeWrapper").style.display = "inline-flex";
      document.getElementById("essayTopicWrapper").style.display = "inline-flex";
      if (userPreferences.includes("Other")) {
        isOtherMode = true;
        document.getElementById("essayTypeText").textContent = "(Custom type)";
        document.getElementById("essayTopicText").textContent = "(Custom topic)";
        document.getElementById("randomTopicBtn").style.display = "none";
        updateVocab();
        document.getElementById("suggestedStructure").innerHTML = "Please choose your essay type above!";
      } else {
        isOtherMode = false;
        document.getElementById("randomTopicBtn").style.display = "inline-block";
        generateEssayTopic();
      }
      document.getElementById("structureCard").style.display = "block";
      document.getElementById("stickyNote").style.display = "block";
      document.getElementById("stickyNoteVocab").style.display = "block";
      document.getElementById("dot").style.display = "none";
      document.getElementById("dotVocab").style.display = "none";
      document.getElementById("dotStructure").style.display = "none";
      if (defaultTimerMinutes > 0) {
        elapsedTime = defaultTimerMinutes * 60;
        const m = Math.floor(elapsedTime / 60);
        const s = elapsedTime % 60;
        document.getElementById("timer").innerText = `${String(m).padStart(2, '0')}:${String(s).padStart(2, '0')}`;
      }
      updateWordCount();
      updateLayout();
    }

    /****************************************************************
     * GENERATE RANDOM ESSAY TOPIC (non-Other mode)
     ****************************************************************/
    function generateEssayTopic() {
      let filteredEssayTypes = [...essayTypes];
      if (!userPreferences.includes("Other")) {
        filteredEssayTypes = filteredEssayTypes.map(type => {
          const filteredTopics = type.topics.filter(t => userPreferences.includes(t.theme));
          return { ...type, topics: filteredTopics };
        }).filter(type => type.topics.length > 0);
      }
      if (filteredEssayTypes.length === 0) {
        filteredEssayTypes = [...essayTypes];
      }
      const randomTypeIndex = Math.floor(Math.random() * filteredEssayTypes.length);
      const selectedType = filteredEssayTypes[randomTypeIndex];
      const randomTopicIndex = Math.floor(Math.random() * selectedType.topics.length);
      const selectedTopic = selectedType.topics[randomTopicIndex];
      currentEssayType = selectedType.type;
      currentEssayTopic = selectedTopic.topic;
      currentTheme = selectedTopic.theme;
      document.getElementById("essayTypeText").textContent = currentEssayType;
      document.getElementById("essayTopicText").textContent = currentEssayTopic;
      updateStructure(currentEssayType);
      updateVocab();
      clearInterval(timerInterval);
      timerInterval = null;
      startTime = null;
      elapsedTime = defaultTimerMinutes * 60 || 0;
      document.getElementById("essayInput").value = "";
      const randomQuote = quotes[Math.floor(Math.random() * quotes.length)];
      document.getElementById("timeProverb").innerText = randomQuote;
    }

    /****************************************************************
     * CHANGE TOPIC
     * - Non-Other: random topic từ essay type hiện tại.
     * - Other: chỉ cho phép nhập (Custom Topic)
     ****************************************************************/
    function changeTopicRandom() {
      if (isOtherMode) {
        showCustomTopicInput();
        return;
      }
      const foundType = essayTypes.find(t => t.type === currentEssayType);
      if (!foundType) {
        generateEssayTopic();
        return;
      }
      let validTopics = foundType.topics;
      if (!userPreferences.includes("Other")) {
        validTopics = foundType.topics.filter(t => userPreferences.includes(t.theme));
        if (validTopics.length === 0) validTopics = foundType.topics;
      }
      const randomIndex = Math.floor(Math.random() * validTopics.length);
      const newTopic = validTopics[randomIndex];
      currentEssayTopic = newTopic.topic;
      currentTheme = newTopic.theme;
      document.getElementById("essayTopicText").textContent = currentEssayTopic;
      updateVocab();
      clearInterval(timerInterval);
      timerInterval = null;
      startTime = null;
      elapsedTime = defaultTimerMinutes * 60 || 0;
      document.getElementById("essayInput").value = "";
      const randomQuote = quotes[Math.floor(Math.random() * quotes.length)];
      document.getElementById("timeProverb").innerText = randomQuote;
    }

    function showCustomTopicInput() {
      document.getElementById("customAssignmentInput").style.display = "block";
      document.getElementById("customAssignmentValue").focus();
    }

    /****************************************************************
     * CHANGE TYPE
     * Hiển thị 6 lựa chọn dạng bài cho cả Other và non-Other.
     ****************************************************************/
    function toggleEditEssayType() {
      isEditingEssayType = true;
      let radioHTML = "";
      const allTypes = [
        "To What Extent Do You Agree or Disagree",
        "Discuss Both Views and Give Your Opinion",
        "Advantages and Disadvantages",
        "Causes, Problems and Solutions",
        "Two-Part Question",
        "Positive or Negative Development"
      ];
      allTypes.forEach(type => {
        radioHTML += `<label style="display:block;">
          <input type="radio" name="essayTypeRadio" value="${type}" onchange="onSelectEssayType('${type}')">
          ${type}
        </label>`;
      });
      document.getElementById("essayTypeText").innerHTML = radioHTML;
    }

    function onSelectEssayType(type) {
      currentEssayType = type;
      document.getElementById("essayTypeText").textContent = currentEssayType;
      isEditingEssayType = false;
      updateStructure(currentEssayType);
    }

    /****************************************************************
     * UPDATE WRITING STRUCTURE
     * Nếu essay type có Template A và B, hiển thị 2 nút cho lựa chọn.
     ****************************************************************/
    function updateStructure(type) {
      let structureHTML = "";
      const found = essayTypes.find(et => et.type === type);
      if (!found) {
        structureHTML = "No structure available for this type.";
      } else {
        if (found["Template A"] && found["Template B"]) {
          structureHTML = `<div>Please select a template:</div>
            <button onclick="selectTemplate('${type}', 'Template A')">Template A</button>
            <button onclick="selectTemplate('${type}', 'Template B')">Template B</button>`;
        } else if (found.structure) {
          structureHTML = found.structure;
        } else if (found["Template A"]) {
          structureHTML = found["Template A"];
        } else {
          structureHTML = "Structure not defined for this type.";
        }
      }
      document.getElementById("suggestedStructure").innerHTML = structureHTML;
    }

    function selectTemplate(type, templateKey) {
      const found = essayTypes.find(et => et.type === type);
      if (found && found[templateKey]) {
        document.getElementById("suggestedStructure").innerHTML = found[templateKey];
      }
    }

    /****************************************************************
     * UPDATE VOCABULARY SUGGESTION
     * Luôn cập nhật từ vựng theo currentTheme.
     ****************************************************************/
    function updateVocab() {
      const vocabList = vocabularies[currentTheme] || [];
      let vocabDisplay = "No vocabulary available.";
      if (vocabList.length > 0) {
        vocabDisplay = vocabList.map(v => `<strong>${v.word} (${v.type})</strong>: ${v.meaning}`).join("<br>");
      }
      document.getElementById("suggestions").innerHTML = vocabDisplay;
    }

    /****************************************************************
     * "Other" MODE HANDLING
     ****************************************************************/
    function handleOtherPreference(radio) {
      const otherInput = document.getElementById("otherInput");
      if (radio.checked) {
        otherInput.classList.remove("hidden");
      } else {
        otherInput.classList.add("hidden");
      }
    }

    function applyCustomOtherTopic() {
      const val = document.getElementById("customOtherTopic").value.trim();
      if (!val) {
        alert("Please enter a topic!");
        return;
      }
      currentEssayTopic = val;
      document.getElementById("essayTopicText").textContent = currentEssayTopic;
      document.getElementById("otherInput").classList.add("hidden");
      updateVocab();
      document.getElementById("essayInput").value = "";
    }

    function applyCustomAssignment() {
      const val = document.getElementById("customAssignmentValue").value.trim();
      if (!val) {
        alert("Please enter a topic!");
        return;
      }
      currentEssayTopic = val;
      document.getElementById("essayTopicText").textContent = currentEssayTopic;
      document.getElementById("customAssignmentInput").style.display = "none";
      document.getElementById("assignmentUpload").classList.remove("hidden");
      updateVocab();
      document.getElementById("essayInput").value = "";
    }

    /****************************************************************
     * OCR (Upload ảnh để scan đề bài)
     ****************************************************************/
    async function processImage(file) {
      try {
        const { data: { text } } = await Tesseract.recognize(file, "eng", { logger: m => console.log(m) });
        return text.trim();
      } catch (err) {
        console.error("Error processing image:", err);
        return "";
      }
    }

    async function handleImageUpload(files) {
      if (!files || files.length === 0) return;
      const file = files[0];
      const reader = new FileReader();
      reader.onload = async (e) => {
        const ocrResult = await processImage(file);
        if (ocrResult && ocrResult.length > 5) {
          currentEssayTopic = ocrResult;
          document.getElementById("essayTopicText").textContent = ocrResult;
        } else {
          currentEssayTopic = "[Image-based topic]";
          document.getElementById("essayTopicText").innerHTML = `<img src="${e.target.result}" style="max-width:100%; max-height:200px; border:1px solid var(--border-color); border-radius:6px;">`;
        }
        document.getElementById("assignmentUpload").classList.add("hidden");
        document.getElementById("customAssignmentInput").style.display = "none";
        document.getElementById("essayInput").value = "";
      };
      reader.readAsDataURL(file);
    }

    /****************************************************************
     * ĐỒNG HỒ BẤM GIỜ
     ****************************************************************/
    function startTimer() {
      if (timerInterval) return;
      startTime = Date.now() - elapsedTime * 1000;
      timerInterval = setInterval(() => {
        elapsedTime = Math.floor((Date.now() - startTime) / 1000);
        const m = Math.floor(elapsedTime / 60);
        const s = elapsedTime % 60;
        document.getElementById("timer").innerText = `${String(m).padStart(2, '0')}:${String(s).padStart(2, '0')}`;
      }, 1000);
    }

    function stopTimer() {
      if (timerInterval) {
        clearInterval(timerInterval);
        timerInterval = null;
      }
    }

    function showTimerSettings() {
      document.getElementById("timerSettings").style.display = "block";
    }

    function setTimer() {
      const minutes = parseInt(document.getElementById("timerMinutes").value) || 0;
      if (minutes > 0) {
        defaultTimerMinutes = minutes;
        localStorage.setItem("defaultTimerMinutes", defaultTimerMinutes);
        elapsedTime = minutes * 60;
        const m = Math.floor(elapsedTime / 60);
        const s = elapsedTime % 60;
        document.getElementById("timer").innerText = `${String(m).padStart(2, '0')}:${String(s).padStart(2, '0')}`;
        document.getElementById("timerSettings").style.display = "none";
      }
    }

    /****************************************************************
     * HIỆN / ẨN NOTE (Sticky note & Vocabulary)
     ****************************************************************/
    function hideNote(noteId, dotId) {
      document.getElementById(noteId).style.display = "none";
      document.getElementById(dotId).style.display = "block";
      updateLayout();
    }

    function showNote(noteId, dotId) {
      document.getElementById(noteId).style.display = "block";
      document.getElementById(dotId).style.display = "none";
      updateLayout();
    }

    function hideStructure() {
      document.getElementById("structureCard").style.display = "none";
      document.getElementById("dotStructure").style.display = "block";
      updateLayout();
    }

    function showStructure() {
      document.getElementById("structureCard").style.display = "block";
      document.getElementById("dotStructure").style.display = "none";
      updateLayout();
    }

    function updateLayout() {
      const stickyNote = document.getElementById("stickyNote");
      const stickyNoteVocab = document.getElementById("stickyNoteVocab");
      const bottomSection = document.getElementById("bottomSection");
      const writingArea = document.getElementById("writingArea");
      const structureCard = document.getElementById("structureCard");
      const dot = document.getElementById("dot");
      const dotVocab = document.getElementById("dotVocab");
      const dotStructure = document.getElementById("dotStructure");
      let visibleDots = [];
      if (dot.style.display === "block") visibleDots.push(dot);
      if (dotVocab.style.display === "block") visibleDots.push(dotVocab);
      if (dotStructure.style.display === "block") visibleDots.push(dotStructure);
      if (visibleDots.length > 0) {
        visibleDots.forEach((d, index) => {
          d.style.top = `${window.innerHeight - 60 - index * 50}px`;
          d.style.right = `20px`;
        });
      }
      bottomSection.style.display =
        (stickyNote.style.display === "none" && stickyNoteVocab.style.display === "none")
          ? "none" : "flex";
      writingArea.classList.toggle("expanded", structureCard.style.display === "none");
    }

    /****************************************************************
     * ĐẾM TỪ
     ****************************************************************/
    function updateWordCount() {
      const text = document.getElementById("essayInput").value;
      const words = text.trim().split(/\s+/).filter(w => w.length > 0).length;
      document.getElementById("wordCount").innerText = `${words} words`;
    }
    document.getElementById("essayInput").addEventListener("input", updateWordCount);

    /****************************************************************
     * DARK MODE
     ****************************************************************/
    function toggleTheme() {
      const body = document.body;
      if (body.getAttribute("data-theme") === "dark") {
        body.removeAttribute("data-theme");
      } else {
        body.setAttribute("data-theme", "dark");
      }
    }

    /****************************************************************
     * EXPORT FILE DOCX (ĐÃ SỬA)
     ****************************************************************/
    function exportEssay() {
      if (typeof JSZip === "undefined" || typeof saveAs === "undefined") {
        alert("Error: Required libraries (JSZip or FileSaver) are not loaded.");
        return;
      }
      const name = prompt("Enter your name:");
      if (!name) return;
      const essayText = document.getElementById("essayInput").value;
      if (!essayText) {
        alert("Please write an essay before exporting!");
        return;
      }
      if (!currentEssayType || !currentEssayTopic) {
        alert("Error: Essay type or topic is missing.");
        return;
      }
      const timeWritten = document.getElementById("timer").innerText;
      const nameParts = name.trim().split(/\s+/);
      let formattedName = name;
      if (nameParts.length > 3) {
        formattedName = `${nameParts[0]} ${nameParts.slice(1,-1).map(n => n[0]+".").join(" ")} ${nameParts[nameParts.length-1]}`;
      }
      const fileName = `Task_2_${currentEssayType.replace(/\s+/g, "_")}_${formattedName.replace(/\s+/g, "_")}.docx`;

      // Hàm escapeXML đã được sửa để loại bỏ ký tự điều khiển
      const escapeXML = str => str.replace(/[\x00-\x1F\x7F]/g, '').replace(/[&<>"']/g, m => ({
        '&': '&amp;',
        '<': '&lt;',
        '>': '&gt;',
        '"': '&quot;',
        "'": '&apos;'
      }[m]));

      // document.xml với đầy đủ namespace
      const documentXML = `<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<w:document
    xmlns:wpc="http://schemas.microsoft.com/office/word/2010/wordprocessingCanvas"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    xmlns:o="urn:schemas-microsoft-com:office:office"
    xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships"
    xmlns:m="http://schemas.openxmlformats.org/officeDocument/2006/math"
    xmlns:v="urn:schemas-microsoft-com:vml"
    xmlns:wp14="http://schemas.microsoft.com/office/word/2010/wordprocessingDrawing"
    xmlns:wp="http://schemas.openxmlformats.org/drawingml/2006/wordprocessingDrawing"
    xmlns:w10="urn:schemas-microsoft-com:office:word"
    xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main"
    xmlns:w14="http://schemas.microsoft.com/office/word/2010/wordml"
    xmlns:w15="http://schemas.microsoft.com/office/word/2012/wordml"
    xmlns:wpg="http://schemas.microsoft.com/office/word/2010/wordprocessingGroup"
    xmlns:wpi="http://schemas.microsoft.com/office/word/2010/wordprocessingInk"
    xmlns:wne="http://schemas.microsoft.com/office/word/2006/wordml"
    xmlns:wps="http://schemas.microsoft.com/office/word/2010/wordprocessingShape"
    mc:Ignorable="w14 w15 wp14">
  <w:body>
    <w:p>
      <w:pPr><w:pStyle w:val="Normal"/></w:pPr>
      <w:r><w:t>${escapeXML(currentEssayType)}</w:t></w:r>
    </w:p>
    <w:p>
      <w:pPr><w:pStyle w:val="Normal"/></w:pPr>
      <w:r><w:t>${escapeXML(currentEssayTopic)}</w:t></w:r>
    </w:p>
    <w:p>
      <w:pPr><w:pStyle w:val="Normal"/></w:pPr>
      <w:r><w:t>${escapeXML(formattedName)}, ${escapeXML(timeWritten)}</w:t></w:r>
    </w:p>
    ${essayText.split('\n').map(p => `
      <w:p>
        <w:pPr><w:pStyle w:val="Normal"/></w:pPr>
        <w:r><w:t xml:space="preserve">${escapeXML(p)}</w:t></w:r>
      </w:p>
    `).join('')}
    <w:sectPr>
      <w:pgSz w:w="12240" w:h="15840"/>
      <w:pgMar w:top="1440" w:right="1440" w:bottom="1440" w:left="1440"/>
    </w:sectPr>
  </w:body>
</w:document>`;

      // styles.xml với cấu hình font đầy đủ
      const stylesXML = `<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<w:styles xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main">
  <w:docDefaults>
    <w:rPrDefault>
      <w:rPr>
        <w:rFonts w:ascii="Times New Roman" w:hAnsi="Times New Roman" w:eastAsia="Times New Roman" w:cs="Times New Roman"/>
        <w:sz w:val="24"/>
        <w:szCs w:val="24"/>
        <w:lang w:val="en-US" w:eastAsia="en-US" w:bidi="ar-SA"/>
      </w:rPr>
    </w:rPrDefault>
  </w:docDefaults>
  <w:style w:type="paragraph" w:styleId="Normal">
    <w:name w:val="Normal"/>
    <w:basedOn w:val="Normal"/>
    <w:pPr>
      <w:spacing w:before="0" w:after="200" w:line="276" w:lineRule="auto"/>
      <w:jc w:val="both"/>
    </w:pPr>
    <w:rPr>
      <w:sz w:val="24"/>
      <w:szCs w:val="24"/>
    </w:rPr>
  </w:style>
</w:styles>`;

      // settings.xml
      const settingsXML = `<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<w:settings xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main">
  <w:zoom w:percent="100"/>
  <w:proofState w:spelling="clean" w:grammar="clean"/>
  <w:defaultTabStop w:val="720"/>
  <w:decimalSymbol w:val="."/>
  <w:listSeparator w:val=","/>
</w:settings>`;

      // numbering.xml
      const numberingXML = `<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<w:numbering xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main">
  <w:abstractNum w:abstractNumId="0">
    <w:nsid w:val="12345678"/>
    <w:multiLevelType w:val="hybridMultilevel"/>
    <w:lvl w:ilvl="0">
      <w:start w:val="1"/>
      <w:numFmt w:val="decimal"/>
      <w:lvlText w:val="%1."/>
      <w:lvlJc w:val="left"/>
    </w:lvl>
  </w:abstractNum>
  <w:num w:numId="1">
    <w:abstractNumId w:val="0"/>
  </w:num>
</w:numbering>`;

      // [Content_Types].xml
      const contentTypesXML = `<?xml version="1.0" encoding="UTF-8"?>
<Types xmlns="http://schemas.openxmlformats.org/package/2006/content-types">
  <Default Extension="rels" ContentType="application/vnd.openxmlformats-package.relationships+xml"/>
  <Default Extension="xml" ContentType="application/xml"/>
  <Override PartName="/word/document.xml" ContentType="application/vnd.openxmlformats-officedocument.wordprocessingml.document.main+xml"/>
  <Override PartName="/word/styles.xml" ContentType="application/vnd.openxmlformats-officedocument.wordprocessingml.styles+xml"/>
  <Override PartName="/word/settings.xml" ContentType="application/vnd.openxmlformats-officedocument.wordprocessingml.settings+xml"/>
  <Override PartName="/word/numbering.xml" ContentType="application/vnd.openxmlformats-officedocument.wordprocessingml.numbering+xml"/>
  <Override PartName="/docProps/core.xml" ContentType="application/vnd.openxmlformats-package.core-properties+xml"/>
</Types>`;

      // _rels/.rels
      const relsXML = `<?xml version="1.0" encoding="UTF-8"?>
<Relationships xmlns="http://schemas.openxmlformats.org/package/2006/relationships">
  <Relationship Id="rId1" Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/officeDocument" Target="word/document.xml"/>
  <Relationship Id="rId2" Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/core-properties" Target="docProps/core.xml"/>
</Relationships>`;

      // word/_rels/document.xml.rels
      const documentRelsXML = `<?xml version="1.0" encoding="UTF-8"?>
<Relationships xmlns="http://schemas.openxmlformats.org/package/2006/relationships">
  <Relationship Id="rId1" Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/styles" Target="styles.xml"/>
  <Relationship Id="rId2" Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/numbering" Target="numbering.xml"/>
  <Relationship Id="rId3" Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/settings" Target="settings.xml"/>
</Relationships>`;

      // docProps/core.xml
      const corePropsXML = `<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<cp:coreProperties
    xmlns:cp="http://schemas.openxmlformats.org/package/2006/metadata/core-properties"
    xmlns:dc="http://purl.org/dc/elements/1.1/"
    xmlns:dcterms="http://purl.org/dc/terms/"
    xmlns:dcmitype="http://purl.org/dc/dcmitype/"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <dc:title>IELTS Essay</dc:title>
  <dc:creator>${escapeXML(name)}</dc:creator>
  <cp:revision>1</cp:revision>
  <dcterms:created xsi:type="dcterms:W3CDTF">${new Date().toISOString()}</dcterms:created>
  <dcterms:modified xsi:type="dcterms:W3CDTF">${new Date().toISOString()}</dcterms:modified>
</cp:coreProperties>`;

      let zip = new JSZip();
      let wordFolder = zip.folder("word");
      let relsFolder = zip.folder("_rels");
      let wordRelsFolder = wordFolder.folder("_rels");
      let docPropsFolder = zip.folder("docProps");

      wordFolder.file("document.xml", documentXML);
      wordFolder.file("styles.xml", stylesXML);
      wordFolder.file("settings.xml", settingsXML);
      wordFolder.file("numbering.xml", numberingXML);
      wordRelsFolder.file("document.xml.rels", documentRelsXML);
      relsFolder.file(".rels", relsXML);
      docPropsFolder.file("core.xml", corePropsXML);
      zip.file("[Content_Types].xml", contentTypesXML);

      zip.generateAsync({ type: "blob", mimeType: "application/vnd.openxmlformats-officedocument.wordprocessingml.document" })
        .then(content => {
          saveAs(content, fileName);
        })
        .catch(err => {
          console.error("Error generating .docx file:", err);
          alert("Có lỗi khi xuất file. Vui lòng thử lại!");
        });
    }
  </script>
</body>
</html>
