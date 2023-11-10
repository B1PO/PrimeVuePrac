<template>
    <div class="p-grid p-fluid">
      <div class="col-12 md:col-12">
        <div class="card">
          <h3>Buscar Empleado por ID</h3>
          <div class="flex align-items-center">
            <InputText placeholder="Ingrese ID del empleado" v-model="searchId" />
            <Button label="Buscar" class="ml-2" @click="buscarEmpleado" />
          </div>
          <div v-if="empleadoBuscado" class="card mt-3">
            <div class="flex align-items-center justify-content-between">
              <div class="flex align-items-center">
                <i class="pi pi-user" style="font-size: 2em; margin-right: .5em;"></i>
                <div>
                  <h4>{{ empleadoBuscado.employee_name }}</h4>
                  <p>Salario: {{ empleadoBuscado.employee_salary }}</p>
                  <p>Edad: {{ empleadoBuscado.employee_age }}</p>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="card">
          <h3>Consumir API REST Empleado</h3>
          <DataTable :value="empleados" paginator :rows="10" :sortField="sortField" :sortOrder="sortOrder">
            <Column field="employee_name" header="Nombre" sortable></Column>
            <Column field="employee_salary" header="Salario" sortable></Column>
            <Column field="employee_age" header="Edad" sortable></Column>
          </DataTable>
          <Button label="Cargar Empleados" @click="cargarEmpleados" />
        </div>
      </div>
      <Toast ref="toast" />
    </div>
  </template>
  
<script>
  import Button from 'primevue/button';
  import InputText from 'primevue/inputtext';
  import DataTable from 'primevue/datatable';
  import Column from 'primevue/column';
  import Toast from 'primevue/toast';
  import Icon from 'primeicons/primeicons.css';
  import axios from 'axios';
  
  export default {
    components: {
      Button,
      InputText,
      DataTable,
      Column,
      Toast,
      Icon
    },
    data() {
      return {
        empleados: [],
        sortField: '',
        sortOrder: 1,
        searchId: '',
        empleadoBuscado: null
      };
    },
    methods: {
      cargarEmpleados() {
        axios.get('https://dummy.restapiexample.com/api/v1/employees')
          .then(response => {
            this.empleados = response.data.data;
          })
          .catch(error => {
            this.handleError(error);
          });
      },
      buscarEmpleado() {
        if (!this.searchId) {
          this.mostrarToast('warn', 'Advertencia', 'Por favor ingrese un ID válido');
          return;
        }
        axios.get(`https://dummy.restapiexample.com/api/v1/employee/${this.searchId}`)
          .then(response => {
            this.empleadoBuscado = response.data.data;
          })
          .catch(error => {
            this.handleError(error);
          });
      },
      handleError(error) {
        let message = 'Error al realizar la solicitud';
        if (error.response) {
          switch (error.response.status) {
            case 429:
              message = 'Demasiadas solicitudes. Por favor, espera un momento antes de intentar de nuevo.';
              break;
            case 404:
              message = 'Empleado no encontrado.';
              break;
            default:
              message = 'Un error ha ocurrido al cargar los datos.';
              break;
          }
        }
        this.mostrarToast('error', 'Error de API', message);
        console.error('Error en la API:', error);
      },
      mostrarToast(severity, summary, detail) {
        this.$refs.toast.add({
          severity: severity,
          summary: summary,
          detail: detail
        });
      }
    }
  };
  </script>
  
  <style>
  .card {
    margin-bottom: 1em;
  }
  
  .pi {
    margin-right: .5em;
  }
  </style>
  
  