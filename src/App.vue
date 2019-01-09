<template>
  <div id="app">
    <div class="mask" v-show="modal"></div>
    <div class="container">
      <header>
        <div class="row py-3 align-items-center">
          <div class="col">
            <span class="pre-title">ADMINISTRACIÓN</span>
            <h1>Transferencias</h1>
          </div>
          <div class="col-auto">
            <a href="#" class="btn btn-primary" @click.prevent="changeModal">Agregar Nueva</a>
          </div>
        </div>
      </header>

      <section class="py-4">
        <div class="row mb-4">
          <div class="col-12">
            <div class="progress">
              <div class="progress-bar" role="progressbar" :style="{ width: getProgress }">{{ getProgress }}</div>
            </div>
          </div>
        </div>
        <div class="row">
          <div class="col-12 mb-4 col-md-6 col-lg-4 mb-0">
            <div class="card">
              <h4 class="card-header bg-warning text-white">Por Hacer</h4>
              <div class="card-body">
                <ul class="list-unstyled list-tasks" v-show="toDo.length">
                  <li v-for="(d, i) in toDo">
                    <div class="row align-items-center">
                      <div class="col">
                        <h5>{{ d.from }} &#8594; {{ d.to }}</h5>
                        <p><span class="badge badge-success">Bs {{ d.amount }}</span> {{ d.concept }}</p>
                      </div>
                      <div class="col-auto">
                        <div class="btn-group mr-2" role="group" aria-label="First group">
                          <button type="button" class="btn btn-success" @click="addToCompleted(i)">&#10003;</button>
                          <button type="button" class="btn btn-danger" @click="removeTask(i)">&#215;</button>
                        </div>
                      </div>
                    </div>
                  </li>
                </ul>
                <p class="text-center" v-show="toDo.length"><a href="#">Ver Todo</a></p>
                <p v-show="!toDo.length">Aún no tienes transferencias</p>
              </div>
            </div>
          </div>
          <div class="col-12 mb-4 col-md-6 col-lg-4 mb-0">
            <div class="card">
              <h4 class="card-header bg-success text-white">Realizadas</h4>
              <div class="card-body">
                <ul class="list-unstyled list-tasks" v-show="completed.length">
                  <li v-for="c in completed">
                    <div class="row">
                      <div class="col">
                        <h5>{{ c.from }} &#8594; {{ c.to }}</h5>
                        <p><span class="badge badge-success">Bs {{ c.amount }}</span> {{ c.concept}}</p>
                      </div>
                      <div class="col-auto">
                        <p>Referencia</p>
                        <span class="badge badge-dark">{{ c.reference }}</span>
                      </div>
                    </div>
                  </li>
                </ul>
                <p class="text-center" v-show="completed.length">
                  <a href="#">Ver Todo</a>
                </p>
                <p class="text-center" v-show="!completed.length">
                  Aún no has realizado ninguna transferencia
                </p>
              </div>
            </div>
          </div>
          <div class="col-12 col-md-12 col-lg-4">
            <div class="card">
              <h4 class="card-header bg-info text-white">Estadísticas</h4>
              <div class="card-body">
                <h6><b>Transferencias</b></h6><hr>
                <div class="row">
                  <div class="col">
                    Por hacer
                  </div>
                  <div class="col-auto">
                    <span class="badge badge-warning text-white">{{ countStatsToDo }}</span>
                  </div>
                </div><hr>
                <div class="row">
                  <div class="col">
                    Realizadas
                  </div>
                  <div class="col-auto">
                    <span class="badge badge-success">{{ countStatsCompleted }}</span>
                  </div>
                </div><hr>
                <div class="row">
                  <div class="col">
                    Bs por transferir
                  </div>
                  <div class="col-auto">
                    <span class="badge badge-info text-white">{{ toTransfer }}</span>
                  </div>
                </div><hr>
                <div class="row">
                  <div class="col">
                    Bs transferidos
                  </div>
                  <div class="col-auto">
                    <span class="badge badge-danger text-white">{{ transferred }}</span>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </section>
      <hr>
      <p class="text-center pb-4"><small>© 2019 Admin. Made by <a href="https://kirlled.com" target="_blank">Kirlled</a></small></p>
    </div>


    <!-- Modal -->
    <div class="modal" v-show="modal" id="exampleModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <form  @submit.prevent="addTask">
            <div class="modal-header">
              <h5 class="modal-title" id="exampleModalLabel">Nueva Transferencia</h5>
              <button type="button" class="close" @click.prevent="changeModal" aria-label="Close">
                <span aria-hidden="true">&times;</span>
              </button>
            </div>
            <div class="modal-body">
                <div class="form-group">
                  <label>¿Quién desea realizar una transferencia?</label>
                  <input type="text" v-model="newTransfer.from" class="form-control" placeholder="Ingrese el nombre">
                </div>
                <div class="form-group">
                  <label>¿Quién recibirá la transferencia?</label>
                  <input type="text" v-model="newTransfer.to" class="form-control" placeholder="Ingrese el nombre">
                </div>
                <div class="form-group">
                  <label>¿Cúal es el monto?</label>
                  <input type="text" v-model="newTransfer.amount" class="form-control" placeholder="Ingrese el monto">
                </div>
                <div class="form-group">
                  <label>¿Cúal es el concepto de la transferencia?</label>
                  <input type="text" v-model="newTransfer.concept" class="form-control" placeholder="Ingrese el concepto">
                </div>
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" @click.prevent="changeModal">Cancelar</button>
              <input type="submit" class="btn btn-primary">
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'app',
  data () {
    return {
      modal: false,
      toDo: [],
      completed: [],
      newTransfer: {
        from: '',
        to: '',
        amount: '',
        concept: ''
      }
    }
  },
  methods: {
    changeModal () {
      this.modal = !this.modal;
    },
    cleanForm () {
      this.newTransfer.from = '';
      this.newTransfer.to = '';
      this.newTransfer.amount = '';
      this.newTransfer.concept = '';
    },
    addTask () {
      this.toDo.push({
        from: this.newTransfer.from, 
        to: this.newTransfer.to, 
        amount: this.newTransfer.amount, 
        concept: this.newTransfer.concept
      });

      localStorage.setItem('tasks', JSON.stringify(this.toDo));
      this.cleanForm();
      this.changeModal();
    },
    removeTask (i) {
      this.toDo.splice(i, 1);
      localStorage.setItem('tasks', JSON.stringify(this.toDo));
    },
    addToCompleted (i) {
      this.completed.push(this.toDo[i]);
      localStorage.setItem('completed', JSON.stringify(this.completed));
      this.removeTask(i);
    }
  },
  mounted() {
    this.toDo = JSON.parse(localStorage.getItem('tasks')) || [];
    this.completed = JSON.parse(localStorage.getItem('completed')) || [];
  },
  computed: {
    countStatsToDo () {
      return this.toDo.length;
    },
    countStatsCompleted () {
      return this.completed.length;
    },
    toTransfer () {
      let total = 0;

      this.toDo.forEach(t => {
        total += parseInt(t.amount);
      });

      return total;
    },
    transferred () {
      let total = 0;

      this.completed.forEach(t => {
        total += parseInt(t.amount);
      });

      return total;
    },
    getProgress () {
      let toDo = this.toDo.length;
      let completed = this.completed.length;

      if (toDo > 0 || completed > 0)
        return `${Math.ceil(100 - (toDo / (toDo + completed)) * 100)}%`;

      return '0%';
    }
  }
}
</script>

<style lang="scss">
  @import './scss/main.scss';
</style>
