<template>
  <div class="main">
    <div class="main__content main-content">
      <div class="main-content__options">
        <FilterOptions v-model:filter="filterTarifss" :options="optionsTarifss" title="Опции тарифа" class="main__tariffs-filter" />
        <FilterOptions v-model:filter="filterCompany" :options="optionsCompany" title="Авиакомпании" class="main__company-filter" />
        <div @click="() => clearAllFilters()" class="main__clear">Сбросить все фильтры</div>
      </div>
      <div class="main-content__tickets main-content-tickets">
        <TicketItem class="main-content-tickets__item"  :key="index" v-for="(item, index) in filteredTarifss" :data="item"/>
      </div>
    </div>
  </div>
</template>

<script>
import FilterOptions from '../components/FilterOptions.vue'
import TicketItem from '../components/TicketItem.vue'
import json from '../../results.json'

export default {
  name: 'Main',
  components: {
    FilterOptions,
    TicketItem
  },
  data() {
    return {
      filterTarifss: [],
      filterCompany: [],
      optionsTarifss: [
        [
          'direct',
          'Только прямые'
        ],
        [
          'baggage',
          'Только с багажом'
        ],
        [
          'refundable',
          'Только возвратные'
        ]
      ],
      optionsCompany: [
        ['ALL', 'Все'],
        ...Object.entries(json.airlines)
      ],
      flights: json.flights
    }
  },
  computed: {
    filteredTarifss() {
      if(this.filterTarifss.length > 0) {
        return this.filteredCompany.filter(item => {
          let condition
          if(this.filterTarifss.length === 1) {
            condition = item[this.filterTarifss[0]]
          } else if (this.filterTarifss.length === 2){
            condition = item[this.filterTarifss[0]] && item[this.filterTarifss[1]]
          } else if (item[this.filterTarifss.length === 3]) {
            condition = item[this.filterTarifss[0]] && item[this.filterTarifss[1]] && item[this.filterTarifss[2]]
          }
          return condition
        })
      } 
      return this.filteredCompany
    },
    filteredCompany() {
      if(this.filterCompany.length > 0 && !this.filterCompany.includes('ALL')) {
        return this.flights.filter(el => {
          return this.filterCompany.includes(el.validating_carrier)
        })
      } else if(this.filterCompany.includes('ALL')) {
        return this.flights
      } else {
        return this.flights
      }
    }
  },
  methods: {
    clearAllFilters() {
      this.filterTarifss = []
      this.filterCompany = []
    }
  },
  mounted() {
    this.flights = this.flights.map(el => {
      return {
        ...el,
        baggage: el.itineraries[0][0].segments[0].baggage_options[0].value > 0 ? true : false,
        direct: el.itineraries[0][0].stops > 0 ? false : true
      }
    })
  }
}

</script>

<style lang="scss">
  .main {
    min-height: 100vh;
    background-color: #D7D7D7;
    &__tariffs-filter {
      width: 240px;
      margin-bottom: 12px;
    }
    &__company-filter {
      width: 240px;
      margin-bottom: 10px;
    }
    &__clear {
      display: inline;
      border-bottom: 1px dashed rgb(114, 132, 228);
      color: rgb(114, 132, 228);
      cursor: pointer;
      font-size: 12px;
      transition: 0.3s;
      &:hover {
        border-bottom: 1px dashed rgba(114, 132, 228, 0.8);
        color: rgba(114, 132, 228, 0.8);
      }
      &:active {
        border-bottom: 1px dashed rgba(114, 132, 228, 0.6);
        color: rgba(114, 132, 228, 0.6);
      }
    }
  }
  .main-content {
    padding: 50px 0;
    display: flex;
    max-width: 1200px;
    margin: 0 auto;
    &__options {
      margin-right: 20px;
    }
    &__tickets {
      flex-grow: 1;
    }
  }
  .main-content-tickets {
    &__item {
      margin-bottom: 12px;
      &:last-child {
        margin-bottom: 0;
      }
    }
  }

  @media (max-width: 1023px) {
    .main {
      &__tariffs-filter {
        width: 40%;
      }
      &__company-filter {
        width: 40%;
      }
    }
    .main-content {
      padding: 16px;
      flex-direction: column;
      &__options {
        display: flex;
        margin-right: 0;
        margin-bottom: 30px;
        position: relative;
        justify-content: space-between;
      }
    }
    .main__clear {
      position: absolute;
      bottom: -15px;
    }
  }

  @media (max-width: 599px) {
    .main {
      &__tariffs-filter {
        width: 100%;
      }
      &__company-filter {
        width: 100%;
      }
    }
    .main-content {
      &__options {
        flex-direction: column;
      }
    }
  }
</style>