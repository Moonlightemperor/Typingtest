<script>
  import { onMount } from 'svelte';

  let timerOption = 'time';
  let timeOption = '30';
  let remainingTime = 0;
  let startTime = null;
  let wordsTyped = 0;
  let totalWords = 0;
  let errors = 0;
  let inputText = '';
  let currentText = '';
  let isTyping = false;
  let timerInterval = null;
  let wpm = 0;
  let showResults = false;
  let correctWordsTyped = 0;
  let showWordsTyped = false;
  let accuracy = 0;
  let showInstructionsOnStart = false;
  let showInstructions = false;
  let showModeSelection = false;
  let testMode = ''; // 'time' or 'quote'
  let showOptionsBar = true;
  let showFooter = true;
  let timerDuration = 30;
  let showReset = true;

  const sentences = [
    'The quick brown fox jumps over the lazy dog.',
    'Svelte is a radical new approach to building user interfaces.',
    'JavaScript is a versatile and powerful programming language.',
    'The cat sat on the mat and looked at the moon.',
    'Typing tests are a great way to improve your typing speed.'
  ];

  const words = [
    'quick', 'brown', 'fox', 'jumps', 'over', 'lazy', 'dog',
    'svelte', 'radical', 'new', 'approach', 'building', 'user', 'interfaces',
    'javaScript', 'versatile', 'powerful', 'programming', 'language',
    'cat', 'sat', 'mat', 'looked', 'moon',
    'typing', 'tests', 'great', 'way', 'improve', 'love', 'speed'
  ];

  function getRandomWords(num) {
    let result = [];
    for (let i = 0; i < num; i++) {
      const randomIndex = Math.floor(Math.random() * words.length);
      result.push(words[randomIndex]);
    }
    return result.join(' ');
  }

  function handleChange(event) {
    timeOption = event.target.value;
    resetTest();
    handleModeChange(timerOption);
  }

  function startTimer() {
    remainingTime = timerDuration;
    isTyping = true;
    clearInterval(timerInterval);
    startTime = Date.now();
    timerInterval = setInterval(() => {
      const elapsedTime = Math.floor((Date.now() - startTime) / 1000);
      remainingTime = timerDuration - elapsedTime;
      if (remainingTime <= 0) {
        clearInterval(timerInterval);
        remainingTime = 0;
        isTyping = false;
        processInputTimerMode(inputText.trim().split(/\s+/));
        showResults = true;
        calculateWPM();
        calculateAccuracy();
        console.log('Test Completed. WPM:', wpm, 'Errors:', errors, 'Accuracy:', accuracy);
      }
    }, 1000);
  }

  function handleInput(event) {
    console.log('Handling Input');
    if (!isTyping) {
      isTyping = true;
      startTime = Date.now();
      showWordsTyped = true;
      startTimer();
    }

    inputText = event.target.value;
    console.log('Updated Input Text:', inputText);

    const inputWords = inputText.trim().split(/\s+/);
    console.log('Input Words:', inputWords);

    const currentWords = currentText.trim().split(/\s+/);
    console.log('Current Words:', currentWords);

    wordsTyped = inputWords.filter(word => word.length > 0).length;

    if (inputWords.length >= currentWords.length && inputText.endsWith(' ')) {
      console.log('Condition Met - Processing Input');
      if (timerOption === 'time' || timerOption === 'quote') {
        processInputTimerMode(inputWords);
      } else {
        processInput(inputWords);
      }
      event.target.value = '';
      inputText = '';
    }
  }

  function processInput(inputWords) {
    console.log('Inside processInput');
    const currentWords = currentText.trim().split(/\s+/);

    inputWords.forEach((word, index) => {
      console.log(`Comparing: ${word} with ${currentWords[index]}`);
      if (currentWords[index] === word) {
        correctWordsTyped++;
      } else {
        errors++;
      }
    });

    totalWords += currentWords.length;
    console.log('Total Words:', totalWords, 'Correct Words Typed:', correctWordsTyped, 'Errors:', errors);

    if (testMode === 'quote') {
      currentText = sentences[Math.floor(Math.random() * sentences.length)];
    } else {
      const newWords = getRandomWords(30);
      currentText = newWords;
      totalWords = 30;
    }
  }

  function processInputTimerMode(inputWords) {
    console.log('Inside processInputTimerMode');
    const currentWords = currentText.trim().split(/\s+/);

    inputWords.forEach((word, index) => {
      console.log(`Comparing: ${word} with ${currentWords[index]}`);
      if (currentWords[index] === word) {
        correctWordsTyped++;
      } else {
        errors++;
      }
    });

    totalWords += currentWords.length;
    console.log('Total Words:', totalWords, 'Correct Words Typed:', correctWordsTyped, 'Errors:', errors);

    if (testMode === 'quote') {
      currentText = sentences[Math.floor(Math.random() * sentences.length)];
    } else {
      const newWords = getRandomWords(30);
      currentText = newWords;
      totalWords = 30;
    }
  }

  function calculateWPM() {
    const elapsedTime = (Date.now() - startTime) / 1000 / 60;
    wpm = Math.round(correctWordsTyped / elapsedTime);
    console.log('Elapsed Time:', elapsedTime, 'Correct Words Typed:', correctWordsTyped);
  }

  function calculateAccuracy() {
    if (totalWords > 0) {
      accuracy = Math.round((correctWordsTyped / totalWords) * 100);
    } else {
      accuracy = 0;
    }
    console.log('Accuracy:', accuracy);
  }

  function handleModeChange(option) {
    console.log('Handling Mode Change:', option);
    timerOption = option;
    resetTest();
    if (timerOption === 'quote') {
      currentText = sentences[Math.floor(Math.random() * sentences.length)];
      totalWords = 0;
    } else {
      const initialWords = getRandomWords(30);
      currentText = initialWords;
      totalWords = initialWords.split(' ').length;
    }
    inputText = '';
    testMode = option;

    console.log('Test Mode:', testMode);
   
  }

  function resetTest() {
    clearInterval(timerInterval);
    remainingTime = 0;
    isTyping = false;
    inputText = '';
    showResults = false;
    wordsTyped = 0;
    correctWordsTyped = 0;
    errors = 0;
    showWordsTyped = false;
    accuracy = 0;
    console.log('Test Reset');
  }

  function refreshPage() {
    window.location.reload();
  }

  onMount(() => {
    showInstructionsOnStart = true;
    handleModeChange(timerOption);
  });

  function handleReset() {
    resetTest();
    inputText = '';
  }

  function handleInstructionsOk() {
    showInstructions = false;
    showModeSelection = true;
  }

  function handleInstructionsOnStart() {
    showInstructionsOnStart = false;
  }

  function startTest(mode) {
    console.log('Starting Test with Mode:', mode);
    handleModeChange(mode);
    testMode = mode;
    showModeSelection = false;
    showOptionsBar = false;
    showFooter = false;
    showReset = false;
    if (mode === 'quote' && !currentText) {
    console.error('Current text not initialized!');
    return; // Prevent starting if text is not set
  }
    startTimer();
  }
</script>

<div class="grid-container">
  <div class="header" on:click={refreshPage}>
    typingtest
  </div>

  {#if showInstructionsOnStart}
  <div class="dialogonstart">
    <p>Welcome to the typing test!</p>
    <p>This site helps you improve your typing speed and accuracy.</p>
    <p>Instructions:</p>
    <ul>
      <li>Select a mode: time or finish statement.</li>
      <li>You will be provided with various time options for practice session in both the modes</li>
      <li>In time mode you will be given set of random words </li>
      <li>In finish statment mode you will be provided with complete meaningful sentences </li>
      <li>There is a test segment  also in which you can practice your skills </li>
      <li>Start  typing quickly and accurately as possible.</li>
      <li>Your results will be shown when your timer stops.</li>
    </ul>
    
    <button  class="dialoguebutton"  on:click={handleInstructionsOnStart}>Get in</button>
  </div>
{/if}


  {#if !showResults}
    {#if !showInstructions && !showModeSelection && !showInstructionsOnStart}
      <div  class={isTyping ? 'options-bar blurred' : 'options-bar'}>
        <button>Practice</button>
        <button on:click={() => { 
          showInstructions = true; 
        }}>Test</button>
        <div class="divider"></div>
        <button class:active={timerOption === 'time'} on:click={() => handleModeChange('time')}>⏱ time</button>
        <button class:active={timerOption === 'quote'} on:click={() => handleModeChange('quote')}>📜 finish Statement</button>
        <div class="divider"></div>
        <div class="timer-options">
          <button class:active={timeOption === '30'} on:click={() => { timeOption = '30'; resetTest(); }}>30</button>
          <button class:active={timeOption === '45'} on:click={() => { timeOption = '45'; resetTest(); }}>45</button>
          <button class:active={timeOption === '60'} on:click={() => { timeOption = '60'; resetTest(); }}>60</button>
          <button class:active={timeOption === '90'} on:click={() => { timeOption = '90'; resetTest(); }}>90</button>
        </div>
      </div>
    {/if}

    {#if showInstructions}
      <div class="dialog">
        <p>Please follow the instructions below:</p>
        <p>1. Click OK to proceed to test mode selection.</p>
        <p>2. Choose between words mode or statement mode.</p>
        <p>3. Time duration of test will be 30 seconds.</p>
        <p>4. The test will start automatically.</p>
        <button on:click={handleInstructionsOk}>OK</button>
      </div>
    {/if}

    {#if showModeSelection}
      <div class="mode-selection">
        <p>Select Test Mode:</p>
        <button on:click={() => startTest('time')}>Words</button>
        <button on:click={() => startTest('quote')}>Statements</button>
      </div>
    {/if}
  {/if}

  <div class="typing-area-wrapper {showInstructions || showModeSelection || showInstructionsOnStart ? 'blurred' : ''}">
    {#if showResults}
      <div class="results">
        <div class="card">
          <p>{totalWords}</p>
          <span>Total Words</span>
        </div>
        <div class="card">
          <p>{wpm}</p>
          <span>Your WPM</span>
        </div>
        <div class="card">
          <p>{errors}</p>
          <span>Total Errors</span>
        </div>
        <div class="card">
          <p>{accuracy}%</p>
          <span>Total accuracy</span>
        </div>
      </div>
      <div class="reset-button-container">
        <button class="start-again-button" on:click={refreshPage}>
          <span class="arrow">↻</span>
          <span>Start Again</span>
        </button>
      </div>
    {:else}
      {#if timerOption === 'time' && isTyping}
        <div class="remaining-time">
          {remainingTime}
        </div>
      {:else if timerOption === 'quote' && isTyping}
        {#if showWordsTyped}
          <!-- Words Typed display can be included here -->
        {/if}
        <div class="remaining-time">
          {remainingTime}
        </div>
      {/if}
      <div class="typing-area">
        <div class="input-overlay">
          {#each currentText.split('') as char, index}
            <span class={inputText[index] === char ? 'correct' : inputText[index] ? 'incorrect' : ''}>{char}</span>
          {/each}
        </div>
        <textarea class="textarea-overlay" bind:value={inputText} on:input={handleInput} class:blurred={showInstructions || showModeSelection|| showInstructionsOnStart}></textarea>
      </div>
      <div class="reset-button-container" style:display={showReset ? 'block' : 'none'}>
        <button class="reset-button" on:click={handleReset} title="Restart Test">Reset</button>
      </div>
    {/if}
  </div>

  {#if !showInstructions && !showModeSelection && !showInstructionsOnStart}
    <div  class={isTyping ? 'footer blurred' : 'footer'} >
      typingtest.com
    </div>
  {/if}
</div>






<style>
/* body {
  background-color: #1c1c1c;
  color: #dcdcdc;
  font-family: 'Consolas', 'Monaco', 'Lucida Console', 'Courier New', monospace;
  margin: 0;
  padding: 0;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
} */

.grid-container {
  display: grid;
  grid-template-columns: 1fr 28fr 1fr;
  grid-template-rows: auto auto 1fr auto;
  gap: 10px;
  width: 90vw;  
  max-width: 1200px;
  height: 80vh;
}

.header {
  grid-column: 1 / 4;
  grid-row: 1;
  display: flex;
  justify-content: left;
  align-items: center;
  padding: 15px;  
  font-size: 40px;  
  font-weight: bold;
  color: #ffd700;
  background: #333;
  cursor: pointer;
  transition: color 0.3s, transform 0.3s;
}

.header:hover {
  color: #ffffff;
  transform: scale(1.05);
}

.options-bar {
  grid-column: 1 / 4;
  grid-row: 2;
  display: flex;
  flex-wrap: wrap;  
  justify-content: center;
  background: #2c2e31;
  padding: 10px;
  border-radius: 8px;
  margin-top: 10px;  
  height: auto;  
  font-family: 'Consolas', 'Monaco', 'Lucida Console', 'Courier New', monospace;
}

.options-bar button,
.timer-options button {
  background: none;
  border: none;
  color: #646669;
  font-size: 18px;  
  cursor: pointer;
  margin: 5px;  
  padding: 5px 8px;  
  border-radius: 4px;
  transition: color 0.3s;
}

.options-bar button:hover,
.timer-options button:hover {
  color: #ffffff;
}

.options-bar button.active,
.timer-options button.active {
  color: #ffd700;
}

.dialog, .mode-selection {
  grid-column: 1 / 4;
  grid-row: 3;
  background: #333;
  color: #ffd700;
  padding: 20px;
  border-radius: 8px;
  text-align: center;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 80%;
  max-width: 600px;
  z-index: 1000;
}

.dialogonstart{
  grid-column: 1 / 4;
  grid-row: 3;
  background: #333;
  color: #ffd700;
  padding: 20px;
  border-radius: 8px;
  text-align: center;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 80%;
  max-width: 600px;
  z-index: 1000;
  justify-content: left;
}

.dialog button, .mode-selection button {
  background: #444;
  color: #ffd700;
  border: none;
  padding: 10px 20px;
  border-radius: 4px;
  cursor: pointer;
  transition: background 0.3s, color 0.3s;
  margin-top: 20px;
}

.dialogonstart {
  grid-column: 1 / 4;
  grid-row: 3;
  background: #333;
  color: #ffd700;
  padding: 20px;
  border-radius: 8px;
  text-align: center; /* This centers the <p> elements */
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 80%;
  max-width: 600px;
  z-index: 1000;
}

.dialogonstart p {
  text-align: center; /* Ensures the <p> elements are centered */
  margin-bottom: 15px; /* Adds some space below each <p> */
}

.dialogonstart ul {
  list-style-type: disc; /* Adds bullet points */
  text-align: left; /* Aligns the list items to the left */
  margin: 0 auto 20px; /* Centers the list and adds space below it */
  padding-left: 20px; /* Adds padding to the left of the list */
  max-width: 80%; /* Keeps the list within a reasonable width */
}

.dialogonstart li {
  margin-bottom: 10px; /* Adds some space between list items */
}

.dialoguebutton {
  display: block;
  margin: 0 auto;
  background: #444;
  color: #ffd700;
  border: none;
  padding: 10px 20px;
  border-radius: 4px;
  cursor: pointer;
  transition: background 0.3s, color 0.3s;
}

.dialoguebutton:hover {
  background: #ffd700;
  color: #333;
}
.dialog button:hover, .mode-selection button:hover {
  background: #ffd700;
  color: #333;
}

.divider {
  width: 6px;
  height: 25px;
  background-color: #646669;
  margin: 15px 10px;
  justify-content: center;
  display: flex;
}

.typing-area-wrapper {
  grid-column: 1 / 4;  
  grid-row: 3;
  position: relative;
}

.remaining-time,
.words-typed {
  position: absolute;
  top: 0;
  left: 0;
  background: #333;
  color: #ffd700;
  padding: 8px;  
  border-radius: 4px;
  font-size: 18px;  
}



.typing-area {
  background: #333;
  color: #dcdcdc;
  padding: 15px;  
  border-radius: 8px;
  font-family: 'Consolas', 'Monaco', 'Lucida Console', 'Courier New', monospace;
  font-size: 30px;  
  height: calc(100% - 50px);
  overflow-y: auto;
  margin-top: 40px;  
  position: relative;
  line-height: 1.5em;
  height: calc(3 * 1.5em + 2rem); /* 3 lines of text plus padding */
  overflow: hidden; /* Hide any overflow */
  box-sizing: border-box;

}

.typing-area-wrapper.blurred {
  filter: blur(5px);
  pointer-events: none;
}

.textarea-overlay.blurred {
  filter: blur(5px);
  pointer-events: none;
}

.input-overlay {
  position: absolute;
  top: 40px;  
  left: 0;
  right: 0;
  bottom: 0;
  color: #646669;
  white-space: pre-wrap;
  z-index: 1;
  text-align: left;
  word-spacing: 0.1em; /* Adjust this value to increase or decrease the spacing */
}


.textarea-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  opacity: 0;
  z-index: 2;
  font-size: 22px;  
  background: transparent;
  color: transparent;
  border: none;
  resize: none;
  outline: none;
  text-align: left;
  cursor: default;
  line-height: 1.5em;

}

.textarea-overlay:hover {
  cursor: default;
}

.correct {
  color: #ffffff;
}

.incorrect {
  color: #ff4d4d;
}

.timer-options {
  display: flex;
    flex-wrap: wrap;
    justify-content: center;
    margin-top: 7px;
}

/* .results {
  grid-column: 1 / 4;
  grid-row: 4;
  font-size: 20px;  
  margin-top: 10px;  
  color: #ffd700;
  background: #333;
  padding: 10px;
  border-radius: 8px;
  text-align: center;
  position: relative;
} */

.results {
  grid-column: 1 / 4;
  grid-row: 4;
  display: flex;
  justify-content: center;
  gap: 15px; /* Space between cards */
  color: #ffd700;
  background: #333;
  padding: 20px;
  border-radius: 8px;
}

.results .card {
  background: #444;
  color: #ffd700;
  padding: 15px;
  border-radius: 8px;
  width: 100%;
  max-width: auto; /* Adjust the width as needed */
  text-align: center;
  transition: transform 0.3s, box-shadow 0.3s; /* Smooth transition for hover effect */
}

.results .card:hover {
  transform: translateY(-10px); /* Slightly lift the card */
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.5); /* Add a shadow for the popup effect */
}

.results .card p {
  margin: 0;
  font-size: 136px; /* Larger font size for numbers */
  font-weight: normal;
  font-family: 'Consolas', 'Monaco', 'Lucida Console', 'Courier New', monospace;
  line-height: 1;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  display: flex;
  justify-content: center;
  align-items: baseline;
}
.results .card .percentage-sign {
  font-size: 0.7em;/* Adjust the font size of the percentage sign */
  margin-left: 4px; /* Adjust space between number and percentage sign */
  align-self: flex-start;
  justify-content: center;
  font-family: 'Consolas', 'Monaco', 'Lucida Console', 'Courier New', monospace;
}

.results .card span {
  display: block;
  margin-top: 5px;
  font-size: 18px; /* Smaller font size for text below numbers */
  color: #dcdcdc;
  font-family: 'Consolas', 'Monaco', 'Lucida Console', 'Courier New', monospace;
}

.reset-button-container {
  text-align: center; /* Center the button container */
  margin: 57px 0; /* Space above and below the button */
}
.reset-button-container {
  display: flex;
  justify-content: center;
  margin-top: 57px;
}

.start-again-button {
  background: #444;
  color: #ffd700;
  border: none;
  padding: 10px 20px;
  border-radius: 50px; /* Round button */
  cursor: pointer;
  transition: background 0.3s, color 0.3s, transform 0.3s;
  display: flex;
  align-items: center;
  font-size: 18px;
}

.start-again-button:hover {
  background: #ffd700;
  color: #333;
  transform: scale(1.05); /* Slightly scale up the button */
}

.start-again-button .arrow {
  font-size: 24px; /* Adjust size of the arrow */
  margin-right: 10px;
}


 
.blurred {
  filter: blur(9px);
  pointer-events: none;
}

.reset-button {
  background: #333;
  color: #ffd700;
  border: 2px solid #ffd700;
  font-size: 18px;
  padding: 10px 20px;
  border-radius: 4px;
  cursor: pointer;
  transition: background 0.3s, color 0.3s;
}
/* .reset-button button:hover {
  background: #ffd700;
  color: #333;
} */

.reset-button::after {
  /* content: "Restart Test"; Tooltip text */
  position: absolute;
  /* Position above the button */
  left: 50%;
  transform: translateX(-50%);
  background-color: #333;
  color: #fff;
  padding: 5px 10px;
  border-radius: 4px;
  white-space: nowrap;
  opacity: 0;
  visibility: hidden;
  transition: opacity 0s, visibility 0s;
  font-size: 14px; /* Adjust as needed */
}

.reset-button:hover::after {
  opacity: 1;
  visibility: visible;
}


.footer {
  grid-column: 1 / 4;
  grid-row: 4;
  font-size: 16px; 
  color: #646669;
  background: #333;
  padding: 50px;
  border-radius: 8px;
  text-align: center;
}
</style>

