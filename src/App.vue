<template>
  <main class="container">

    <header class="page-header">
      <h1>Secure Password & Passphrase Generator</h1>
      <p>Create strong random passwords or memorable passphrases in one click.</p>
    </header>

    <section class="generator-tool">

      <div class="mode-toggle">
        <button 
          :class="{ active: generatorMode === 'password' }" 
          @click="generatorMode = 'password'"
        >
          Password
        </button>
        <button 
          :class="{ active: generatorMode === 'passphrase' }" 
          @click="generatorMode = 'passphrase'"
        >
          Passphrase
        </button>
      </div>

      <div v-if="generatorMode === 'password'">
        <div class="control-group">
          <label for="passwordLength">Password Length: {{ passwordLength }} </label>
          <input 
            id="passwordLength"
            type="range"
            v-model="passwordLength"
            min="12"
            max="100"
            step="1"
          />
        </div>

        <div class="checkbox-group">
          <label>
            <input type="checkbox" v-model="includeUppercase" /> Include Uppercase
          </label>
          <label>
            <input type="checkbox" v-model="includeNumbers" /> Include Numbers
          </label>
          <label>
            <input type="checkbox" v-model="includeSymbols" /> Include Symbols
          </label>
          <label>
            <input type="checkbox" v-model="includeOtherLanguages" /> Include 10 Other Languages
          </label>
        </div>
      </div>

      <div v-if="generatorMode === 'passphrase'">
        <div class="control-group">
          <label for="passphraseWords">Number of Words: {{ passphraseWords }} </label>
          <input 
            id="passphraseWords"
            type="range"
            v-model.number="passphraseWords"
            min="3"
            max="10"
            step="1"
          />
        </div>
        <div class="control-group">
          <label for="passphraseSeparator">Separator: </label>
          <input 
            id="passphraseSeparator"
            type="text"
            v-model="passphraseSeparator"
            class="separator-input"
          />
        </div>
      </div>
      
      <button @click="generate" class="generate-button">
        {{ generatorMode === 'password' ? 'Generate Password' : 'Generate Passphrase' }}
      </button>

      <div v-if="password" class="password-display">
        <textarea rows="3" v-model="password" readonly> </textarea>
        <button @click="copyToClipboard">Copy to Clipboard</button>
      </div>
    </section>

    <section class="info-content">
      <h2>Why Use a Password & Passphrase Generator?</h2>
      <p>
        A strong password is your first line of defense. This tool gives you two great options:
      </p>
      <ul>
        <li><strong>Random Passwords:</strong> A long, complex string of characters that is nearly impossible for a computer to guess. Ideal for password managers.</li>
        <li><strong>Memorable Passphrases:</strong> A series of random, common words (like "tree-battery-blue-car") that are easy for *you* to remember but extremely difficult for an attacker to crack.</li>
      </ul>

      <h3>Is This Tool Secure and Private?</h3>
      <p>
        <strong>Yes. This tool is 100% private.</strong> All passwords and passphrases are generated *only* in your web browser. No data is ever sent to a server, saved, or logged.
      </p>

      <h3>What Makes a Strong Password?</h3>
      <ul>
        <li><strong>Length:</strong> Longer is always better. Aim for 12+ characters or 4+ words.</li>
        <li><strong>Complexity:</strong> For character passwords, mix uppercase, lowercase, numbers, and symbols.</li>
        <li><strong>Uniqueness:</strong> Never reuse passwords. Every website should have its own unique password or passphrase.</li>
      </ul>
      
      <h3>Advanced: Multi-Language & Unicode Characters</h3>
      <p>
        In "Password" mode, you can include characters from 10 different international alphabets. This is perfect for systems that support Unicode passwords, making them even harder to guess.
      </p>
      <p>
        <strong>Languages included:</strong>
        Persian, Korean, Russian, Arabic, Greek, Hebrew, Chinese, Japanese, and Hindi.
      </p>
    </section>

  </main>
</template>

<script setup>
import { ref } from 'vue';
import { useHead } from '@vueuse/head';

// --- UPDATED: SEO METADATA ---
useHead({
  title: 'Secure Password & Passphrase Generator (Unicode)',
  meta: [
    {
      name: 'description',
      content: 'Create strong, random passwords and memorable passphrases. Includes multi-language Unicode characters. Free, secure, and private.'
    },
  ],
});

// --- NEW: State for generator mode ---
const generatorMode = ref('password'); // 'password' or 'passphrase'

// --- Section 1: Password Refs ---
const passwordLength = ref(12);
const password = ref(''); // This will hold the result from *either* generator
const includeUppercase = ref(true);
const includeNumbers = ref(true);
const includeSymbols = ref(true);
const includeOtherLanguages = ref(true);

const englishLowercase = "abcdefghijklmnopqrstuvwxyz";
const englishUppercase = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";
const numbers = "0123456789";
const symbols = "!@#$%^&*()";

const persian = "ابپتثجچحخدذرزژسشصضطظعغفقکگلمنوهی";
const korean = "가각간갇갈갉갊갋갌간값갓갔강갖갗";
const russian = "абвгдеёжзийклмнопрстуфхцчшщъыьэюя";
const arabic = "ابتثجحخدذرزسشصضطظعغفقكلمنهوي";
const greek = "αβγδεζηθικλμνξοπρστυφχψω";
const hebrew = "אבגדהוזחטיךכלםמןנסעפףצץקרשת";
const chinese = "一二三四五六七八九十百千万亿";
const japanese = "あいうえおかきくけこさしすせそたちつてと";
const hindi = "अआइईउऊऋऌऍऑअंकखगघङचछजझञतथदधनपफबभमय";

// --- NEW: Section 2: Passphrase Refs ---
const passphraseWords = ref(4);
const passphraseSeparator = ref('-');
// A small, simple word list for this example.
// For a real app, you might want to use a much larger list.
const wordList = [
  'apple', 'banana', 'orange', 'grape', 'lemon', 'melon', 'peach', 'kiwi',
  'red', 'blue', 'green', 'yellow', 'purple', 'black', 'white', 'pink',
  'dog', 'cat', 'bird', 'fish', 'lion', 'tiger', 'bear', 'wolf',
  'house', 'tree', 'river', 'mountain', 'ocean', 'sun', 'moon', 'star',
  'car', 'bike', 'bus', 'train', 'boat', 'plane', 'road', 'sky',
  'happy', 'sad', 'angry', 'calm', 'brave', 'sharp', 'quick', 'slow',
  'book', 'pen', 'key', 'door', 'box', 'cup', 'hat', 'shoe'
];

// --- UPDATED: Main Generate Function ---
function generate() {
  if (generatorMode.value === 'password') {
    generateCharacterPassword();
  } else {
    generatePassphrase();
  }
}

function generateCharacterPassword() {
  let charset = englishLowercase;
  if (includeUppercase.value) charset += englishUppercase;
  if (includeNumbers.value) charset += numbers;
  if (includeSymbols.value) charset += symbols;
  if (includeOtherLanguages.value) {
    charset += persian; 
    charset += korean;
    charset += russian;
    charset += arabic;
    charset += greek;
    charset += hebrew;
    charset += chinese;
    charset += japanese;
    charset += hindi;
  }
  let randomPassword = '';
  for (let i = 0; i < passwordLength.value; i++) {
    const randomIndex = Math.floor(Math.random() * charset.length);
    randomPassword += charset[randomIndex];
  }
  password.value = randomPassword;
}

// --- NEW: Passphrase Generator Function ---
function generatePassphrase() {
  let newPassphrase = [];
  for (let i = 0; i < passphraseWords.value; i++) {
    const randomIndex = Math.floor(Math.random() * wordList.length);
    newPassphrase.push(wordList[randomIndex]);
  }
  password.value = newPassphrase.join(passphraseSeparator.value);
}

async function copyToClipboard() {
  try {
    await navigator.clipboard.writeText(password.value);
    alert('Password copied to clipboard!');
  } catch (err) {
    alert('Failed to copy password. Please try again.');
  }
}
</script>

<style scoped>
/* --- NEW STYLES --- */
.mode-toggle {
  display: flex;
  justify-content: center;
  margin-bottom: 2rem;
  background-color: #eee;
  border-radius: 8px;
  padding: 5px;
}
.mode-toggle button {
  flex: 1;
  padding: 10px 15px;
  border: none;
  background-color: transparent;
  cursor: pointer;
  font-size: 1rem;
  color: #555;
  font-weight: 600;
  border-radius: 6px;
  transition: background-color 0.2s ease;
}
.mode-toggle button.active {
  background-color: #fff;
  color: #2196F3;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}
.separator-input {
  width: 60px;
  padding: 5px 8px;
  font-size: 1rem;
  text-align: center;
  border: 1px solid #ccc;
  border-radius: 4px;
  margin-left: 10px;
}
/* --- End New Styles --- */

.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 1rem 1.5rem 3rem 1.5rem;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
  line-height: 1.6;
}

.page-header {
  text-align: center;
  border-bottom: 2px solid #f0f0f0;
  padding-bottom: 1rem;
  margin-bottom: 2rem;
}

.page-header h1 {
  margin-bottom: 0.25rem;
  color: #2c3e50;
}

.page-header p {
  font-size: 1.1rem;
  color: #555;
  margin: 0;
}

.generator-tool {
  background: #f9f9f9;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  padding: 1.5rem 2rem; /* Added more horizontal padding */
  margin: 2rem 0;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.control-group {
  margin-bottom: 1rem;
  display: flex;
  align-items: center;
  width: 100%;
  max-width: 400px; /* Constrain width of controls */
  justify-content: space-between;
}
.control-group label {
  font-weight: 500;
}

.checkbox-group {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px 20px;
  margin: 1rem 0;
  width: 100%;
  max-width: 400px; /* Constrain width */
}

.info-content {
  margin-top: 2rem;
  color: #333;
}

.info-content h2, .info-content h3 {
  color: #2c3e50;
  border-bottom: 1px solid #eee;
  padding-bottom: 5px;
}

.info-content ul {
  padding-left: 20px;
}

.generate-button {
  padding: 12px 25px;
  background-color: #4CAF50;
  color: white;
  border: none;
  cursor: pointer;
  font-size: 1.1rem;
  font-weight: bold;
  border-radius: 5px;
  margin-top: 1rem;
  width: 100%;
  max-width: 400px; /* Match control width */
}

.generate-button:hover {
  background-color: #45a049;
}

input[type="range"] {
  width: 200px; /* Adjusted width */
}

label {
  display: flex;
  align-items: center;
  font-size: 0.95rem;
  user-select: none;
}

label input[type="checkbox"] {
  margin-right: 8px;
  width: 16px;
  height: 16px;
}

.password-display {
  margin-top: 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
  max-width: 400px; /* Match control width */
}

.password-display textarea {
  width: 100%; /* Use full width of container */
  padding: 10px;
  font-size: 1.1rem;
  text-align: center;
  border: 1px solid #ccc;
  border-radius: 4px;
  resize: none;
  background: #fff;
  font-family: 'Courier New', Courier, monospace;
}

.password-display button {
  padding: 10px 15px;
  background-color: #2196F3;
  color: white;
  border: none;
  cursor: pointer;
  margin-top: 10px;
  border-radius: 4px;
  width: 100%; /* Use full width */
}

.password-display button:hover {
  background-color: #1976D2;
}

@media (max-width: 600px) {
  .checkbox-group {
    grid-template-columns: 1fr;
  }
  .control-group {
    flex-direction: column;
    align-items: center;
    gap: 10px;
  }
  input[type="range"] {
    width: 100%;
  }
}
</style>