<script setup>
defineProps(["data", "columns", "loading"]);
</script>

<template>
  <div class="table-responsive">
    <div v-if="loading" class="loading">Loading data...</div>

    <table v-else-if="data.length > 0">
      <thead>
        <tr>
          <!-- Headers are passed from parent -->
          <th v-for="key in columns" :key="key">
            {{
              key
                .replace(/([A-Z])/g, " $1")
                .replace(/^./, (str) => str.toUpperCase())
            }}
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(row, index) in data" :key="row.id || index">
          <td v-for="key in columns" :key="key">{{ row[key] }}</td>
        </tr>
      </tbody>
    </table>

    <div v-else class="no-data">No users found.</div>
  </div>
</template>

<style scoped>
.table-responsive {
  overflow-x: auto;
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
}
table {
  width: 100%;
  border-collapse: collapse;
  min-width: 600px;
}
th,
td {
  text-align: left;
  padding: 12px 15px;
  border-bottom: 1px solid #ddd;
}
th {
  background-color: #f8f9fa;
  font-weight: 600;
  color: #333;
}
.no-data,
.loading {
  text-align: center;
  padding: 2rem;
  color: #777;
}
</style>
