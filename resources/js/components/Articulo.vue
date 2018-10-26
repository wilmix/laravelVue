<template>
    <!-- Contenido Principal -->
    <main class="main">
        <!-- Breadcrumb -->
        <ol class="breadcrumb">
            <li class="breadcrumb-item">Home</li>
            <li class="breadcrumb-item"><a href="#">Escritorio</a></li>
            <li class="breadcrumb-item active">Articulo</li>
        </ol>
        <div class="container-fluid">
            <!-- Ejemplo de tabla Listado -->
            <div class="card">
                <div class="card-header">
                    <i class="fa fa-align-justify"></i> Articulos
                    <button type="button" @click="abrirModal('articulo', 'registrar')" class="btn btn-secondary">
                        <i class="icon-plus"></i>&nbsp;Nuevo
                    </button>
                </div>
                <div class="card-body">
                    <div class="form-group row">
                        <div class="col-md-6">
                            <div class="input-group">
                                <select class="form-control col-md-3" v-model="criterio">
                                    <option value="nombre">Nombre</option>
                                    <option value="descripcion">Descripción</option>
                                </select>
                                <input type="text" @keyup.enter="listarArticulo(1,buscar,criterio)" v-model="buscar" class="form-control" placeholder="Texto a buscar">
                                <button type="submit" @keyup.enter="listarArticulo(1,buscar,criterio)" class="btn btn-primary"><i class="fa fa-search"></i> Buscar</button>
                            </div>
                        </div>
                    </div>
                    <table class="table table-bordered table-striped table-sm">
                        <thead>
                            <tr>
                                <th>Opciones</th>
                                <th>Codigo</th>
                                <th>Nombre</th>
                                <th>Categoria</th>
                                <th>Precio Venta</th>
                                <th>Stock</th>
                                <th>Descripción</th>
                                <th>Estado</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="articulo in arrayArticulo" :key="articulo.id">
                                <td>
                                    <button type="button" @click="abrirModal('articulo', 'actualizar', articulo)" class="btn btn-warning btn-sm">
                                        <i class="icon-pencil"></i>
                                    </button> &nbsp;
                                    <template v-if="articulo.condicion">
                                        <button type="button" @click="desactivarArticulo(articulo.id, articulo.nombre)" class="btn btn-danger btn-sm">
                                            <i class="icon-trash"></i>
                                        </button>
                                    </template>
                                    <template v-else>
                                        <button type="button" @click="activarArticulo(articulo.id)" class="btn btn-info btn-sm">
                                            <i class="icon-check"></i>
                                        </button>
                                    </template>
                                    
                                </td>
                                <td v-text="articulo.codigo"></td>
                                <td v-text="articulo.nombre"></td>
                                <td v-text="articulo.nombre_categoria"></td>
                                <td v-text="articulo.precio_venta"></td>
                                <td v-text="articulo.stock"></td>
                                <td v-text="articulo.descripcion"></td>
                                <td>
                                    <div v-if="articulo.condicion == 0">
                                        <span class="badge badge-danger">Inactivo</span>
                                    </div>
                                    <div v-else>
                                        <span class="badge badge-success">Activo</span>
                                    </div>
                                    
                                </td>
                            </tr>
                        </tbody>
                    </table>
                   <nav>
                        <ul class="pagination">
                            <li class="page-item" v-if="pagination.current_page > 1">
                                <a class="page-link" href="#" @click.prevent="cambiarPagina(pagination.current_page - 1,buscar,criterio)">Ant</a>
                            </li>
                            <li class="page-item" v-for="page in pagesNumber" :key="page" :class="[page == isActived ? 'active' : '']">
                                <a class="page-link" href="#" @click.prevent="cambiarPagina(page,buscar,criterio)" v-text="page"></a>
                            </li>
                            <li class="page-item" v-if="pagination.current_page < pagination.last_page">
                                <a class="page-link" href="#" @click.prevent="cambiarPagina(pagination.current_page + 1,buscar,criterio)">Sig</a>
                            </li>
                        </ul>
                    </nav>
                </div>
            </div>
            <!-- Fin ejemplo de tabla Listado -->
        </div>
        <!--Inicio del modal agregar/actualizar-->
        <div class="modal fade" tabindex="-1" :class="{'mostrar' : modal}" role="dialog" aria-labelledby="myModalLabel" style="display: none;" aria-hidden="true">
            <div class="modal-dialog modal-primary modal-lg" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <h4 class="modal-title" v-text="tituloModal"></h4>
                        <button type="button" class="close" @click="cerrarModal()" aria-label="Close">
                            <span aria-hidden="true">×</span>
                        </button>
                    </div>
                    <div class="modal-body">
                        <form action="" method="post" enctype="multipart/form-data" class="form-horizontal">
                            <div class="form-group row">
                                <label class="col-md-3 form-control-label" for="text-input">Categoria</label>
                                <div class="col-md-9">
                                    <select class="form-control" v-model="idcategoria">
                                        <option value="0" disabled> Seleccione </option>
                                        <option v-for="categoria in arrayCategoria" 
                                                :key="categoria.id"
                                                :value="categoria.id"
                                                v-text="categoria.nombre">
                                        </option>
                                    </select>
                                </div>
                            </div>
                            <div class="form-group row">
                                <label class="col-md-3 form-control-label" for="text-input">Codigo</label>
                                <div class="col-md-9">
                                    <input type="text" v-model="codigo" class="form-control" placeholder="Codigo de Barras">
                                </div>
                            </div>
                            <div class="form-group row">
                                <label class="col-md-3 form-control-label" for="text-input">Nombre</label>
                                <div class="col-md-9">
                                    <input type="text" v-model="nombre" class="form-control" placeholder="Nombre de articulo">
                                </div>
                            </div>
                            <div class="form-group row">
                                <label class="col-md-3 form-control-label" for="text-input">Precio Venta</label>
                                <div class="col-md-9">
                                    <input type="number" v-model="precio_venta" class="form-control">
                                </div>
                            </div>
                            <div class="form-group row">
                                <label class="col-md-3 form-control-label" for="text-input">Stock</label>
                                <div class="col-md-9">
                                    <input type="number" v-model="stock" class="form-control">
                                </div>
                            </div>
                            <div class="form-group row">
                                <label class="col-md-3 form-control-label" for="email-input">Descripción</label>
                                <div class="col-md-9">
                                    <input type="text" v-model="descripcion" class="form-control" placeholder="Nombre Categoria">
                                </div>
                            </div>
                            <div v-show="errorArticulo" class="grom-group row div-error">
                                <div class="text-center text-error">
                                    <div v-for="error in errorMostrarMsjArticulo" :key="error" v-text="error">
                                    </div>
                                </div>
                            </div>
                        </form>
                    </div>
                    <div class="modal-footer">
                        <button type="button" @click="cerrarModal()" class="btn btn-secondary">Cerrar</button>
                        <button type="button" v-if="tipoAccion == 1" class="btn btn-primary" @click="registrarArticulo()">Guardar</button>
                        <button type="button" v-if="tipoAccion == 2" class="btn btn-primary" @click="actualizarArticulo()">Modificar</button>

                    </div>
                </div>
                <!-- /.modal-content -->
            </div>
            <!-- /.modal-dialog -->
        </div>
        <!--Fin del modal-->
    </main>
    <!-- /Fin del contenido principal -->
</template>

<script>
    export default {
        data(){
            return{
                nombre: '',
                descripcion: '',
                arrayArticulo:[],
                tipoAccion: 0,
                errorArticulo: 0,
                errorMostrarMsjArticulo: [],
                articulo_id: 0,
                idcategoria: 0,
                arrayCategoria:[],
                nombre_categoria: '',
                codigo: '',
                precio_venta: 0,
                stock: 0,
                criterio: 'nombre',
                modal: 0,
                tituloModal: '',
                buscar: '',
                pagination:{
                    'total'        : 0,
                    'current_page' : 0,
                    'per_page'     : 0,
                    'last_page'    : 0,
                    'from'         : 0,
                    'to'           : 0,
                },
                offset: 3,
            }
        },
        computed:{
             isActived: function(){
                return this.pagination.current_page;
            },
            //Calcula los elementos de la paginación
            pagesNumber: function() {
                if(!this.pagination.to) {
                    return [];
                }
                
                let from = this.pagination.current_page - this.offset; 
                if(from < 1) {
                    from = 1;
                }

                let to = from + (this.offset * 2); 
                if(to >= this.pagination.last_page){
                    to = this.pagination.last_page;
                }  

                let pagesArray = [];
                while(from <= to) {
                    pagesArray.push(from);
                    from++;
                }
                return pagesArray;             

            }
        },
        methods:{
            listarArticulo(page,buscar,criterio){
                let me = this
                let url = '/articulo?page=' + page + '&buscar=' + buscar + '&criterio=' + criterio
                axios.get(url)
                .then(function (response) {
                    let respuesta = response.data
                    me.arrayArticulo = respuesta.articulos.data
                    me.pagination = respuesta.pagination
                })
                .catch(function (error) {
                    console.log(error);
                })
            },
            selectCategoria(){
                let me = this
                let url = '/categoria/selectCategoria'
                axios.get(url)
                .then(function (response) {
                    let respuesta = response.data
                    me.arrayCategoria = respuesta.categorias
                })
                .catch(function (error) {
                    console.log(error);
                })
            },
            registrarArticulo(){
                if (this.validarArticulo()) {
                    return
                }
                let me = this
                axios.post('/articulo/registrar', {
                    'idcategoria': this.idcategoria,
                    'codigo': this.codigo,
                    'nombre': this.nombre,
                    'stock': this.stock,
                    'precio_venta': this.precio_venta,
                    'descripcion': this.descripcion
                })
                .then(function (response) {
                    me.cerrarModal()
                    me.listarArticulo(1,'','nombre')
                })
                .catch(function (error) {
                    console.log(error);
                })
            },
            validarArticulo(){
                this.errorArticulo = 0
                this.errorMostrarMsjArticulo = []
                if (this.idcategoria == 0) this.errorMostrarMsjArticulo.push("Seleccione categoria")
                if (!this.stock) this.errorMostrarMsjArticulo.push("Stcok obligatorio")
                if (!this.precio_venta) this.errorMostrarMsjArticulo.push("precio venta obligatorio")
                if (!this.nombre) this.errorMostrarMsjArticulo.push("NOMBRE obligatorio")

                if (this.errorMostrarMsjArticulo.length) this.errorArticulo = 1
                return this.errorArticulo
            },
            actualizarArticulo(){
                if (this.validarArticulo()) {
                    return
                }
                let me = this
                axios.put('/articulo/actualizar', {
                    'idcategoria': this.idcategoria,
                    'codigo': this.codigo,
                    'nombre': this.nombre,
                    'stock': this.stock,
                    'precio_venta': this.precio_venta,
                    'descripcion': this.descripcion,
                    'id': this.articulo_id
                })
                .then(function (response) {
                    Vue.swal({
                    position: 'top-end',
                    type: 'success',
                    title: 'Articulo ' + response.data + ' modificado con exito',
                    showConfirmButton: false,
                    timer: 1500
                    })
                    me.cerrarModal()
                    me.listarArticulo(1,'','nombre')
                })
                .catch(function (error) {
                    console.log(error);
                })
            },
            abrirModal(modelo, accion, data =[]){
                this.selectCategoria()
                switch (modelo) {
                    case 'articulo':{
                        switch (accion) {
                            case 'registrar':
                            {
                                this.modal = 1
                                this.tituloModal = 'Resgistrar Articulo'
                                this.idcategoria = 0
                                this.nombre_categoria = ''
                                this.codigo = ''
                                this.nombre = ''
                                this.precio_venta = 0
                                this.stock = 0
                                this.descripcion = ''
                                this.tipoAccion =1
                                break;
                            }
                            
                            case 'actualizar':
                            {
                                this.modal = 1
                                this.tituloModal = 'Actualizar Articulo'
                                this.tipoAccion = 2
                                this.articulo_id=data['id']
                                this.idcategoria = data['idcategoria']
                                this.codigo = data['codigo']
                                this.nombre = data['nombre']
                                this.stock = data['stock']
                                this.precio_venta = data['precio_venta']
                                this.descripcion = data['descripcion']
                                break;
                            }
                        }
                    }
                }
            },
            cerrarModal(){
                this.modal= 0
                this.tituloModal = ''
                this.idcategoria = 0
                this.nombre = ''
                this.nombre_categoria = ''
                this.codigo = ''
                this.precio_venta = 0
                this.stock = 0
                this.descripcion = ''
                this.errorArticulo = 0
            },
            desactivarArticulo(id, nombre){
                let me = this
                Vue.swal({
                title: 'Estas segruro de desactivar ' + nombre + '?',
                //text: 'You will not be able to recover this imaginary file!',
                type: 'warning',
                showCancelButton: true,
                confirmButtonText: 'Desactivar!',
                cancelButtonText: 'Cancelar'
                }).then((result) => {
                    if (result.value) {
                        axios.put('/articulo/desactivar', {
                            'id': id
                        }).then(function (response) {
                            Vue.swal({
                            position: 'top-end',
                            type: 'success',
                            title: 'Articulo desactivado con exito',
                            showConfirmButton: false,
                            timer: 1500
                            })
                            me.listarArticulo(1,'',nombre)
                            
                        })
                        .catch(function (error) {
                            console.log(error);
                        })
                    } else {
                        Vue.swal(
                        'Cancelled',
                        'No se desactivo la categoria :)',
                        'error'
                        )
                    }
                })
            },
            activarArticulo(id){
                let me = this
                Vue.swal({
                title: 'Estas segruro de activar Articulo?',
                //text: 'You will not be able to recover this imaginary file!',
                type: 'warning',
                showCancelButton: true,
                confirmButtonText: 'Activar!',
                cancelButtonText: 'Cancelar'
                }).then((result) => {
                    if (result.value) {
                        axios.put('/articulo/activar', {
                            'id': id
                        }).then(function (response) {
                            me.listarArticulo(1,'','nombre')
                            Vue.swal({
                            position: 'top-end',
                            type: 'success',
                            title: 'Articulo activado con exito',
                            showConfirmButton: false,
                            timer: 1500
                            })
                            
                        })
                        .catch(function (error) {
                            console.log(error);
                        })
                    } else {
                        Vue.swal(
                        'Cancelled',
                        'No se activo la categoria :)',
                        'error'
                        )
                    }
                })
            },
            cambiarPagina(page, buscar, criterio){
                let me = this
                me.pagination.current_page = page
                me.listarArticulo(page,buscar,criterio)
            }
        },
        mounted() {
            this.listarArticulo(1,this.buscar,this.criterio)
        }
    }
</script>
<style>
    .modal-content{
        width: 100% !important;
        position: absolute !important;
    }
    .mostrar{
        display: list-item !important;
        opacity: 1 !important;
        position: absolute !important;
        background-color: #3c29297a !important;
    }
    .div-error{
        display: flex;
        justify-content: center;

    }
    .text-error{
        color: red;
        font-weight: bold;
    }
</style>

