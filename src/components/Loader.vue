<template>
    <div v-if="showLoader" class="loader">
        <img class="preloader" src="/static/images/loader.gif" />
        <span class="label">Loading..</span>
    </div>
</template>

<script>
export default {
  name: 'Loader',
  props: ['showLoader'],
};
</script>
