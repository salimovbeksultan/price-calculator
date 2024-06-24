<script>
import accounting from '@/assets/accounting.json'
import employee from '@/assets/employee.json'
import oneTime from '@/assets/one-time.json'
import soldiier from '@/assets/soldier.json'
import payment from '@/assets/payment.json'
import SegmentItem from '@/components/SegmentItem.vue'

export default {
  name: 'MainApp',
  components: { SegmentItem },
  watch: {
    calcType(newValue) {
      this.parent = []
      this.calcTypes.forEach((calc) => {
        if (newValue.includes(calc.name)) {
          this.parent.push(calc)
        }
      })
    }
  },
  methods: {
    updatePrice(index, value) {
      this.parent[index].price = value
    }
  },
  computed: {
    totalPrice() {
      return this.parent.reduce((acc, item) => acc + item.price, 0)
    }
  },
  data() {
    return {
      parent: [],
      calcType: [],
      calcTypes: [
        { name: 'Бухгалтерское сопровождение', data: accounting },
        { name: 'Разовые услуги', data: oneTime },
        { name: 'Кадровый учет', data: employee },
        { name: 'Воинский учет', data: soldiier },
        { name: 'Расчет зарплат', data: payment }
      ],
      children: null
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
      <div v-for="calc in calcTypes" :key="calc.name" class="form-check form-check-inline">
        <input
          v-model="calcType"
          class="form-check-input"
          type="checkbox"
          :id="calc.name"
          :value="calc.name"
        />
        <label class="form-check-label" :for="calc.name">{{ calc.name }}</label>
      </div>

      <segment-item
        v-for="(item, index) in parent"
        :key="index"
        :index="index"
        :parent="item"
        @price="updatePrice"
      ></segment-item>

      <h3 class="text-body-emphasis mt-5">Итоговая цена</h3>
      <span>{{ Math.ceil(totalPrice, 2) }}</span>
    </main>
  </div>
</template>

<style scoped></style>
