<template>
    <div class="p-grid">
        <div class="p-col-12">
            <div class="card">
                <Panel header="PUNTO DE VENTA (POS)" style="height: 100%">
                    <Toolbar class="p-mb-4">
                        <template v-slot:start>
                            <InputText type="text" size="40" placeholder="Nombre del producto..." v-model="productoItem.nomProducto" />
                            <InputText type="text" placeholder="Cantidad" v-model="productoItem.cantidad" />
                            <InputText type="text" placeholder="$ Precio U." v-model="productoItem.precioUnitario" />
                        </template>
                        <template v-slot:end>
                            <Button label="Registrar" icon="pi pi-plus" class="p-button-success p-mr-2" @click="registrarCompra" />
                        </template>
                    </Toolbar>
                    <br />

                    <DataTable
                        :value="tablaCompras"
                        v-model:selection="productoItem"
                        class="p-datatable-gridlines"
                        dataKey="cns"
                        :rows="5"
                        paginatorTemplate="FirstPageLink PrevPageLink PageLinks NextPageLink LastPageLink CurrentPageReport RowsPerPageDropdown"
                        :rowsPerPageOptions="[5, 10, 25]"
                        currentPageReportTemplate="Visualizando {last} de {totalRecords} productos"
                        style="margin-bottom: 20px"
                        :paginator="true"
                        responsiveLayout="scroll"
                    >
                        <Column field="cns" header="Cns" :sortable="true" style="width: 50px"></Column>
                        <Column field="nomProducto" header="Nombre del Producto" style="width: 370px"></Column>
                        <Column field="precioUnitario" header="Precio U." dataType="numeric" style="width: 80px">
                            <template #body="slotProps">
                                {{ formatoMoneda(slotProps.data.precioUnitario) }}
                            </template>
                        </Column>
                        <Column field="cantidad" header="Cant." style="width: 60px; text-align: center"></Column>
                        <Column field="precioParcial" header="Precio P." style="width: 80px">
                            <template #body="slotProps">
                                {{ formatoMoneda(slotProps.data.precioParcial) }}
                            </template></Column
                        >
                        <Column style="width: 140px">
                            <template #header> Acciones </template>
                            <template #body="slotProps">
                                <Button icon="pi pi-pencil" type="button" class="p-button-success p-mr-2 p-mb-1" @click="editarProducto(slotProps.data)" />
                                <Button icon="pi pi-trash" type="button" class="p-button-danger p-mb-1" @click="eliminarProducto(slotProps.data)"></Button>
                            </template>
                        </Column>
                    </DataTable>

                    <br />
                    <Toolbar class="p-mb-4">
                        <template v-slot:start> </template>
                        <template v-slot:end>
                            <label for="subtotal">Subtotal: </label>
                            <InputText type="text" placeholder="$ " :value="formatoMoneda(subtotalCompra)" disabled />

                            <label for="iva">IVA (16%): </label>
                            <InputText type="text" placeholder="$ " :value="formatoMoneda(ivaCompra)" disabled />

                            <label for="total">Total: </label>
                            <InputText type="text" placeholder="$ " :value="formatoMoneda(totalCompra)" disabled />
                        </template>
                    </Toolbar>
                </Panel>

                <Toast />
            </div>
        </div>

        <Dialog v-model:visible="visible" modal header="Detalle del Producto" :style="{ width: '50vw' }" :breakpoints="{ '1199px': '75vw', '575px': '90vw' }">
            <div class="formgroup-inline">
                <div class="field">
                    <label for="productName">Nombre del Producto:</label>
                    <InputText id="productName" v-model="product.name" />
                </div>
                <div class="field">
                    <label for="unitPrice">Precio Unitario:</label>
                    <InputText id="unitPrice" v-model="product.unitPrice" type="number" />
                </div>
                <div class="field">
                    <label for="quantity">Cantidad:</label>
                    <InputText id="quantity" v-model="product.quantity" type="number" />
                </div>
                <div class="field">
                    <label for="partialPrice">Precio Parcial:</label>
                    <InputText id="partialPrice" v-model="partialPrice" type="number" disabled />
                </div>
                <div class="field">
                    <Button label="Guardar" icon="pi pi-check" @click="saveAction()" style="margin-right: 10px" />
                    <Button label="Cancelar" icon="pi pi-times" @click="visible = false" class="p-button-secondary" />
                </div>
            </div>
        </Dialog>
        <Dialog v-model:visible="deleteDialogVisible" header="Confirmar Eliminación" :modal="true" :closable="false" :style="{ width: '30vw' }">
            <p>¿Estás seguro de que deseas eliminar el producto "{{ productToDelete?.nomProducto }}"?</p>
            <template #footer>
                <Button label="No" icon="pi pi-times" @click="deleteDialogVisible = false" class="p-button-text" />
                <Button label="Sí" icon="pi pi-check" @click="confirmDeleteProduct()" class="p-button-danger" />
            </template>
        </Dialog>
    </div>
</template>


<script>
import { ref, computed } from 'vue';
import Button from 'primevue/button';
import Dialog from 'primevue/dialog';
import InputText from 'primevue/inputtext';

export default {
    components: {
        Button,
        Dialog,
        InputText
    },
    data() {
        return {
            deleteDialogVisible: false,
            productToDelete: null,
            selectedProduct: null,
            tablaCompras: [
                { cns: 1, nomProducto: 'Impresora LaserJet Color', cantidad: 2, precioUnitario: 5200.0, precioParcial: 10400.0 },
                { cns: 2, nomProducto: 'Monitor LED 31 plg.', cantidad: 3, precioUnitario: 1700.0, precioParcial: 5100.0 }
            ],
            productoItem: {
                cns: null,
                nomProducto: null,
                cantidad: null,
                precioUnitario: null,
                precioParcial: null
            },
            visible: false
        };
    },
    setup() {
        const product = ref({
            name: '',
            unitPrice: 0,
            quantity: 0
        });

        const partialPrice = computed(() => {
            return (product.value.unitPrice * product.value.quantity).toFixed(2);
        });

        function saveAction() {
            console.log('Producto guardado', product.value);
            visible.value = false;
        }

        return {
            product,
            partialPrice,
            saveAction
        };
    },
    methods: {
        eliminarProducto(item) {
            this.productToDelete = item;
            this.deleteDialogVisible = true;
        },
        confirmDeleteProduct() {
            const index = this.tablaCompras.indexOf(this.productToDelete);
            if (index > -1) {
                this.tablaCompras.splice(index, 1);
                this.$toast.add({
                    severity: 'success',
                    summary: 'Producto Eliminado',
                    detail: 'El producto ha sido eliminado',
                    life: 3000
                });
            }
            
            this.productToDelete = null;
            this.deleteDialogVisible = false;
        },
        formatoMoneda(value) {
            if (value) return value.toLocaleString('en-US', { style: 'currency', currency: 'USD' });
            return;
        },
        registrarCompra() {
            if (!this.productoItem.nomProducto || !this.productoItem.cantidad || !this.productoItem.precioUnitario) {
                this.$toast.add({ severity: 'error', summary: 'Error', detail: 'Todos los campos son obligatorios', life: 3000 });
                return;
            }

            if (this.productoItem.cantidad <= 0 || this.productoItem.precioUnitario <= 0) {
                this.$toast.add({ severity: 'error', summary: 'Error', detail: 'La cantidad y el precio deben ser valores positivos', life: 3000 });
                return; 
            }
            const nextCns = this.tablaCompras.reduce((acc, item) => item.cns > acc ? item.cns : acc, 0) + 1;
            this.productoItem.cns = nextCns;

            this.productoItem.cns = this.tablaCompras.length + 1;
            this.productoItem.precioParcial = this.productoItem.precioUnitario * this.productoItem.cantidad;
            this.tablaCompras.push(this.productoItem);
            this.$toast.add({ severity: 'success', summary: 'Confirmación', detail: 'Nueva compra registrada', life: 3000 });
            this.productoItem = {
                cns: null,
                nomProducto: '',
                cantidad: null,
                precioUnitario: null,
                precioParcial: null
            };
        },
		editarProducto(item) {
            this.selectedProduct = item;
            this.product.name = item.nomProducto;
            this.product.unitPrice = item.precioUnitario;
            this.product.quantity = item.cantidad;
            this.visible = true;
        },
        saveAction() {
            if (this.selectedProduct) {
                const index = this.tablaCompras.findIndex((product) => product.cns === this.selectedProduct.cns);
                if (index !== -1) {
                    this.tablaCompras[index].nomProducto = this.product.name;
                    this.tablaCompras[index].precioUnitario = parseFloat(this.product.unitPrice);
                    this.tablaCompras[index].cantidad = parseInt(this.product.quantity);
                    this.tablaCompras[index].precioParcial = parseFloat(this.partialPrice);
                    this.visible = false;
                    this.selectedProduct = null;
                    this.$toast.add({ severity: 'success', summary: 'Producto Actualizado', detail: 'El producto ha sido actualizado correctamente', life: 3000 });
                }
            } else {
                console.error('No se seleccionó un producto para guardar.');
            }
        }
    },
    computed: {
        subtotalCompra() {
            return this.tablaCompras.reduce((acumulador, producto) => {
                return acumulador + Number(producto.precioParcial);
            }, 0);
        },
        ivaCompra() {
            return this.subtotalCompra * 0.16;
        },
        totalCompra() {
            return this.subtotalCompra + this.ivaCompra;
        }
    }
};
</script>