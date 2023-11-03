<script setup>
import { onMounted, reactive, ref, watch } from 'vue';
import ProductService from '@/service/ProductService';
import { useLayout } from '@/layout/composables/layout';

const { isDarkTheme } = useLayout();

const products = ref(null);
const lineData = reactive({
    labels: ['January', 'February', 'March', 'April', 'May', 'June', 'July'],
    datasets: [
        {
            label: 'First Dataset',
            data: [65, 59, 80, 81, 56, 55, 40],
            fill: false,
            backgroundColor: '#2f4860',
            borderColor: '#2f4860',
            tension: 0.4
        },
        {
            label: 'Second Dataset',
            data: [28, 48, 40, 19, 86, 27, 90],
            fill: false,
            backgroundColor: '#00bb7e',
            borderColor: '#00bb7e',
            tension: 0.4
        }
    ]
});
const items = ref([
    { label: 'Add New', icon: 'pi pi-fw pi-plus' },
    { label: 'Remove', icon: 'pi pi-fw pi-minus' }
]);
const lineOptions = ref(null);
const productService = new ProductService();

onMounted(() => {
    productService.getProductsSmall().then((data) => (products.value = data));
});

const formatCurrency = (value) => {
    return value.toLocaleString('en-US', { style: 'currency', currency: 'USD' });
};
const applyLightTheme = () => {
    lineOptions.value = {
        plugins: {
            legend: {
                labels: {
                    color: '#495057'
                }
            }
        },
        scales: {
            x: {
                ticks: {
                    color: '#495057'
                },
                grid: {
                    color: '#ebedef'
                }
            },
            y: {
                ticks: {
                    color: '#495057'
                },
                grid: {
                    color: '#ebedef'
                }
            }
        }
    };
};

const applyDarkTheme = () => {
    lineOptions.value = {
        plugins: {
            legend: {
                labels: {
                    color: '#ebedef'
                }
            }
        },
        scales: {
            x: {
                ticks: {
                    color: '#ebedef'
                },
                grid: {
                    color: 'rgba(160, 167, 181, .3)'
                }
            },
            y: {
                ticks: {
                    color: '#ebedef'
                },
                grid: {
                    color: 'rgba(160, 167, 181, .3)'
                }
            }
        }
    };
};

watch(
    isDarkTheme,
    (val) => {
        if (val) {
            applyDarkTheme();
        } else {
            applyLightTheme();
        }
    },
    { immediate: true }
);
</script>

<template>
  <div class="grid">
    <div class="col-12">
      <div class="card">
        <div class="p-fluid formgrid grid mt-5">
            <img src="https://www.unach.mx/images/logo_unach_p_fondo_claro.png" alt="unach logo" class="col-3 rounded-full">
        </div>
        <h2 class="text-3xl font-bold mb-2">Universidad Autónoma de Chiapas</h2>

        <h3 class="text-xl font-semibold mb-2">Desarrollo de Aplicaciones Web y Móviles - Séptimo Semestre "D"</h3>
        <p class="mb-4">
          Bienvenido a la vitrina de evidencias de la materia de Desarrollo de Aplicaciones Web y Móviles de nuestro equipo. Aquí presentamos las prácticas realizadas, demostrando la aplicación de los conocimientos adquiridos en un entorno real y dinámico.
        </p>
      </div>
    </div>

    <div class="col-12 lg:col-8">
      <div class="card">
        <h3 class="text-xl font-semibold mb-2">Sobre los Estudiantes</h3>
        <div class="grid">
          <div class="col-12 md:col-6">
            <div class="card p-4 text-center">
              <img src="https://avatars.githubusercontent.com/u/82168337?v=4" alt="Emilia Zuñiga Losada" class="mb-3 max-w-full rounded-full">
              <h4 class="font-semibold">Emilia Zuñiga Losada</h4>
              <p>Matrícula: B200152</p>
            </div>
          </div>
          <div class="col-12 md:col-6">
            <div class="card p-4 text-center">
              <img src="https://avatars.githubusercontent.com/u/86985483?v=4" alt="Pedro Octavio Culebro Prado" class="mb-3 max-w-full rounded-full">
              <h4 class="font-semibold">Pedro Octavio Culebro Prado</h4>
              <p>Matrícula: B200227</p>
            </div>
          </div>
        </div>
      </div>
      
    </div>

    <div class="col-12 lg:col-4">
      <div class="card">
        <h3 class="text-xl font-semibold mb-2">Nuestras Prácticas</h3>
        <p>
          Cada práctica presentada aquí incorpora un conjunto de habilidades técnicas y conceptos de diseño que reflejan nuestra metodología de aprendizaje basada en proyectos. Desde el manejo de componentes de PrimeVUE hasta la creación de interfaces de usuario interactivas y reactivas, transformando la teoría en aplicaciónes listas para utilizar.
        </p>
          </div>
    </div>
    
    <div class="col-12 lg:col-8">
      <div class="card">
            <h3 class="text-xl font-semibold mb-2">Visite el repositorio</h3>
            <a href="https://github.com/B1PO/PrimeVuePrac" target="_blank">
                <img src="https://i0.wp.com/puresourcecode.com/wp-content/uploads/2022/11/github-wallpaper-scaled.jpeg?fit=2560%2C1440&ssl=1" alt="Prime Blocks" class="w-full mt-3 rounded-full" />
            </a>
        
        
        <!-- Aquí puedes agregar más contenido relacionado con los proyectos o prácticas -->
      </div>
    </div>
  </div>
</template>

<style scoped>
.rounded-full {
  border-radius: 5%;
}
</style>