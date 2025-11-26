<script setup>
import { ref } from "vue";

const emit = defineEmits(["login-success"]);
const username = ref("");
const password = ref("");
const error = ref("");

const handleLogin = () => {
  if (username.value === "admin" && password.value === "admin123") {
    const token = "test-token-" + Math.random().toString(36).substr(2);
    emit("login-success", token);
  } else {
    error.value = "Invalid credentials. Try admin / admin123";
  }
};
</script>

<template>
  <div class="login-wrapper">
    <div class="login-card">
      <h2>Login</h2>
      <form @submit.prevent="handleLogin">
        <div class="form-group">
          <label for="username">Username</label>
          <input
            type="text"
            id="username"
            v-model="username"
            placeholder="admin"
            required
          />
        </div>
        <div class="form-group">
          <label for="password">Password</label>
          <input
            type="password"
            id="password"
            v-model="password"
            placeholder="admin123"
            required
          />
        </div>
        <button type="submit" class="btn-primary">Login</button>
        <p v-if="error" class="error-msg">{{ error }}</p>
      </form>
      <div class="hint">(Hint: admin / admin123)</div>
    </div>
  </div>
</template>

<style scoped>
.login-wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
.login-card {
  background: white;
  padding: 2rem;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  width: 100%;
  max-width: 400px;
  text-align: center;
}
.form-group {
  margin-bottom: 1rem;
  text-align: left;
}
.hint {
  margin-top: 15px;
  font-size: 0.8rem;
  color: #666;
}
.error-msg {
  color: #e74c3c;
  margin-top: 10px;
  font-size: 0.9rem;
}
input {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  box-sizing: border-box;
}
.btn-primary {
  width: 100%;
  padding: 10px;
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
.btn-primary:hover {
  background-color: #3aa876;
}
</style>
