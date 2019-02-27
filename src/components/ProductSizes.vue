<template>
    <span :class="'product-size ' + extraClass">
        Available in size
        <span class="product-size__value"
                v-for="(size, i) in sizes" v-bind:key="i">
                {{ size }}
        </span>
    </span>
</template>

<script>
export default {
  name: 'ProductSizes',
  props: {
    sizes: Array,
    extraClass: {
      type: String,
      default: '',
    },
  },
};
</script>
