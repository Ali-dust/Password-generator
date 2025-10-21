<template>
  <div class="button-container">
    <div>
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
    <div>
      <label>
        <input type="checkbox" v-model="includeUppercase" /> Include Uppercase Letters
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
    
    <button @click="generatePassword">Generate Password</button>

    <!-- Display the generated password -->
    <div v-if="password" class="password-display">
      <textarea rows="4" cols="50" v-model="password" readonly> </textarea>
      <button @click="copyToClipboard">Copy to Clipboard</button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const passwordLength = ref(12); // Define password length (adjust as needed)
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

  // Add character sets based on user selection
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

  // Generate a random password
  let randomPassword = '';
  for (let i = 0; i < passwordLength.value; i++) {
    const randomIndex = Math.floor(Math.random() * charset.length);
    randomPassword += charset[randomIndex];
  }

  password.value = randomPassword;
}

async function copyToClipboard() {
  try {
    await navigator.clipboard.writeText(password.value); // Copy password to clipboard
    alert('Password copied to clipboard!');
  } catch (err) {
    alert('Failed to copy password. Please try again.');
  }
}
</script>

<style scoped>
.button-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
  margin: 0;
  padding: 0;
}

button {
  padding: 10px;
  background-color: #4CAF50;
  color: white;
  border: none;
  cursor: pointer;
}

button:hover {
  background-color: #45a049;
}

textarea[type="range"] {
  width: 200px;
}

label {
  margin: 5px 0;
}

/* Styling for the password display area */
.password-display {
  margin-top: 20px;
  display: flex;
  align-items: center;
}

.password-display input {
  padding: 10px;
  margin-right: 10px;
  font-size: 16px;
  width: 300px;
  text-align: center;
}

.password-display button {
  padding: 10px 15px;
  background-color: #2196F3;
  color: white;
  border: none;
  cursor: pointer;
}

.password-display button:hover {
  background-color: #1976D2;
}
</style>
