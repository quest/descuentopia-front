<template>
  <div class="row g-5">
    <div class="col-md-2">
      <div class="position-sticky p-2" style="top: 2rem">
        <div class="mb-3">
          <input
            id="store"
            type="text"
            v-model="search"
            class="form-control"
            placeholder="Buscar tienda"
          />
        </div>
        <div class="mb-3">
          <label for="provider" class="form-label">Convenio</label>
          <select id="provider" v-model="provider" class="form-select">
            <option selected>Todos</option>
            <option value="Banco de Chile">Banco de Chile</option>
            <option value="Banco Santander">Banco Santander</option>
            <option value="Banco Falabella">Banco Falabella</option>
            <option value="Banco ITAU">Banco ITAU</option>
            <option value="ACHS">ACHS</option>
            <option value="Claro Chile">Claro Chile</option>
            <option value="Caja Los Andes">Caja Los Andes</option>
          </select>
        </div>
        <!-- <div class="mb-3">
          <label for="category" class="form-label">Categoría</label>
          <select id="category" v-model="category" class="form-select">
            <option selected>Todas</option>
            <option value="Comida">Comida</option>
            <option value="Tecnología">Tecnología</option>
            <option value="Tiempo Libre">Tiempo Libre</option>
            <option value="Salud">Salud</option>
            <option value="Educación">Educación</option>
            <option value="Infantil">Infantil</option>
            <option value="Vida Sana">Vida Sana</option>
            <option value="Hogar">Hogar</option>
            <option value="Mascotas">Mascotas</option>
            <option value="Servicios y delivery">Servicios y delivery</option>
            <option value="Beneficios y Descuentos">
              Beneficios y Descuentos
            </option>
          </select>
        </div> -->
        <div class="mb-3">
          <label for="day" class="form-label">Día</label>
          <select id="day" v-model="day" class="form-select">
            <option selected>Todos</option>
            <option value="Lunes">Lunes</option>
            <option value="Martes">Martes</option>
            <option value="Miércoles">Miércoles</option>
            <option value="Jueves">Jueves</option>
            <option value="Viernes">Viernes</option>
            <option value="Sábado">Sábado</option>
            <option value="Domingo">Domingo</option>
          </select>
        </div>
      </div>
    </div>

    <div class="col-md-10">
      <CategoriesList :data="getCategories" v-model="category" />
      <BaseTable :data="filteredData" />
    </div>
  </div>
</template>
<script>
import BaseTable from "./components/BaseTable";
import CategoriesList from "./components/CategoriesList";
import axios from "axios";
export default {
  data() {
    return {
      data: {
        headers: ["Dcto.", "Tienda", "Convenio", "Expira"],
        rows: []
      },
      discounts: [],
      search: null,
      day: "Todos",
      provider: "Todos",
      category: "Todas",
      categories: [
        { id: "category-all", value: "Todas", icon: "🌟" },
        { id: "comida", value: "Comida", icon: "🍔" },
        { id: "tecnologia", value: "Tecnología", icon: "📱" },
        { id: "tiempo-libre", value: "Tiempo Libre", icon: "🏞️" },
        { id: "salud", value: "Salud", icon: "🏥" },
        { id: "educacion", value: "Educación", icon: "📚" },
        { id: "infantil", value: "Infantil", icon: "👶" },
        { id: "vida-sana", value: "Vida Sana", icon: "🍏" },
        { id: "hogar", value: "Hogar", icon: "🏡" },
        { id: "mascotas", value: "Mascotas", icon: "🐾" },
        {
          id: "servicios-y-delivery",
          value: "Servicios y delivery",
          icon: "🚚"
        },
        {
          id: "beneficios-y-descuentos",
          value: "Beneficios y Descuentos",
          icon: "💰"
        }
      ]
    };
  },
  created() {
    this.fetchDiscounts();
  },
  components: {
    BaseTable,
    CategoriesList
  },
  methods: {
    fetchDiscounts() {
      axios
        .get("https://www.victorsanmartin.com/api/descuentos.json?v=17")
        .then(({ data }) => {
          this.discounts = data.slice(0, 4);
          this.mapper(data);
        })
        .catch((error) => {
          console.log(error);
        });
    },
    mapper(data) {
      this.data.rows = data.map((i) => {
        let days = `<span class="badge text-bg-primary">Lunes a domingo</span>`;
        if (i.discount_days.length !== 7) {
          days = i.discount_days
            .map((day) => {
              return [
                "Domingo",
                "Lunes",
                "Martes",
                "Miércoles",
                "Jueves",
                "Viernes",
                "Sábado"
              ].at(day.day_of_week);
            })
            .map((day) => {
              return `<span class="badge text-bg-primary">${day}</span>`;
            })
            .join(" ");
        }

        return [
          `<span class="text-gradient d-inline">${i.discount}%</span>`,
          `<a href="${i.url}" target="_blank">${i.store.name}</a><br><small>${days}</small>`,
          i.provider.name,
          i.end_date.split("-").reverse().join("-"),
          i.category.name,
          i.discount_days
            .map((day) => {
              return [
                "Domingo",
                "Lunes",
                "Martes",
                "Miércoles",
                "Jueves",
                "Viernes",
                "Sábado"
              ].at(day.day_of_week);
            })
            .map((day) => {
              return day;
            })
            .join(" ")
        ];
      });
    }
  },
  computed: {
    getCategories() {
      return this.categories;
    },
    filteredData() {
      if (this.search || this.day || this.provider || this.category) {
        return {
          ...this.data,
          rows: this.data.rows.filter((item) => {
            let r = true;
            if (this.search) {
              // r = item.some((i) => i.toLowerCase().includes(this.search));
              r = item[1].toLowerCase().includes(this.search.toLowerCase());
            }
            if (r && this.day && this.day !== "Todos") {
              r = item[5].includes(this.day);
            }
            if (r && this.provider && this.provider !== "Todos") {
              r = item[2].includes(this.provider);
            }
            if (r && this.category && this.category !== "Todas") {
              r = item[4].includes(this.category);
            }
            return r;
          })
        };
      } else {
        return this.data;
      }
    }
  }
};
</script>
