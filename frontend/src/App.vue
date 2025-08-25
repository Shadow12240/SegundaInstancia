<template>
  <div class="container">
    <h1>Estacionamiento - Registro de Empleados</h1>

    <div class="controls">
      <input v-model="search" placeholder="Buscar por nombre..." />
      <select v-model="filter">
        <option value="">Todos</option>
        <option value="automóvil">Automóvil</option>
        <option value="moto">Moto</option>
      </select>
    </div>

    <form @submit.prevent="addEmployee">
      <input v-model="newEmployee.nombre" placeholder="Nombre" required />
      <input v-model="newEmployee.area" placeholder="Área" required />
      <select v-model="newEmployee.tipo" required>
        <option value="automóvil">Automóvil</option>
        <option value="moto">Moto</option>
      </select>
      <input v-model="newEmployee.placa" placeholder="Placa" required />
      <input v-model="newEmployee.color" placeholder="Color" required />
      <button type="submit">Agregar</button>
    </form>

    <table>
      <thead>
        <tr>
          <th>Nombre</th>
          <th>Área</th>
          <th>Tipo</th>
          <th>Placa</th>
          <th>Color</th>
          <th>Acciones</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="emp in filteredEmployees" :key="emp.id">
          <td><input v-model="emp.nombre" /></td>
          <td><input v-model="emp.area" /></td>
          <td>
            <select v-model="emp.tipo">
              <option value="automóvil">Automóvil</option>
              <option value="moto">Moto</option>
            </select>
          </td>
          <td><input v-model="emp.placa" /></td>
          <td><input v-model="emp.color" /></td>
          <td>
            <button @click="updateEmployee(emp)">Guardar</button>
            <button @click="deleteEmployee(emp.id)">Eliminar</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      employees: [],
      search: '',
      filter: '',
      newEmployee: {
        nombre: '',
        area: '',
        tipo: 'automóvil',
        placa: '',
        color: ''
      }
    }
  },
  computed: {
    filteredEmployees() {
      return this.employees.filter(emp => {
        const matchSearch = emp.nombre.toLowerCase().includes(this.search.toLowerCase())
        const matchFilter = this.filter ? emp.tipo === this.filter : true
        return matchSearch && matchFilter
      })
    }
  },
  methods: {
    async fetchEmployees() {
      const res = await axios.get('http://localhost:3000/employees')
      this.employees = res.data
    },
    async addEmployee() {
      const res = await axios.post('http://localhost:3000/employees', this.newEmployee)
      this.employees.push(res.data)
      this.newEmployee = { nombre: '', area: '', tipo: 'automóvil', placa: '', color: '' }
    },
    async updateEmployee(emp) {
      await axios.put(`http://localhost:3000/employees/${emp.id}`, emp)
      alert('Empleado actualizado')
    },
    async deleteEmployee(id) {
      await axios.delete(`http://localhost:3000/employees/${id}`)
      this.employees = this.employees.filter(e => e.id !== id)
    }
  },
  mounted() {
    this.fetchEmployees()
  }
}
</script>

<style>
.container {
  width: 80%;
  margin: auto;
  font-family: Arial, sans-serif;
}
h1 {
  text-align: center;
}
.controls {
  margin: 10px 0;
  display: flex;
  gap: 10px;
}
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}
table, th, td {
  border: 1px solid #ddd;
}
th, td {
  padding: 8px;
  text-align: center;
}
form {
  display: flex;
  gap: 10px;
  margin: 20px 0;
}
button {
  cursor: pointer;
}
</style>
