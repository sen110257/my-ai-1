<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

// 搜索框相关数据
const searchValue = ref('')

// 轮播图相关数据
const currentSlide = ref(0)
const autoPlayInterval = ref(null)
const isPaused = ref(false)

const slides = ref([
  {
    id: 1,
    image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=modern%20fashion%20clothing%20store%20banner%20with%20models%20wearing%20trendy%20outfits%20professional%20photography%20wide%20screen&image_size=landscape_16_9',
    title: '新品上市',
    description: '探索最新潮流时尚'
  },
  {
    id: 2,
    image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=summer%20sale%20shopping%20banner%20with%20colorful%20shopping%20bags%20and%20discount%20tags%20vibrant%20colors&image_size=landscape_16_9',
    title: '限时特惠',
    description: '精选商品低至5折'
  },
  {
    id: 3,
    image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=electronics%20gadgets%20showcase%20banner%20with%20smartphones%20laptops%20headphones%20tech%20style&image_size=landscape_16_9',
    title: '数码精选',
    description: '科技改变生活'
  },
  {
    id: 4,
    image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=home%20furniture%20interior%20design%20banner%20modern%20minimalist%20living%20room%20cozy%20atmosphere&image_size=landscape_16_9',
    title: '家居生活',
    description: '打造温馨舒适的家'
  }
])

// 导航菜单数据
const navItems = ref([
  { id: 1, icon: '👕', name: '服装', color: '#FF6B6B' },
  { id: 2, icon: '📱', name: '数码', color: '#4ECDC4' },
  { id: 3, icon: '🏠', name: '家居', color: '#45B7D1' },
  { id: 4, icon: '💄', name: '美妆', color: '#F38181' },
  { id: 5, icon: '🍔', name: '美食', color: '#FCBAD3' },
  { id: 6, icon: '🎮', name: '娱乐', color: '#A8D8EA' },
  { id: 7, icon: '📚', name: '图书', color: '#DCD6F7' },
  { id: 8, icon: '💪', name: '运动', color: '#B5EAD7' },
  { id: 9, icon: '🎁', name: '礼品', color: '#FFB7B2' },
  { id: 10, icon: '🎯', name: '母婴', color: '#FFDAC1' }
])

// 商品列表数据
const products = ref([
  {
    id: 1,
    image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=classic%20white%20sneakers%20shoes%20product%20photography%20clean%20white%20background&image_size=square_hd',
    name: '经典百搭小白鞋',
    price: 299,
    originalPrice: 599,
    sales: 2356
  },
  {
    id: 2,
    image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=wireless%20bluetooth%20headphones%20black%20modern%20design%20product%20photo%20white%20background&image_size=square_hd',
    name: '无线蓝牙耳机',
    price: 199,
    originalPrice: 399,
    sales: 5892
  },
  {
    id: 3,
    image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=minimalist%20wooden%20coffee%20table%20modern%20furniture%20product%20photography%20white%20background&image_size=square_hd',
    name: '北欧简约茶几',
    price: 499,
    originalPrice: 899,
    sales: 856
  },
  {
    id: 4,
    image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=premium%20skincare%20set%20elegant%20cosmetics%20bottles%20luxury%20packaging%20white%20background&image_size=square_hd',
    name: '高端护肤套装',
    price: 699,
    originalPrice: 1299,
    sales: 3421
  },
  {
    id: 5,
    image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=smart%20fitness%20watch%20black%20modern%20wearable%20tech%20product%20photography%20white%20background&image_size=square_hd',
    name: '智能运动手表',
    price: 899,
    originalPrice: 1599,
    sales: 1256
  },
  {
    id: 6,
    image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=stylish%20leather%20handbag%20brown%20fashion%20accessory%20product%20photography%20white%20background&image_size=square_hd',
    name: '时尚真皮手提包',
    price: 599,
    originalPrice: 1199,
    sales: 2134
  },
  {
    id: 7,
    image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=portable%20bluetooth%20speaker%20colorful%20modern%20audio%20device%20product%20photography%20white%20background&image_size=square_hd',
    name: '便携蓝牙音箱',
    price: 159,
    originalPrice: 299,
    sales: 8765
  },
  {
    id: 8,
    image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=cozy%20knitted%20sweater%20warm%20winter%20clothing%20fashion%20product%20photography%20white%20background&image_size=square_hd',
    name: '温暖针织毛衣',
    price: 259,
    originalPrice: 499,
    sales: 4567
  }
])

// 轮播图控制方法
const goToSlide = (index) => {
  currentSlide.value = index
}

const nextSlide = () => {
  currentSlide.value = (currentSlide.value + 1) % slides.value.length
}

const prevSlide = () => {
  currentSlide.value = (currentSlide.value - 1 + slides.value.length) % slides.value.length
}

// 自动轮播
const startAutoPlay = () => {
  autoPlayInterval.value = setInterval(() => {
    if (!isPaused.value) {
      nextSlide()
    }
  }, 3000)
}

const stopAutoPlay = () => {
  if (autoPlayInterval.value) {
    clearInterval(autoPlayInterval.value)
    autoPlayInterval.value = null
  }
}

onMounted(() => {
  startAutoPlay()
})

onUnmounted(() => {
  stopAutoPlay()
})
</script>

<template>
  <div class="home-page">
    <!-- 顶部搜索栏 -->
    <header class="search-header">
      <div class="search-container">
        <el-input
          v-model="searchValue"
          placeholder="搜索商品、品牌、店铺..."
          class="search-input"
          clearable
        >
          <template #prefix>
            <el-icon class="search-icon">
              <Search />
            </el-icon>
          </template>
          <template #append>
            <el-button type="primary" class="search-btn">
              <el-icon>
                <Search />
              </el-icon>
              搜索
            </el-button>
          </template>
        </el-input>
      </div>
    </header>

    <!-- 轮播图区域 -->
    <section 
      class="carousel-section"
      @mouseenter="isPaused = true"
      @mouseleave="isPaused = false"
    >
      <div class="carousel-container">
        <div 
          class="carousel-track"
          :style="{ transform: `translateX(-${currentSlide * 100}%)` }"
        >
          <div 
            v-for="slide in slides" 
            :key="slide.id" 
            class="carousel-slide"
          >
            <img 
              :src="slide.image" 
              :alt="slide.title" 
              class="slide-image"
            />
            <div class="slide-content">
              <h2 class="slide-title">{{ slide.title }}</h2>
              <p class="slide-description">{{ slide.description }}</p>
            </div>
          </div>
        </div>

        <!-- 左右切换按钮 -->
        <button class="carousel-btn prev-btn" @click="prevSlide">
          <el-icon>
            <ArrowLeft />
          </el-icon>
        </button>
        <button class="carousel-btn next-btn" @click="nextSlide">
          <el-icon>
            <ArrowRight />
          </el-icon>
        </button>

        <!-- 指示器 -->
        <div class="carousel-indicators">
          <span
            v-for="(slide, index) in slides"
            :key="slide.id"
            class="indicator"
            :class="{ active: currentSlide === index }"
            @click="goToSlide(index)"
          ></span>
        </div>
      </div>
    </section>

    <!-- 魔方导航区域 -->
    <section class="nav-section">
      <div class="nav-container">
        <div class="nav-grid">
          <div 
            v-for="item in navItems" 
            :key="item.id" 
            class="nav-item"
            :style="{ '--nav-color': item.color }"
          >
            <span class="nav-icon">{{ item.icon }}</span>
            <span class="nav-name">{{ item.name }}</span>
          </div>
        </div>
      </div>
    </section>

    <!-- 商品列表区域 -->
    <section class="products-section">
      <div class="products-container">
        <div class="section-header">
          <h2 class="section-title">热门推荐</h2>
          <span class="section-more">查看更多 ></span>
        </div>
        
        <div class="products-grid">
          <div 
            v-for="product in products" 
            :key="product.id" 
            class="product-card"
          >
            <div class="product-image-wrapper">
              <img 
                :src="product.image" 
                :alt="product.name" 
                class="product-image"
              />
            </div>
            <div class="product-info">
              <h3 class="product-name">{{ product.name }}</h3>
              <div class="product-price-row">
                <span class="product-price">¥{{ product.price }}</span>
                <span class="product-original-price">¥{{ product.originalPrice }}</span>
              </div>
              <div class="product-sales">
                <span>已售 {{ product.sales }} 件</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<style scoped>
/* 全局样式重置 */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.home-page {
  width: 100%;
  min-height: 100vh;
  background-color: #f5f5f5;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
}

/* ========== 顶部搜索栏 ========== */
.search-header {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 1000;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  padding: 12px 20px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.1);
}

.search-container {
  max-width: 1200px;
  margin: 0 auto;
}

.search-input {
  max-width: 600px;
  margin: 0 auto;
  display: flex;
}

.search-input :deep(.el-input__wrapper) {
  border-radius: 25px 0 0 25px;
  box-shadow: none;
  background-color: #fff;
}

.search-input :deep(.el-input__inner) {
  padding-left: 8px;
}

.search-icon {
  color: #999;
}

.search-btn {
  border-radius: 0 25px 25px 0;
  background: linear-gradient(135deg, #ff6b6b 0%, #ee5a24 100%);
  border: none;
  padding: 0 24px;
  height: 100%;
}

.search-btn:hover {
  background: linear-gradient(135deg, #ee5a24 0%, #ff6b6b 100%);
}

/* ========== 轮播图区域 ========== */
.carousel-section {
  width: 100%;
  margin-top: 56px;
  overflow: hidden;
  position: relative;
}

.carousel-container {
  width: 100%;
  position: relative;
  overflow: hidden;
}

.carousel-track {
  display: flex;
  transition: transform 0.5s ease-in-out;
  width: 100%;
}

.carousel-slide {
  min-width: 100%;
  position: relative;
  height: 400px;
}

.slide-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  display: block;
}

.slide-content {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  padding: 40px 60px;
  background: linear-gradient(transparent, rgba(0, 0, 0, 0.7));
  color: white;
}

.slide-title {
  font-size: 32px;
  font-weight: 600;
  margin-bottom: 8px;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
}

.slide-description {
  font-size: 16px;
  opacity: 0.9;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.3);
}

/* 轮播按钮 */
.carousel-btn {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  width: 48px;
  height: 48px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.9);
  border: none;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 20px;
  color: #333;
  transition: all 0.3s ease;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  z-index: 10;
}

.carousel-btn:hover {
  background: #fff;
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.2);
  transform: translateY(-50%) scale(1.1);
}

.prev-btn {
  left: 20px;
}

.next-btn {
  right: 20px;
}

/* 轮播指示器 */
.carousel-indicators {
  position: absolute;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  gap: 8px;
  z-index: 10;
}

.indicator {
  width: 12px;
  height: 12px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.5);
  cursor: pointer;
  transition: all 0.3s ease;
}

.indicator:hover {
  background: rgba(255, 255, 255, 0.8);
}

.indicator.active {
  width: 32px;
  border-radius: 6px;
  background: #fff;
}

/* ========== 魔方导航区域 ========== */
.nav-section {
  width: 100%;
  padding: 24px 0;
  background: #fff;
  margin-bottom: 16px;
}

.nav-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 16px;
}

.nav-grid {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  gap: 16px;
}

.nav-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 20px 12px;
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.3s ease;
  background: linear-gradient(135deg, var(--nav-color) 0%, var(--nav-color) 100%);
  opacity: 0.9;
}

.nav-item:hover {
  transform: translateY(-4px);
  opacity: 1;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.15);
}

.nav-icon {
  font-size: 32px;
  margin-bottom: 8px;
}

.nav-name {
  font-size: 14px;
  color: #fff;
  font-weight: 500;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
}

/* ========== 商品列表区域 ========== */
.products-section {
  width: 100%;
  padding-bottom: 32px;
}

.products-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 16px;
}

.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 16px 0;
  margin-bottom: 16px;
}

.section-title {
  font-size: 20px;
  font-weight: 600;
  color: #333;
  position: relative;
  padding-left: 12px;
}

.section-title::before {
  content: '';
  position: absolute;
  left: 0;
  top: 50%;
  transform: translateY(-50%);
  width: 4px;
  height: 20px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border-radius: 2px;
}

.section-more {
  font-size: 14px;
  color: #999;
  cursor: pointer;
  transition: color 0.3s ease;
}

.section-more:hover {
  color: #667eea;
}

.products-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 16px;
}

.product-card {
  background: #fff;
  border-radius: 12px;
  overflow: hidden;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
}

.product-card:hover {
  transform: translateY(-6px);
  box-shadow: 0 12px 24px rgba(0, 0, 0, 0.12);
}

.product-image-wrapper {
  width: 100%;
  aspect-ratio: 1;
  overflow: hidden;
  background: #f8f8f8;
}

.product-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.3s ease;
}

.product-card:hover .product-image {
  transform: scale(1.05);
}

.product-info {
  padding: 12px;
}

.product-name {
  font-size: 14px;
  color: #333;
  font-weight: 500;
  line-height: 1.4;
  margin-bottom: 8px;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.product-price-row {
  display: flex;
  align-items: baseline;
  gap: 8px;
  margin-bottom: 6px;
}

.product-price {
  font-size: 18px;
  font-weight: 600;
  color: #ff6b6b;
}

.product-original-price {
  font-size: 12px;
  color: #999;
  text-decoration: line-through;
}

.product-sales {
  font-size: 12px;
  color: #999;
}

/* ========== 响应式适配 ========== */
/* 平板适配 */
@media (max-width: 992px) {
  .carousel-slide {
    height: 300px;
  }

  .slide-content {
    padding: 30px 40px;
  }

  .slide-title {
    font-size: 24px;
  }

  .slide-description {
    font-size: 14px;
  }

  .nav-grid {
    grid-template-columns: repeat(5, 1fr);
  }

  .products-grid {
    grid-template-columns: repeat(3, 1fr);
  }
}

/* 手机适配 */
@media (max-width: 768px) {
  .search-header {
    padding: 10px 16px;
  }

  .search-input {
    max-width: 100%;
  }

  .search-btn {
    padding: 0 16px;
  }

  .carousel-section {
    margin-top: 52px;
  }

  .carousel-slide {
    height: 220px;
  }

  .slide-content {
    padding: 20px 24px;
  }

  .slide-title {
    font-size: 18px;
    margin-bottom: 4px;
  }

  .slide-description {
    font-size: 12px;
  }

  .carousel-btn {
    width: 36px;
    height: 36px;
    font-size: 16px;
  }

  .prev-btn {
    left: 12px;
  }

  .next-btn {
    right: 12px;
  }

  .carousel-indicators {
    bottom: 12px;
  }

  .indicator {
    width: 8px;
    height: 8px;
  }

  .indicator.active {
    width: 24px;
  }

  .nav-grid {
    grid-template-columns: repeat(5, 1fr);
    gap: 12px;
  }

  .nav-item {
    padding: 16px 8px;
    border-radius: 8px;
  }

  .nav-icon {
    font-size: 24px;
    margin-bottom: 6px;
  }

  .nav-name {
    font-size: 12px;
  }

  .products-grid {
    grid-template-columns: repeat(2, 1fr);
    gap: 12px;
  }

  .product-info {
    padding: 10px;
  }

  .product-name {
    font-size: 13px;
  }

  .product-price {
    font-size: 16px;
  }
}

/* 小屏手机适配 */
@media (max-width: 480px) {
  .carousel-slide {
    height: 180px;
  }

  .slide-content {
    padding: 16px 20px;
  }

  .slide-title {
    font-size: 16px;
  }

  .nav-grid {
    grid-template-columns: repeat(5, 1fr);
    gap: 8px;
  }

  .nav-item {
    padding: 12px 6px;
  }

  .nav-icon {
    font-size: 20px;
    margin-bottom: 4px;
  }

  .nav-name {
    font-size: 11px;
  }

  .section-title {
    font-size: 16px;
  }

  .product-name {
    font-size: 12px;
  }

  .product-price {
    font-size: 14px;
  }

  .product-original-price {
    font-size: 11px;
  }
}
</style>
