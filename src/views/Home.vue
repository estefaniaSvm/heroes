<template>
  <v-container>
    <v-row justify="center" align="end">
      <v-col cols="auto">
        <v-text-field
          hide-details="auto"
          label="buscar super heroe"
          v-model="name"
        ></v-text-field>
      </v-col>
      <v-col cols="auto">
        <v-btn icon @click="buscarHeroe"><v-icon>mdi-magnify</v-icon></v-btn>
      </v-col>
    </v-row>
    <v-row justify="center">
      <v-data-table
        :headers="headers"
        :items="heroes"
        :items-per-page="5"
        class="elevation-1"
      ></v-data-table>
    </v-row>
  </v-container>
</template>

<script lang="ts">
import { Vue, Component } from "vue-property-decorator";

interface Heroe {
  nombre: string;
  bando: string;
  puntaje: number;
}

interface ResponseApi {
  response: "success";
  "results-for": string;
  results: Array<{
    id: string;
    name: string;
    powerstats: {
      intelligence: string;
      strength: string;
      speed: string;
      durability: string;
      power: string;
      combat: string;
    };
    biography: {
      "full-name": string;
      "alter-egos": string;
      aliases: Array<string>;
      "place-of-birth": string;
      "first-appearance": string;
      publisher: string;
      alignment: string;
    };
    appearance: {
      gender: string;
      race: string;
      height: Array<string>; //pulgadas y cm
      weight: Array<string>; //libras y kilos
      "eye-color": string;
      "hair-color": string;
    };
    work: {
      occupation: string;
      base: string;
    };
    connections: {
      "group-affiliation": string;
      relatives: string;
    };
    image: {
      url: string;
    };
  }>;
}

@Component({})
export default class Home extends Vue {
  async created(): Promise<void> {
    this.respuesta = await fetch(
      "https://www.superheroapi.com/api.php/10221038309291863/search/batman"
    )
      .then((res) => {
        return res.json();
      })
      .catch((err) => {
        console.log(err);
        return null;
      });
  }

  respuesta: ResponseApi | null = null;
  name = "";
  headers = [
    {
      text: "Nombre",
      value: "nombre",
    },
    { text: "Bando", value: "bando" },
    { text: "Puntaje", value: "puntaje" },
  ];

  get heroes(): Array<Heroe> {
    if (!this.respuesta?.results) {
      return [];
    }
    return this.respuesta.results.map((heroe) => {
      return {
        nombre: heroe.name,
        bando: heroe.connections["group-affiliation"],
        puntaje: Object.values(heroe.powerstats).reduce((sum, item) => {
          const valor = parseInt(item);
          if (isNaN(valor)) {
            return sum;
          }

          return (sum = sum + valor);
        }, 0),
      };
    });
  }

  async buscarHeroe(): Promise<void> {
    this.respuesta = await fetch(
      "https://www.superheroapi.com/api.php/10221038309291863/search/" +
        this.name
    )
      .then((res) => {
        return res.json();
      })
      .catch((err) => {
        console.log(err);
        return null;
      });
  }
}
</script>
