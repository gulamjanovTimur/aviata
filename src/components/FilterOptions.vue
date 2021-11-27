<template>
  <div class="tariffs-options">
    <div class="tariffs-options__header tariffs-options-header">
      <span class="tariffs-options-header__title">{{title}}</span>
      <div class="tariffs-options-header__icon">
        <InlineSvg
          :id="`clearAll + ${title}`"
          class="animateClearAll" @click="() => clearAll(`clearAll + ${title}`)"
          fill="#B9B9B9"
          :src="require('../assets/clear-filter.svg')"
        />
      </div>
    </div>
    <div 
      :key="index"
      v-for="(tariff, index) in options"
      class="tariffs-options__item tariffs-options-item"
    >
    <label @change="(e) => changeFilter(tariff[0], e)"  class="control control-checkbox">
      <div class="control__name">
        {{tariff[1]}}
      </div>
      <input id="checkbox" type="checkbox" :checked="filter.includes(tariff[0])" />
      <div class="control_indicator"></div>
    </label>
      <!-- <input @change="(e) => changeFilter(tariff[0], e)" :checked="filter.includes(tariff[0]) ? true : false" type="checkbox" class="tariffs-options-item__checkbox" />
      <div class="tariffs-options-item__title">{{tariff[1]}}</div> -->
    </div>
  </div>
</template>

<script>
import InlineSvg from 'vue-inline-svg'

export default {
  name: 'TariffsOptions',
  props: ['title', 'options', 'filter'],
  components: {
    InlineSvg,
  },
  data() {
    return {
      filterArr: []
    }
  },
  emits: ['update:filter'],
  methods: {
    changeFilter(val, e) {
      if(e.target.checked) {
        if(val === 'ALL') {
          this.filterArr = []
          for (let i = 0; i < this.options.length; i++) {
            const el = this.options[i];
            this.filterArr.push(el[0])
          }
          this.$emit('update:filter', this.filterArr)
        } else {
          this.filterArr.push(val)
          this.$emit('update:filter', this.filterArr)
        }
      } else {
        if(val === 'ALL') {
          this.filterArr = []
          this.$emit('update:filter', this.filterArr)
        }
        const deleteInd = this.filterArr.indexOf(val)
        this.filterArr.splice(deleteInd, 1)
        this.$emit('update:filter', this.filterArr)
      }
    },
    clearAll(id) {
      document.getElementById(id).style.fill = '#7284E4'
      this.filterArr = []
      this.$emit('update:filter', this.filterArr)
      setTimeout(() => {
        document.getElementById(id).style.fill = '#B9B9B9'
      }, 300)
    }
  },
  watch: {
    filter() {
      if(this.filter.length === 0) {
        this.filterArr = []
      }
    }
  }
}
</script>

<style lang="scss">
  .tariffs-options {
    background-color: #F5F5F5;
    border-radius: 4px;
    padding: 12px;
    max-height: 320px;
    overflow: auto;
    &__header {
      margin-bottom: 20px;
    }
    &__item {
      margin-bottom: 10px;
      &:last-child {
        margin-bottom: 0;
      }
    }
  }
  .tariffs-options-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    &__title {
      font-weight: bold;
      font-size: 14px;
    }
    &__icon {
      cursor: pointer;
    }
  }
  .tariffs-options-item {
    display: flex;
    align-items: center;
    &__checkbox {
      margin-right: 10px;
    }
    &__title {
      font-size: 12px;
    }
  }
  .animateClearAll {
    transition: 0.3s;
  }

  .control {
  display: block;
  position: relative;
  padding-left: 30px;
  margin-bottom: 5px;
  padding-top: 3px;
  cursor: pointer;
  font-size: 16px;
  transition: 0.3s;
  &__name {
    font-size: 12px;
  }
}
.control input {
  position: absolute;
  z-index: -1;
  opacity: 0;
}
.control_indicator {
  position: absolute;
  top: 3px;
  left: 0;
  height: 15px;
  width: 15px;
  background: #e6e6e6;
  border: 0px solid #b9b9b9;
  border-radius: 0px;
}
.control:hover input ~ .control_indicator,
.control input:focus ~ .control_indicator {
  background: #cccccc;
}

.control input:checked ~ .control_indicator {
  background: #55bb06;
}
.control:hover input:not([disabled]):checked ~ .control_indicator,
.control input:checked:focus ~ .control_indicator {
  background: #0e6647;
}
.control input:disabled ~ .control_indicator {
  background: #e6e6e6;
  opacity: 0.6;
  pointer-events: none;
}
.control_indicator:after {
  box-sizing: unset;
  content: "";
  position: absolute;
  display: none;
}
.control input:checked ~ .control_indicator:after {
  display: block;
}
.control-checkbox .control_indicator:after {
  left: 5px;
  top: 2px;
  width: 4px;
  height: 7px;
  border: solid #ffffff;
  border-width: 0 2px 2px 0;
  transform: rotate(45deg);
}
.control-checkbox input:disabled ~ .control_indicator:after {
  border-color: #7b7b7b;
}
</style>