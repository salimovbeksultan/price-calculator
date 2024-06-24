<template>
  <div class="v-container mt-5">
    <h1 class="text-body-emphasis mb-3">{{ parent.name }}</h1>

    <div v-for="item in parent.data" :key="item.name" class="form-check form-check-inline">
      <input
        v-model="subType"
        class="form-check-input"
        type="radio"
        :id="`${item.name}-input`"
        :value="item"
      />
      <label class="form-check-label" :for="`${item.name}-input`">{{ item.name }}</label>
    </div>
    <div v-if="parent && subType" class="row">
      <div class="col-md-4 mt-3" v-for="(item, index) in subType.children" :key="item.name">
        <h5 class="text-body-emphasis">{{ item.name }}</h5>
        <div v-for="newItem in item.children" :key="newItem.name" class="form-check">
          <input
            v-model="values[index]"
            :class="newItem.special ? 'form-control' : 'form-check-input'"
            :type="newItem.special ? 'number' : 'radio'"
            :id="`${newItem.name}-input`"
            :value="newItem.price"
          />
          <label class="form-check-label" :for="`${newItem.name}-input`">{{ newItem.name }}</label>
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
          <label class="form-label">Количество {{ item.special.unit }}:</label>
          <input v-model="values[index]" type="number" class="form-control" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'SegmentItem',
  props: ['parent', 'index'],
  data() {
    return {
      values: null,
      subType: null,
      finalPrice: 0
    }
  },
  watch: {
    subType(newValue) {
      this.values = Array(newValue.children.length).fill(null)
    },
    values: {
      deep: true,
      handler(value) {
        this.finalPrice = 0
        value.forEach((item, index) => {
          if (this.subType.children[index].special) {
            const minimum = this.subType.children[index].special.minimum
            if (typeof this.subType.children[index].special.amount === 'string') {
              const percent = parseFloat(this.subType.children[index].special.amount) / 100
              if (minimum && minimum > this.finalPrice * percent * item && item > 0) {
                this.finalPrice += minimum
              } else {
                this.finalPrice += this.finalPrice * percent * item
              }
            } else {
              this.finalPrice +=
                this.finalPrice + item * this.subType.children[index].special.amount
            }
          } else {
            if (typeof item === 'number') {
              this.finalPrice += item
            } else if (typeof item === 'string') {
              const percent = parseFloat(item) / 100
              this.finalPrice += this.finalPrice * percent
            } else if (typeof item === 'boolean') {
              if (typeof this.subType.children[index].price === 'number') {
                this.finalPrice += this.subType.children[index].price
              } else if (typeof this.subType.children[index].price === 'string') {
                const percent = parseFloat(this.subType.children[index].price) / 100
                this.finalPrice += this.finalPrice * percent
              }
            }
          }
          this.$emit('price', this.index, this.finalPrice)
        })
      }
    }
  }
}
</script>
