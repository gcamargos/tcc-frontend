<template>
  <nav class="navBarContainer">
    <div>
      <button
        type="button"
        :class="optionMenu === 1 ? 'buttonSelected' : 'button'"
        v-on:click="showListHome"
      >
        HOME
      </button>
    </div>
    <div>
      <button
        type="button"
        :class="optionMenu === 2 ? 'buttonSelected' : 'button'"
        v-on:click="showListAtrasados"
      >
        ATRASADOS
      </button>
    </div>
    <div class="save-btn">
      <button
        type="button"
        :class="optionMenu === 3 ? 'buttonSelected' : 'button'"
        v-on:click="toSchedule"
        id="show-modal"
        @click="showModal = true"
      >
        AGENDAR
      </button>
    </div>
  </nav>
  <table>
    <tbody>
      <section class="headerMedicineContainer">
        <tr>
          <th id="1" class="">REMÉDIO</th>
          <th id="2">HORA AGENDADA</th>
          <th id="3">HORA DE CONSUMO</th>
        </tr>
      </section>
      <section class="bodyMedicineContainer">
        <tr v-for="item in renderList" :key="item.length">
          <th id="1">
            {{ item.nome }}
          </th>
          <th id="2">
            {{
              new Date(item.agendado).toLocaleDateString("pt-br", {
                year: "numeric",
                month: "numeric",
                day: "numeric",
                hour: "numeric",
                minute: "numeric",
                second: "numeric",
              })
            }}
          </th>
          <th id="3">
            {{
              item.consumido
                ? new Date(item.consumido).toLocaleDateString("pt-br", {
                    year: "numeric",
                    month: "numeric",
                    day: "numeric",
                    hour: "numeric",
                    minute: "numeric",
                    second: "numeric",
                  })
                : ""
            }}
          </th>
        </tr>
      </section>
    </tbody>
  </table>
  <SavedModal v-show="showModal" @close-modal="showModal = false" />
</template>

<script>
import SavedModal from "../components/SavedModal.vue";
export default {
  name: "HomeView",
  components: { SavedModal },
  data() {
    return {
      showModal: false,
      optionMenu: 1,
      renderList: [],
    };
  },
  methods: {
    async showListHome() {
      this.optionMenu = 1;
      const response = await fetch(
        "https://tcc-backend-chi.vercel.app/remedios"
      );
      this.renderList = await response.json();
    },
    async showListAtrasados() {
      this.optionMenu = 2;
      const response = await fetch(
        "https://tcc-backend-chi.vercel.app/remedios"
      );
      const atrasados = await response.json();
      if (!atrasados?.length) return;
      this.renderList = atrasados.filter(
        (item) =>
          item.consumido === null &&
          new Date(item.agendado) < new Date(Date.now())
      );
    },
    async toSchedule() {
      this.optionMenu = 3;
      const response = await fetch(
        "https://tcc-backend-chi.vercel.app/remedios"
      );
      this.renderList = await response.json();
    },
  },
  beforeMount() {
    this.showListHome();
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Poppins:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&family=Raleway:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap");

body {
  background-color: #202324;
  font-family: "Poppins", "Raleway", sans-serif;
  color: white;
}

.navBarContainer {
  align-items: center;
  display: flex;
  font-size: 30px;
  font-weight: 400;
  height: 100px;
  justify-content: space-around;
  margin: auto;
  margin-top: 40px;
  width: 90%;
}

table {
  border: 1px solid rgba(152, 82, 7, 0.953);
  border-radius: 30px 30px 0px 0px;
  margin: auto;
  margin-top: 40px;
  width: 90%;
}

tr {
  align-items: center;
  display: flex;
  font-size: 20px;
  font-weight: 400;
  height: 50px;
  justify-content: space-around;
  margin: auto;
  text-align: center;
  width: 100%;
}

tr:nth-child(even) {
  background: #22292C;
}

.headerMedicineContainer {
  border-radius: 30px 30px 0px 0px;
  background: #282B2D;
}

.bodyMedicineContainer {
  border-radius: 0px 0px 30px 30px;
}

.bodyMedicineContainer th {
  font-weight: 400;
}

th {
  display: flex;
  flex-direction: column;
  height: 100%;
  justify-content: center;
  width: 100%;
  font-weight: 600;
}

.button {
  box-sizing: border-box;
  padding: 16px 42px;
  font-family: "Poppins", "Raleway", sans-serif;
  background: #22292C;
  text-decoration: none;
  color: white;
  font-size: 16px;
  font-weight: 400;
  border: 1px solid rgba(152, 82, 7, 0.953);
  border-radius: 10px;
  cursor: pointer;
}

.button:hover {
  color: white;
  font-weight: 700;
  border: 1px solid black;
}

.buttonSelected {
  box-sizing: border-box;
  padding: 16px 42px;
  font-family: "Poppins", "Raleway", sans-serif;
  background: #1c1d20;
  text-decoration: none;
  color: white;
  font-size: 16px;
  font-weight: 700;
  border: 1px solid rgba(152, 82, 7, 0.953);
  border-radius: 10px;
  cursor: pointer;
}
</style>
