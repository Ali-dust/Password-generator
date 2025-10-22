<template>
  <main class="container">

    <header class="page-header">
      <h1>Maximum Security Password Generator</h1> <p>Create complex Unicode passwords or memorable passphrases.</p> </header>

    <section class="generator-tool card">

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

      <div v-if="generatorMode === 'password'" class="options-grid">
        <div class="control-group slider-group">
          <label for="passwordLength">Password Length: <span>{{ passwordLength }}</span></label>
          <input 
            id="passwordLength"
            type="range"
            v-model.number="passwordLength"
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
            <input type="checkbox" v-model="includeOtherLanguages" /> Include 10+ Languages
          </label>
        </div>
        
        </div>

      <div v-if="generatorMode === 'passphrase'" class="options-grid">
         <div class="control-group slider-group">
          <label for="passphraseWords">Number of Words: <span>{{ passphraseWords }}</span></label>
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
          <label for="passphraseSeparator">Separator:</label>
          <input 
            id="passphraseSeparator"
            type="text"
            v-model="passphraseSeparator"
            class="separator-input"
          />
        </div>
      </div>
      
      <div v-if="password" class="password-display">
        <input type="text" v-model="password" readonly />
        <button @click="copyToClipboard" title="Copy to Clipboard">
          <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor">
            <path d="M7 3.5A1.5 1.5 0 018.5 2h3.879a1.5 1.5 0 011.06.44l3.122 3.12A1.5 1.5 0 0117 6.622V17.5a1.5 1.5 0 01-1.5 1.5h-7A1.5 1.5 0 017 17.5v-14z" />
            <path d="M5 3.5A1.5 1.5 0 003.5 5v12.5A1.5 1.5 0 005 19h7a1.5 1.5 0 001.5-1.5v-2.5a.75.75 0 00-1.5 0v2.5a.5.5 0 01-.5.5h-7a.5.5 0 01-.5-.5V5a.5.5 0 01.5-.5h2.5a.75.75 0 000-1.5H5z" />
          </svg>
          <span>{{ copyButtonText }}</span>
        </button>
      </div>
      
      <button @click="generate" class="generate-button">
        {{ generatorMode === 'password' ? 'Generate Password' : 'Generate Passphrase' }}
      </button>
    </section>

    <section class="info-content card">
      <h2>Why Use a Secure Generator?</h2>
      <p>
        A strong password is your first line of defense. This tool gives you two great options:
      </p>
      <ul>
        <li><strong>Memorable Passphrases:</strong> A series of random, common words (like "tree-battery-blue-car") that are easy for *you* to remember but extremely difficult for an attacker to crack.</li>
        <li><strong>Maximum Security Passwords:</strong> A long, complex string of characters that is nearly impossible for a computer to guess. Ideal for password managers.</li>
      </ul>

      <h3>Is This Tool Secure and Private?</h3>
      <p>
        <strong>Yes. This tool is 100% private.</strong> All passwords and passphrases are generated *only* in your web browser. No data is ever sent to a server, saved, or logged.
      </p>
      
      <h3>Maximum Security: Multi-Language & Unicode</h3>
      <p>
        For high-security systems that accept Unicode, the "Include 10+ Languages" option adds thousands of characters from Russian, Japanese, Korean, Arabic, and more.
      </p>
      <p>
        <strong>This is not for typing.</strong> This feature is designed to create an extremely complex password that you <strong>copy and paste</strong> from a password manager for maximum security.
      </p>
    </section>

  </main>
</template>

<script setup>
import { ref } from 'vue';
import { useHead } from '@vueuse/head';

// UPDATED: SEO metadata to focus on "Maximum Security"
useHead({
  title: 'Maximum Security Unicode Password Generator',
  meta: [
    {
      name: 'description',
      content: 'Generate ultra-secure, complex passwords with Unicode characters from 10+ languages. Designed for copy-pasting into high-security systems.'
    },
  ],
});

// --- Generator Mode ---
const generatorMode = ref('password');

// --- Password Refs ---
const passwordLength = ref(16);
const password = ref('');
const includeUppercase = ref(true);
const includeNumbers = ref(true);
const includeSymbols = ref(true);
const includeOtherLanguages = ref(true); // RE-ADDED

// --- Passphrase Refs ---
const passphraseWords = ref(4);
const passphraseSeparator = ref('-');

// --- Copy Button Text ---
const copyButtonText = ref('Copy');

// --- Character Sets ---
const englishLowercase = "abcdefghijklmnopqrstuvwxyz";
const englishUppercase = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";
const numbers = "0123456789";
const symbols = "!@#$%^&*()_+-=[]{}|;:,.<>?";
const accents = "áéíóúÁÉÍÓÚàèìòùÀÈÌÒÙâêîôûÂÊÎÔÛäëïöüÄËÏÖÜñÑçÇ";

const languageSets = {
  persian: "ابپتثجچحخدذرزژسشصضطظعغفقکگلمنوهی",
  korean: "가각간갇갈갉갊갋갌간값갓갔강갖갗",
  russian: "абвгдеёжзийклмнопрстуфхцчшщъыьэюя",
  arabic: "ابتثجحخدذرزسشصضطظعغفقكلمنهوي",
  greek: "αβγδεζηθικλμνξοπρστυφχψω",
  hebrew: "אבגדהוזחטיךכלםמןנסעפףצץקרשת",
  chinese: "一二三四五六七八九十百千万亿",
  japanese: "あいうえおかきくけこさしすせそたちつてと",
  hindi: "अआइईउऊऋऌऍऑअंकखगघङचछجझञतथदधनपfबभमय",
  accents: accents
};

// --- Word List ---
const wordList = [
  'apple', 'banana', 'orange', 'grape', 'lemon', 'melon', 'peach', 'kiwi',
  'red', 'blue', 'green', 'yellow', 'purple', 'black', 'white', 'pink',
  'dog', 'cat', 'bird', 'fish', 'lion', 'tiger', 'bear', 'wolf',
  'house', 'tree', 'river', 'mountain', 'ocean', 'sun', 'moon', 'star',
  'car', 'bike', 'bus', 'train', 'boat', 'plane', 'road', 'sky',
  'happy', 'sad', 'angry', 'calm', 'brave', 'sharp', 'quick', 'slow',
  'book', 'pen', 'key', 'door', 'box', 'cup', 'hat', 'shoe', 'lake', 'fire',
  'ice', 'snow', 'wind', 'rain', 'leaf', 'seed', 'root', 'path', 'jump', 'run'
];

// --- Main Generate Function ---
function generate() {
  if (generatorMode.value === 'password') {
    generateCharacterPassword();
  } else {
    generatePassphrase();
  }
  copyButtonText.value = 'Copy'; // Reset copy button text
}

// UPDATED: Password logic restored
function generateCharacterPassword() {
  let charset = englishLowercase;
  if (includeUppercase.value) charset += englishUppercase;
  if (includeNumbers.value) charset += numbers;
  if (includeSymbols.value) charset += symbols;
  
  // RE-ADDED: Logic to add all languages
  if (includeOtherLanguages.value) {
    for (const lang in languageSets) {
      charset += languageSets[lang];
    }
  }

  let randomPassword = '';
  for (let i = 0; i < passwordLength.value; i++) {
    const randomIndex = Math.floor(Math.random() * charset.length);
    randomPassword += charset[randomIndex];
  }
  password.value = randomPassword;
}

function generatePassphrase() {
  let newPassphrase = [];
  for (let i = 0; i < passphraseWords.value; i++) {
    const randomIndex = Math.floor(Math.random() * wordList.length);
    newPassphrase.push(wordList[randomIndex]);
  }
  password.value = newPassphrase.join(passphraseSeparator.value);
}

async function copyToClipboard() {
  if (!password.value) return;
  try {
    await navigator.clipboard.writeText(password.value);
    copyButtonText.value = 'Copied!'; // Provide visual feedback
  } catch (err) {
    alert('Failed to copy password.');
  }
}
</script>

<style>
/* --- GLOBAL STYLES (NOT scoped) --- */
:root {
  --primary-color: #4f46e5; /* Indigo */
  --primary-hover: #4338ca;
  --text-dark: #111827;
  --text-medium: #4B5563;
  --text-light: #6B7280;
  --bg-light: #f9fafb;
  --border-color: #E5E7EB;
}

body {
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
  background-color: var(--bg-light);
  color: var(--text-medium);
  margin: 0;
  padding: 0;
}
</style>

<style scoped>
/* --- PAGE LAYOUT --- */
.container {
  max-width: 700px;
  margin: 0 auto;
  padding: 2rem 1.5rem 4rem 1.5rem;
}

.page-header {
  text-align: center;
  margin-bottom: 2rem;
}
.page-header h1 {
  color: var(--text-dark);
  font-size: 2.25rem;
  font-weight: 700;
  margin: 0 0 0.5rem 0;
}
.page-header p {
  font-size: 1.125rem;
  color: var(--text-light);
  margin: 0;
}

/* --- CARD STYLES --- */
.card {
  background: #ffffff;
  border: 1px solid var(--border-color);
  border-radius: 16px;
  padding: 2rem;
  margin-bottom: 2rem;
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.05), 0 2px 4px -2px rgba(0, 0, 0, 0.05);
}

.generator-tool {
  padding: 1.5rem;
}

/* --- MODE TOGGLE --- */
.mode-toggle {
  display: flex;
  justify-content: center;
  margin-bottom: 2.5rem;
  background-color: var(--bg-light);
  border-radius: 12px;
  padding: 6px;
  border: 1px solid var(--border-color);
}
.mode-toggle button {
  flex: 1;
  padding: 10px 15px;
  border: none;
  background-color: transparent;
  cursor: pointer;
  font-size: 0.95rem;
  color: var(--text-light);
  font-weight: 600;
  border-radius: 9px;
  transition: all 0.2s ease;
}
.mode-toggle button.active {
  background-color: #fff;
  color: var(--primary-color);
  box-shadow: 0 2px 4px rgba(0,0,0,0.05);
}

/* --- FORM CONTROLS --- */
.options-grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 1.75rem;
  margin-bottom: 2rem;
}
.control-group {
  display: flex;
  flex-direction: column;
  gap: 12px;
}
.control-group label {
  font-weight: 500;
  color: var(--text-dark);
}
.slider-group label span {
  font-weight: 600;
  color: var(--primary-color);
  background-color: #eef2ff;
  padding: 2px 8px;
  border-radius: 6px;
  font-size: 0.9rem;
}

.checkbox-group {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 15px;
}
.checkbox-group label {
  display: flex;
  align-items: center;
  font-size: 0.95rem;
  user-select: none;
  cursor: pointer;
  color: var(--text-medium);
}
.checkbox-group input[type="checkbox"] {
  margin-right: 10px;
  width: 18px;
  height: 18px;
  accent-color: var(--primary-color); /* Modern way to style checkboxes */
  cursor: pointer;
}

/* REMOVED .language-select */

.separator-input {
  width: 80px;
  padding: 12px 14px;
  border-radius: 8px;
  border: 1px solid var(--border-color);
  font-size: 0.95rem;
  background-color: #fff;
  color: var(--text-medium);
}


/* --- RANGE SLIDER --- */
input[type="range"] {
  -webkit-appearance: none;
  appearance: none;
  width: 100%;
  height: 8px;
  background: var(--border-color);
  border-radius: 5px;
  outline: none;
  cursor: pointer;
}
input[type="range"]::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 20px;
  height: 20px;
  background: var(--primary-color);
  border-radius: 50%;
  border: 2px solid white;
  box-shadow: 0 1px 3px rgba(0,0,0,0.1);
  cursor: pointer;
}
input[type="range"]::-moz-range-thumb {
  width: 20px;
  height: 20px;
  background: var(--primary-color);
  border-radius: 50%;
  border: 2px solid white;
  box-shadow: 0 1px 3px rgba(0,0,0,0.1);
  cursor: pointer;
}

/* --- PASSWORD DISPLAY (NEW) --- */
.password-display {
  display: flex;
  margin-bottom: 1.5rem;
  width: 100%;
}
.password-display input {
  flex-grow: 1;
  padding: 14px 16px;
  font-size: 1.25rem;
  font-family: 'Courier New', Courier, monospace;
  text-align: center;
  border: 1px solid var(--border-color);
  border-right: none;
  border-radius: 8px 0 0 8px;
  background: var(--bg-light);
  color: var(--text-dark);
  font-weight: 600;
  outline: none;
}
.password-display button {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 0 16px;
  background-color: var(--primary-color);
  color: white;
  border: 1px solid var(--primary-color);
  border-radius: 0 8px 8px 0;
  cursor: pointer;
  font-size: 0.95rem;
  font-weight: 600;
  transition: background-color 0.2s ease;
}
.password-display button:hover {
  background-color: var(--primary-hover);
}
.password-display button svg {
  width: 20px;
  height: 20px;
}

/* --- GENERATE BUTTON --- */
.generate-button {
  padding: 16px 25px;
  background-color: var(--primary-color);
  color: white;
  border: none;
  cursor: pointer;
  font-size: 1.1rem;
  font-weight: 600;
  border-radius: 10px;
  width: 100%;
  transition: all 0.2s ease;
  box-shadow: 0 2px 4px rgba(0,0,0,0.05);
}
.generate-button:hover {
  background-color: var(--primary-hover);
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0,0,0,0.05);
}

/* --- INFO CONTENT --- */
.info-content {
  color: var(--text-medium);
  line-height: 1.7;
}
.info-content h2, .info-content h3 {
  color: var(--text-dark);
  border-bottom: 1px solid var(--border-color);
  padding-bottom: 8px;
  margin-top: 2rem;
}
.info-content h2 {
  font-size: 1.75rem;
}
.info-content h3 {
  font-size: 1.25rem;
}
.info-content ul {
  padding-left: 20px;
}
.info-content li {
  margin-bottom: 0.5rem;
}
.info-content strong {
  color: var(--text-dark);
}

/* --- RESPONSIVE --- */
@media (max-width: 600px) {
  .container {
    padding: 1rem 1rem 3rem 1rem;
  }
  .card {
    padding: 1.5rem;
  }
  .page-header h1 {
    font-size: 1.75rem;
  }
  .page-header p {
    font-size: 1rem;
  }
  .checkbox-group {
    grid-template-columns: 1fr;
  }
  .password-display input {
    font-size: 1rem;
  }
  .password-display button {
    padding: 0 12px;
  }
  .password-display button span {
    display: none; /* Hide "Copy" text on mobile, show icon only */
  }
}
</style>