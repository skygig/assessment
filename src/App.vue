<script setup>
import { ref, computed, onMounted } from "vue";
import Login from "./components/Login.vue";
import DynamicTable from "./components/DynamicTable.vue";

const isAuthenticated = ref(false);
const users = ref([]);
const loading = ref(false);

// New State for "Search by Fields" UI
const fieldQuery = ref("firstName,lastName,age");
const filterText = ref("");
const sortField = ref("");
const sortOrder = ref("asc");

onMounted(() => {
  if (localStorage.getItem("auth_token")) {
    isAuthenticated.value = true;
    fetchUsers();
  }
});

const onLoginSuccess = (token) => {
  localStorage.setItem("auth_token", token);
  isAuthenticated.value = true;
  fetchUsers();
};

const handleLogout = () => {
  localStorage.removeItem("auth_token");
  isAuthenticated.value = false;
  users.value = [];
};

// Requirement 2: Dynamically calculate columns based on actual data
const columns = computed(() => {
  if (!users.value || users.value.length === 0) return [];
  return Object.keys(users.value[0]);
});

// Process data (Filter & Sort) in parent to control the table
const processedUsers = computed(() => {
  let result = [...users.value];

  // Filter
  if (filterText.value) {
    const lowerFilter = filterText.value.toLowerCase();
    result = result.filter((item) => {
      // Check any string field or specific fields
      return (
        (item.firstName &&
          item.firstName.toLowerCase().startsWith(lowerFilter)) ||
        (item.lastName && item.lastName.toLowerCase().startsWith(lowerFilter))
      );
    });
  }

  // Sort
  if (sortField.value && sortField.value !== "None") {
    result.sort((a, b) => {
      let valA = a[sortField.value];
      let valB = b[sortField.value];

      // Basic null handling
      if (valA === null || valA === undefined) valA = "";
      if (valB === null || valB === undefined) valB = "";

      if (valA < valB) return sortOrder.value === "asc" ? -1 : 1;
      if (valA > valB) return sortOrder.value === "asc" ? 1 : -1;
      return 0;
    });
  }

  return result;
});

const fetchUsers = async () => {
  loading.value = true;
  try {
    // Search by manipulating the 'select' projection
    const url = `https://dummyjson.com/users?select=${fieldQuery.value}`;

    const response = await fetch(url);
    const data = await response.json();
    users.value = data.users;
  } catch (err) {
    console.error(err);
  } finally {
    loading.value = false;
  }
};
</script>

<template>
  <div class="app-container">
    <Login v-if="!isAuthenticated" @login-success="onLoginSuccess" />

    <div v-else class="dashboard">
      <header>
        <h2>User Directory</h2>
        <button @click="handleLogout" class="logout-btn">Logout</button>
      </header>

      <!-- Search Card (Matching Example Image) -->
      <div class="card search-card">
        <div class="search-row">
          <input
            type="text"
            v-model="fieldQuery"
            placeholder="firstName,lastName,age"
            @keyup.enter="fetchUsers"
            class="field-input"
          />
          <button @click="fetchUsers" class="btn-primary search-btn">
            Search
          </button>
        </div>
        <div class="helper-text">
          Try changing fields: firstName,lastName,age,email,phone
        </div>
      </div>

      <!-- Controls Card (Filter & Sort) -->
      <div class="card controls-bar">
        <div class="control-group">
          <label>Filter by name:</label>
          <input v-model="filterText" placeholder="Start typing..." />
        </div>

        <div class="control-group">
          <label>Sort by:</label>
          <select v-model="sortField">
            <option value="">None</option>
            <option v-for="col in columns" :key="col" :value="col">
              {{ col }}
            </option>
          </select>

          <select v-model="sortOrder">
            <option value="asc">Ascending</option>
            <option value="desc">Descending</option>
          </select>
        </div>
      </div>

      <DynamicTable
        :data="processedUsers"
        :columns="columns"
        :loading="loading"
      />
    </div>
  </div>
</template>

<style>
/* Global */
body {
  font-family: "Segoe UI", sans-serif;
  background-color: #f4f7f6;
  margin: 0;
}
.dashboard {
  max-width: 1000px;
  margin: 0 auto;
  padding: 20px;
}

/* Header */
header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1.5rem;
}
.logout-btn {
  background: transparent;
  border: 1px solid #dc3545;
  color: #dc3545;
  padding: 6px 12px;
  border-radius: 4px;
  cursor: pointer;
}

/* Cards */
.card {
  background: white;
  padding: 1.5rem;
  border-radius: 8px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.05);
  margin-bottom: 1.5rem;
}

/* Search Section */
.search-row {
  display: flex;
  gap: 10px;
  margin-bottom: 10px;
}
.field-input {
  flex-grow: 1;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 1rem;
}
.search-btn {
  padding: 10px 25px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 1rem;
}
.search-btn:hover {
  background-color: #0056b3;
}
.helper-text {
  text-align: center;
  color: #6c757d;
  font-size: 0.9rem;
}

/* Controls Section */
.controls-bar {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  justify-content: space-between;
  gap: 20px;
}
.control-group {
  display: flex;
  align-items: center;
  gap: 10px;
}
.control-group label {
  font-weight: 600;
  color: #333;
}
.control-group input,
.control-group select {
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 4px;
}
</style>
