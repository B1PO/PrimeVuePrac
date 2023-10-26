<script>
import InputText from 'primevue/inputtext';
import Calendar from 'primevue/calendar';
import Button from 'primevue/button';

export default {
  components: {
    InputText,
    Calendar,
    Button
  },
  data() {
    return {
      nombre: "",
      apellidoPaterno: "",
      apellidoMaterno: "",
      fechaNacimiento: null,
      rfc: ""
    };
  },
  computed: {
    nombreVacio() {
      return !this.nombre || this.nombre.trim() === '';
    }
    // apellidoPaternoVacio() {
    //   return this.apellidoPaterno.trim() === '';
    // },
    // apellidoMaternoVacio() {
    //   return this.apellidoMaterno.trim() === '';
    // },
    // fechaNacimientoVacia() {
    //   return !this.fechaNacimiento;
    // }
  },
  methods: {
    generarRFC() {
      const iniciales = this.apellidoPaterno.substring(0,2) + this.apellidoMaterno.charAt(0) + this.nombre.charAt(0);

      const fecha = this.fechaNacimiento 
        ? this.fechaNacimiento.getFullYear().toString().slice(-2) + 
          (this.fechaNacimiento.getMonth() + 1).toString().padStart(2, '0') + 
          this.fechaNacimiento.getDate().toString().padStart(2, '0')
        : "";

      this.rfc = (iniciales + fecha).toUpperCase();
    }
  }
};
</script>

  <template>
    <div class="p-grid p-fluid">
      <div class="col-12 md:col-12">
        <div class="card p-fluid">
          <h3 class="mr-8">GENERAR EL RFC DE UNA PERSONA</h3>

          <div class="p-fluid formgrid grid mt-5">
          <div class="flex flex-column gap-2 col-4">
            <label for="name1">Nombre(s):</label>
            <InputText id="name1" type="text" v-model="nombre" />
            <span v-if="nombreVacio" class="error-message">Se requiere el nombre</span>
          </div>
          <div class="flex flex-column gap-2 col-4">
            <label for="appat1">Apellido paterno</label>
            <InputText id="appat1" type="text" v-model="apellidoPaterno" />
          </div>
          <div class="flex flex-column gap-2 col-4">
            <label for="apmat1">Apellido materno</label>
            <InputText id="apmat1" type="text" v-model="apellidoMaterno" />
          </div>
          </div>

          <div class="field">
            <label for="fechaNacimiento">Fecha de nacimiento <span v-if="fechaNacimientoInvalida" class="text-danger">Campo requerido</span></label>
            <Calendar id="fechaNacimiento" v-model="fechaNacimiento" dateFormat="dd/mm/yy" :showIcon="true" />
          </div>
          <div class="field">
            <label for="rfc">RFC</label>
            <InputText id="rfc" type="text" v-model="rfc" readonly placeholder="RFC generado" />
          </div>
          <div class="field flex justify-end"> 
            <div class="w-12/12">
              <Button label="Generar RFC" @click="generarRFC" />
            </div>
          </div> 

        </div>
      </div>
      <Toast position="top-right"></Toast>
    </div>
  </template>