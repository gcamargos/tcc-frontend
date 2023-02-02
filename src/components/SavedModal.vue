<template>
  <transition name="modal-fade">
    <div class="modal-overlay" @click="$emit('close-modal')">
      <div class="modal" @click.stop>
        <label
          ><span>Nome do medicamento:</span>
          <select name="medicamentos" class="medicamentos" v-model="escolhaMedicamento">
            <option value="selecionar" selected disabled hidden>Selecionar</option>
            <option :name="medicamentos" v-for="medicamentos in opcoesMedicamento" :key="medicamentos + '-medicamento'" >
              {{medicamentos}}
            </option>
          </select>
        </label>
        <br />
        <label v-show="escolhaMedicamento !== 'selecionar'">
          <span>Dispositivo: </span>
          <select name="dispositivos" class="dispositivos" v-model="escolhaDispositivo">
            <option value="selecionar" selected disabled hidden>Selecionar</option>
            <option :name="dispositivo.dispositivo" v-for="dispositivo in opcoesDispositivo" :key="dispositivo.id + '-dispositivo'" >
              {{dispositivo.dispositivo.descricao}}
            </option>
          </select>
        </label>
        <br />
        <label
          ><span>Hora do remédio:</span>
          <input
            type="datetime-local"
            name="dataRemedio"
            placeholder="Escolha a data para tomar o remédio"
            v-model="dataRemedio"
          />
        </label>
        <br />
        <button
          class="buttonHome"
          v-on:click="submitForm"
          @click="$emit('close-modal')"
        >
          Marcar Remédio
        </button>
      </div>
      <div class="close" @click="$emit('close-modal')">
        <img class="close-img" src="../assets/close-icon.svg" alt="X" />
      </div>
    </div>
  </transition>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      nomeRemedio: "",
      dataRemedio: "",
      opcoesMedicamento: [],
      escolhaMedicamento: "selecionar",
      opcoesDispositivo: [],
      escolhaDispositivo: "selecionar",
    };
  },
  watch: {
    escolhaMedicamento(newValue, oldValue) {
      if (newValue !== oldValue){
        this.getOpcoesDispositivo();
      }
    }
  },
  methods: {
    async submitForm(e) {
      e.preventDefault();
      let dispositivoId;
      this.opcoesDispositivo.map(item => { 
       if (item.dispositivo.descricao === this.escolhaDispositivo) {
        dispositivoId = item.dispositivo.dispositivo;
       }
    })
      const jsonBody = {
        nomeRemedio: this.escolhaMedicamento,
        dataRemedio: this.dataRemedio,
        dispositivoId: dispositivoId
      };
      console.log(jsonBody);
      axios.post("https://tcc-backend-chi.vercel.app/agendar", jsonBody)
      .then((response) => {console.log(response)})
      .catch((error) => {console.log(error)});
    },
    async getOpcoesMedicamento() {
      const response = await axios.get(
        "https://tcc-backend-chi.vercel.app/medicamentos"
      );
      console.log(response);
      const medicamentos = await response.data;
      if (!medicamentos?.length) return;
      await medicamentos.forEach((item) => {
        if (!(this.opcoesMedicamento.includes(item.medicamento))){
          this.opcoesMedicamento.push(item.medicamento);
        }
      })
    },
    async getOpcoesDispositivo() {
      const jsonBody = {
        medicamento: this.escolhaMedicamento
      }
      console.log(jsonBody);
      axios.post(
        "https://tcc-backend-chi.vercel.app/dispositivos", jsonBody
      ).then((response) => {
        this.opcoesDispositivo = response.data
        }).catch((error) => console.log(error));
    }
  },
  mounted() {
    this.getOpcoesMedicamento();
  }
};
</script>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Poppins:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&family=Raleway:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap");
.modal-overlay {
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  display: flex;
  justify-content: center;
  background-color: #000000da;
}

.modal {
  text-align: center;
  background-color: white;
  color: black;
  height: 500px;
  width: 500px;
  margin-top: 10%;
  padding: 60px 0;
  border-radius: 20px;
}
.close {
  margin: 10% 0 0 16px;
  cursor: pointer;
}
.buttonHome {
  cursor: pointer;
}

.close-img {
  width: 25px;
}

.check {
  width: 150px;
}

h6 {
  font-weight: 500;
  font-size: 28px;
  margin: 20px 0;
}

p {
  font-size: 16px;
  margin: 20px 0;
}

button {
  background-color: #ac003e;
  width: 150px;
  height: 40px;
  color: white;
  font-size: 14px;
  border-radius: 16px;
  margin-top: 50px;
}

label span{
  padding-right: 12px;
}

.medicamentos {
  width: 90px;
}
</style>
