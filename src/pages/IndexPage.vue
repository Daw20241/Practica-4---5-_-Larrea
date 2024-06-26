<template>
  <div class="q-pa-md flex flex-lefth container column">
    <div class="row items-center q-gutter-sm">
      <q-form @submit="submitForm" class="row items-center q-gutter-sm">
        <q-input
          v-model="fechaInicio"
          label="Fecha Inicio"
          dense
          placeholder="yyyy-mm-dd"
          ref="fechaInicioInput"
        >
          <template v-slot:append>
            <q-icon
              name="event"
              class="cursor-pointer"
              @click="openPopup('inicio')"
            />
          </template>
          <q-popup-proxy
            ref="inicioPopup"
            transition-show="scale"
            transition-hide="scale"
          >
            <q-date v-model="fechaInicio" mask="YYYY-MM-DD" />
          </q-popup-proxy>
        </q-input>

        <q-input
          v-model="fechaFin"
          label="Fecha Fin"
          dense
          placeholder="yyyy-mm-dd"
          ref="fechaFinInput"
        >
          <template v-slot:append>
            <q-icon
              name="event"
              class="cursor-pointer"
              @click="openPopup('fin')"
            />
          </template>
          <q-popup-proxy
            ref="finPopup"
            transition-show="scale"
            transition-hide="scale"
          >
            <q-date v-model="fechaFin" mask="YYYY-MM-DD" />
          </q-popup-proxy>
        </q-input>

        <q-btn type="submit" label="Buscar" color="primary" icon="search" />
      </q-form>
    </div>

    <div class="q-mt-md" style="width: 100%">
      <q-table
        flat
        bordered
        grid
        title="Foto del dia"
        :rows="informaciones"
        :columns="columns"
        row-key="date"
        :filter="filter"
        hide-header
      >
        <template v-slot:item="props">
          <div
            class="q-pa-xs col-xs-12 col-sm-6 col-md-4 col-lg-3 grid-style-transition"
          >
            <q-card bordered flat>
              <q-img
                :src="props.row.url"
                style="max-height: 200px; object-fit: cover"
              >
                <div class="absolute-bottom text-h6">
                  {{ props.row.title }} {{ props.row.date }}
                </div>
              </q-img>
              <q-card-section>
                <div style="max-height: 100px; overflow-y: auto">
                  {{ props.row.explanation }}
                </div>
              </q-card-section>
            </q-card>
          </div>
        </template>
        <template v-slot:no-data>
          <div class="text-center q-pa-md">
            No hay datos disponibles para mostrar.
          </div>
        </template>
      </q-table>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      informaciones: [],
      fechaInicio: "",
      fechaFin: "",
      filter: "",
      columns: [
        {
          name: "info",
          required: true,
          label: "Info",
          align: "left",
          field: (row) => row.title,
          format: (val) => `${val}`,
          sortable: true,
        },
      ],
    };
  },
  methods: {
    async submitForm() {
      if (this.fechaInicio === "" || this.fechaFin === "") {
        this.$q.notify({
          position: "top",
          type: "warning",
          message: "Por favor seleccionar las fechas",
        });
        return;
      }

      try {
        var url =
          "https://api.nasa.gov/planetary/apod?api_key=St2xte0rLuGnLvb6AHTrniQdcrEphjaRlFHM4KSw&start_date=" +
          this.fechaInicio +
          "&end_date=" +
          this.fechaFin;
        const response = await axios.get(url);
        this.informaciones = response.data;
        console.log("respuesta exitosa:", response.data);
      } catch (error) {
        console.error(error);
      }
    },
    openPopup(popup) {
      if (popup === "inicio") {
        this.$refs.inicioPopup.show();
      } else if (popup === "fin") {
        this.$refs.finPopup.show();
      }
    },
  },
};
</script>

<style>
.grid-style-transition {
  transition: transform 0.3s ease;
}

.q-table__grid-item .q-img {
  width: 100%;
  height: auto;
}

.q-table__grid-item .q-card-section {
  padding: 10px;
}
</style>
