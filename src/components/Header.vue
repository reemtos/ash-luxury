<template>
    <div>
        <header class="section section__header">
            <div class="container">
                <div class="top-section">
                    <div class="logo-box">
                        <a href="/" class="logo">Ash Luxury</a>
                        <span class="tagline">Fashion & Lifestyle</span>
                    </div>
                    <div class="cart-section" @click="viewCart">
                        <i class="fas fa-shopping-cart icon"></i>
                        <span class="label">Cart</span>
                        <span class="count">{{ cartItemsLength }}</span>
                    </div>
                </div>
                <div class="company-info">
                    <p class="text">
                        <span><i class="fas fa-envelope icon"></i> admin@ashluxury.com</span>
                        <span><i class="fas fa-phone icon"></i> Whatsapp: +234 816 976 126</span>
                        <span><i class="fas fa-globe icon"></i> Worldwide shipping</span>
                    </p>
                    <p class="text">
                        <span><i class="fas fa-map-marker-alt icon"></i> 22B, Admiralty Way</span>
                        <span>
                            <i class="fas fa-clock icon"></i> HQ opens 10:00AM - 8:00PM (Mon - Sat)
                        </span>
                    </p>
                </div>
            </div>
        </header>
    </div>
</template>

<script>
export default {
  methods: {
    viewCart() {
      this.$parent.viewCart();
    },
  },
  name: 'Header',
  props: ['cartItemsLength'],
};
</script>
