<template>
  <div class="ticket">
    <div class="ticket__main ticket-main">
      <div class="ticket-main__top ticket-top">
        <div class="ticket-top__name">
          <div class="ticket-top__icon">
            <img :src="`https://aviata.kz/static/airline-logos/80x80/${data.validating_carrier}.png`" alt="">
          </div>
          {{data.itineraries[0][0].carrier_name}}
        </div>
        <div class="ticket-top__date ticket-date">
          <div class="ticket-date__number">{{getDateNumber(data.itineraries[0][0].dep_date)}}</div>
          <div class="ticket-date__time">{{getDateTime(data.itineraries[0][0].dep_date)}}</div>
        </div>
        <div class="ticket-top__way ticket-way">
          <div class="ticket-way__main ticket-way-main">
            <div class="ticket-way-main__city">{{data.itineraries[0][0].segments[0].origin_code}}</div>
            <div class="ticket-way-main__time">{{secondsToHm(data.itineraries[0][0].traveltime)}}</div>
            <div class="ticket-way-main__city">{{data.itineraries[0][0].segments[data.itineraries[0][0].segments.length - 1].dest_code}}</div>
          </div>
          <img style="width: 100%" src="../assets/way.svg" alt="way">
          <template v-if="data.itineraries[0][0].segments.length > 1">
            <div 
              :key="index"
              v-for="(seg, index) in segmentsForTitle"
              class="ticket-way__title"
            >
              {{`${index === 0 ? 'через' : 'и'}   ${seg.airport_dest}, ${segTime(this.data.itineraries[0][0].segments[index].dep_time_iso, seg.dep_time_iso)}`}}
            </div>
          </template>
        </div>
        <div class="ticket-top__date ticket-date">
          <div class="ticket-date__number">{{getDateNumber(data.itineraries[0][0].arr_date)}}</div>
          <div class="ticket-date__time">{{getDateTime(data.itineraries[0][0].arr_date)}}</div>
        </div>
      </div>
      <div class="ticket-main__bot ticket-bot">
        <div class="ticket-bot__link">Детали перелета</div>
        <div class="ticket-bot__link">Условия тарифа</div>
        <div class="ticket-bot__option">
          <template v-if="data.itineraries[0][0].refundable">
            <div class="ticket-bot__option-icon">
              <InlineSvg  :src="require('../assets/returnable.svg')" />
            </div>
            Возвратный
          </template>
          <template v-else>
            <div class="ticket-bot__option-icon">
              <InlineSvg  :src="require('../assets/non-returnable.svg')" />
            </div>
            Невозвратный
          </template>
          
        </div>
      </div>
    </div>
    <div class="ticket__choose ticket-choose">
      <div class="ticket-choose__price">
        {{data.price}}
        <span class="ticket-choose__price--ccy">{{data.currency === 'KZT' ? '₸' : data.currency}}</span>
        </div>
      <button class="ticket-choose__btn">Выбрать</button>
      <div class="ticket-choose__info">Цена за всех пассажиров</div>
      <div class="ticket-choose__bag ticket-bag">
        <div class="ticket-bag__value">{{baggage}}</div>
        <div class="ticket-bag__chip">+ Добавить багаж</div>
      </div>
    </div>
  </div>
</template>

<script>
import InlineSvg from 'vue-inline-svg'

export default {
  name: 'TicketItem',
  components: {
    InlineSvg
  },
  props: ['data'],
  computed: {
    baggage() {
      const option = this.data.itineraries[0][0].segments[0].baggage_options[0]
      const merge = option.value + option.unit
      if(merge === '0PC') {
        return 'Нет багажа'
      } else if(merge === '1PC') {
        return 'Один чемодан/сумка'
      }
      return option.value + (option.unit === 'KG' ? ' кг' : option.unit)
    },
    segmentsForTitle() {
      let result = []
      for (let i = 1; i < this.data.itineraries[0][0].segments.length; i++) {
        result.push(this.data.itineraries[0][0].segments[i])
      }
      return result
    }
  },
  methods: {
    segTime(timeStart, timeSeg) {
      return this.miliSecondsToHm(Date.parse(timeSeg) - Date.parse(timeStart))
    },
    getDateNumber(val) {
      const date = new Date(val)
      const day = date.getDate()
      const month = this.convertMonth(date.getMonth())
      const dayWeek = this.convertDay(date.getDay())
      return `${day} ${month}, ${dayWeek}` 
    },
    getDateTime(val) {
      const date = new Date(val).toLocaleString()
      return date.substr(11, 6)
    },
    miliSecondsToHm(ms) {
      let minutes = parseInt((ms / (1000 * 60)) % 60)
      let hours = parseInt((ms / (1000 * 60 * 60)) % 24)
      minutes = (minutes < 10) ? "0" + minutes : minutes;
      return hours + " ч " + minutes + " м"
    },
    secondsToHm(d) {
      d = Number(d);
      var h = Math.floor(d / 3600);
      var m = Math.floor(d % 3600 / 60);

      var hDisplay = h > 0 ? h + ' ч ' : "";
      var mDisplay = m > 0 ? m + ' м' : "";
      return hDisplay + mDisplay; 
    },
    convertMonth(number) {
      const types = {
        0: 'Январь',
        1: 'Февраль',
        2: 'Апрель',
        3: 'Март',
        4: 'Май',
        5: 'Июнь',
        6: 'Июль',
        7: 'Август',
        8: 'Сентябрь',
        9: 'Октябрь',
        10: 'Ноябрь',
        11: 'Декабрь',
      }
      return types[number]
    },
    convertDay(number) {
      const types = {
        0: 'Вс',
        1: 'Пн',
        2: 'Вт',
        3: 'Ср',
        4: 'Чт',
        5: 'Пт',
        6: 'Сб'
      }
      return types[number]
    },
  }
}
</script>

<style lang="scss">
  .ticket {
    display: flex;
    position: relative;
    justify-content: space-between;
    box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.15);
    &__main {
      flex-grow: 1;
    }
    &__choose {
      width: 240px
    }
  }
  .ticket-main {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    background-color: #fff;
    border-top-left-radius: 4px;
    border-bottom-left-radius: 4px;
    padding: 40px 90px 20px 40px;
  }
  .ticket-choose {
    border-top-right-radius: 4px;
    border-bottom-right-radius: 4px;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    align-items: center;
    background-color: #F5F5F5;
    padding: 15px 20px;
    &__price {
      font-size: 24px;
      margin-bottom: 15px;
      &--ccy {
        font-size: 18px;
      }
    }
    &__btn {
      width: 100%;
      font-size: 18px;
      border-radius: 4px;
      border: none;
      color: #fff;
      padding: 10px 0;
      cursor: pointer;
      background-color: rgb(85, 187, 6);
      transition: 0.3s;
      &:hover {
        background-color: rgba(85, 187, 6, 0.8);
      }
      &:active {
        background-color: rgba(85, 187, 6, 0.6);
      }
    }
    &__info {
      color: #707276;
      margin-top: 10px;
      margin-bottom: 15px;
      font-size: 12px;
    }
  }
  .ticket-top {
    display: flex;
    justify-content: space-between;
    align-items: center;
    &__name {
      display: flex;
      font-weight: bold;
      font-size: 14px;
    }
    &__icon {
      display: flex;
      justify-content: center;
      align-items: center;
      margin-right: 5px;
      img {
        height: 20px;
      }
    }
  }
  .ticket-bot {
    display: flex;
    font-size: 12px;
    &__link {
      margin-right: 20px;
      border-bottom: 1px dashed rgb(114, 132, 228);
      color: rgb(114, 132, 228);
      cursor: pointer;
      transition: 0.3s;
      &:hover {
        color: rgba(114, 132, 228, 0.8);
        border-bottom: 1px dashed rgba(114, 132, 228, 0.8);
      }
      &:active {
        color: rgba(114, 132, 228, 0.6);
        border-bottom: 1px dashed rgba(114, 132, 228, 0.6);
      }
    }
    &__option {
      margin-left: 25px;
      color: #707276;
      display: flex;
      align-items: center;
      &-icon {
        margin-right: 10px;
        display: flex;
        justify-content: center;
        align-items: center;
      }
    }
  }
  .ticket-date {
    display: flex;
    flex-direction: column;
    align-items: center;
    &__number {
      font-size: 12px;
    }
    &__time {
      font-weight: bold;
      font-size: 24px;
    }
  }
  .ticket-way {
    &__title {
      font-size: 12px;
      color: #FF9900;
      padding: 0 20px;
      text-align: center;
    }
  }
  .ticket-way-main {
    display: flex;
    justify-content: space-between;
    align-items: center;
    &__city {
      color: #B9B9B9;
      font-size: 10px;
    }
    &__time {
      font-size: 12px;
    }
    &__main {
      font-size: 12px;
    }
  }
  .ticket-bag {
    display: flex;
    justify-content: space-between;
    align-items: center;
    width: 100%;
    font-size: 12px;
    &__value {
      text-align: center;
      flex-grow: 1;
    }
    &__chip {
      padding: 5px 10px;
      background-color: rgb(234, 240, 250);
      color: rgb(87, 99, 179);
      font-weight: bold;
      cursor: pointer;
      border-radius: 2px;
      transition: 0.3s;
      &:hover {
        color: rgba(87, 99, 179, 0.8);
        background-color: rgba(234, 240, 250, 0.6);
      }
      &:active {
        color: rgba(87, 99, 179, 0.6);
        background-color: rgba(234, 240, 250, 0.6);
      }
    }
  }
  @media (max-width: 599px) {
    .ticket {
      flex-direction: column;
    }
    .ticket-main {
      border-bottom-left-radius: 0;
      border-top-right-radius: 4px;
    }
    .ticket-top {
      &__name {
        position: absolute;
        top: 20px;
        left: 20px;
      }
    }
    .ticket-bot {
      display: none;
    }
    .ticket-way {
      padding: 20px;
      position: absolute;
      bottom: 170px;
      left: 0;
      width: 100%
    }
    .ticket-bag {
      &__value {
        position: absolute;
        top: 20px;
        right: 20px;
      }
      &__chip {
        display: none;
      }
    }
    .ticket-choose {
      width: 100%;
      padding: 15px 45px;
      border-bottom-left-radius: 4px;
      border-bottom-right-radius: 4px;
      border-top-right-radius: 0;
    }
    .ticket-main {
      padding: 60px 20px 100px 20px;
    }
  }
</style>