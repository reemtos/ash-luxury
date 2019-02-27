<template>
    <div class="page">
        <Notification :responseType="responseType" :responseMessage="responseMessage" />
        <Header v-bind:cartItemsLength="cartItems.length" />
        <section class="section section-products">
            <div class="section-bg"></div>
            <div class="container">
                <div class="products" v-if="!loader">
                    <div class="product"
                        v-if="itemIndex < productList.length"
                        v-for="itemIndex in itemsToShow"
                        v-bind:key="itemIndex">
                        <div class="product__info">
                            <img class="product__image"
                                v-bind:src="'/static/images/products/'
                                    + productList[itemIndex].image"
                                />
                            <div class="product__info--extra">
                                <span class="product__price">
                                    {{'\u20A6'}} {{ productList[itemIndex].price }}
                                </span>
                                <ProductSizes :sizes="productList[itemIndex].sizes" />
                                <button class="btn btn-primary"
                                        v-if="!itemIsInCart(productList[itemIndex])"
                                        @click="addToCart(productList[itemIndex])">
                                    Add to cart
                                </button>
                                <button class="btn btn-primary"
                                        v-if="itemIsInCart(productList[itemIndex])"
                                        @click="removeFromCart(productList[itemIndex])">
                                    Remove from cart
                                </button>
                            </div>
                            <div class="product__price-section">
                                <span class="price">
                                    {{'\u20A6'}} {{ productList[itemIndex].price }}
                                </span>
                            </div>
                        </div>
                        <div class="product__details">
                            <div class="details">
                                <h5 class="product__name"
                                    @click="viewProduct(productList[itemIndex])">
                                    {{ productList[itemIndex].name }}
                                </h5>
                                <ProductSizes :sizes="productList[itemIndex].sizes"
                                                :extraClass="'light-color'" />
                            </div>
                            <div class="action">
                                <button class="btn btn-primary bg-orange"
                                        v-if="!itemIsInCart(productList[itemIndex])"
                                        @click="addToCart(productList[itemIndex])">
                                    Add to cart
                                </button>
                                <button class="btn btn-primary bg-orange"
                                        v-if="itemIsInCart(productList[itemIndex])"
                                        @click="removeFromCart(productList[itemIndex])">
                                    Remove from cart
                                </button>
                            </div>
                        </div>
                    </div>
                </div>
                <Loader v-bind:showLoader="loader" />
            </div>
        </section>
        <section class="section section-load-more">
            <div class="container">
                <button class="btn btn-primary outline big"
                        @click="itemsToShow += 3">
                    Load more
                </button>
            </div>
        </section>
        <CTASection />
        <!-- Modal for Product View -->
        <b-modal ref="ProductViewModal" modal-class="product-view" size="md">
            <div slot="modal-header" class="head-section">
                <button class="close" @click="hideProductModal">
                    <i class="fas fa-times-circle"></i>
                </button>
            </div>
            <div class="single-product">
                <img class="single-product__image"
                    v-bind:src="'/static/images/products/' + selectedProduct.image" />
                <div class="single-product__details">
                    <div class="left">
                        <h5 class="single-product__name">{{ selectedProduct.name }}</h5>
                        <ProductSizes :sizes="selectedProduct.sizes" />
                    </div>
                    <div class="right">
                        <span class="single-product__qty">
                            {{ selectedProduct.stockAmount - selectedProductQuantity }} left
                        </span>
                    </div>
                </div>
                <div class="actions">
                    <div class="button-group">
                        <button class="button-quantity minus"
                                @click="decrementQuantity(selectedProduct)">-</button>
                        <label class="button-label">
                            Quantity : {{ selectedProductQuantity }}
                        </label>
                        <button class="button-quantity plus"
                                @click="incrementQuantity(selectedProduct)">+</button>
                    </div>
                    <button class="btn btn-primary main"
                            v-if="!itemIsInCart(selectedProduct)"
                            @click="addToCart(selectedProduct)">Add to cart</button>
                    <button class="btn btn-primary main"
                            v-if="itemIsInCart(selectedProduct)"
                            @click="removeFromCart(selectedProduct)">Remove from cart</button>
                    <span class="checkout" @click="checkoutNow">Checkout now</span>
                </div>
            </div>
            <div slot="modal-footer"></div>
        </b-modal>
        <!-- Modal for Cart View -->
        <b-modal ref="CartModal" modal-class="product-view" size="md">
            <div slot="modal-header"></div>
            <div class="cart-header">
                <h3 class="cart-header__title">Your Cart</h3>
            </div>
            <div class="cart-list" v-if="cartItems.length">
                <div class="cart-list__item"
                    v-for="(item, index) in cartItems"
                    v-bind:key="index">
                    <div class="image-section">
                        <img class="image"
                            v-bind:src="'/static/images/products/' + item.image" />
                    </div>
                    <div class="details-section">
                        <h5 class="name">{{ item.name }}</h5>
                        <ProductSizes :sizes="item.sizes" :extraClass="'light-color'" />
                        <h6 class="price">{{'\u20A6'}} {{ item.price }}</h6>
                    </div>
                    <div class="action-section">
                        <div class="button-group">
                            <button class="button-quantity minus"
                                    @click="decrementQuantity(item)">-</button>
                            <label class="button-label">
                                {{ item.quantity }}
                            </label>
                            <button class="button-quantity plus"
                                    @click="incrementQuantity(item)">+</button>
                        </div>
                        <span class="remove"
                                @click="removeFromCart(item)">remove</span>
                    </div>
                </div>
                <div class="extra-section">
                    <div class="total">
                        <span>Total</span>
                        <span>{{'\u20A6'}} {{ getCartTotal(cartItems) }}</span>
                    </div>
                    <span class="continue" @click="hideCartModal">Continue shopping</span>
                </div>
                <button class="btn btn-primary main full-width"
                        @click="checkout">checkout</button>
            </div>
            <p v-if="!cartItems.length">There are no items in your cart.</p>
            <div slot="modal-footer"></div>
        </b-modal>
    </div>
</template>

<script>
import axios from 'axios';
import BModal from 'bootstrap-vue/es/components/modal/modal';
import BModalDirective from 'bootstrap-vue/es/directives/modal/modal';
import CTASection from '../components/CTASection';
import Header from '../components/Header';
import Loader from '../components/Loader';
import Notification from '../components/Notification';
import ProductSizes from '../components/ProductSizes';

export default {
  components: {
    'b-modal': BModal,
    CTASection,
    Header,
    Loader,
    Notification,
    ProductSizes,
  },
  created() {
    axios.get('/static/data/data.products.json').then((response) => {
      this.productList = response.data;
      this.loader = false;
    });
  },
  data() {
    return {
      cartItems: [],
      itemsToShow: 12,
      loader: true,
      postUrl: 'http://www.mocky.io/v2/5be477442f00004900d9f521',
      productList: [],
      responseMessage: '',
      responseType: '',
      selectedProduct: {},
      selectedProductQuantity: 1,
      totalCartPrice: 0,
    };
  },
  directives: {
    'b-modal': BModalDirective,
  },
  methods: {
    addToCart(item) {
      const index = this.cartItems.findIndex(i => i.id === item.id);
      if (index < 0) {
        this.cartItems.push({ ...item, quantity: this.selectedProductQuantity });
        this.totalCartPrice += item.price;
      }
    },

    checkoutNow() {
      this.$refs.ProductViewModal.hide();
      this.viewCart();
    },

    checkout() {
      axios
        .post(this.postUrl, this.cartItems)
        .then((response) => {
          this.responseMessage = response.data.message;
          this.cartItems = [];
          this.responseType = 'success';
        })
        .catch((response) => {
          this.responseMessage = response;
          this.responseType = 'error';
        });
      this.$refs.CartModal.hide();
    },

    decrementQuantity(item) {
      if (item.quantity) {
        this.selectedProductQuantity = item.quantity;
      }
      if (this.selectedProductQuantity > 1) {
        this.selectedProductQuantity = this.selectedProductQuantity - 1;
        if (item.quantity) {
          item.quantity = this.selectedProductQuantity;
        }
      }
    },

    getCartTotal(cart) {
      return cart.reduce((total, item) => {
        let result = total;
        result += item.price * item.quantity;
        return result;
      }, 0);
    },

    incrementQuantity(item) {
      if (item.quantity) {
        this.selectedProductQuantity = item.quantity;
      }
      if (this.selectedProductQuantity < item.stockAmount) {
        this.selectedProductQuantity = this.selectedProductQuantity + 1;
        if (item.quantity) {
          item.quantity = this.selectedProductQuantity;
        }
      }
    },

    itemIsInCart(item) {
      return this.cartItems.findIndex(i => i.id === item.id) > -1;
    },

    removeFromCart(item) {
      const index = this.cartItems.findIndex(i => i.id === item.id);
      if (index > -1) {
        this.cartItems.splice(index, 1);
      }
    },

    hideProductModal() {
      this.$refs.ProductViewModal.hide();
      this.selectedProductQuantity = 1;
    },

    hideCartModal() {
      this.$refs.CartModal.hide();
    },

    viewCart() {
      this.$refs.CartModal.show();
    },

    viewProduct(item) {
      this.selectedProduct = item;
      this.$refs.ProductViewModal.show();
    },
  },
  mounted() {},
  name: 'Home',
};
</script>
