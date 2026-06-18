<template>
  <div class="app-shell" :class="{ 'sidebar-expanded': isSidebarExpanded }">
    <!-- EXPANDABLE LEFT SIDEBAR -->
    <aside class="side-nav categories-sidebar" :class="{ expanded: isSidebarExpanded }">
      <button class="sidebar-toggle" type="button" @click="toggleSidebar" aria-label="Toggle categories sidebar">
        <i class="fa-solid fa-layer-group"></i>
        <span>Categories</span>
      </button>

      <div class="side-nav-group side-scroll-group categories-only-sidebar">
        <a
          v-for="item in categorySidebarItems"
          :key="item.label"
          :href="item.href"
          :class="{ active: activeSidebar === item.label }"
          @click="setActiveSidebar(item.label)"
        >
          <span class="nav-icon"><i :class="item.icon"></i></span>
          <span class="nav-label">{{ item.label }}</span>
        </a>
      </div>
    </aside>

    <main class="page-wrapper">
      <!-- TOP OFFER BAR -->
      <div class="top-strip">
        Free delivery island wide &nbsp; | &nbsp; pickup on store &nbsp; | &nbsp; premium deals every week
      </div>

      <!-- HEADER -->
      <header class="main-header">
        <nav class="navbar navbar-expand-lg navbar-custom">
          <div class="container-fluid px-4 px-lg-5">
            <a class="brand" href="#">
              lksh<span>o</span>p
            </a>

            <div class="search-box mx-lg-auto">
              <i class="fa-solid fa-magnifying-glass"></i>
              <input
                v-model="searchTerm"
                type="text"
                placeholder="Search for products, brands and categories..."
              />
            </div>

            <div class="d-flex align-items-center gap-2 ms-lg-4">
              <button class="header-icon d-none d-md-grid" type="button" aria-label="Wishlist">
                <i class="fa-regular fa-heart"></i>
                <span v-if="wishlist.length" class="wishlist-count">{{ wishlist.length }}</span>
              </button>

              <button class="header-icon d-none d-md-grid" type="button">
                <i class="fa-regular fa-user"></i>
              </button>

              <button class="header-icon" type="button" @click="openCart">
                <i class="fa-solid fa-cart-shopping"></i>
                <span class="cart-count">{{ cart.length }}</span>
              </button>
            </div>
          </div>
        </nav>
      </header>

      <!-- HERO -->
      <section class="hero-section">
        <div class="hero-full-banner">
          <img
            class="hero-static-img"
            :src="heroBg"
            alt="Premium electronics mega banner"
          />

          <div class="hero-floating">
            <div class="hero-stat">
              <strong>40%</strong>
              <small>Weekly discounts</small>
            </div>

            <div class="hero-stat">
              <strong>24H</strong>
              <small>Fast delivery</small>
            </div>

            <div class="hero-stat">
              <strong>500+</strong>
              <small>Products</small>
            </div>
          </div>
        </div>
      </section>

      <!-- CATEGORY STRIP -->
      <div class="category-strip">
        <div class="category-track">
          <template v-for="round in 2" :key="round">
            <template v-for="category in scrollingCategories" :key="`${round}-${category}`">
              <span>{{ category }}</span>
              <span>|</span>
            </template>
          </template>
        </div>
      </div>


      <!-- RECOMMENDED -->
      <ProductSection
        id="deals"
        title="Recommendation for you"
        subtitle="Recommended products arranged in a clean 5-card row with full-cover product visuals."
        :chips="['New Products', 'Featured Products', 'Top Deals', 'Best Sellers']"
        :products="filteredRecommendedProducts"
        :wishlist-items="wishlistTitles"
        @add-to-cart="addToCart"
        @quick-view="openQuickView"
        @add-wishlist="addToWishlist"
      />


      <!-- SINGLE HOME APPLIANCE IMAGE BANNER -->
      <section class="appliance-banner-section" id="home-appliances">
        <img
          class="appliance-full-banner-img"
          :src="applianceBanner"
          alt="Home appliances promotional banner"
        />
      </section>


      <!-- ELECTRONICS -->
      <ProductSection
        id="electronics"
        title="Electronics"
        subtitle="Gadgets, gaming, computer accessories and smart devices."
        :chips="['Gaming', 'Audio Devices', 'Computer Accessories', 'Cameras', 'Home Appliances']"
        :products="filteredElectronicsProducts"
        :wishlist-items="wishlistTitles"
        @add-to-cart="addToCart"
        @quick-view="openQuickView"
        @add-wishlist="addToWishlist"
      />


      <!-- FEATURED PRODUCT IMAGE GRID -->
      <section class="feature-showcase-section" id="featured-banners">
        <div class="container-fluid px-4 px-lg-5">
          <div class="feature-showcase-head">
            <div>
              <span class="section-kicker">Premium collections</span>
              <h2 class="section-title">Featured Products</h2>
            </div>
            <a href="#deals">View all <i class="fa-solid fa-arrow-right"></i></a>
          </div>

          <div class="feature-showcase-grid">
            <article
              v-for="item in featuredProductBanners"
              :key="item.title"
              class="feature-showcase-card"
              :class="item.className"
            >
              <img :src="item.img" :alt="item.title" />
            </article>
          </div>
        </div>
      </section>


      <!-- HOME ACCESSORIES COLLECTION -->
      <section class="section-block product-grid-section home-accessories-section" id="home-accessories">
        <div class="container-fluid px-4 px-lg-5">
          <div class="section-head product-section-head home-accessories-head">
            <div>
              <span class="section-kicker">Curated home living</span>
              <h2 class="section-title">Home Accessories</h2>
              <p class="section-sub">Premium decor, lighting and everyday accents for modern homes.</p>
            </div>
            <a href="#home-accessories" class="view-link">View more <i class="fa-solid fa-arrow-right ms-1"></i></a>
          </div>

          <div class="home-accessories-layout">
            <aside class="home-accessories-category-card">
              <img
                class="home-accessories-bg"
                :src="homeAccessoriesFeatureImage"
                alt="Home accessories background"
              />
              <div class="home-accessories-overlay"></div>

              <div class="home-accessories-category-content premium-filter-card">
                <div class="home-filter-topbar">
                  <span class="home-filter-brand">LK Shop</span>
                  <span class="home-filter-menu"><i class="fa-solid fa-grip-lines"></i></span>
                </div>

                <div class="home-filter-copy">
                  <span>Home Accessories</span>
                  <h3>Design Your Living Space</h3>
                  <p>Choose a category and discover refined pieces for every corner.</p>
                </div>

                <div class="home-filter-visual-wrap">
                  <img class="home-filter-visual" :src="product4" alt="Premium home accessories visual" />
                </div>

                <div class="home-accessories-tabs premium-filter-tabs">
                  <button
                    v-for="category in homeAccessoryCategories"
                    :key="category"
                    type="button"
                    :class="{ active: activeHomeAccessoryCategory === category }"
                    @click="activeHomeAccessoryCategory = category"
                  >
                    {{ category }}
                  </button>
                </div>
              </div>
            </aside>

            <div class="home-accessories-products product-section-grid">
              <article
                v-for="product in filteredHomeAccessoryProducts"
                :key="product.title"
                class="product-card home-accessory-product-card"
              >
                <div class="product-img">
                  <span v-if="product.badge" class="product-ribbon">{{ product.badge }}</span>
                  <div class="product-actions modern-hover-actions">
                    <button
                      type="button"
                      :class="{ active: isWishlisted(product) }"
                      title="Add to wishlist"
                      aria-label="Add to wishlist"
                      @click.stop="addToWishlist(product)"
                    >
                      <i :class="isWishlisted(product) ? 'fa-solid fa-heart' : 'fa-regular fa-heart'"></i>
                    </button>
                    <button
                      type="button"
                      title="Quick view"
                      aria-label="Quick view"
                      @click.stop="openQuickView(product)"
                    >
                      <i class="fa-regular fa-eye"></i>
                    </button>
                  </div>
                  <img :src="product.img" :alt="product.title" @click="openQuickView(product)" />
                </div>

                <div class="product-body">
                  <div class="product-category">{{ product.category }}</div>
                  <h3 class="product-title">{{ product.title }}</h3>

                  <div class="price">
                    <del v-if="product.old">{{ formatPrice(product.old) }}</del>
                    <strong>{{ formatPrice(product.price) }}</strong>
                  </div>

                  <p class="installment-line">
                    or 3 X <strong>{{ formatPrice(Math.round(product.price / 3)) }}</strong> with
                    <span class="koko-label">KOKO</span>
                  </p>

                  <button class="add-btn" type="button" @click="addToCart(product)">
                    {{ product.buttonLabel || 'Add To Cart' }}
                  </button>
                </div>
              </article>
            </div>
          </div>
        </div>
      </section>

      <!-- HOME ACCESSORIES IMAGE BANNER -->
      <section class="home-accessories-image-banner-section" id="home-accessories-banner">
        <img
          class="home-accessories-image-banner-img"
          :src="homeAccessoriesBanner"
          alt="Home accessories promotional banner"
        />
      </section>

      <!-- BEST OF WEEK -->
      <ProductSection
        id="best-week"
        title="Best of The Week"
        subtitle="Top selling items with special weekly prices."
        :chips="['Personal Care Gadget', 'Office Equipment', 'Gaming', 'Top Deals']"
        :products="filteredBestProducts"
        :wishlist-items="wishlistTitles"
        @add-to-cart="addToCart"
        @quick-view="openQuickView"
        @add-wishlist="addToWishlist"
      />

      <!-- MODERN CUSTOMER TESTIMONIALS -->
      <section class="modern-testimonials-section" id="reviews">
        <div class="container-fluid px-4 px-lg-5">
          <div class="modern-testimonial-head">
            <div>
              <span class="section-kicker">Customer stories</span>
              <h2 class="section-title">Customer Testimonials</h2>
              <p class="section-sub">Real shopping experiences from customers who trust LK Shop.</p>
            </div>
            <a href="#deals" class="view-link">Shop customer picks <i class="fa-solid fa-arrow-right ms-1"></i></a>
          </div>

          <div class="modern-testimonial-grid">
            <article
              v-for="review in reviews"
              :key="review.name"
              class="modern-testimonial-card"
            >
              <div class="testimonial-quote-icon">
                <i class="fa-solid fa-quote-left"></i>
              </div>
              <div class="modern-rating">★★★★★</div>
              <p class="modern-testimonial-text">{{ review.text }}</p>

              <div class="modern-testimonial-person">
                <div class="modern-avatar">{{ review.initial }}</div>
                <div>
                  <h5>{{ review.name }}</h5>
                  <small>{{ review.time }}</small>
                </div>
              </div>
            </article>
          </div>
        </div>
      </section>

      <!-- MODERN FOOTER -->
      <footer class="modern-footer" id="footer">
        <div class="container-fluid px-4 px-lg-5">
          <div class="modern-footer-top">
            <div class="modern-footer-brand">
              <a class="footer-brand" href="#">lksh<span>o</span>p</a>
              <p>
                Premium ecommerce experience for electronics, appliances, lifestyle products and daily essentials.
              </p>
              <div class="footer-socials">
                <a href="#" aria-label="Facebook"><i class="fa-brands fa-facebook-f"></i></a>
                <a href="#" aria-label="Instagram"><i class="fa-brands fa-instagram"></i></a>
                <a href="#" aria-label="LinkedIn"><i class="fa-brands fa-linkedin-in"></i></a>
                <a href="#" aria-label="X"><i class="fa-brands fa-x-twitter"></i></a>
              </div>
            </div>

            <div class="modern-footer-links">
              <div class="footer-link-col">
                <h5>Shop</h5>
                <a href="#electronics">Electronics</a>
                <a href="#home-appliances">Home Appliances</a>
                <a href="#home-accessories">Home Accessories</a>
                <a href="#best-week">Latest Products</a>
              </div>

              <div class="footer-link-col">
                <h5>Support</h5>
                <a href="#">Help Center</a>
                <a href="#">Delivery Information</a>
                <a href="#">Returns & Refunds</a>
                <a href="#">Warranty Support</a>
              </div>

              <div class="footer-link-col">
                <h5>Company</h5>
                <a href="#">About LK Shop</a>
                <a href="#">Careers</a>
                <a href="#">Partner With Us</a>
                <a href="#">Contact Us</a>
              </div>
            </div>

            <div class="footer-newsletter-card">
              <span>Stay updated</span>
              <h4>Get new deals first</h4>
              <p>Subscribe for weekly offers, new arrivals and limited product drops.</p>
              <form class="footer-newsletter-form" @submit.prevent="subscribeUser">
                <input v-model="newsletterEmail" type="email" placeholder="Email address" />
                <button type="submit"><i class="fa-solid fa-arrow-right"></i></button>
              </form>
            </div>
          </div>

          <div class="modern-footer-bottom">
            <span>Copyright © 2026 LK Shop. All Rights Reserved.</span>
            <div>
              <a href="#">Privacy Policy</a>
              <a href="#">Terms</a>
              <a href="#">Cookies</a>
            </div>
          </div>
        </div>
      </footer>
    </main>

    <!-- ADD TO CART TOAST -->
    <div class="cart-toast" :class="{ show: toast.visible }">
      <img :src="toast.product?.img || ''" alt="Added product" />
      <div>
        <h6>{{ toast.product?.title || 'Product added' }}</h6>
        <p>{{ toast.message || 'Added to your cart successfully.' }}</p>
      </div>
    </div>

    <!-- CART DRAWER -->
    <div class="overlay" :class="{ show: isCartOpen }" @click="closeCart"></div>

    <aside class="cart-drawer" :class="{ open: isCartOpen }">
      <div class="drawer-head">
        <h4>Your Cart</h4>
        <button class="btn-close" type="button" @click="closeCart"></button>
      </div>

      <div class="drawer-body">
        <p v-if="cart.length === 0" class="text-muted fw-semibold">Your cart is empty.</p>

        <div v-for="(item, index) in cart" :key="`${item.title}-${index}`" class="drawer-item">
          <img :src="item.img" :alt="item.title" />
          <div class="flex-grow-1">
            <h6>{{ item.title }}</h6>
            <p>{{ formatPrice(item.price) }}</p>
          </div>
          <button class="remove-btn" type="button" @click="removeFromCart(index)">
            <i class="fa-solid fa-xmark"></i>
          </button>
        </div>
      </div>

      <div class="drawer-footer">
        <div class="d-flex justify-content-between mb-3 fw-bold">
          <span>Total</span>
          <span>{{ formatPrice(cartTotal) }}</span>
        </div>
        <button class="checkout-btn">Proceed to Checkout</button>
      </div>
    </aside>

    <!-- MODERN QUICK VIEW -->
    <div
      class="quick-view-backdrop"
      :class="{ show: quickViewProduct }"
      @click="closeQuickView"
    ></div>

    <section
      v-if="quickViewProduct"
      class="quick-view-modal"
      role="dialog"
      aria-modal="true"
      aria-label="Product quick view"
    >
      <button class="quick-view-close" type="button" aria-label="Close quick view" @click="closeQuickView">
        <i class="fa-solid fa-xmark"></i>
      </button>

      <div class="quick-view-media">
        <span v-if="quickViewProduct.badge" class="quick-view-badge">{{ quickViewProduct.badge }}</span>
        <div class="quick-view-orb"></div>
        <img :src="quickViewProduct.img" :alt="quickViewProduct.title" />
      </div>

      <div class="quick-view-content">
        <span class="quick-view-kicker">{{ quickViewProduct.category }}</span>
        <h2>{{ quickViewProduct.title }}</h2>
        <p class="quick-view-description">
          A premium selected product from LK Shop with fast delivery, secure checkout and a cleaner shopping experience.
        </p>

        <div class="quick-view-price-row">
          <strong>{{ formatPrice(quickViewProduct.price) }}</strong>
          <del v-if="quickViewProduct.old">{{ formatPrice(quickViewProduct.old) }}</del>
        </div>

        <p class="quick-view-installment">
          or 3 X <strong>{{ formatPrice(Math.round(quickViewProduct.price / 3)) }}</strong> with
          <span class="koko-label">KOKO</span>
        </p>

        <div class="quick-view-meta-grid">
          <div>
            <i class="fa-solid fa-truck-fast"></i>
            <span>Fast delivery</span>
          </div>
          <div>
            <i class="fa-solid fa-shield-halved"></i>
            <span>Secure checkout</span>
          </div>
          <div>
            <i class="fa-solid fa-rotate-left"></i>
            <span>Easy returns</span>
          </div>
        </div>

        <div class="quick-view-actions">
          <button class="quick-add-cart" type="button" @click="addToCart(quickViewProduct)">
            <i class="fa-solid fa-cart-shopping"></i>
            Add to cart
          </button>
          <button class="quick-checkout" type="button" @click="quickCheckout(quickViewProduct)">
            Quick checkout
            <i class="fa-solid fa-arrow-right"></i>
          </button>
        </div>

        <button
          class="quick-wishlist-btn"
          type="button"
          :class="{ active: isWishlisted(quickViewProduct) }"
          @click="addToWishlist(quickViewProduct)"
        >
          <i :class="isWishlisted(quickViewProduct) ? 'fa-solid fa-heart' : 'fa-regular fa-heart'"></i>
          {{ isWishlisted(quickViewProduct) ? 'Added to wishlist' : 'Add to wishlist' }}
        </button>
      </div>
    </section>
  </div>
</template>

<script setup>
import { computed, defineComponent, h, onUnmounted, ref } from 'vue'
import heroBg from '@/assets/hero-bg.png'
import applianceBanner from '@/assets/appliance-banner.png'
import product1 from '@/assets/product-1.webp'
import product2 from '@/assets/product-2.webp'
import product3 from '@/assets/product-3.webp'
import product4 from '@/assets/product-4.webp'
import product5 from '@/assets/product-5.webp'
import homeAccessoriesBanner from '@/assets/home-accessories-banner.png'
import featured1 from '@/assets/featured-1.png'
import featured2 from '@/assets/featured-2.png'
import featured3 from '@/assets/featured-3.png'
import featured4 from '@/assets/featured-4.png'

const ProductSection = defineComponent({
  name: 'ProductSection',
  props: {
    id: {
      type: String,
      default: undefined,
    },
    title: {
      type: String,
      required: true,
    },
    subtitle: {
      type: String,
      required: true,
    },
    chips: {
      type: Array,
      required: true,
    },
    products: {
      type: Array,
      required: true,
    },
    wishlistItems: {
      type: Array,
      default: () => [],
    },
  },
  emits: ['add-to-cart', 'quick-view', 'add-wishlist'],
  setup(props, { emit }) {
    const activeChip = ref(props.chips[0])
    const formatPrice = (price) => `Rs ${Number(price).toLocaleString('en-LK')}.00`
    const formatInstallment = (price) => `Rs ${Number(price).toLocaleString('en-LK')}.00`

    return () =>
      h('section', { class: 'section-block product-grid-section', id: props.id }, [
        h('div', { class: 'container-fluid px-4 px-lg-5' }, [
          h('div', { class: 'section-head product-section-head' }, [
            h('div', {}, [
              h('h2', { class: 'section-title' }, props.title),
              h('p', { class: 'section-sub' }, props.subtitle),
            ]),
            h('a', { href: '#', class: 'view-link' }, [
              'View more ',
              h('i', { class: 'fa-solid fa-arrow-right ms-1' }),
            ]),
          ]),

          h(
            'div',
            { class: 'chip-row product-chip-row' },
            props.chips.map((chip) =>
              h(
                'button',
                {
                  class: ['chip', { active: activeChip.value === chip }],
                  type: 'button',
                  onClick: () => {
                    activeChip.value = chip
                  },
                },
                chip,
              ),
            ),
          ),

          props.products.length
            ? h(
                'div',
                { class: 'products-grid product-section-grid' },
                props.products.map((product) => {
                  const isOutOfStock =
                    product.outOfStock ||
                    product.stock === 'out' ||
                    product.stockStatus === 'out' ||
                    String(product.badge || '').toLowerCase().includes('out of stock')
                  const ribbonText = isOutOfStock ? 'OUT OF STOCK' : product.badge

                  return h(
                    'article',
                    { class: ['product-card', { 'is-out-of-stock': isOutOfStock }], key: product.title },
                    [
                      h('div', { class: 'product-img' }, [
                        ribbonText
                          ? h(
                              'span',
                              { class: ['product-ribbon', { 'out-stock': isOutOfStock }] },
                              ribbonText,
                            )
                          : null,
                        h('div', { class: 'product-actions modern-hover-actions' }, [
                          h(
                            'button',
                            {
                              type: 'button',
                              title: 'Add to wishlist',
                              'aria-label': 'Add to wishlist',
                              class: { active: props.wishlistItems.includes(product.title) },
                              onClick: (event) => {
                                event.stopPropagation()
                                emit('add-wishlist', product)
                              },
                            },
                            [h('i', { class: props.wishlistItems.includes(product.title) ? 'fa-solid fa-heart' : 'fa-regular fa-heart' })],
                          ),
                          h(
                            'button',
                            {
                              type: 'button',
                              title: 'Quick view',
                              'aria-label': 'Quick view',
                              onClick: (event) => {
                                event.stopPropagation()
                                emit('quick-view', product)
                              },
                            },
                            [h('i', { class: 'fa-regular fa-eye' })],
                          ),
                        ]),
                        h('img', {
                          src: product.img,
                          alt: product.title,
                          onClick: () => emit('quick-view', product),
                        }),
                      ]),
                      h('div', { class: 'product-body' }, [
                        h('div', { class: 'product-category' }, product.category),
                        h('h3', { class: 'product-title' }, product.title),
                        h('div', { class: 'price' }, [
                          product.old ? h('del', {}, formatPrice(product.old)) : null,
                          h('strong', {}, formatPrice(product.price)),
                        ]),
                        h('p', { class: 'installment-line' }, [
                          'or 3 X ',
                          h('strong', {}, formatInstallment(Math.round(product.price / 3))),
                          ' with ',
                          h('span', { class: 'koko-label' }, 'KOKO'),
                        ]),
                        h(
                          'button',
                          {
                            class: ['add-btn', { disabled: isOutOfStock }],
                            type: 'button',
                            disabled: isOutOfStock,
                            onClick: () => {
                              if (!isOutOfStock) emit('add-to-cart', product)
                            },
                          },
                          isOutOfStock ? 'Out Of Stock' : product.buttonLabel || 'Add To Cart',
                        ),
                      ]),
                    ],
                  )
                }),
              )
            : h('div', { class: 'empty-results' }, 'No products found.'),
        ]),
      ])
  },
})

const searchTerm = ref('')
const newsletterEmail = ref('')
const cart = ref([])
const wishlist = ref([])
const quickViewProduct = ref(null)
const isCartOpen = ref(false)
const isSidebarExpanded = ref(false)
const activeSidebar = ref('Electronics')
const toast = ref({
  visible: false,
  product: null,
  message: 'Added to your cart successfully.',
})
let toastTimer = null

onUnmounted(() => {
  clearTimeout(toastTimer)
})


const productImages = [product1, product2, product3, product4, product5]

const applyProductImages = (items) => {
  items.forEach((item, index) => {
    item.img = productImages[index % productImages.length]
  })

  return items
}

const categorySidebarItems = [
  { label: 'Electronics', href: '#electronics', icon: 'fa-solid fa-microchip' },
  { label: 'Home Appliances', href: '#home-appliances', icon: 'fa-solid fa-blender-phone' },
  { label: 'Home Accessories', href: '#home-accessories', icon: 'fa-solid fa-couch' },
  { label: 'Mobile & Tablets', href: '#electronics', icon: 'fa-solid fa-mobile-screen-button' },
  { label: 'Computer Accessories', href: '#electronics', icon: 'fa-solid fa-keyboard' },
  { label: 'Gaming', href: '#featured-banners', icon: 'fa-solid fa-gamepad' },
  { label: 'Audio Devices', href: '#electronics', icon: 'fa-solid fa-headphones-simple' },
  { label: 'Cameras', href: '#electronics', icon: 'fa-solid fa-camera-retro' },
  { label: 'Smart Home', href: '#home-appliances', icon: 'fa-solid fa-house-signal' },
  { label: 'Health & Beauty', href: '#deals', icon: 'fa-solid fa-spa' },
  { label: 'Office Equipment', href: '#best-week', icon: 'fa-solid fa-briefcase' },
  { label: 'Cleaning Products', href: '#deals', icon: 'fa-solid fa-spray-can-sparkles' },
  { label: 'Kitchen Accessories', href: '#home-accessories', icon: 'fa-solid fa-mug-hot' },
  { label: 'Storage', href: '#home-accessories', icon: 'fa-solid fa-box-archive' },
  { label: 'Bedding & Cushions', href: '#home-accessories', icon: 'fa-solid fa-bed' },
  { label: 'Tableware', href: '#home-accessories', icon: 'fa-solid fa-utensils' },
  { label: 'Fashion', href: '#deals', icon: 'fa-solid fa-shirt' },
  { label: 'Sports & Outdoors', href: '#reviews', icon: 'fa-solid fa-dumbbell' },
  { label: 'Toys & Baby', href: '#featured-banners', icon: 'fa-solid fa-puzzle-piece' },
  { label: 'Best Sellers', href: '#best-week', icon: 'fa-solid fa-crown' },
]

const scrollingCategories = [
  'Audio Devices',
  'Cameras',
  'Computer Accessories',
  'E-Readers',
  'Gaming Consoles',
  'Home Appliances',
  'Office Equipment',
  'Personal Care',
]

const promos = [
  {
    badge: 'Trending',
    title: 'Gaming Zone',
    text: 'Controllers, consoles and accessories',
    image: 'https://images.unsplash.com/photo-1542751371-adc38448a05e?q=80&w=900&auto=format&fit=crop',
  },
  {
    badge: '40% Off',
    title: 'Top Deals',
    text: 'Special prices for selected products',
    image: 'https://images.unsplash.com/photo-1599940824399-b87987ceb72a?q=80&w=900&auto=format&fit=crop',
  },
  {
    badge: 'New',
    title: 'Headphones',
    text: 'Newest audio arrivals',
    image: 'https://images.unsplash.com/photo-1505740420928-5e560c06d30e?q=80&w=900&auto=format&fit=crop',
  },
  {
    badge: 'Hot',
    title: 'PS5 Games',
    text: 'Gaming products for every player',
    image: 'https://images.unsplash.com/photo-1605901309584-818e25960a8f?q=80&w=900&auto=format&fit=crop',
  },
]

const featuredProductBanners = [
  {
    title: 'Featured product main banner',
    img: featured1,
    className: 'feature-tile-large',
  },
  {
    title: 'Featured product top banner one',
    img: featured2,
    className: 'feature-tile-small-a',
  },
  {
    title: 'Featured product top banner two',
    img: featured3,
    className: 'feature-tile-small-b',
  },
  {
    title: 'Featured product wide banner',
    img: featured4,
    className: 'feature-tile-wide',
  },
]

const weeklyShowcaseCards = [
  {
    heading: 'Best of the Week',
    title: 'INNO64 Nissan Skyline GT-R Top Secret White IN64-R34TS-WHI',
    category: 'Diecast Model Cars',
    price: 6950,
    old: 8250,
    badge: '',
    cta: 'Add To Cart',
    img: 'https://images.unsplash.com/photo-1581235720704-06d3acfcb36f?q=80&w=900&auto=format&fit=crop',
  },
  {
    heading: 'Sale Products',
    title: 'GCD 1:64 Mini Cooper 1964 Racecar Set 4 Versions',
    category: 'Collectable Toys',
    price: 8950,
    old: 10500,
    badge: '8% OFF',
    cta: 'Select Options',
    img: 'https://images.unsplash.com/photo-1594736797933-d0501ba2fe65?q=80&w=900&auto=format&fit=crop',
  },
]

const topWideBanners = [
  {
    badge: 'NEW',
    title: 'The Latest E-Readers',
    img: 'https://images.unsplash.com/photo-1513001900722-370f803f498d?q=80&w=1200&auto=format&fit=crop',
  },
  {
    badge: 'NEW',
    title: 'Decorate Your Table With a Desk Clock',
    img: 'https://images.unsplash.com/photo-1501139083538-0139583c060f?q=80&w=1200&auto=format&fit=crop',
  },
]

const homeAccessoryCategories = [
  'All',
  'Home Decor',
  'Lighting',
  'Kitchen Accessories',
  'Storage',
  'Bedding & Cushions',
  'Tableware',
]
const activeHomeAccessoryCategory = ref('All')
const homeAccessoriesFeatureImage =
  'https://images.unsplash.com/photo-1616046229478-9901c5536a45?q=80&w=1100&auto=format&fit=crop'

const homeAccessoryProducts = [
  {
    title: 'Minimal Ceramic Vase Set',
    category: 'Home Decor',
    price: 6950,
    old: 8250,
    badge: 'New',
    buttonLabel: 'Add To Cart',
  },
  {
    title: 'Modern Table Lamp',
    category: 'Lighting',
    price: 8950,
    old: 10500,
    badge: '15% Off',
    buttonLabel: 'Add To Cart',
  },
  {
    title: 'Premium Cushion Collection',
    category: 'Bedding & Cushions',
    price: 6630,
    old: 7800,
    badge: 'Deal',
    buttonLabel: 'Add To Cart',
  },
  {
    title: 'Decorative Storage Basket',
    category: 'Storage',
    price: 10370,
    old: 12200,
    badge: 'Best',
    buttonLabel: 'Select Options',
  },
  {
    title: 'Marble Serving Tray',
    category: 'Kitchen Accessories',
    price: 5850,
    old: 6900,
    badge: 'New',
    buttonLabel: 'Add To Cart',
  },
  {
    title: 'Glass Tableware Set',
    category: 'Tableware',
    price: 12950,
    old: 14950,
    badge: 'Premium',
    buttonLabel: 'Add To Cart',
  },
  {
    title: 'Soft Linen Throw Pillow',
    category: 'Bedding & Cushions',
    price: 4450,
    old: 5200,
    badge: 'Deal',
    buttonLabel: 'Add To Cart',
  },
  {
    title: 'Wooden Desk Organizer',
    category: 'Storage',
    price: 7650,
    old: 8900,
    badge: 'Best',
    buttonLabel: 'Add To Cart',
  },
]

const mosaicBanners = [
  {
    title: 'Unleash Your Music',
    badge: '-30%',
    className: 'tile-a',
    img: 'https://images.unsplash.com/photo-1608043152269-423dbba4e7e1?q=80&w=900&auto=format&fit=crop',
  },
  {
    title: 'Top Brand Toys 60% Off',
    badge: 'BIG SALE',
    className: 'tile-b',
    img: 'https://images.unsplash.com/photo-1596461404969-9ae70f2830c1?q=80&w=900&auto=format&fit=crop',
  },
  {
    title: 'Immersive Gaming Realm',
    badge: 'NEW',
    className: 'tile-c',
    img: 'https://images.unsplash.com/photo-1605901309584-818e25960a8f?q=80&w=900&auto=format&fit=crop',
  },
  {
    title: 'Immersive Gaming Realm',
    badge: 'NEW',
    className: 'tile-d',
    img: 'https://images.unsplash.com/photo-1607853202273-797f1c22a38e?q=80&w=900&auto=format&fit=crop',
  },
  {
    title: 'Camera Sale!',
    kicker: 'Popular Brands',
    className: 'tile-e',
    img: 'https://images.unsplash.com/photo-1516035069371-29a1b244cc32?q=80&w=1100&auto=format&fit=crop',
  },
]

const recommendedProducts = [
  {
    title: 'Harpic Power Plus 10X Cleaner',
    category: 'Cleaning Product',
    price: 4374,
    old: 5450,
    reviews: 24,
    badge: '10% Off',
    img: 'https://images.unsplash.com/photo-1585421514284-efb74c2b69ba?q=80&w=900&auto=format&fit=crop',
  },
  {
    title: 'Premium Floral Birthday Cake',
    category: 'Lifestyle',
    price: 2500,
    old: 3200,
    reviews: 31,
    badge: 'New',
    img: 'https://images.unsplash.com/photo-1578985545062-69928b1d9587?q=80&w=900&auto=format&fit=crop',
  },
  {
    title: 'Atlas Active Fit School Bag C1',
    category: 'School Bags',
    price: 2750,
    old: 3750,
    reviews: 19,
    badge: 'Deal',
    img: 'https://images.unsplash.com/photo-1622560480605-d83c853bc5c3?q=80&w=900&auto=format&fit=crop',
  },
  {
    title: 'Casio FX-991ES Plus Scientific Calculator',
    category: 'Office Equipment',
    price: 8200,
    old: 9200,
    reviews: 52,
    badge: 'Popular',
    img: 'https://images.unsplash.com/photo-1564473185935-58113cba1e80?q=80&w=900&auto=format&fit=crop',
  },
  {
    title: 'STC General Hot Water Shower',
    category: 'Home Appliance',
    price: 44491,
    old: 48990,
    reviews: 14,
    badge: 'Premium',
    img: 'https://images.unsplash.com/photo-1584622650111-993a426fbf0a?q=80&w=900&auto=format&fit=crop',
  },
]

const electronicsProducts = [
  {
    title: 'Q16 AMD Console 3.5 Inch Game Player',
    category: 'Gaming Console',
    price: 9500,
    old: 11900,
    reviews: 67,
    badge: 'Top',
    img: 'https://images.unsplash.com/photo-1606144042614-b2417e99c4e3?q=80&w=600&auto=format&fit=crop',
  },
  {
    title: 'UGREEN 4K HDMI Extension Cable',
    category: 'Computer Accessories',
    price: 8450,
    old: 9350,
    reviews: 44,
    badge: 'New',
    img: 'https://images.unsplash.com/photo-1611174797138-52c2873ca2b1?q=80&w=600&auto=format&fit=crop',
  },
  {
    title: 'PS5 Hot Wheels Monster Trucks',
    category: 'PS5 Games',
    price: 13950,
    old: 15950,
    reviews: 37,
    badge: 'Sale',
    img: 'https://images.unsplash.com/photo-1607853202273-797f1c22a38e?q=80&w=600&auto=format&fit=crop',
  },
  {
    title: 'ADATA SC750 External SSD',
    category: 'Storage Devices',
    price: 39950,
    old: 45950,
    reviews: 23,
    badge: 'Fast',
    img: 'https://images.unsplash.com/photo-1601737487795-dab272f52420?q=80&w=600&auto=format&fit=crop',
  },
  {
    title: 'Logitech MK215 Wireless Keyboard',
    category: 'Computer Accessories',
    price: 7950,
    old: 8950,
    reviews: 81,
    badge: 'Best',
    img: 'https://images.unsplash.com/photo-1587829741301-dc798b83add3?q=80&w=600&auto=format&fit=crop',
  },
]

const premiumFeature = {
  title: 'Sony WH-1000XM Premium Headphones',
  category: 'Premium Audio',
  price: 98500,
  old: 115000,
  reviews: 96,
  badge: 'Premium',
  img: 'https://images.unsplash.com/photo-1505740420928-5e560c06d30e?q=80&w=900&auto=format&fit=crop',
}

const premiumProducts = [
  {
    title: 'PS5 DualSense Controller',
    category: 'Gaming',
    price: 28950,
    old: 32950,
    img: 'https://images.unsplash.com/photo-1605901309584-818e25960a8f?q=80&w=600&auto=format&fit=crop',
  },
  {
    title: 'Apple Watch Style Tracker',
    category: 'Wearable',
    price: 74900,
    old: 89900,
    img: 'https://images.unsplash.com/photo-1523275335684-37898b6baf30?q=80&w=600&auto=format&fit=crop',
  },
  {
    title: 'Premium Portable Speaker',
    category: 'Audio',
    price: 24500,
    old: 29900,
    img: 'https://images.unsplash.com/photo-1608043152269-423dbba4e7e1?q=80&w=600&auto=format&fit=crop',
  },
  {
    title: 'Ergonomic Wireless Mouse',
    category: 'Office',
    price: 13950,
    old: 16500,
    img: 'https://images.unsplash.com/photo-1527814050087-3793815479db?q=80&w=600&auto=format&fit=crop',
  },
]

const lifestyleProducts = [
  {
    title: 'Harpic Bathroom Cleaner',
    category: 'Personal Care',
    price: 4374,
    old: 5450,
    reviews: 25,
    badge: '10% Off',
    img: 'https://images.unsplash.com/photo-1585421514284-efb74c2b69ba?q=80&w=900&auto=format&fit=crop',
  },
  {
    title: 'Atlas Active Fit Backpack',
    category: 'Bags',
    price: 2750,
    old: 3750,
    reviews: 30,
    badge: 'Deal',
    img: 'https://images.unsplash.com/photo-1553062407-98eeb64c6a62?q=80&w=600&auto=format&fit=crop',
  },
  {
    title: 'Casio Scientific Calculator',
    category: 'Office',
    price: 8200,
    old: 9200,
    reviews: 52,
    badge: 'Popular',
    img: 'https://images.unsplash.com/photo-1564473185935-58113cba1e80?q=80&w=900&auto=format&fit=crop',
  },
  {
    title: 'Premium Hot Water Shower',
    category: 'Home',
    price: 44491,
    old: 48990,
    reviews: 14,
    badge: 'Premium',
    img: 'https://images.unsplash.com/photo-1584622650111-993a426fbf0a?q=80&w=900&auto=format&fit=crop',
  },
  {
    title: 'Double A A4 80 GSM Paper',
    category: 'Paper Products',
    price: 1580,
    old: 1750,
    reviews: 63,
    badge: 'Office',
    img: 'https://images.unsplash.com/photo-1586075010923-2dd4570fb338?q=80&w=600&auto=format&fit=crop',
  },
]

const bestProducts = [
  {
    title: 'Wireless Gaming Controller',
    category: 'Gaming',
    price: 10950,
    old: 12500,
    reviews: 38,
    badge: 'Best',
    img: 'https://images.unsplash.com/photo-1605901309584-818e25960a8f?q=80&w=600&auto=format&fit=crop',
  },
  {
    title: 'Bluetooth Headphone Pro',
    category: 'Audio',
    price: 8950,
    old: 10950,
    reviews: 52,
    badge: 'Sale',
    img: 'https://images.unsplash.com/photo-1505740420928-5e560c06d30e?q=80&w=600&auto=format&fit=crop',
  },
  {
    title: 'Smart Watch Fitness Tracker',
    category: 'Wearable',
    price: 7250,
    old: 8900,
    reviews: 77,
    badge: 'Hot',
    img: 'https://images.unsplash.com/photo-1523275335684-37898b6baf30?q=80&w=600&auto=format&fit=crop',
  },
  {
    title: 'Portable Speaker Mini',
    category: 'Audio Devices',
    price: 5950,
    old: 7500,
    reviews: 49,
    badge: 'New',
    img: 'https://images.unsplash.com/photo-1608043152269-423dbba4e7e1?q=80&w=600&auto=format&fit=crop',
  },
  {
    title: 'Modern Office Mouse',
    category: 'Office Equipment',
    price: 3850,
    old: 4500,
    reviews: 21,
    badge: 'Office',
    img: 'https://images.unsplash.com/photo-1527814050087-3793815479db?q=80&w=600&auto=format&fit=crop',
  },
]


applyProductImages(weeklyShowcaseCards)
applyProductImages(homeAccessoryProducts)
applyProductImages(recommendedProducts)
applyProductImages(electronicsProducts)
applyProductImages(premiumProducts)
applyProductImages(lifestyleProducts)
applyProductImages(bestProducts)
premiumFeature.img = product1

const moreProductGridProducts = [
  {
    title: 'Smart Home Control Hub',
    category: 'Smart Devices',
    price: 14950,
    old: 17950,
    reviews: 46,
    badge: 'New',
  },
  {
    title: 'Premium Desk Setup Kit',
    category: 'Office Picks',
    price: 12950,
    old: 14950,
    reviews: 29,
    badge: 'Popular',
  },
  {
    title: 'Wireless Audio Dock',
    category: 'Audio',
    price: 16950,
    old: 19950,
    reviews: 38,
    badge: 'Hot',
  },
  {
    title: 'Compact Kitchen Helper',
    category: 'Home Essentials',
    price: 9950,
    old: 11950,
    reviews: 31,
    badge: 'Deal',
  },
  {
    title: 'Everyday Tech Organizer',
    category: 'Top Rated',
    price: 5750,
    old: 6950,
    reviews: 54,
    badge: 'Best',
  },
]


applyProductImages(moreProductGridProducts)
const reviews = [
  {
    initial: 'P',
    name: 'Pasandul Senadhira',
    time: '1 month ago',
    text: 'Good service, fast delivery and product quality was better than expected.',
  },
  {
    initial: 'A',
    name: 'Abdullah Hisham',
    time: '1 month ago',
    text: 'Purchase came next day and the packaging was very clean.',
  },
  {
    initial: 'T',
    name: 'Thorushka Dinujaya',
    time: '2 months ago',
    text: 'Best customer support. They arranged same day delivery for me.',
  },
  {
    initial: 'A',
    name: 'Ajeema Shafeek',
    time: '1 month ago',
    text: 'Nice collection and very easy shopping experience.',
  },
]

const formatPrice = (price) => `Rs.${Number(price).toLocaleString('en-LK')}`

const filterProducts = (items) => {
  const keyword = searchTerm.value.trim().toLowerCase()

  if (!keyword) return items

  return items.filter((item) => `${item.title} ${item.category}`.toLowerCase().includes(keyword))
}

const filteredRecommendedProducts = computed(() => filterProducts(recommendedProducts))
const filteredElectronicsProducts = computed(() => filterProducts(electronicsProducts))
const filteredHomeAccessoryProducts = computed(() => {
  const category = activeHomeAccessoryCategory.value
  const categoryItems = category === 'All'
    ? homeAccessoryProducts
    : homeAccessoryProducts.filter((item) => item.category === category)

  return filterProducts(categoryItems)
})
const filteredLifestyleProducts = computed(() => filterProducts(lifestyleProducts))
const filteredMoreProducts = computed(() => filterProducts(moreProductGridProducts))
const filteredBestProducts = computed(() => filterProducts(bestProducts))
const cartTotal = computed(() => cart.value.reduce((sum, item) => sum + item.price, 0))
const wishlistTitles = computed(() => wishlist.value.map((item) => item.title))

const toggleSidebar = () => {
  isSidebarExpanded.value = !isSidebarExpanded.value
}

const setActiveSidebar = (label) => {
  activeSidebar.value = label
}

const showProductToast = (product, message = 'Added to your cart successfully.') => {
  toast.value = {
    visible: true,
    product,
    message,
  }

  clearTimeout(toastTimer)
  toastTimer = setTimeout(() => {
    toast.value.visible = false
  }, 2600)
}

const addToCart = (product) => {
  cart.value.push(product)
  showProductToast(product)
}

const addToWishlist = (product) => {
  const exists = wishlist.value.some((item) => item.title === product.title)

  if (!exists) {
    wishlist.value.push(product)
    showProductToast(product, 'Added to wishlist successfully.')
  }
}

const isWishlisted = (product) => wishlist.value.some((item) => item.title === product.title)

const openQuickView = (product) => {
  quickViewProduct.value = product
}

const closeQuickView = () => {
  quickViewProduct.value = null
}

const quickCheckout = (product) => {
  addToCart(product)
  closeQuickView()
  openCart()
}

const removeFromCart = (index) => {
  cart.value.splice(index, 1)
}

const openCart = () => {
  isCartOpen.value = true
}

const closeCart = () => {
  isCartOpen.value = false
}

const subscribeUser = () => {
  if (!newsletterEmail.value) {
    alert('Please enter your email address.')
    return
  }

  alert(`Thank you for subscribing: ${newsletterEmail.value}`)
  newsletterEmail.value = ''
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Manrope:wght@400;500;600;700;800;900&display=swap');

:root {
  --primary: #0b4a8b;
  --primary-dark: #073664;
  --accent: #ff3b30;
  --blue: #0077ff;
  --text: #151922;
  --muted: #6b7280;
  --light: #f6f8fb;
  --border: #e8edf4;
  --white: #ffffff;
  --dark: #111827;
  --radius: 22px;
  --sidebar-collapsed: 78px;
  --sidebar-expanded: 244px;
  --shadow: 0 18px 45px rgba(17, 24, 39, 0.08);
  --premium-shadow: 0 35px 90px rgba(9, 38, 88, 0.22);
}

* {
  box-sizing: border-box;
}

html {
  scroll-behavior: smooth;
}

body {
  margin: 0;
  font-family: 'Manrope', sans-serif;
  background: #fff;
  color: var(--text);
  overflow-x: hidden;
}

a {
  text-decoration: none;
  color: inherit;
}

img {
  max-width: 100%;
  display: block;
}

button {
  font-family: inherit;
}

/* EXPANDABLE SIDEBAR */
.side-nav {
  position: fixed;
  top: 0;
  left: 0;
  width: var(--sidebar-collapsed);
  height: 100vh;
  background: #ffffff;
  border-right: 1px solid var(--border);
  z-index: 1050;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 18px 10px;
  gap: 10px;
  transition: width 0.28s ease, box-shadow 0.28s ease;
  overflow: hidden;
}

.side-nav.expanded {
  width: var(--sidebar-expanded);
  align-items: stretch;
  box-shadow: 18px 0 45px rgba(15, 23, 42, 0.08);
}

.sidebar-toggle {
  width: 48px;
  height: 48px;
  border: 0;
  background: transparent;
  color: #64748b;
  border-radius: 14px;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 12px;
  font-size: 20px;
  transition: 0.25s ease;
  flex-shrink: 0;
}

.sidebar-toggle span {
  display: none;
  font-size: 17px;
  font-weight: 900;
  color: var(--text);
  letter-spacing: -0.8px;
}

.side-nav.expanded .sidebar-toggle {
  width: 100%;
  justify-content: flex-start;
  padding: 0 14px;
  background: #f7f9fc;
}

.side-nav.expanded .sidebar-toggle span {
  display: inline-flex;
}

.side-nav-group {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
}

.side-nav.expanded .side-nav-group {
  align-items: stretch;
}

.side-scroll-group {
  flex: 1;
  max-height: none;
  overflow: hidden auto;
  padding: 6px 0 18px;
  scrollbar-width: thin;
  scrollbar-color: rgba(11, 74, 139, 0.22) transparent;
}

.side-scroll-group::-webkit-scrollbar {
  width: 4px;
}

.side-scroll-group::-webkit-scrollbar-thumb {
  background: rgba(11, 74, 139, 0.22);
  border-radius: 999px;
}

.categories-sidebar {
  min-height: 100vh;
}

.categories-only-sidebar {
  justify-content: flex-start;
}

.side-nav a {
  width: 48px;
  min-height: 48px;
  border-radius: 0 15px 15px 0;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #64748b;
  font-size: 18px;
  position: relative;
  transition: 0.25s ease;
  flex-shrink: 0;
}

.side-nav.expanded a {
  width: 100%;
  justify-content: flex-start;
  border-radius: 16px;
  padding: 0 12px;
  gap: 12px;
}

.side-nav a::before {
  content: '';
  position: absolute;
  left: -10px;
  top: 8px;
  bottom: 8px;
  width: 4px;
  border-radius: 999px;
  background: transparent;
  transition: 0.25s ease;
}

.side-nav a:hover,
.side-nav a.active {
  color: var(--primary);
  background: #f2f7ff;
}

.side-nav a.active::before {
  background: var(--blue);
}

.nav-icon {
  width: 26px;
  height: 26px;
  display: grid;
  place-items: center;
  flex-shrink: 0;
}

.nav-label {
  display: none;
  font-size: 13px;
  font-weight: 850;
  color: #334155;
  white-space: nowrap;
}

.side-nav.expanded .nav-label {
  display: inline-flex;
}

.nav-badge {
  display: none;
  margin-left: auto;
  background: #ffefed;
  color: var(--accent);
  font-size: 10px;
  font-weight: 900;
  border-radius: 999px;
  padding: 4px 7px;
}

.side-nav.expanded .nav-badge {
  display: inline-flex;
}

.side-separator {
  width: 36px;
  height: 1px;
  background: var(--border);
  margin: 8px 0;
  flex-shrink: 0;
}

.side-nav.expanded .side-separator {
  width: 100%;
}

.page-wrapper {
  margin-left: var(--sidebar-collapsed);
  transition: margin-left 0.28s ease;
}

.app-shell.sidebar-expanded .page-wrapper {
  margin-left: var(--sidebar-expanded);
}

/* TOP BAR */
.top-strip {
  height: 34px;
  background: #111827;
  color: #fff;
  font-size: 11px;
  letter-spacing: 1.3px;
  display: flex;
  align-items: center;
  justify-content: center;
  text-transform: uppercase;
  font-weight: 800;
}

.main-header {
  position: sticky;
  top: 0;
  z-index: 1040;
  background: rgba(255, 255, 255, 0.88);
  backdrop-filter: blur(18px);
  border-bottom: 1px solid var(--border);
}

.navbar-custom {
  min-height: 78px;
}

.brand {
  display: flex;
  align-items: center;
  gap: 8px;
  font-weight: 900;
  font-size: 28px;
  letter-spacing: -1px;
  color: var(--text);
}

.brand span {
  color: var(--accent);
}

.search-box {
  max-width: 580px;
  width: 100%;
  position: relative;
}

.search-box input {
  height: 48px;
  border-radius: 999px;
  border: 1px solid var(--border);
  background: var(--light);
  padding-left: 52px;
  padding-right: 18px;
  font-size: 14px;
  font-weight: 650;
  outline: none;
  box-shadow: none;
  width: 100%;
}

.search-box i {
  position: absolute;
  top: 50%;
  left: 20px;
  transform: translateY(-50%);
  color: var(--muted);
}

.header-icon {
  width: 42px;
  height: 42px;
  border-radius: 50%;
  border: 1px solid var(--border);
  display: grid;
  place-items: center;
  background: #fff;
  color: var(--primary);
  position: relative;
  transition: 0.25s ease;
  cursor: pointer;
}

.header-icon:hover {
  background: var(--primary);
  color: #fff;
}

.cart-count {
  position: absolute;
  top: -5px;
  right: -5px;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background: var(--accent);
  color: #fff;
  font-size: 11px;
  font-weight: 900;
  display: grid;
  place-items: center;
}

/* HERO */
.hero-section {
  padding: 18px 0 28px;
  background: #ffffff;
  overflow: visible;
}

.hero-full-banner {
  width: 100%;
  min-height: clamp(430px, 42vw, 760px);
  position: relative;
  overflow: visible;
  background: #ffffff;
}

.hero-static-img {
  position: absolute;
  inset: 0;
  z-index: 1;
  width: 100%;
  height: 100%;
  object-fit: contain;
  object-position: center;
}

.hero-floating {
  position: absolute;
  left: clamp(28px, 7vw, 120px);
  bottom: clamp(28px, 5vw, 78px);
  z-index: 3;
  display: flex;
  gap: 12px;
  align-items: center;
}

.hero-stat {
  background: rgba(9, 20, 42, 0.72);
  border: 1px solid rgba(255, 255, 255, 0.28);
  backdrop-filter: blur(16px);
  color: #fff;
  border-radius: 18px;
  padding: 14px 18px;
  min-width: 118px;
  box-shadow: 0 18px 42px rgba(15, 23, 42, 0.18);
}

.hero-stat strong {
  display: block;
  font-size: 24px;
  line-height: 1;
  font-weight: 900;
}

.hero-stat small {
  color: rgba(255, 255, 255, 0.78);
  font-weight: 750;
  font-size: 11px;
}

/* CATEGORY MARQUEE */
.category-strip {
  background: var(--blue);
  color: #fff;
  overflow: hidden;
  padding: 11px 0;
  white-space: nowrap;
  font-size: 12px;
  letter-spacing: 1.5px;
  text-transform: uppercase;
  font-weight: 900;
}

.category-track {
  display: inline-flex;
  gap: 24px;
  animation: moveText 28s linear infinite;
}

@keyframes moveText {
  from {
    transform: translateX(0);
  }

  to {
    transform: translateX(-50%);
  }
}


/* SINGLE HOME APPLIANCE IMAGE BANNER */
.appliance-banner-section {
  width: 100%;
  padding: 22px 0 36px;
  background: #ffffff;
  overflow: hidden;
}

.appliance-full-banner-img {
  display: block;
  width: 100%;
  height: clamp(360px, 32vw, 500px);
  object-fit: cover;
  object-position: center;
}

/* SHOPIFY STYLE PREMIUM FEATURE BANNERS */
.shopify-banners-section {
  padding: 34px 0 22px;
  background:
    radial-gradient(circle at 15% 15%, rgba(0, 119, 255, 0.08), transparent 28%),
    linear-gradient(180deg, #ffffff 0%, #f7f9fc 100%);
}

.shopify-banners-head {
  display: flex;
  justify-content: space-between;
  align-items: end;
  gap: 20px;
  margin-bottom: 22px;
}

.section-kicker {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  color: var(--blue);
  font-size: 12px;
  font-weight: 950;
  text-transform: uppercase;
  letter-spacing: 1.2px;
  margin-bottom: 8px;
}

.shopify-banners-head h2 {
  margin: 0;
  font-size: clamp(28px, 3vw, 44px);
  line-height: 1;
  font-weight: 950;
  letter-spacing: -1.8px;
  color: var(--text);
}

.shopify-banners-head a {
  color: var(--dark);
  font-size: 12px;
  font-weight: 950;
  text-transform: uppercase;
  letter-spacing: 0.8px;
  display: inline-flex;
  align-items: center;
  gap: 8px;
  white-space: nowrap;
}

.shopify-banners-grid {
  display: grid;
  grid-template-columns: 1.15fr 0.92fr 0.92fr;
  gap: 22px;
}

.shopify-feature-banner {
  min-height: 390px;
  border-radius: 32px;
  overflow: hidden;
  position: relative;
  isolation: isolate;
  background: #f4f7fb;
  border: 1px solid rgba(226, 232, 240, 0.75);
  box-shadow: 0 24px 70px rgba(15, 23, 42, 0.08);
  transition: transform 0.35s ease, box-shadow 0.35s ease;
}

.shopify-feature-banner:hover {
  transform: translateY(-8px);
  box-shadow: 0 34px 95px rgba(15, 23, 42, 0.14);
}

.banner-style-1 {
  background:
    linear-gradient(135deg, rgba(8, 18, 34, 0.92), rgba(11, 74, 139, 0.7)),
    linear-gradient(180deg, #eaf1fb, #ffffff);
  color: #fff;
}

.banner-style-2 {
  background:
    radial-gradient(circle at 75% 15%, rgba(0, 119, 255, 0.2), transparent 28%),
    linear-gradient(145deg, #f8fbff, #eaf1f8);
}

.banner-style-3 {
  background:
    radial-gradient(circle at 20% 20%, rgba(255, 59, 48, 0.16), transparent 30%),
    linear-gradient(145deg, #101827, #0b4a8b);
  color: #fff;
}

.banner-glow {
  position: absolute;
  width: 260px;
  height: 260px;
  border-radius: 50%;
  right: -90px;
  top: -90px;
  background: rgba(255, 255, 255, 0.28);
  filter: blur(4px);
  z-index: -1;
}

.banner-copy {
  position: relative;
  z-index: 2;
  max-width: 310px;
  padding: 34px 30px;
}

.banner-copy span {
  display: inline-flex;
  align-items: center;
  width: fit-content;
  border-radius: 999px;
  background: var(--blue);
  color: #fff;
  padding: 7px 11px;
  font-size: 10px;
  font-weight: 950;
  text-transform: uppercase;
  letter-spacing: 0.7px;
  margin-bottom: 16px;
}

.banner-style-1 .banner-copy span,
.banner-style-3 .banner-copy span {
  background: rgba(255, 255, 255, 0.16);
  border: 1px solid rgba(255, 255, 255, 0.24);
  backdrop-filter: blur(12px);
}

.banner-copy h3 {
  margin: 0 0 12px;
  font-size: clamp(27px, 2.2vw, 40px);
  line-height: 1.02;
  font-weight: 950;
  letter-spacing: -1.4px;
}

.banner-copy p {
  margin: 0 0 18px;
  color: #5f6b7a;
  font-size: 13px;
  font-weight: 700;
  line-height: 1.75;
}

.banner-style-1 .banner-copy p,
.banner-style-3 .banner-copy p {
  color: rgba(255, 255, 255, 0.76);
}

.banner-price-row {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 20px;
}

.banner-price-row strong {
  font-size: 18px;
  font-weight: 950;
  color: var(--primary);
}

.banner-style-1 .banner-price-row strong,
.banner-style-3 .banner-price-row strong {
  color: #fff;
}

.banner-price-row del {
  color: #9aa4b2;
  font-size: 13px;
  font-weight: 800;
}

.banner-copy button {
  border: 0;
  height: 43px;
  border-radius: 999px;
  padding: 0 18px;
  display: inline-flex;
  align-items: center;
  gap: 9px;
  background: #fff;
  color: var(--dark);
  font-size: 12px;
  font-weight: 950;
  text-transform: uppercase;
  letter-spacing: 0.7px;
  box-shadow: 0 14px 30px rgba(15, 23, 42, 0.12);
  transition: 0.25s ease;
}

.banner-style-2 .banner-copy button {
  background: var(--dark);
  color: #fff;
}

.banner-copy button:hover {
  transform: translateY(-2px);
  background: var(--accent);
  color: #fff;
}

.banner-product-img {
  position: absolute;
  right: -8%;
  bottom: -8%;
  width: 78%;
  height: 72%;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1;
}

.banner-style-1 .banner-product-img {
  width: 74%;
  height: 78%;
  right: -12%;
}

.banner-style-2 .banner-product-img {
  width: 72%;
  height: 70%;
  right: -15%;
  bottom: -4%;
}

.banner-style-3 .banner-product-img {
  width: 76%;
  height: 74%;
  right: -17%;
  bottom: -6%;
}

.banner-product-img img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 28px 0 0 0;
  transform: rotate(-2deg) scale(1.02);
  filter: drop-shadow(0 34px 42px rgba(0, 0, 0, 0.22));
  transition: 0.4s ease;
}

.banner-style-2 .banner-product-img img {
  object-position: center;
}

.shopify-feature-banner:hover .banner-product-img img {
  transform: rotate(0deg) scale(1.08);
}

/* PROMOS */
.promo-grid {
  padding: 34px 0 58px;
}

.promo-card {
  min-height: 250px;
  border-radius: 24px;
  overflow: hidden;
  position: relative;
  background-size: cover;
  background-position: center;
  box-shadow: 0 15px 35px rgba(15, 23, 42, 0.08);
  transition: 0.3s ease;
}

.promo-card:hover {
  transform: translateY(-6px);
}

.promo-card::before {
  content: '';
  position: absolute;
  inset: 0;
  background: linear-gradient(180deg, rgba(0, 0, 0, 0.08), rgba(0, 0, 0, 0.62));
}

.promo-info {
  position: absolute;
  left: 24px;
  bottom: 22px;
  right: 24px;
  color: #fff;
  z-index: 2;
}

.promo-info h3 {
  font-size: 22px;
  font-weight: 900;
  letter-spacing: -0.6px;
  margin: 0;
}

.promo-info p {
  margin: 6px 0 0;
  color: rgba(255, 255, 255, 0.8);
  font-size: 13px;
  font-weight: 650;
}

.promo-badge {
  position: absolute;
  top: 18px;
  left: 18px;
  z-index: 3;
  background: #fff;
  color: var(--dark);
  padding: 7px 10px;
  border-radius: 10px;
  font-size: 12px;
  font-weight: 900;
}

/* SECTIONS */
.section-block {
  padding: 28px 0 54px;
}

.section-head {
  display: flex;
  justify-content: space-between;
  align-items: end;
  gap: 18px;
  margin-bottom: 22px;
  border-bottom: 1px solid var(--border);
  padding-bottom: 18px;
}

.section-title {
  font-size: 27px;
  font-weight: 900;
  letter-spacing: -1px;
  margin: 0;
}

.section-sub {
  margin: 4px 0 0;
  color: var(--muted);
  font-size: 13px;
  font-weight: 650;
}

.view-link {
  font-size: 12px;
  font-weight: 900;
  text-transform: uppercase;
  letter-spacing: 0.8px;
  color: var(--primary);
  white-space: nowrap;
}

.chip-row {
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
  margin-bottom: 26px;
}

.chip {
  border: 1px solid var(--border);
  background: #fff;
  border-radius: 999px;
  padding: 8px 15px;
  color: #475569;
  font-size: 12px;
  font-weight: 850;
  cursor: pointer;
  transition: 0.25s ease;
}

.chip:hover,
.chip.active {
  background: var(--primary);
  border-color: var(--primary);
  color: #fff;
}

.empty-results {
  padding: 28px;
  border: 1px dashed var(--border);
  border-radius: 18px;
  color: var(--muted);
  font-size: 14px;
  font-weight: 750;
  background: var(--light);
}

/* STRICT 5-CARD DESKTOP PRODUCT ROW */
.products-grid {
  display: grid;
  grid-template-columns: repeat(5, minmax(0, 1fr));
  gap: 22px;
}

.product-card {
  border: 1px solid var(--border);
  border-radius: 22px;
  overflow: hidden;
  background: #fff;
  height: 100%;
  transition: 0.3s ease;
  position: relative;
  box-shadow: 0 10px 30px rgba(15, 23, 42, 0.035);
}

.product-card:hover {
  transform: translateY(-7px);
  box-shadow: var(--shadow);
  border-color: transparent;
}

.product-img {
  height: 215px;
  background: linear-gradient(180deg, #f8fbff, #f3f6fb);
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 28px;
  position: relative;
  overflow: hidden;
}

#deals .product-img {
  height: 250px;
  padding: 0;
  background: #eef3f8;
}

.product-img::after {
  content: '';
  position: absolute;
  width: 120px;
  height: 120px;
  border-radius: 50%;
  background: rgba(11, 74, 139, 0.07);
  bottom: -45px;
  right: -35px;
}

.product-img img {
  width: 100%;
  height: 100%;
  object-fit: contain;
  transition: 0.35s ease;
  z-index: 1;
}

#deals .product-img img {
  object-fit: cover;
  object-position: center;
}

#deals .product-img::after {
  display: none;
}

.product-card:hover .product-img img {
  transform: scale(1.07);
}

.product-ribbon {
  position: absolute;
  top: 18px;
  right: -42px;
  z-index: 6;
  width: 145px;
  height: 29px;
  display: grid;
  place-items: center;
  transform: rotate(45deg);
  background: linear-gradient(135deg, #0d8cff, #005fe8);
  color: #fff;
  font-size: 11px;
  font-weight: 500;
  line-height: 1;
  letter-spacing: 0.7px;
  text-align: center;
  text-transform: uppercase;
  box-shadow: 0 8px 18px rgba(0, 95, 232, 0.22);
  pointer-events: none;
}

.product-ribbon.out-stock {
  background: linear-gradient(135deg, #111827, #64748b);
  box-shadow: 0 8px 18px rgba(15, 23, 42, 0.2);
}

.product-actions {
  position: absolute;
  top: 12px;
  left: 12px;
  z-index: 4;
  display: flex;
  flex-direction: column;
  gap: 8px;
  opacity: 0;
  transform: translateX(-8px);
  transition: 0.25s ease;
}

.product-card:hover .product-actions {
  opacity: 1;
  transform: translateX(0);
}

.product-actions button {
  width: 34px;
  height: 34px;
  border-radius: 50%;
  border: 1px solid var(--border);
  background: #fff;
  color: var(--primary);
  display: grid;
  place-items: center;
  box-shadow: 0 8px 20px rgba(15, 23, 42, 0.08);
}

.product-body {
  padding: 18px;
}

.product-category {
  color: var(--muted);
  font-size: 11px;
  font-weight: 900;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 7px;
}

.product-title {
  min-height: 42px;
  font-size: 14px;
  font-weight: 900;
  line-height: 1.45;
  margin-bottom: 8px;
  color: var(--text);
}

.rating {
  color: #ffb000;
  font-size: 12px;
  margin-bottom: 8px;
}

.rating span {
  color: #94a3b8;
  font-weight: 800;
  margin-left: 4px;
}

.price {
  display: flex;
  align-items: center;
  gap: 8px;
  flex-wrap: wrap;
  margin-bottom: 14px;
}

.price strong {
  font-size: 15px;
  color: var(--primary);
  font-weight: 900;
}

.price del {
  color: #9ca3af;
  font-size: 12px;
  font-weight: 750;
}

.add-btn {
  height: 39px;
  width: 100%;
  border-radius: 12px;
  border: 1px solid var(--primary);
  background: #fff;
  color: var(--primary);
  font-size: 12px;
  font-weight: 900;
  transition: 0.25s ease;
}

.add-btn:hover {
  background: var(--primary);
  color: #fff;
}

.add-btn.disabled,
.add-btn:disabled {
  border-color: #cbd5e1;
  background: #f1f5f9;
  color: #94a3b8;
  cursor: not-allowed;
}

.add-btn.disabled:hover,
.add-btn:disabled:hover {
  background: #f1f5f9;
  color: #94a3b8;
}

.product-card.is-out-of-stock .product-img img {
  filter: grayscale(0.65);
  opacity: 0.74;
}


/* CLEAN PRODUCT GRID SECTION - reference style */
.product-grid-section {
  padding: 72px 0 78px;
  background: #fff;
}

.product-grid-section .container-fluid {
  max-width: 1420px;
}

.product-section-head {
  align-items: center;
  margin-bottom: 42px;
  padding-bottom: 24px;
  border-bottom: 1px solid #dedede;
}

.product-grid-section .section-title {
  font-size: 35px;
  font-weight: 500;
  letter-spacing: -0.6px;
  color: #2d2d2f;
}

.product-grid-section .section-sub {
  display: none;
}

.product-grid-section .view-link {
  color: #202124;
  font-size: 12px;
  font-weight: 900;
  letter-spacing: 1.15px;
}

.product-chip-row {
  justify-content: center;
  gap: 32px;
  margin-bottom: 64px;
}

.product-chip-row .chip {
  min-height: 31px;
  padding: 5px 13px;
  border-radius: 999px;
  border-color: #cfd3d8;
  background: #fff;
  color: #303236;
  font-size: 17px;
  font-weight: 500;
  line-height: 1;
  box-shadow: none;
}

.product-chip-row .chip:hover,
.product-chip-row .chip.active {
  background: #fff;
  border-color: #aeb4bc;
  color: #111827;
  box-shadow: 0 7px 18px rgba(15, 23, 42, 0.07);
}

.product-section-grid {
  gap: 18px;
  align-items: stretch;
}

.product-grid-section .product-card {
  min-height: 474px;
  border-radius: 19px;
  border: 1px solid #e4e4e4;
  box-shadow: 0 3px 12px rgba(15, 23, 42, 0.08);
  overflow: hidden;
  display: flex;
  flex-direction: column;
}

.product-grid-section .product-card:hover {
  transform: translateY(-4px);
  border-color: #dddddd;
  box-shadow: 0 9px 24px rgba(15, 23, 42, 0.12);
}

.product-grid-section .product-img,
#deals .product-img {
  height: 236px;
  padding: 30px 28px 14px;
  background: #fff;
}

.product-grid-section .product-img::after,
#deals .product-img::after {
  display: none;
}

.product-grid-section .product-img img,
#deals .product-img img {
  width: 100%;
  height: 100%;
  object-fit: contain;
  object-position: center;
}

.product-grid-section .product-card:hover .product-img img {
  transform: scale(1.035);
}

.product-grid-section .product-ribbon {
  top: 16px;
  right: -44px;
  width: 150px;
  height: 29px;
  font-size: 11px;
  font-weight: 500;
  letter-spacing: 0.5px;
  background: #0878dd;
  box-shadow: none;
}

.product-grid-section .product-ribbon.out-stock {
  background: linear-gradient(135deg, #111827, #64748b);
  box-shadow: none;
}

.product-grid-section .product-body {
  padding: 8px 24px 20px;
  text-align: center;
  display: flex;
  flex: 1;
  flex-direction: column;
  align-items: center;
}

.product-grid-section .product-category {
  min-height: 38px;
  margin-bottom: 8px;
  color: #838383;
  font-size: 12.5px;
  font-weight: 500;
  line-height: 1.45;
  text-transform: none;
  letter-spacing: 0;
}

.product-grid-section .product-title {
  min-height: 56px;
  margin-bottom: 14px;
  color: #2a2a2a;
  font-size: 17px;
  font-weight: 400;
  line-height: 1.48;
  letter-spacing: 0.15px;
}

.product-grid-section .rating {
  display: none;
}

.product-grid-section .price {
  justify-content: center;
  gap: 4px;
  margin-bottom: 9px;
  width: 100%;
  line-height: 1.2;
}

.product-grid-section .price del {
  color: #b9b9b9;
  font-size: 16px;
  font-weight: 800;
}

.product-grid-section .price strong {
  color: #050505;
  font-size: 16px;
  font-weight: 850;
}

.installment-line {
  margin: 0 0 16px;
  color: #8a8a8a;
  font-size: 13px;
  font-weight: 500;
}

.installment-line strong {
  color: #8e8e8e;
  font-weight: 850;
}

.koko-label {
  color: #5f7dff;
  font-size: 11px;
  font-weight: 950;
  text-shadow: 1px 0 #ff4ea3, -1px 0 #3bd4ff;
}

.product-grid-section .add-btn {
  width: auto;
  min-width: 132px;
  height: 31px;
  margin-top: auto;
  border-radius: 0;
  border: 1px solid #dddddd;
  background: #fff;
  color: #4b4b4b;
  font-size: 13px;
  font-weight: 500;
  letter-spacing: 0.2px;
}

.product-grid-section .add-btn:hover {
  border-color: #0b74de;
  background: #0b74de;
  color: #fff;
}

.product-grid-section .add-btn.disabled,
.product-grid-section .add-btn:disabled {
  border-color: #dddddd;
  background: #f5f5f5;
  color: #9b9b9b;
}

.product-grid-section .product-actions {
  display: flex;
}

/* PREMIUM SHOWCASE */
.premium-showcase {
  padding: 50px 0 64px;
}

.premium-wrapper {
  position: relative;
  overflow: hidden;
  border-radius: 34px;
  background:
    radial-gradient(circle at 20% 15%, rgba(0, 119, 255, 0.34), transparent 28%),
    radial-gradient(circle at 80% 20%, rgba(255, 212, 0, 0.18), transparent 25%),
    linear-gradient(135deg, #061b3d, #0a3970 45%, #08111f);
  color: #fff;
  padding: 42px;
  display: grid;
  grid-template-columns: 1.05fr 0.85fr 1fr;
  gap: 28px;
  align-items: stretch;
  box-shadow: var(--premium-shadow);
}

.premium-wrapper::before {
  content: '';
  position: absolute;
  inset: 1px;
  border-radius: 33px;
  border: 1px solid rgba(255, 255, 255, 0.14);
  pointer-events: none;
}

.premium-copy,
.premium-feature-card,
.premium-mini-grid {
  position: relative;
  z-index: 1;
}

.premium-kicker {
  display: inline-flex;
  padding: 8px 12px;
  border-radius: 999px;
  background: rgba(255, 255, 255, 0.12);
  border: 1px solid rgba(255, 255, 255, 0.18);
  font-size: 11px;
  font-weight: 900;
  text-transform: uppercase;
  letter-spacing: 1px;
  margin-bottom: 18px;
}

.premium-copy h2 {
  font-size: clamp(32px, 4vw, 56px);
  font-weight: 950;
  line-height: 0.98;
  letter-spacing: -2px;
  margin-bottom: 18px;
}

.premium-copy p {
  color: rgba(255, 255, 255, 0.76);
  font-size: 14px;
  line-height: 1.8;
  max-width: 520px;
}

.premium-points {
  display: flex;
  flex-direction: column;
  gap: 10px;
  margin: 24px 0;
}

.premium-points span {
  display: inline-flex;
  align-items: center;
  gap: 9px;
  color: rgba(255, 255, 255, 0.86);
  font-size: 13px;
  font-weight: 800;
}

.premium-points i {
  color: #ffd400;
}

.premium-main-btn {
  min-height: 48px;
  border: 0;
  border-radius: 999px;
  background: #fff;
  color: var(--dark);
  padding: 0 22px;
  display: inline-flex;
  align-items: center;
  gap: 10px;
  font-size: 13px;
  font-weight: 900;
  text-transform: uppercase;
  letter-spacing: 0.4px;
  transition: 0.25s ease;
}

.premium-main-btn:hover {
  background: #ffd400;
  transform: translateY(-2px);
}

.premium-feature-card {
  border-radius: 28px;
  overflow: hidden;
  padding: 22px;
  min-height: 430px;
  background: rgba(255, 255, 255, 0.12);
  border: 1px solid rgba(255, 255, 255, 0.16);
  backdrop-filter: blur(16px);
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.premium-glow {
  position: absolute;
  width: 220px;
  height: 220px;
  border-radius: 50%;
  background: rgba(255, 212, 0, 0.14);
  top: 80px;
  left: 50%;
  transform: translateX(-50%);
  filter: blur(10px);
}

.premium-tag {
  align-self: flex-start;
  background: #ffd400;
  color: #101827;
  border-radius: 999px;
  padding: 8px 11px;
  font-size: 11px;
  font-weight: 950;
  text-transform: uppercase;
  z-index: 2;
}

.premium-feature-card img {
  height: 235px;
  width: 100%;
  object-fit: contain;
  filter: drop-shadow(0 25px 35px rgba(0, 0, 0, 0.28));
  z-index: 2;
}

.premium-feature-card small,
.premium-mini-card span {
  color: rgba(255, 255, 255, 0.64);
  font-size: 11px;
  font-weight: 850;
  text-transform: uppercase;
  letter-spacing: 0.6px;
}

.premium-feature-card h3 {
  font-size: 22px;
  line-height: 1.2;
  font-weight: 950;
  margin: 7px 0 10px;
}

.premium-price {
  display: flex;
  align-items: center;
  gap: 10px;
}

.premium-price strong {
  font-size: 18px;
  font-weight: 950;
  color: #ffd400;
}

.premium-price del {
  color: rgba(255, 255, 255, 0.48);
  font-size: 13px;
  font-weight: 800;
}

.premium-mini-grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 14px;
}

.premium-mini-card {
  text-align: left;
  border: 1px solid rgba(255, 255, 255, 0.16);
  background: rgba(255, 255, 255, 0.1);
  color: #fff;
  border-radius: 22px;
  padding: 16px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  transition: 0.25s ease;
  min-height: 206px;
}

.premium-mini-card:hover {
  background: rgba(255, 255, 255, 0.16);
  transform: translateY(-4px);
}

.premium-mini-card img {
  height: 92px;
  width: 100%;
  object-fit: contain;
  margin-bottom: 12px;
  filter: drop-shadow(0 14px 20px rgba(0, 0, 0, 0.24));
}

.premium-mini-card h4 {
  font-size: 13px;
  font-weight: 900;
  line-height: 1.35;
  margin: 5px 0 7px;
}

.premium-mini-card strong {
  color: #ffd400;
  font-size: 13px;
  font-weight: 950;
}

/* WIDE BANNER */
.wide-banner {
  margin: 26px 0 60px;
  min-height: 360px;
  background:
    linear-gradient(90deg, rgba(0, 0, 0, 0.72), rgba(0, 0, 0, 0.22)),
    url('https://images.unsplash.com/photo-1516321318423-f06f85e504b3?q=80&w=1800&auto=format&fit=crop');
  background-size: cover;
  background-position: center;
  display: flex;
  align-items: center;
  color: #fff;
}

.wide-banner h2 {
  font-size: clamp(38px, 6vw, 82px);
  line-height: 0.95;
  font-weight: 950;
  letter-spacing: -3px;
  margin-bottom: 18px;
}

.wide-banner p {
  max-width: 530px;
  color: rgba(255, 255, 255, 0.82);
  font-weight: 650;
  line-height: 1.8;
}

/* REVIEWS */
.review-card {
  border: 1px solid var(--border);
  border-radius: 20px;
  padding: 22px;
  height: 100%;
  background: #fff;
  box-shadow: 0 10px 30px rgba(15, 23, 42, 0.035);
}

.review-top {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 12px;
}

.avatar {
  width: 42px;
  height: 42px;
  border-radius: 50%;
  background: var(--light);
  display: grid;
  place-items: center;
  font-weight: 900;
  color: var(--primary);
}

.review-card h5 {
  font-size: 14px;
  font-weight: 900;
  margin: 0;
}

.review-card small {
  color: var(--muted);
  font-size: 11px;
  font-weight: 750;
}

.review-card p {
  color: #4b5563;
  font-size: 13px;
  line-height: 1.7;
  margin: 0;
  font-weight: 650;
}

/* NEWSLETTER */
.newsletter {
  padding: 70px 0;
  text-align: center;
}

.newsletter h3 {
  font-size: 24px;
  font-weight: 900;
  letter-spacing: -0.7px;
}

.newsletter p {
  color: var(--muted);
  font-size: 14px;
  font-weight: 650;
  margin-bottom: 22px;
}

.newsletter-form {
  max-width: 560px;
  margin: auto;
  display: flex;
  background: #fff;
  border: 1px solid var(--border);
  border-radius: 999px;
  padding: 6px;
  box-shadow: 0 12px 30px rgba(15, 23, 42, 0.06);
}

.newsletter-form input {
  border: 0;
  outline: 0;
  flex: 1;
  padding: 0 18px;
  font-size: 14px;
  font-weight: 650;
}

.newsletter-form button {
  border: 0;
  border-radius: 999px;
  background: var(--dark);
  color: #fff;
  height: 44px;
  padding: 0 22px;
  font-size: 13px;
  font-weight: 900;
}

/* FOOTER */
.footer {
  background: #161616;
  color: #fff;
  padding: 70px 0 25px;
}

.footer h5 {
  font-size: 13px;
  text-transform: uppercase;
  letter-spacing: 0.8px;
  font-weight: 900;
  margin-bottom: 18px;
}

.footer a,
.footer p {
  color: rgba(255, 255, 255, 0.66);
  font-size: 13px;
  font-weight: 650;
  display: block;
  margin-bottom: 10px;
}

.footer a:hover {
  color: #fff;
}

.socials {
  display: flex;
  gap: 10px;
}

.socials a {
  width: 36px;
  height: 36px;
  border-radius: 50%;
  display: grid;
  place-items: center;
  background: rgba(255, 255, 255, 0.08);
  color: #fff;
  margin: 0;
}

.footer-bottom {
  border-top: 1px solid rgba(255, 255, 255, 0.1);
  margin-top: 45px;
  padding-top: 20px;
  color: rgba(255, 255, 255, 0.55);
  font-size: 12px;
  font-weight: 650;
  display: flex;
  justify-content: space-between;
  gap: 16px;
  flex-wrap: wrap;
}

/* TOAST */
.cart-toast {
  position: fixed;
  right: 24px;
  top: 100px;
  width: 320px;
  background: #fff;
  border: 1px solid var(--border);
  border-radius: 18px;
  box-shadow: var(--shadow);
  padding: 14px;
  display: flex;
  gap: 12px;
  align-items: center;
  z-index: 2000;
  transform: translateX(120%);
  opacity: 0;
  transition: 0.35s ease;
}

.cart-toast.show {
  transform: translateX(0);
  opacity: 1;
}

.cart-toast img {
  width: 58px;
  height: 58px;
  border-radius: 14px;
  object-fit: contain;
  background: var(--light);
  padding: 6px;
}

.cart-toast h6 {
  font-size: 13px;
  font-weight: 900;
  margin: 0 0 4px;
}

.cart-toast p {
  font-size: 12px;
  font-weight: 750;
  color: var(--muted);
  margin: 0;
}

/* MODERN PRODUCT HOVER ACTIONS */
.wishlist-count {
  position: absolute;
  top: -5px;
  right: -5px;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background: var(--blue);
  color: #fff;
  font-size: 11px;
  font-weight: 900;
  display: grid;
  place-items: center;
}

.modern-hover-actions {
  left: auto;
  right: 14px;
  top: 14px;
  z-index: 8;
  gap: 9px;
  transform: translateY(-8px) scale(0.96);
}

.product-card:hover .modern-hover-actions {
  transform: translateY(0) scale(1);
}

.modern-hover-actions button {
  width: 39px;
  height: 39px;
  background: rgba(255, 255, 255, 0.88);
  color: #101827;
  border: 1px solid rgba(226, 232, 240, 0.9);
  backdrop-filter: blur(14px);
  box-shadow: 0 16px 34px rgba(15, 23, 42, 0.12);
}

.modern-hover-actions button:hover,
.modern-hover-actions button.active {
  background: #0b4a8b;
  border-color: #0b4a8b;
  color: #fff;
}

.product-img img {
  cursor: pointer;
}

.product-card::before {
  content: '';
  position: absolute;
  inset: 0;
  z-index: 2;
  background: linear-gradient(180deg, rgba(255,255,255,0), rgba(11,74,139,0.06));
  opacity: 0;
  pointer-events: none;
  transition: 0.28s ease;
}

.product-card:hover::before {
  opacity: 1;
}

/* MODERN QUICK VIEW */
.quick-view-backdrop {
  position: fixed;
  inset: 0;
  z-index: 2200;
  background: rgba(8, 15, 28, 0.56);
  backdrop-filter: blur(12px);
  opacity: 0;
  pointer-events: none;
  transition: 0.3s ease;
}

.quick-view-backdrop.show {
  opacity: 1;
  pointer-events: auto;
}

.quick-view-modal {
  position: fixed;
  top: 50%;
  left: 50%;
  z-index: 2210;
  width: min(980px, calc(100vw - 34px));
  max-height: calc(100vh - 34px);
  display: grid;
  grid-template-columns: 0.95fr 1.05fr;
  overflow: hidden;
  border-radius: 32px;
  background: rgba(255, 255, 255, 0.96);
  border: 1px solid rgba(255, 255, 255, 0.72);
  box-shadow: 0 45px 120px rgba(2, 6, 23, 0.35);
  transform: translate(-50%, -50%);
}

.quick-view-close {
  position: absolute;
  top: 18px;
  right: 18px;
  z-index: 4;
  width: 42px;
  height: 42px;
  border-radius: 50%;
  border: 1px solid rgba(226, 232, 240, 0.9);
  background: rgba(255, 255, 255, 0.82);
  backdrop-filter: blur(12px);
  color: #111827;
  display: grid;
  place-items: center;
  cursor: pointer;
}

.quick-view-media {
  position: relative;
  min-height: 570px;
  padding: 54px;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  background:
    radial-gradient(circle at 28% 24%, rgba(11, 74, 139, 0.16), transparent 32%),
    linear-gradient(145deg, #f8fbff, #eef4fb);
}

.quick-view-orb {
  position: absolute;
  width: 360px;
  height: 360px;
  border-radius: 50%;
  background: #ffffff;
  box-shadow: inset 0 0 0 1px rgba(226, 232, 240, 0.9), 0 34px 90px rgba(15, 23, 42, 0.08);
}

.quick-view-media img {
  position: relative;
  z-index: 2;
  width: 86%;
  max-height: 430px;
  object-fit: contain;
  filter: drop-shadow(0 30px 35px rgba(15, 23, 42, 0.18));
}

.quick-view-badge {
  position: absolute;
  top: 28px;
  left: 28px;
  z-index: 3;
  border-radius: 999px;
  padding: 8px 13px;
  background: #0b4a8b;
  color: #fff;
  font-size: 11px;
  font-weight: 900;
  text-transform: uppercase;
  letter-spacing: 0.8px;
}

.quick-view-content {
  padding: 62px 48px 46px;
  overflow-y: auto;
}

.quick-view-kicker {
  display: inline-flex;
  margin-bottom: 14px;
  color: var(--blue);
  font-size: 12px;
  font-weight: 900;
  text-transform: uppercase;
  letter-spacing: 1.2px;
}

.quick-view-content h2 {
  margin: 0 0 16px;
  color: var(--text);
  font-size: clamp(30px, 3vw, 48px);
  font-weight: 650;
  letter-spacing: -1.8px;
  line-height: 1.05;
}

.quick-view-description {
  margin: 0 0 24px;
  color: #667085;
  font-size: 15px;
  font-weight: 500;
  line-height: 1.8;
}

.quick-view-price-row {
  display: flex;
  align-items: center;
  gap: 12px;
  flex-wrap: wrap;
  margin-bottom: 8px;
}

.quick-view-price-row strong {
  color: var(--primary);
  font-size: 27px;
  font-weight: 900;
}

.quick-view-price-row del {
  color: #a3aab5;
  font-size: 15px;
  font-weight: 700;
}

.quick-view-installment {
  margin: 0 0 24px;
  color: #667085;
  font-size: 13px;
  font-weight: 700;
}

.quick-view-meta-grid {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 10px;
  margin-bottom: 26px;
}

.quick-view-meta-grid div {
  min-height: 82px;
  border: 1px solid #e7ecf4;
  border-radius: 18px;
  background: #f8fbff;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  justify-content: center;
  gap: 8px;
  padding: 14px;
}

.quick-view-meta-grid i {
  color: var(--primary);
  font-size: 18px;
}

.quick-view-meta-grid span {
  color: #344054;
  font-size: 12px;
  font-weight: 850;
}

.quick-view-actions {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
  margin-bottom: 13px;
}

.quick-add-cart,
.quick-checkout,
.quick-wishlist-btn {
  height: 52px;
  border-radius: 16px;
  border: 0;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  font-size: 13px;
  font-weight: 900;
  cursor: pointer;
  transition: 0.25s ease;
}

.quick-add-cart {
  background: #0b4a8b;
  color: #fff;
  box-shadow: 0 18px 34px rgba(11, 74, 139, 0.22);
}

.quick-checkout {
  background: #111827;
  color: #fff;
  box-shadow: 0 18px 34px rgba(17, 24, 39, 0.14);
}

.quick-wishlist-btn {
  width: 100%;
  background: #fff;
  color: #344054;
  border: 1px solid #e5eaf2;
}

.quick-wishlist-btn.active {
  background: #fff2f2;
  color: #e11d48;
  border-color: #ffd5dc;
}

.quick-add-cart:hover,
.quick-checkout:hover,
.quick-wishlist-btn:hover {
  transform: translateY(-2px);
}

/* CART DRAWER */
.cart-drawer {
  position: fixed;
  top: 0;
  right: -420px;
  width: 390px;
  max-width: 92vw;
  height: 100vh;
  background: #fff;
  z-index: 2100;
  box-shadow: -20px 0 50px rgba(15, 23, 42, 0.15);
  transition: 0.35s ease;
  display: flex;
  flex-direction: column;
}

.cart-drawer.open {
  right: 0;
}

.drawer-head {
  padding: 22px;
  border-bottom: 1px solid var(--border);
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.drawer-head h4 {
  font-size: 18px;
  font-weight: 900;
  margin: 0;
}

.drawer-body {
  padding: 18px;
  flex: 1;
  overflow-y: auto;
}

.drawer-item {
  display: flex;
  gap: 12px;
  padding: 12px 0;
  border-bottom: 1px solid var(--border);
  align-items: center;
}

.drawer-item img {
  width: 62px;
  height: 62px;
  object-fit: contain;
  background: var(--light);
  border-radius: 14px;
  padding: 8px;
}

.drawer-item h6 {
  font-size: 13px;
  font-weight: 900;
  margin: 0 0 5px;
}

.drawer-item p {
  font-size: 12px;
  color: var(--primary);
  font-weight: 900;
  margin: 0;
}

.remove-btn {
  width: 30px;
  height: 30px;
  border-radius: 50%;
  border: 1px solid var(--border);
  background: #fff;
  color: var(--muted);
  display: grid;
  place-items: center;
  transition: 0.25s ease;
}

.remove-btn:hover {
  background: var(--accent);
  border-color: var(--accent);
  color: #fff;
}

.drawer-footer {
  padding: 18px;
  border-top: 1px solid var(--border);
}

.checkout-btn {
  height: 48px;
  border-radius: 14px;
  border: 0;
  width: 100%;
  background: var(--primary);
  color: #fff;
  font-weight: 900;
}

.overlay {
  position: fixed;
  inset: 0;
  background: rgba(15, 23, 42, 0.38);
  z-index: 2050;
  opacity: 0;
  pointer-events: none;
  transition: 0.25s ease;
}

.overlay.show {
  opacity: 1;
  pointer-events: all;
}

@media (max-width: 1399px) {
  .products-grid {
    grid-template-columns: repeat(4, minmax(0, 1fr));
  }

  .premium-wrapper {
    grid-template-columns: 1fr 1fr;
  }

  .premium-copy {
    grid-column: 1 / -1;
  }
}

@media (max-width: 1199px) {
  .products-grid {
    grid-template-columns: repeat(3, minmax(0, 1fr));
  }
}



/* PREMIUM CURATED DEALS - CLEAN PRODUCT + BANNER LAYOUT */
.curated-deals-section {
  padding: 42px 0 78px;
  background: linear-gradient(180deg, #f7f7f7 0%, #ffffff 100%);
}

.curated-deals-section .container-fluid {
  max-width: 1510px;
}

.curated-deals-grid {
  display: grid;
  grid-template-columns: minmax(255px, 0.82fr) minmax(255px, 0.82fr) minmax(520px, 1.62fr);
  gap: 24px;
  align-items: stretch;
}

.curated-product-card {
  min-height: 520px;
  background: #fff;
  border-radius: 28px;
  padding: 30px 28px 24px;
  position: relative;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  border: 1px solid rgba(226, 232, 240, 0.95);
  box-shadow: 0 18px 46px rgba(15, 23, 42, 0.055);
  transition: transform 0.28s ease, box-shadow 0.28s ease, border-color 0.28s ease;
}

.curated-product-card:hover {
  transform: translateY(-5px);
  border-color: rgba(203, 213, 225, 0.9);
  box-shadow: 0 26px 70px rgba(15, 23, 42, 0.1);
}

.curated-card-head {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 14px;
  position: relative;
  z-index: 2;
  margin-bottom: 22px;
}

.curated-card-head h3 {
  margin: 0;
  font-size: 24px;
  font-weight: 650;
  letter-spacing: -0.7px;
  color: #1f2937;
}

.curated-arrows {
  display: inline-flex;
  align-items: center;
  gap: 8px;
}

.curated-arrows button {
  width: 34px;
  height: 34px;
  border: 1px solid transparent;
  border-radius: 50%;
  background: transparent;
  color: #111827;
  font-size: 16px;
  display: grid;
  place-items: center;
  transition: 0.25s ease;
}

.curated-arrows button:hover {
  color: var(--primary);
  background: #f1f5f9;
  border-color: #e2e8f0;
}

.orange-mini-badge {
  position: absolute;
  top: 18px;
  right: -42px;
  z-index: 6;
  width: 145px;
  height: 29px;
  display: grid;
  place-items: center;
  transform: rotate(45deg);
  background: linear-gradient(135deg, #ff9b22, #ff6b00);
  color: #fff;
  font-size: 11px;
  font-weight: 500;
  line-height: 1;
  letter-spacing: 0.7px;
  text-align: center;
  text-transform: uppercase;
  box-shadow: 0 8px 18px rgba(255, 107, 0, 0.2);
  pointer-events: none;
}

.curated-main-img {
  width: min(100%, 260px);
  height: 230px;
  object-fit: contain;
  margin: 0 auto 28px;
  padding: 8px;
  border-radius: 24px;
  background: #fafafa;
  filter: drop-shadow(0 20px 24px rgba(15, 23, 42, 0.06));
  transition: transform 0.28s ease;
}

.curated-product-card:hover .curated-main-img {
  transform: scale(1.035);
}

.curated-product-info {
  margin-top: auto;
  text-align: center;
}

.stock-label {
  color: #099b26;
  font-size: 12px;
  font-weight: 600;
  display: inline-block;
  margin-bottom: 10px;
}

.curated-product-info h4 {
  min-height: 50px;
  margin: 0 auto 13px;
  max-width: 330px;
  font-size: 18px;
  line-height: 1.22;
  font-weight: 650;
  letter-spacing: -0.25px;
  color: #111827;
}

.curated-product-info p {
  margin: 0 0 8px;
  font-size: 14px;
  font-weight: 600;
  color: #111827;
}

.curated-product-info p strong {
  color: var(--blue);
  font-weight: 750;
}

.curated-product-info small {
  color: #9a9a9a;
  font-size: 12px;
  font-weight: 500;
  display: block;
  margin-bottom: 16px;
}

.curated-product-info small b {
  color: #747bff;
}

.curated-product-info button {
  border: 0;
  background: transparent;
  color: #ff711f;
  font-size: 15px;
  font-weight: 600;
  margin-bottom: 8px;
}

.mini-dots {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
}

.mini-dots span {
  width: 4px;
  height: 4px;
  border-radius: 50%;
  background: #d1d5db;
}

.mini-dots span:first-child {
  background: #111827;
  width: 5px;
  height: 5px;
}

.curated-banner-stack {
  display: grid;
  grid-template-rows: repeat(2, minmax(0, 1fr));
  gap: 24px;
  height: 100%;
  min-height: 520px;
}

.curated-wide-banner {
  min-height: 0;
  border-radius: 28px;
  background: #edf3f8;
  overflow: hidden;
  position: relative;
  display: grid;
  grid-template-columns: minmax(240px, 46%) 1fr;
  align-items: center;
  border: 1px solid rgba(226, 232, 240, 0.95);
  box-shadow: 0 18px 46px rgba(15, 23, 42, 0.055);
  transition: transform 0.28s ease, box-shadow 0.28s ease, border-color 0.28s ease;
}

.curated-wide-banner:hover {
  transform: translateY(-5px);
  border-color: rgba(203, 213, 225, 0.9);
  box-shadow: 0 26px 70px rgba(15, 23, 42, 0.1);
}

.curated-wide-banner img {
  width: 100%;
  height: 100%;
  min-height: 248px;
  object-fit: cover;
  opacity: 0.96;
  transition: transform 0.35s ease;
}

.curated-wide-banner:hover img {
  transform: scale(1.04);
}

.curated-wide-copy {
  position: relative;
  z-index: 2;
  padding: 30px 34px;
}

.curated-wide-copy span,
.mosaic-copy span {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  min-height: 24px;
  padding: 4px 9px;
  border-radius: 999px;
  background: #1477db;
  color: #fff;
  font-size: 10px;
  font-weight: 700;
  margin-bottom: 15px;
  text-transform: uppercase;
  letter-spacing: 0.4px;
}

.curated-wide-copy h3 {
  margin: 0 0 18px;
  max-width: 360px;
  font-size: clamp(22px, 2vw, 30px);
  line-height: 1.16;
  font-weight: 650;
  letter-spacing: -0.7px;
  color: #1f2937;
}

.curated-wide-copy a,
.mosaic-copy a {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  border-radius: 11px;
  background: #fff;
  padding: 9px 16px;
  font-size: 12px;
  text-transform: uppercase;
  letter-spacing: 0.7px;
  font-weight: 700;
  color: #111827;
  text-decoration: none !important;
  box-shadow: 0 8px 20px rgba(15, 23, 42, 0.06);
  transition: 0.25s ease;
}

.curated-wide-copy a:hover,
.mosaic-copy a:hover {
  background: var(--primary);
  color: #fff;
}


/* HOME ACCESSORIES SECTION */
.home-accessories-section {
  padding: 64px 0 78px;
  background: #ffffff;
}

.home-accessories-section .container-fluid {
  max-width: 1420px;
}

.home-accessories-head {
  margin-bottom: 34px;
}

.home-accessories-head .section-kicker {
  margin-bottom: 9px;
}

.home-accessories-layout {
  display: grid;
  grid-template-columns: 315px 1fr;
  gap: 28px;
  align-items: stretch;
}

.home-accessories-category-card {
  min-height: 420px;
  border-radius: 24px;
  overflow: hidden;
  position: relative;
  background: #122d28;
  border: 1px solid rgba(226, 232, 240, 0.95);
  box-shadow: 0 18px 45px rgba(15, 23, 42, 0.08);
}

.home-accessories-bg {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  opacity: 0.18;
  transform: scale(1.04);
  filter: saturate(0.8);
}

.home-accessories-overlay {
  position: absolute;
  inset: 0;
  background:
    radial-gradient(circle at 58% 32%, rgba(222, 255, 239, 0.32), transparent 28%),
    linear-gradient(160deg, rgba(20, 62, 55, 0.95), rgba(14, 29, 30, 0.96) 58%, rgba(8, 13, 17, 0.98));
}

.home-accessories-category-content {
  position: relative;
  z-index: 2;
  height: 100%;
  padding: 22px 22px 20px;
  display: flex;
  flex-direction: column;
  color: #fff;
}

.home-accessories-category-content > span {
  display: inline-flex;
  width: fit-content;
  padding: 8px 12px;
  border-radius: 999px;
  background: rgba(255, 255, 255, 0.14);
  border: 1px solid rgba(255, 255, 255, 0.2);
  color: rgba(255, 255, 255, 0.9);
  font-size: 11px;
  font-weight: 700;
  letter-spacing: 0.8px;
  text-transform: uppercase;
  backdrop-filter: blur(12px);
}

.home-accessories-category-content h3 {
  margin: 10px 0 8px;
  font-size: 29px;
  line-height: 1.02;
  font-weight: 600;
  letter-spacing: -1px;
}

.home-filter-topbar {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 18px;
  color: rgba(255, 255, 255, 0.82);
}

.home-filter-brand {
  font-size: 14px;
  font-weight: 800;
  letter-spacing: -0.3px;
}

.home-filter-menu {
  width: 32px;
  height: 32px;
  display: grid;
  place-items: center;
  border-radius: 10px;
  background: rgba(255, 255, 255, 0.12);
  border: 1px solid rgba(255, 255, 255, 0.16);
}

.home-filter-copy span {
  display: inline-flex;
  margin-bottom: 7px;
  color: rgba(255, 255, 255, 0.82);
  font-size: 13px;
  font-weight: 500;
  letter-spacing: 0.4px;
}

.home-filter-copy p {
  max-width: 220px;
  margin: 0;
  color: rgba(255, 255, 255, 0.72);
  font-size: 13px;
  line-height: 1.55;
  font-weight: 400;
}

.home-filter-visual-wrap {
  position: relative;
  flex: 1;
  min-height: 118px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 8px 0 12px;
}

.home-filter-visual-wrap::before {
  content: '';
  position: absolute;
  width: 172px;
  height: 172px;
  border-radius: 50%;
  background: radial-gradient(circle, rgba(255,255,255,0.2), rgba(255,255,255,0.02) 66%, transparent 70%);
}

.home-filter-visual {
  position: relative;
  width: min(72%, 165px);
  max-height: 126px;
  object-fit: contain;
  filter: drop-shadow(0 24px 35px rgba(0, 0, 0, 0.38));
  transform: rotate(-7deg);
}

.premium-filter-tabs {
  gap: 7px;
}

.home-accessories-tabs {
  margin-top: auto;
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.home-accessories-tabs button {
  width: 100%;
  min-height: 36px;
  border: 1px solid rgba(255, 255, 255, 0.18);
  border-radius: 999px;
  background: rgba(255, 255, 255, 0.1);
  color: rgba(255, 255, 255, 0.88);
  padding: 0 15px;
  text-align: left;
  font-size: 14px;
  font-weight: 500;
  transition: 0.25s ease;
}

.home-accessories-tabs button:hover,
.home-accessories-tabs button.active {
  background: #ffffff;
  color: #111827;
  border-color: #ffffff;
  transform: translateX(4px);
}

.home-accessories-products {
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));
  gap: 22px;
  align-items: stretch;
}

.home-accessories-section .product-card {
  min-height: 412px;
}

.home-accessories-section .product-img {
  height: 168px;
  padding: 22px 24px 8px;
  background: #fff;
}

.home-accessories-section .product-img::after {
  display: none;
}

.home-accessories-section .product-img img {
  width: 100%;
  height: 100%;
  object-fit: contain;
  object-position: center;
}

.home-accessories-section .product-card:hover .product-img img {
  transform: scale(1.035);
}

.home-accessories-section .product-ribbon {
  top: 16px;
  right: -44px;
  width: 150px;
  height: 29px;
  font-size: 11px;
  font-weight: 500;
  letter-spacing: 0.5px;
  background: #0878dd;
  box-shadow: none;
}

@media (max-width: 1199px) {
  .home-accessories-layout {
    grid-template-columns: 1fr;
  }

  .home-accessories-category-card {
    min-height: 330px;
  }

  .home-accessories-products {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }
}

@media (max-width: 767px) {
  .home-accessories-section {
    padding: 46px 0 56px;
  }

  .home-accessories-products {
    gap: 16px;
  }

  .home-accessories-section .product-img {
    height: 156px;
  }

  .home-filter-visual-wrap {
    min-height: 96px;
  }

  .home-filter-visual {
    max-height: 104px;
  }
}

@media (max-width: 460px) {
  .home-accessories-products {
    grid-template-columns: 1fr;
  }
}

/* HOME ACCESSORIES TYPOGRAPHY */
.home-accessories-head .section-title {
  font-size: 27px;
  line-height: 1.15;
  font-weight: 900;
  letter-spacing: -1px;
}

.home-accessories-head .section-sub {
  margin: 4px 0 0;
  color: var(--muted);
  font-size: 13px;
  font-weight: 650;
}

.home-accessories-section .product-title {
  min-height: 44px;
  margin-bottom: 8px;
  font-size: 15px;
  font-weight: 800;
  line-height: 1.35;
  letter-spacing: -0.35px;
}


.home-accessories-section .product-body {
  padding: 6px 18px 16px;
}

.home-accessories-section .product-category {
  min-height: 24px;
  margin-bottom: 6px;
  font-size: 12px;
}

.home-accessories-section .price {
  margin-bottom: 6px;
}

.home-accessories-section .installment-line {
  margin-bottom: 11px;
  font-size: 12px;
}

.home-accessories-section .add-btn {
  height: 29px;
  min-width: 124px;
  font-size: 12px;
}

/* HOME ACCESSORIES IMAGE BANNER */
.home-accessories-image-banner-section {
  width: 100%;
  padding: 12px 0 18px;
  background: #ffffff;
  display: flex;
  justify-content: center;
  overflow: visible;
}

#best-week.product-grid-section {
  padding-top: 24px;
  margin-top: -18px;
}

.home-accessories-image-banner-img {
  width: min(100%, 1810px);
  height: auto;
  display: block;
  object-fit: contain;
  object-position: center;
  border: 0;
  border-radius: 0;
  box-shadow: none;
  background: #ffffff;
}

@media (max-width: 767px) {
  .home-accessories-image-banner-section {
    padding: 8px 0 18px;
  }

  #best-week.product-grid-section {
    padding-top: 28px;
    margin-top: -8px;
  }

  .home-accessories-image-banner-img {
    width: 100%;
  }
}

/* PREMIUM MOSAIC BANNERS - SCREENSHOT LAYOUT 03 */
.premium-mosaic-section {
  padding: 26px 0 70px;
  background: #f6f6f6;
}

.premium-mosaic-grid {
  min-height: 570px;
  border-radius: 24px;
  overflow: hidden;
  background: #fff;
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  grid-template-rows: 1fr 1fr;
  border: 1px solid rgba(15, 23, 42, 0.05);
}

.mosaic-tile {
  position: relative;
  min-height: 280px;
  overflow: hidden;
  border-right: 1px solid #f0f0f0;
  border-bottom: 1px solid #f0f0f0;
  background: #fff;
}

.mosaic-tile img {
  position: absolute;
  width: 48%;
  height: 78%;
  left: 8%;
  top: 12%;
  object-fit: contain;
  filter: drop-shadow(0 22px 22px rgba(15, 23, 42, 0.08));
}

.mosaic-copy {
  position: absolute;
  right: 9%;
  top: 50%;
  transform: translateY(-50%);
  width: 42%;
  z-index: 2;
}

.mosaic-copy small {
  display: block;
  margin-bottom: 16px;
  color: #9a9a9a;
  font-size: 12px;
  text-transform: uppercase;
  letter-spacing: 0.6px;
  font-weight: 700;
}

.mosaic-copy h3 {
  margin: 0 0 18px;
  font-size: 26px;
  line-height: 1.15;
  letter-spacing: -0.7px;
  font-weight: 900;
  color: #333;
}

.tile-e {
  grid-column: 2 / 4;
}

.tile-e img {
  left: 9%;
  width: 43%;
  height: 86%;
  top: 8%;
}

.tile-e .mosaic-copy {
  width: 30%;
  right: 13%;
}

.tile-d img {
  width: 42%;
  left: 12%;
}

@media (max-width: 991px) {
  .page-wrapper,
  .app-shell.sidebar-expanded .page-wrapper {
    margin-left: 0;
    padding-bottom: 74px;
  }

  .side-nav,
  .side-nav.expanded {
    top: auto;
    bottom: 0;
    width: 100%;
    height: 68px;
    flex-direction: row;
    justify-content: space-around;
    align-items: center;
    padding: 8px 10px;
    border-top: 1px solid var(--border);
    border-right: 0;
    box-shadow: 0 -12px 35px rgba(15, 23, 42, 0.08);
    overflow: visible;
  }

  .sidebar-toggle,
  .side-separator,
  .nav-label,
  .nav-badge {
    display: none !important;
  }

  .side-nav-group,
  .side-nav.expanded .side-nav-group {
    flex-direction: row;
    align-items: center;
    justify-content: flex-start;
    width: 100%;
    gap: 8px;
  }

  .side-scroll-group {
    display: flex !important;
    flex: 1;
    max-height: none;
    overflow-x: auto;
    overflow-y: hidden;
    padding: 0 4px;
    scrollbar-width: none;
  }

  .side-scroll-group::-webkit-scrollbar {
    display: none;
  }

  .side-nav a,
  .side-nav.expanded a {
    width: 46px;
    min-width: 46px;
    min-height: 46px;
    justify-content: center;
    padding: 0;
    border-radius: 16px;
  }

  .side-nav a::before {
    left: 50%;
    right: auto;
    top: auto;
    bottom: -8px;
    width: 24px;
    height: 4px;
    transform: translateX(-50%);
  }

  .hero-full-banner {
    min-height: clamp(360px, 48vw, 520px);
  }

  .hero-floating {
    left: 28px;
    bottom: 24px;
    flex-wrap: wrap;
    max-width: 48%;
  }

  .hero-stat {
    padding: 12px 14px;
    min-width: 104px;
  }

  .hero-stat strong {
    font-size: 22px;
  }

  .search-box {
    order: 3;
    max-width: 100%;
    margin-top: 14px;
  }

  .navbar-custom {
    padding-top: 14px;
    padding-bottom: 14px;
  }

  .premium-wrapper {
    grid-template-columns: 1fr;
  }

  .premium-feature-card {
    min-height: auto;
  }
}

@media (max-width: 767px) {
  .products-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: 16px;
  }

  .product-img {
    height: 180px;
    padding: 20px;
  }

  .premium-wrapper {
    padding: 26px;
    border-radius: 24px;
  }

  .premium-mini-grid {
    grid-template-columns: 1fr;
  }
}

@media (max-width: 575px) {
  .top-strip {
    font-size: 9px;
    text-align: center;
    padding: 0 12px;
  }

  .brand {
    font-size: 23px;
  }

  .promo-card {
    min-height: 210px;
  }

  .hero-full-banner {
    min-height: 310px;
  }

  .hero-floating {
    display: none;
  }

  .newsletter-form {
    border-radius: 20px;
    flex-direction: column;
    gap: 8px;
    padding: 10px;
  }

  .newsletter-form input {
    height: 42px;
  }

  .newsletter-form button {
    width: 100%;
  }

  .cart-toast {
    left: 14px;
    right: 14px;
    width: auto;
  }

  .section-head {
    align-items: flex-start;
    flex-direction: column;
  }
}

@media (max-width: 460px) {
  .products-grid {
    grid-template-columns: 1fr;
  }
}


@media (max-width: 1399px) {
  .curated-deals-grid {
    grid-template-columns: 1fr 1fr;
  }

  .curated-banner-stack {
    grid-column: 1 / -1;
    grid-template-columns: repeat(2, minmax(0, 1fr));
    grid-template-rows: none;
    min-height: auto;
  }

  .curated-wide-banner {
    min-height: 280px;
  }
}

@media (max-width: 1199px) {
  .curated-deals-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }

  .premium-mosaic-grid {
    grid-template-columns: 1fr 1fr;
  }

  .tile-e {
    grid-column: auto;
  }
}

@media (max-width: 767px) {
  .curated-deals-section,
  .premium-mosaic-section {
    padding: 28px 0 44px;
  }

  .curated-deals-grid,
  .curated-banner-stack,
  .premium-mosaic-grid {
    grid-template-columns: 1fr;
  }

  .curated-product-card {
    min-height: auto;
    padding: 26px 22px;
  }

  .curated-main-img {
    height: 220px;
  }

  .curated-banner-stack {
    grid-template-rows: none;
    min-height: auto;
  }

  .curated-wide-banner {
    grid-template-columns: 1fr;
    min-height: auto;
  }

  .curated-wide-banner img {
    height: 210px;
    min-height: 210px;
  }

  .curated-wide-copy {
    padding: 24px;
  }

  .mosaic-tile {
    min-height: 350px;
  }

  .mosaic-tile img,
  .tile-e img,
  .tile-d img {
    width: 72%;
    height: 52%;
    left: 14%;
    top: 7%;
  }

  .mosaic-copy,
  .tile-e .mosaic-copy {
    width: auto;
    left: 26px;
    right: 26px;
    top: auto;
    bottom: 28px;
    transform: none;
  }
}


@media (max-width: 1199px) {
  .shopify-banners-grid {
    grid-template-columns: 1fr;
  }

  .shopify-feature-banner {
    min-height: 360px;
  }

  .banner-product-img {
    width: 56%;
    right: -6%;
  }
}

@media (max-width: 575px) {
  .shopify-banners-head {
    align-items: flex-start;
    flex-direction: column;
  }

  .shopify-feature-banner {
    min-height: 430px;
    border-radius: 24px;
  }

  .banner-copy {
    padding: 28px 24px;
    max-width: 100%;
  }

  .banner-product-img,
  .banner-style-1 .banner-product-img,
  .banner-style-2 .banner-product-img,
  .banner-style-3 .banner-product-img {
    width: 88%;
    height: 48%;
    right: -18%;
    bottom: -2%;
  }
}


@media (max-width: 1199px) {
  .product-chip-row {
    gap: 14px;
    margin-bottom: 42px;
  }

  .product-chip-row .chip {
    font-size: 14px;
  }
}

@media (max-width: 767px) {
  .product-grid-section {
    padding: 46px 0 56px;
  }

  .product-section-head {
    margin-bottom: 28px;
  }

  .product-grid-section .section-title {
    font-size: 29px;
    font-weight: 500;
  }

  .product-chip-row {
    justify-content: flex-start;
    gap: 10px;
    margin-bottom: 30px;
  }

  .product-chip-row .chip {
    font-size: 13px;
  }

  .product-grid-section .product-img,
  #deals .product-img {
    height: 190px;
  }
}


@media (max-width: 991px) {
  .appliance-banner-section {
    padding: 18px 0 30px;
  }

  .appliance-full-banner-img {
    height: clamp(290px, 42vw, 390px);
  }
}

@media (max-width: 575px) {
  .appliance-banner-section {
    padding: 14px 0 24px;
  }

  .appliance-full-banner-img {
    width: 100%;
    height: 230px;
  }
}



/* MINIMAL FEATURED BENTO GRID - cleaner ecommerce layout */
.shopify-banners-section {
  padding: 72px 0 78px;
  background: #fff;
}

.shopify-banners-section .container-fluid {
  max-width: 1420px;
}

.shopify-banners-head {
  align-items: center;
  margin-bottom: 32px;
  padding-bottom: 24px;
  border-bottom: 1px solid #dedede;
}

.shopify-banners-head h2 {
  font-size: 35px;
  font-weight: 500;
  line-height: 1.12;
  letter-spacing: -0.6px;
  color: #2d2d2f;
}

.shopify-banners-head a {
  color: #202124;
  font-size: 12px;
  font-weight: 800;
  letter-spacing: 1.15px;
  text-decoration: none !important;
}

.section-kicker {
  color: #0077ff;
  font-size: 11px;
  font-weight: 700;
  letter-spacing: 1.25px;
  margin-bottom: 8px;
}

.featured-bento-grid,
.shopify-banners-grid.featured-bento-grid {
  display: grid;
  grid-template-columns: repeat(6, minmax(0, 1fr));
  gap: 18px;
}

.featured-bento-card,
.shopify-feature-banner.featured-bento-card {
  min-height: 318px;
  border-radius: 20px;
  overflow: hidden;
  position: relative;
  isolation: isolate;
  border: 1px solid #e4e4e4;
  background: #f6f7f9;
  box-shadow: 0 3px 12px rgba(15, 23, 42, 0.08);
  transition: transform 0.3s ease, box-shadow 0.3s ease, border-color 0.3s ease;
}

.featured-bento-card:hover {
  transform: translateY(-4px);
  border-color: #dddddd;
  box-shadow: 0 9px 24px rgba(15, 23, 42, 0.12);
}

.bento-card-1,
.bento-card-2,
.bento-card-3 {
  grid-column: span 2;
}

.bento-card-4,
.bento-card-5 {
  grid-column: span 3;
  min-height: 270px;
}

.bento-bg-img {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  object-position: center;
  z-index: 1;
  transform: scale(1.01);
  transition: transform 0.45s ease;
}

.featured-bento-card:hover .bento-bg-img {
  transform: scale(1.055);
}

.bento-soft-overlay {
  position: absolute;
  inset: 0;
  z-index: 2;
  background:
    linear-gradient(90deg, rgba(255, 255, 255, 0.92) 0%, rgba(255, 255, 255, 0.68) 36%, rgba(255, 255, 255, 0.08) 72%),
    linear-gradient(180deg, rgba(0, 0, 0, 0.02), rgba(0, 0, 0, 0.16));
}

.bento-card-1 .bento-soft-overlay,
.bento-card-3 .bento-soft-overlay {
  background:
    linear-gradient(90deg, rgba(9, 22, 42, 0.78) 0%, rgba(9, 22, 42, 0.46) 40%, rgba(9, 22, 42, 0.05) 78%),
    linear-gradient(180deg, rgba(0, 0, 0, 0.04), rgba(0, 0, 0, 0.16));
}

.bento-card-4 .bento-soft-overlay,
.bento-card-5 .bento-soft-overlay {
  background:
    linear-gradient(90deg, rgba(9, 22, 42, 0.72) 0%, rgba(9, 22, 42, 0.34) 42%, rgba(9, 22, 42, 0.04) 78%),
    linear-gradient(180deg, rgba(0, 0, 0, 0.04), rgba(0, 0, 0, 0.18));
}

.bento-copy,
.banner-copy.bento-copy {
  position: relative;
  z-index: 3;
  width: min(78%, 330px);
  height: 100%;
  min-height: inherit;
  padding: 30px 28px;
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
  align-items: flex-start;
  max-width: none;
}

.bento-copy span,
.banner-copy.bento-copy span {
  width: fit-content;
  min-height: 25px;
  margin-bottom: 14px;
  padding: 5px 10px;
  border-radius: 999px;
  border: 1px solid rgba(255, 255, 255, 0.55);
  background: rgba(255, 255, 255, 0.76);
  color: #111827;
  backdrop-filter: blur(12px);
  font-size: 10px;
  font-weight: 600;
  letter-spacing: 0.7px;
  text-transform: uppercase;
}

.bento-card-1 .bento-copy span,
.bento-card-3 .bento-copy span,
.bento-card-4 .bento-copy span,
.bento-card-5 .bento-copy span {
  background: rgba(255, 255, 255, 0.88);
  color: #111827;
}

.bento-copy h3,
.banner-copy.bento-copy h3 {
  margin: 0 0 16px;
  color: #111827;
  font-size: clamp(24px, 2.1vw, 34px);
  line-height: 1.08;
  font-weight: 500;
  letter-spacing: -0.6px;
}

.bento-card-1 .bento-copy h3,
.bento-card-3 .bento-copy h3,
.bento-card-4 .bento-copy h3,
.bento-card-5 .bento-copy h3 {
  color: #fff;
}

/* Bento card descriptions removed for a cleaner layout. */
.bento-copy p,
.banner-copy.bento-copy p {
  display: none;
}

.bento-copy .banner-price-row {
  gap: 8px;
  margin: 0 0 18px;
}

.bento-copy .banner-price-row strong {
  color: #111827;
  font-size: 16px;
  font-weight: 700;
}

.bento-copy .banner-price-row del {
  color: #8b95a1;
  font-size: 12px;
  font-weight: 600;
}

.bento-card-1 .banner-price-row strong,
.bento-card-3 .banner-price-row strong,
.bento-card-4 .banner-price-row strong,
.bento-card-5 .banner-price-row strong {
  color: #fff;
}

.bento-card-1 .banner-price-row del,
.bento-card-3 .banner-price-row del,
.bento-card-4 .banner-price-row del,
.bento-card-5 .banner-price-row del {
  color: rgba(255, 255, 255, 0.58);
}

.bento-copy button,
.banner-copy.bento-copy button {
  height: 39px;
  border: 0;
  border-radius: 999px;
  padding: 0 16px;
  display: inline-flex;
  align-items: center;
  gap: 9px;
  background: #ffffff;
  color: #111827;
  box-shadow: 0 10px 24px rgba(15, 23, 42, 0.12);
  font-size: 11px;
  font-weight: 700;
  letter-spacing: 0.55px;
  text-transform: uppercase;
  text-decoration: none !important;
  transition: 0.25s ease;
}

.bento-card-2 .bento-copy button {
  background: #111827;
  color: #fff;
}

.bento-copy button:hover {
  transform: translateY(-2px);
  background: #0b74de;
  color: #fff;
}

@media (max-width: 1199px) {
  .featured-bento-grid,
  .shopify-banners-grid.featured-bento-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }

  .bento-card-1,
  .bento-card-2,
  .bento-card-3,
  .bento-card-4,
  .bento-card-5 {
    grid-column: span 1;
    min-height: 300px;
  }
}

@media (max-width: 767px) {
  .shopify-banners-section {
    padding: 46px 0 56px;
  }

  .shopify-banners-head {
    align-items: flex-start;
    flex-direction: column;
    margin-bottom: 28px;
  }

  .shopify-banners-head h2 {
    font-size: 29px;
    font-weight: 500;
  }

  .featured-bento-grid,
  .shopify-banners-grid.featured-bento-grid {
    grid-template-columns: 1fr;
  }

  .featured-bento-card,
  .shopify-feature-banner.featured-bento-card {
    min-height: 310px;
    border-radius: 18px;
  }

  .bento-copy,
  .banner-copy.bento-copy {
    width: 86%;
    padding: 24px;
  }
}


/* FEATURED PRODUCT IMAGE GRID - layout like requested screenshot */
.feature-showcase-section {
  padding: 48px 0 58px;
  background: #ffffff;
  overflow: hidden;
}

.feature-showcase-section .container-fluid {
  max-width: 1680px;
}

.feature-showcase-head {
  display: flex;
  justify-content: space-between;
  align-items: flex-end;
  gap: 20px;
  margin-bottom: 24px;
  padding-bottom: 18px;
  border-bottom: 1px solid var(--border);
}

.feature-showcase-head h2 {
  margin: 0;
  color: var(--text);
  font-size: 27px;
  line-height: 1.15;
  font-weight: 900;
  letter-spacing: -1px;
}

.feature-showcase-head a {
  display: inline-flex;
  align-items: center;
  gap: 9px;
  color: var(--dark);
  font-size: 12px;
  font-weight: 800;
  letter-spacing: 0.9px;
  text-transform: uppercase;
  text-decoration: none !important;
  white-space: nowrap;
}

.feature-showcase-grid {
  display: grid;
  grid-template-columns: 1.45fr 0.5fr 0.5fr;
  grid-template-rows: minmax(170px, 0.82fr) minmax(280px, 1.18fr);
  gap: 18px;
  height: clamp(500px, 47vw, 760px);
  width: 100%;
  overflow: hidden;
}

.feature-showcase-card {
  position: relative;
  overflow: hidden;
  border-radius: 10px;
  background: #f3f5f8;
  box-shadow: 0 10px 28px rgba(15, 23, 42, 0.08);
  transition: transform 0.28s ease, box-shadow 0.28s ease;
}

.feature-showcase-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 16px 36px rgba(15, 23, 42, 0.13);
}

.feature-showcase-card img {
  width: 100%;
  height: 100%;
  display: block;
  object-fit: cover;
  object-position: center;
  transition: transform 0.45s ease;
}

.feature-showcase-card:hover img {
  transform: scale(1.035);
}

.feature-tile-large {
  grid-column: 1 / 2;
  grid-row: 1 / 3;
}

.feature-tile-small-a {
  grid-column: 2 / 3;
  grid-row: 1 / 2;
}

.feature-tile-small-b {
  grid-column: 3 / 4;
  grid-row: 1 / 2;
}

.feature-tile-wide {
  grid-column: 2 / 4;
  grid-row: 2 / 3;
}

@media (max-width: 1199px) {
  .feature-showcase-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
    grid-template-rows: auto;
    height: auto;
  }

  .feature-tile-large,
  .feature-tile-wide {
    grid-column: 1 / -1;
    grid-row: auto;
    height: clamp(320px, 48vw, 520px);
  }

  .feature-tile-small-a,
  .feature-tile-small-b {
    grid-column: auto;
    grid-row: auto;
    height: clamp(220px, 28vw, 340px);
  }
}

@media (max-width: 767px) {
  .feature-showcase-section {
    padding: 38px 0 46px;
  }

  .feature-showcase-head {
    align-items: flex-start;
    flex-direction: column;
  }

  .feature-showcase-head h2 {
    font-size: 27px;
  }

  .feature-showcase-grid {
    grid-template-columns: 1fr;
    gap: 14px;
  }

  .feature-tile-large,
  .feature-tile-small-a,
  .feature-tile-small-b,
  .feature-tile-wide {
    grid-column: auto;
    grid-row: auto;
    height: 240px;
  }

  .feature-tile-large {
    height: 320px;
  }
}


/* CONSISTENT SECTION TITLES + FINAL TUNING */
.feature-showcase-head .section-title,
.home-accessories-head .section-title,
.modern-testimonial-head .section-title {
  font-size: 27px !important;
  line-height: 1.15 !important;
  font-weight: 900 !important;
  letter-spacing: -1px !important;
  margin: 0 !important;
}

.feature-showcase-head,
.modern-testimonial-head {
  display: flex;
  justify-content: space-between;
  align-items: flex-end;
  gap: 18px;
  margin-bottom: 22px;
  border-bottom: 1px solid var(--border);
  padding-bottom: 18px;
}

.feature-showcase-section {
  padding: 42px 0 54px;
}

.home-accessories-section {
  padding: 52px 0 66px;
}

.home-accessories-head .section-sub,
.modern-testimonial-head .section-sub {
  margin: 4px 0 0;
  color: var(--muted);
  font-size: 13px;
  font-weight: 650;
}

/* MODERN TESTIMONIALS */
.modern-testimonials-section {
  padding: 32px 0 74px;
  background:
    radial-gradient(circle at 15% 0%, rgba(0, 119, 255, 0.08), transparent 28%),
    linear-gradient(180deg, #ffffff 0%, #f7f9fc 100%);
}

.modern-testimonials-section .container-fluid {
  max-width: 1420px;
}

.modern-testimonial-grid {
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));
  gap: 22px;
}

.modern-testimonial-card {
  position: relative;
  min-height: 260px;
  padding: 28px 24px;
  border: 1px solid rgba(226, 232, 240, 0.9);
  border-radius: 28px;
  background: rgba(255, 255, 255, 0.82);
  box-shadow: 0 18px 55px rgba(15, 23, 42, 0.07);
  backdrop-filter: blur(18px);
  transition: transform 0.28s ease, box-shadow 0.28s ease, border-color 0.28s ease;
  overflow: hidden;
}

.modern-testimonial-card::after {
  content: '';
  position: absolute;
  width: 120px;
  height: 120px;
  right: -44px;
  top: -44px;
  border-radius: 50%;
  background: rgba(0, 119, 255, 0.08);
}

.modern-testimonial-card:hover {
  transform: translateY(-6px);
  box-shadow: 0 28px 75px rgba(15, 23, 42, 0.12);
  border-color: rgba(0, 119, 255, 0.2);
}

.testimonial-quote-icon {
  width: 42px;
  height: 42px;
  border-radius: 16px;
  display: grid;
  place-items: center;
  background: #f2f7ff;
  color: var(--primary);
  margin-bottom: 18px;
}

.modern-rating {
  color: #f59e0b;
  font-size: 13px;
  letter-spacing: 2px;
  margin-bottom: 14px;
}

.modern-testimonial-text {
  min-height: 82px;
  margin: 0 0 24px;
  color: #475569;
  font-size: 14px;
  line-height: 1.8;
  font-weight: 600;
}

.modern-testimonial-person {
  display: flex;
  align-items: center;
  gap: 13px;
  position: relative;
  z-index: 1;
}

.modern-avatar {
  width: 46px;
  height: 46px;
  border-radius: 16px;
  display: grid;
  place-items: center;
  background: linear-gradient(135deg, #0b4a8b, #0077ff);
  color: #fff;
  font-size: 15px;
  font-weight: 900;
}

.modern-testimonial-person h5 {
  margin: 0;
  color: var(--text);
  font-size: 14px;
  font-weight: 900;
  letter-spacing: -0.2px;
}

.modern-testimonial-person small {
  color: var(--muted);
  font-size: 12px;
  font-weight: 700;
}

/* MODERN FOOTER */
.modern-footer {
  background:
    radial-gradient(circle at 18% 12%, rgba(0, 119, 255, 0.16), transparent 28%),
    linear-gradient(135deg, #070b14 0%, #111827 54%, #05070b 100%);
  color: #fff;
  padding: 76px 0 26px;
  position: relative;
  overflow: hidden;
}

.modern-footer::before {
  content: '';
  position: absolute;
  inset: 0;
  background: linear-gradient(90deg, rgba(255,255,255,0.04), transparent 46%, rgba(255,255,255,0.025));
  pointer-events: none;
}

.modern-footer .container-fluid {
  max-width: 1420px;
  position: relative;
  z-index: 1;
}

.modern-footer-top {
  display: grid;
  grid-template-columns: 1.05fr 1.45fr 0.95fr;
  gap: 42px;
  align-items: start;
}

.footer-brand {
  display: inline-flex;
  align-items: center;
  font-size: 34px;
  line-height: 1;
  font-weight: 900;
  letter-spacing: -1.4px;
  margin-bottom: 20px;
  color: #fff;
  text-decoration: none !important;
}

.footer-brand span {
  color: var(--accent);
}

.modern-footer-brand p,
.footer-newsletter-card p {
  margin: 0 0 22px;
  color: rgba(255, 255, 255, 0.68);
  font-size: 14px;
  line-height: 1.8;
  font-weight: 600;
  max-width: 330px;
}

.footer-socials {
  display: flex;
  gap: 10px;
}

.footer-socials a {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  display: grid;
  place-items: center;
  background: rgba(255, 255, 255, 0.08);
  color: #fff;
  transition: 0.25s ease;
}

.footer-socials a:hover {
  transform: translateY(-3px);
  background: var(--blue);
}

.modern-footer-links {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 28px;
}

.footer-link-col h5,
.footer-newsletter-card span {
  margin: 0 0 18px;
  color: #ffffff;
  font-size: 12px;
  font-weight: 900;
  letter-spacing: 1px;
  text-transform: uppercase;
}

.footer-link-col a {
  display: block;
  margin-bottom: 11px;
  color: rgba(255, 255, 255, 0.62);
  font-size: 13px;
  font-weight: 650;
  text-decoration: none !important;
  transition: 0.2s ease;
}

.footer-link-col a:hover,
.modern-footer-bottom a:hover {
  color: #ffffff;
  transform: translateX(3px);
}

.footer-newsletter-card {
  padding: 26px;
  border: 1px solid rgba(255, 255, 255, 0.12);
  border-radius: 26px;
  background: rgba(255, 255, 255, 0.07);
  backdrop-filter: blur(16px);
}

.footer-newsletter-card h4 {
  margin: 0 0 10px;
  color: #fff;
  font-size: 23px;
  line-height: 1.15;
  font-weight: 900;
  letter-spacing: -0.7px;
}

.footer-newsletter-form {
  display: flex;
  align-items: center;
  gap: 10px;
  height: 50px;
  border-radius: 999px;
  background: #ffffff;
  padding: 5px;
}

.footer-newsletter-form input {
  flex: 1;
  min-width: 0;
  height: 100%;
  border: 0;
  outline: none;
  padding: 0 15px;
  color: var(--text);
  font-size: 13px;
  font-weight: 700;
  background: transparent;
}

.footer-newsletter-form button {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  border: 0;
  display: grid;
  place-items: center;
  background: var(--primary);
  color: #fff;
  transition: 0.25s ease;
}

.footer-newsletter-form button:hover {
  background: var(--accent);
  transform: translateX(2px);
}

.modern-footer-bottom {
  margin-top: 48px;
  padding-top: 22px;
  border-top: 1px solid rgba(255, 255, 255, 0.1);
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 18px;
  flex-wrap: wrap;
  color: rgba(255, 255, 255, 0.55);
  font-size: 12px;
  font-weight: 650;
}

.modern-footer-bottom div {
  display: flex;
  gap: 18px;
  flex-wrap: wrap;
}

.modern-footer-bottom a {
  color: rgba(255, 255, 255, 0.55);
  text-decoration: none !important;
  transition: 0.2s ease;
}

@media (max-width: 1199px) {
  .modern-testimonial-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }

  .modern-footer-top {
    grid-template-columns: 1fr;
  }
}

@media (max-width: 767px) {
  .feature-showcase-head,
  .modern-testimonial-head {
    align-items: flex-start;
    flex-direction: column;
  }

  .feature-showcase-head .section-title,
  .home-accessories-head .section-title,
  .modern-testimonial-head .section-title {
    font-size: 27px !important;
  }

  .modern-testimonial-grid,
  .modern-footer-links {
    grid-template-columns: 1fr;
  }

  .modern-testimonials-section {
    padding: 24px 0 56px;
  }
}


/* FINAL CLEANUP: keep section titles consistent with Electronics and remove sidebar underline separators */
.feature-showcase-head .section-title,
.home-accessories-head .section-title,
.product-section-head .section-title,
.modern-testimonial-head .section-title {
  font-family: 'Manrope', sans-serif !important;
  font-size: 27px !important;
  line-height: 1.15 !important;
  font-weight: 900 !important;
  letter-spacing: -1px !important;
  margin: 0 !important;
}

.side-separator {
  display: none !important;
}

.side-nav a,
.side-nav a:hover,
.side-nav a:focus,
.side-nav a:active {
  text-decoration: none !important;
  border-bottom: 0 !important;
  box-shadow: none;
}

.side-nav a {
  border-radius: 16px;
}

.nav-icon {
  width: 34px;
  height: 34px;
  border-radius: 13px;
  background: linear-gradient(145deg, #f7faff, #eef4ff);
  color: #0b4a8b;
  box-shadow: inset 0 0 0 1px rgba(11, 74, 139, 0.08);
  transition: 0.25s ease;
}

.side-nav a:hover .nav-icon,
.side-nav a.active .nav-icon {
  background: linear-gradient(145deg, #0b4a8b, #0077ff);
  color: #ffffff;
  box-shadow: 0 10px 24px rgba(0, 119, 255, 0.24);
}


/* CATEGORY-ONLY SIDEBAR REFINEMENT */
.categories-sidebar .side-scroll-group {
  height: calc(100vh - 78px);
}

.categories-sidebar .side-nav-group a {
  margin: 1px 0;
}

.categories-sidebar.expanded .nav-label {
  font-weight: 760;
  letter-spacing: -0.15px;
}

.categories-sidebar.expanded .nav-icon {
  width: 36px;
  height: 36px;
}

@media (max-width: 991px) {
  .categories-sidebar,
  .categories-sidebar.expanded {
    min-height: 68px;
    height: 68px;
  }

  .categories-sidebar .side-scroll-group {
    height: auto;
  }
}


@media (max-width: 991px) {
  .quick-view-modal {
    grid-template-columns: 1fr;
    overflow-y: auto;
  }

  .quick-view-media {
    min-height: 360px;
    padding: 38px;
  }

  .quick-view-media img {
    max-height: 270px;
  }

  .quick-view-content {
    padding: 34px 26px 28px;
  }
}

@media (max-width: 575px) {
  .quick-view-modal {
    width: calc(100vw - 20px);
    border-radius: 24px;
  }

  .quick-view-meta-grid,
  .quick-view-actions {
    grid-template-columns: 1fr;
  }

  .modern-hover-actions {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

</style>