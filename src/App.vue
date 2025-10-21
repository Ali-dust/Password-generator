<template>
  <main class="container">

    <header class="page-header">
      <h1>Secure Random Password Generator</h1>
      <p>Create strong, unique, and unguessable passwords in one click.</p>
    </header>

    <section class="generator-tool">
      
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
      
      <button @click="generatePassword" class="generate-button">Generate Password</button>

      <div v-if="password" class="password-display">
        <textarea rows="3" v-model="password" readonly> </textarea>
        <button @click="copyToClipboard">Copy to Clipboard</button>
      </div>
    </section>

    <AdDisplay />

    <section class="info-content">
      <h2>Why Use a Secure Password Generator?</h2>
      <p>
        In today's digital world, a strong password is your first line of defense. Using weak or repeated passwords like "123456" or "password" makes it easy for attackers to access your private accounts. 
      </p>
      <p>
        A password generator creates a long, complex, and random string of characters that is nearly impossible for a human or computer to guess. This tool helps you create unique, secure passwords for every account, which dramatically increases your personal security.
      </p>

      <h3>Is This Tool Secure and Private?</h3>
      <p>
        <strong>Yes. This tool is 100% private.</strong> All passwords are generated *only* in your web browser. No passwords, settings, or data are ever sent to a server, saved, or logged. You can even disconnect from the internet after loading the page, and it will still work perfectly.
      </p>

      <h3>What Makes a Strong Password?</h3>
      <ul>
        <li><strong>Length:</strong> Longer is always better. We recommend at least 12-16 characters.</li>
        <li><strong>Complexity:</strong> A good password mixes uppercase letters, lowercase letters, numbers, and symbols.</li>
        <li><strong>Uniqueness:</strong> Never reuse passwords. Every website should have its own unique password.</li>
      </ul>
    </section>

  </main>
</template>

<script setup>
import { ref } from 'vue';
import { useHead } from '@vueuse/head'; // <-- 1. IMPORT useHead
import AdDisplay from './components/AdDisplay.vue'; // <-- 2. IMPORT Ad component

// --- 3. ADD SEO METADATA ---
useHead({
  title: 'Secure Random Password Generator - Free & Simple Tool',
  meta: [
    {
      name: 'description',
      content: 'Generate strong, secure, and random passwords for all your accounts. Free, simple, and works 100% in your browser.'
    },
  ],
});

// --- Your existing code (all good!) ---
const passwordLength = ref(12);
const password = ref('');
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


function generatePassword() {
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
.container {
  max-width: 800px;
  margin: 0 auto; /* Centers the content on the page */
  padding: 1rem 1.5rem 3rem 1.5rem; /* Add padding: top, sides, bottom */
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
  padding: 1.5rem;
  margin: 2rem 0;
  display: flex;
  flex-direction: column;
  align-items: center; /* Keeps the tool's contents centered */
}

.control-group {
  margin-bottom: 1rem;
}

.checkbox-group {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px 20px;
  margin: 1rem 0;
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

/* --- ADJUSTED ORIGINAL STYLES --- */
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
}

.generate-button:hover {
  background-color: #45a049;
}

input[type="range"] {
  width: 220px;
  margin-left: 10px;
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
}

.password-display textarea {
  width: 95%;
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
}

.password-display button:hover {
  background-color: #1976D2;
}

/* --- REMOVED ORIGINAL STYLE --- */
/*
.button-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
  margin: 0;
  padding: 0;
}
*/

/* --- RESPONSIVE STYLE --- */
@media (max-width: 600px) {
  .checkbox-group {
    grid-template-columns: 1fr;
  }
}
</style>