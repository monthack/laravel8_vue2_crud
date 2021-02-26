<template>
  <div>
      <h1 class="text-center">Gestionar Articulos</h1>
      <hr>

      <!-- Button to Open the Modal -->
      <button @click="modificar=false;  abrirmodal();" type="button" class="btn btn-primary my-4">
          Nuevo
      </button>

      <!-- The Modal -->
<div class="modal" :class="{mostrar:modal}">
  <div class="modal-dialog">
    <div class="modal-content">

      <!-- Modal Header -->
      <div class="modal-header">
        <h4 class="modal-title">{{ titulomodal }}</h4>
        <button type="button" @click="cerrarmodal();" class="close" data-dismiss="modal">&times;</button>
      </div>

      <!-- Modal body -->
      <div class="modal-body">
        <div class="my-4">
          <label for="nombre">Nombre</label>
          <input v-model="articulo.nombre" type="text" class="form-control" id="nombre" placeholder="Nombre del articulo">
        </div>
        <div class="my-4">
          <label for="descripcion">Descripcion</label>
          <input v-model="articulo.descripcion" type="text" class="form-control" id="descripcion" placeholder="Descripcion del articulo">
        </div>
        <div class="my-4">
          <label for="stock">Stock</label>
          <input v-model="articulo.stock" type="number" class="form-control" id="stock" placeholder="Stock del articulo">
        </div>
      </div>

      <!-- Modal footer -->
      <div class="modal-footer">
        <button type="button" @click="cerrarmodal();" class="btn btn-secondary" data-dismiss="modal">Close</button>
        <button type="button"  @click="guardar();" class="btn btn-success" data-dismiss="modal">Guardar</button>
      </div>

    </div>
  </div>
</div>

      <table class="table">
  <thead class="thead-dark">
    <tr>
      <th scope="col">#</th>
      <th scope="col">Nombre</th>
      <th scope="col">Descripción</th>
      <th scope="col">Stock</th>
      <th scope="col" colspan="2" class="text-center">Acción</th>
    </tr>
  </thead>
  <tbody>
    <tr v-for="art in articulos" :key="art.id">
      <th scope="row">{{ art.id }}</th>
      <td>{{ art.nombre }}</td>
      <td>{{ art.descripcion }}</td>
      <td>{{ art.stock }}</td>
      <td>
          <button @click="modificar=true; abrirmodal(art);" class="btn btn-warning">Editar</button>
      </td>
      <td>
          <button @click="eliminar(art.id)" class="btn btn-danger">Eliminar</button>
      </td>
    </tr>   
  </tbody>
</table>
  </div>
</template>

<script>
export default {
    data() {
        return {
            articulo: {
              nombre:'',
              descripcion: '',
              stock: '',
            },
            id: 0,//editar
            modificar: true,
            modal: 0, //variable del modal si va hacer 0 esta cerrado y si va ser 1 va estar abierto
            titulomodal: '', //es una variable que va a cambiar
            articulos: [],
        }
    },
    methods: {
        async listar (){//async es para evitarnos las promesas dado que es mucho codigo, es una funcion asincrona
            const res=await axios.get('articulos');
            this.articulos=res.data;
        },
        async eliminar (id){
            const res=await axios.delete('/articulos/'+id);
            this.listar(); //para volver a cargar el array y se muestre inmediato
        },
        abrirmodal (data={}){//para pasarle el objeto
            this.modal = 1;
            if(this.modificar){
              this.id=data.id;//editar
              this.titulomodal= "Modificar Articulo";
              this.articulo.nombre = data.nombre;
              this.articulo.descripcion = data.descripcion;
              this.articulo.stock = data.stock;
            }else{
              this.id= 0;//editar
              this.titulomodal="Crear articulo";
              this.articulo.nombre = '';
              this.articulo.descripcion = '';
              this.articulo.stock = 1;

            }
        },
        cerrarmodal (){
            this.modal = 0;
        },
        async guardar (){
          if(this.modificar){
              const res=await axios.put('/articulos/'+this.id, this.articulo);//Editar
          }else{
              const res=await axios.post('/articulos', this.articulo); //guardar
          }
          this.cerrarmodal();
          this.listar();

        }
    },

    created (){//significa que cuando inmediatamente se crea se llame
        this.listar();
    }

  }
</script>

<style>
/**darle estilos al modal */
.mostrar {
    display: list-item;
    opacity: 1;
    background: rgba(52, 45, 78, 0.527);
}

</style>