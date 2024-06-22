<script>
import accounting from '@/assets/accounting.json'

export default {
  name: 'MainApp',
  watch: {
    accountingType(newValue) {
      this.children = accounting.find((element) => element.name === newValue).children
      if (this.children) {
        this.values = Array(this.children.length).fill(null)
      }
    },
    values: {
      deep: true,
      handler(value) {
        this.finalPrice = 0
        value.forEach((item, index) => {
          if (this.children[index].special) {
            const minimum = this.children[index].special.minimum
            if (typeof this.children[index].special.amount === 'string') {
              const percent = parseFloat(this.children[index].special.amount) / 100
              console.log(minimum)
              if (minimum && minimum > this.finalPrice * percent * item && item > 0) {
                this.finalPrice += minimum
              } else {
                this.finalPrice += this.finalPrice * percent * item
              }
            } else {
              this.finalPrice += this.finalPrice + item * this.children[index].special.amount
            }
          } else {
            if (typeof item === 'number') {
              this.finalPrice += item
            } else if (typeof item === 'string') {
              const percent = parseFloat(item) / 100
              this.finalPrice += this.finalPrice * percent
            } else if (typeof item === 'boolean') {
              if (typeof this.children[index].price === 'number') {
                this.finalPrice += this.children[index].price
              } else if (typeof this.children[index].price === 'string') {
                const percent = parseFloat(this.children[index].price) / 100
                this.finalPrice += this.finalPrice * percent
              }
            }
          }
        })
      }
    }
  },
  data() {
    return {
      accounting: accounting,
      accountingType: null,
      children: null,
      values: null,
      finalPrice: 0
    }
  }
}
</script>

<template>
  <div class="col-lg-8 mx-auto p-4 py-md-5">
    <header class="d-flex align-items-center pb-3 mb-5 border-bottom">
      <a class="d-flex align-items-center text-body-emphasis text-decoration-none" href="#">
        <span class="fs-4">Калькулятор</span>
      </a>
    </header>

    <main>
      <h1 class="text-body-emphasis mb-5">Бухгалтерское сопровождение</h1>
      <div v-for="item in accounting" :key="item.name" class="form-check form-check-inline">
        <input
          v-model="accountingType"
          class="form-check-input"
          type="radio"
          :id="`${item.name}-input`"
          :value="item.name"
        />
        <label class="form-check-label" :for="`${item.name}-input`">{{ item.name }}</label>
      </div>
      <div v-if="children" class="row">
        <div class="col-md-4 mt-3" v-for="(item, index) in children" :key="item.name">
          <h5 class="text-body-emphasis">{{ item.name }}</h5>
          <div v-for="newItem in item.children" :key="newItem.name" class="form-check">
            <input
              v-model="values[index]"
              class="form-check-input"
              type="radio"
              :id="`${newItem.name}-input`"
              :value="newItem.price"
            />
            <label class="form-check-label" :for="`${newItem.name}-input`">{{
              newItem.name
            }}</label>
          </div>
          <div v-if="!item.children && !item.special">
            <div class="form-check form-switch">
              <input
                v-model="values[index]"
                class="form-check-input"
                type="checkbox"
                role="switch"
                :value="item.price"
                :id="`${item.name}-input`"
              />
              <label class="form-check-label" :for="`${item.name}-input`">Добавить</label>
            </div>
          </div>
          <div v-if="item.special">
            <label class="form-label">Количество кредитных договоров</label>
            <input v-model="values[index]" type="number" class="form-control" />
          </div>
        </div>
      </div>
      <h3 class="text-body-emphasis mt-3">Итоговая цена</h3>
      <span>{{ Math.ceil(finalPrice, 2) }}</span>
    </main>
  </div>
</template>

<style scoped></style>
