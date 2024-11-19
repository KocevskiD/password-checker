<template>
  <div class="register-wrapper">
    <div class="register">
      <form @submit.prevent="checkPassword">
        <div class="input-group">
          <label for="username">Username:</label>
          <input type="text" id="username" v-model="username" placeholder="Enter your username" />
        </div>
        <div class="input-group">
          <label for="password">Password:</label>
          <input type="password" id="password" v-model="password" placeholder="Enter your password" />
        </div>
        <button type="submit">Check Password</button>
      </form>
      <div v-if="feedback">
        <h2>Password Strength: {{ strength }}</h2>
        <p>Score: {{ score }}/10</p>
        <ul>
          <li v-for="(msg, index) in feedback" :key="index">{{ msg }}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import zxcvbn from "zxcvbn";

export default {
  name: "AppRegister",
  data() {
    return {
      username: "",
      password: "",
      feedback: null,
      score: 0,
      strength: "",
    };
  },
  methods: {
    checkPassword() {
      const commonPasswords = ["password", "12345", "qwerty", "admin", "letmein"];
      const password = this.password;
      const username = this.username;

      let feedbackMessages = [];
      let score = 0;

      if (password.length >= 8) {
        score += 2;
      } else {
        feedbackMessages.push("Password should be at least 8 characters long.");
      }

      const hasLower = /[a-z]/.test(password);
      const hasUpper = /[A-Z]/.test(password);
      const hasDigit = /\d/.test(password);
      const hasSpecial = /[!@#$%^&*(),.?":{}|<>]/.test(password);

      if (hasLower) score++;
      if (hasUpper) score++;
      if (hasDigit) score++;
      if (hasSpecial) score++;

      if(password === ''){
        feedbackMessages.push("Password should not be empty.")
      }

      if (!(hasLower && hasUpper && hasDigit && hasSpecial)) {
        feedbackMessages.push(
          "Password should contain at least one lowercase letter, one uppercase letter, one digit, and one special character."
        );
      }

      const isCommon = commonPasswords.some((common) =>
        password.toLowerCase().includes(common)
      );
      if (isCommon) {
        feedbackMessages.push("Avoid using common words or phrases in your password.");
      } else {
        score += 2;
      }

      if (password.toLowerCase() === username.toLowerCase()) {
        feedbackMessages.push("Password should not be identical to your username.");
      } else {
        score += 1;
      }

      const zxcvbnResult = zxcvbn(password);
      score += zxcvbnResult.score;

      let strengthLabel = "Weak";
      if (score >= 8) strengthLabel = "Strong";
      else if (score >= 5) strengthLabel = "Moderate";

      this.feedback = feedbackMessages.length
        ? feedbackMessages
        : ["Your password is strong!"];
      this.score = Math.min(score, 10);
      this.strength = strengthLabel;
    },
  },
};
</script>

<style scoped>
.register-wrapper{
  height: 100vh;
  position: relative;
}
.register {
  max-width: 400px;
  margin: auto auto;
  font-family: Arial, sans-serif;
  border: 1px solid #0056b3;
  padding: 15px;
  border-radius: 5px;
  position: absolute;
  left: 50%;
  top: 40%;
  transform: translate(-50%, -50%);
}
.input-group {
  margin-bottom: 15px;
}
input {
  width: 100%;
  padding: 8px;
  margin-top: 5px;
  box-sizing: border-box;
}
button {
  padding: 10px 20px;
  background-color: #007bff;
  color: white;
  border: none;
  cursor: pointer;
}
button:hover {
  background-color: #0056b3;
}
.feedback {
  margin-top: 20px;
}
h2 {
  margin: 10px 0;
}
</style>
