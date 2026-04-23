<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

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
    description: '探索最新潮流时尚',
    tag: 'NEW'
  },
  {
    id: 2,
    image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=summer%20sale%20shopping%20banner%20with%20colorful%20shopping%20bags%20and%20discount%20tags%20vibrant%20colors&image_size=landscape_16_9',
    title: '限时特惠',
    description: '精选商品低至5折',
    tag: 'HOT'
  },
  {
    id: 3,
    image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=electronics%20gadgets%20showcase%20banner%20with%20smartphones%20laptops%20headphones%20tech%20style&image_size=landscape_16_9',
    title: '数码精选',
    description: '科技改变生活',
    tag: 'TECH'
  },
  {
    id: 4,
    image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=home%20furniture%20interior%20design%20banner%20modern%20minimalist%20living%20room%20cozy%20atmosphere&image_size=landscape_16_9',
    title: '家居生活',
    description: '打造温馨舒适的家',
    tag: 'HOME'
  }
])

// 导航菜单数据
const navItems = ref([
  { id: 1, icon: '👕', name: '服装', bgColor: '#fff5f5', iconBg: '#FF6B6B' },
  { id: 2, icon: '📱', name: '数码', bgColor: '#f0fffb', iconBg: '#4ECDC4' },
  { id: 3, icon: '🏠', name: '家居', bgColor: '#f0f8ff', iconBg: '#45B7D1' },
  { id: 4, icon: '💄', name: '美妆', bgColor: '#fff5f5', iconBg: '#F38181' },
  { id: 5, icon: '🍔', name: '美食', bgColor: '#fffaf5', iconBg: '#FF9F43' },
  { id: 6, icon: '🎮', name: '娱乐', bgColor: '#f5faff', iconBg: '#A8D8EA' },
  { id: 7, icon: '📚', name: '图书', bgColor: '#faf5ff', iconBg: '#DCD6F7' },
  { id: 8, icon: '💪', name: '运动', bgColor: '#f5fff9', iconBg: '#B5EAD7' },
  { id: 9, icon: '🎁', name: '礼品', bgColor: '#fff5f5', iconBg: '#FFB7B2' },
  { id: 10, icon: '🎯', name: '母婴', bgColor: '#fffaf5', iconBg: '#FFDAC1' }
])

// 商品列表数据 - 增强价格优惠属性
const products = ref([
  {
    id: 1,
    image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=classic%20white%20sneakers%20shoes%20product%20photography%20clean%20white%20background&image_size=square_hd',
    name: '经典百搭小白鞋 潮流运动鞋 舒适透气',
    price: 299,
    originalPrice: 599,
    sales: 2356,
    tags: ['热卖', '限时折扣'],
    coupon: '满300减30'
  },
  {
    id: 2,
    image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=wireless%20bluetooth%20headphones%20black%20modern%20design%20product%20photo%20white%20background&image_size=square_hd',
    name: '无线蓝牙耳机 主动降噪 超长续航',
    price: 199,
    originalPrice: 399,
    sales: 5892,
    tags: ['爆款', '新品'],
    coupon: ''
  },
  {
    id: 3,
    image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=minimalist%20wooden%20coffee%20table%20modern%20furniture%20product%20photography%20white%20background&image_size=square_hd',
    name: '北欧简约茶几 实木茶几 小户型必备',
    price: 499,
    originalPrice: 899,
    sales: 856,
    tags: ['包邮'],
    coupon: '满500减50'
  },
  {
    id: 4,
    image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=premium%20skincare%20set%20elegant%20cosmetics%20bottles%20luxury%20packaging%20white%20background&image_size=square_hd',
    name: '高端护肤套装 补水保湿 抗老紧致',
    price: 699,
    originalPrice: 1299,
    sales: 3421,
    tags: ['限时', '买一送一'],
    coupon: '满800减100'
  },
  {
    id: 5,
    image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=smart%20fitness%20watch%20black%20modern%20wearable%20tech%20product%20photography%20white%20background&image_size=square_hd',
    name: '智能运动手表 心率监测 GPS定位',
    price: 899,
    originalPrice: 1599,
    sales: 1256,
    tags: ['新品'],
    coupon: ''
  },
  {
    id: 6,
    image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=stylish%20leather%20handbag%20brown%20fashion%20accessory%20product%20photography%20white%20background&image_size=square_hd',
    name: '时尚真皮手提包 轻奢女包 大容量',
    price: 599,
    originalPrice: 1199,
    sales: 2134,
    tags: ['热卖', '限时'],
    coupon: '满600减60'
  },
  {
    id: 7,
    image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=portable%20bluetooth%20speaker%20colorful%20modern%20audio%20device%20product%20photography%20white%20background&image_size=square_hd',
    name: '便携蓝牙音箱 重低音 防水设计',
    price: 159,
    originalPrice: 299,
    sales: 8765,
    tags: ['爆款', '包邮'],
    coupon: ''
  },
  {
    id: 8,
    image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=cozy%20knitted%20sweater%20warm%20winter%20clothing%20fashion%20product%20photography%20white%20background&image_size=square_hd',
    name: '温暖针织毛衣 秋冬新款 时尚百搭',
    price: 259,
    originalPrice: 499,
    sales: 4567,
    tags: ['限时折扣'],
    coupon: '满300减30'
  }
])

// 计算折扣
const getDiscount = (price, originalPrice) => {
  if (!originalPrice || originalPrice <= 0) return 0
  return Math.round((1 - price / originalPrice) * 10)
}

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
        <div class="search-bar">
          <div class="search-icon-wrapper">
            <el-icon class="search-icon">
              <Search />
            </el-icon>
          </div>
          <input 
            v-model="searchValue"
            type="text"
            placeholder="搜索商品、品牌、店铺..."
            class="search-input-field"
          />
          <button class="search-btn">
            <span>搜索</span>
          </button>
        </div>
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
            <div class="slide-image-wrapper">
              <img 
                :src="slide.image" 
                :alt="slide.title" 
                class="slide-image"
              />
            </div>
            <div class="slide-overlay">
              <div class="slide-content">
                <span class="slide-tag">{{ slide.tag }}</span>
                <h2 class="slide-title">{{ slide.title }}</h2>
                <p class="slide-description">{{ slide.description }}</p>
              </div>
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
          >
            <div class="nav-icon-bg" :style="{ backgroundColor: item.iconBg }">
              <span class="nav-icon">{{ item.icon }}</span>
            </div>
            <span class="nav-name">{{ item.name }}</span>
          </div>
        </div>
      </div>
    </section>

    <!-- 商品列表区域 -->
    <section class="products-section">
      <div class="products-container">
        <div class="section-header">
          <h2 class="section-title">
            <span class="title-icon">🔥</span>
            热门推荐
          </h2>
          <span class="section-more">
            查看更多
            <el-icon class="more-icon"><ArrowRight /></el-icon>
          </span>
        </div>
        
        <div class="products-grid">
          <div 
            v-for="product in products" 
            :key="product.id" 
            class="product-card"
          >
            <div class="product-image-wrapper">
              <!-- 折扣角标 -->
              <div class="discount-badge" v-if="getDiscount(product.price, product.originalPrice) > 0">
                <span class="discount-text">{{ getDiscount(product.price, product.originalPrice) }}折</span>
              </div>
              
              <!-- 促销标签 -->
              <div class="promo-tags">
                <span 
                  v-for="(tag, index) in product.tags.slice(0, 2)" 
                  :key="index"
                  class="promo-tag"
                  :class="`tag-${tag}`"
                >
                  {{ tag }}
                </span>
              </div>
              
              <img 
                :src="product.image" 
                :alt="product.name" 
                class="product-image"
              />
            </div>
            
            <div class="product-info">
              <h3 class="product-name">{{ product.name }}</h3>
              
              <!-- 优惠券标签 -->
              <div class="coupon-row" v-if="product.coupon">
                <span class="coupon-label">券</span>
                <span class="coupon-text">{{ product.coupon }}</span>
              </div>
              
              <!-- 价格区域 -->
              <div class="price-area">
                <div class="price-main">
                  <span class="price-symbol">¥</span>
                  <span class="price-value">{{ product.price }}</span>
                </div>
                <span class="price-original">¥{{ product.originalPrice }}</span>
                <span class="price-save">省¥{{ product.originalPrice - product.price }}</span>
              </div>
              
              <div class="product-footer">
                <span class="product-sales">已售 {{ product.sales }}+</span>
                <span class="product-action">去抢购</span>
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
  background-color: #f2f3f5;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', Roboto, 'Helvetica Neue', Arial, sans-serif;
}

/* ========== 顶部搜索栏 - 优化版 ========== */
.search-header {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 1000;
  background: linear-gradient(180deg, #ff6b6b 0%, #ff4757 100%);
  padding: 10px 12px;
}

.search-container {
  max-width: 100%;
  margin: 0 auto;
}

.search-bar {
  display: flex;
  align-items: center;
  background: #fff;
  border-radius: 20px;
  padding: 0 14px;
  height: 36px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
}

.search-icon-wrapper {
  display: flex;
  align-items: center;
  margin-right: 8px;
}

.search-icon {
  color: #999;
  font-size: 16px;
}

.search-input-field {
  flex: 1;
  border: none;
  outline: none;
  font-size: 14px;
  color: #333;
  background: transparent;
}

.search-input-field::placeholder {
  color: #bbb;
}

.search-btn {
  background: linear-gradient(135deg, #ff6b6b 0%, #ff4757 100%);
  border: none;
  border-radius: 15px;
  padding: 6px 16px;
  cursor: pointer;
  transition: all 0.2s ease;
}

.search-btn span {
  color: #fff;
  font-size: 13px;
  font-weight: 500;
}

.search-btn:hover {
  background: linear-gradient(135deg, #ff4757 0%, #ff6b6b 100%);
  transform: scale(1.02);
}

/* ========== 轮播图区域 - 优化版 ========== */
.carousel-section {
  width: 100%;
  margin-top: 56px;
  overflow: hidden;
  position: relative;
  background: #fff;
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
  height: 180px;
}

.slide-image-wrapper {
  width: 100%;
  height: 100%;
  overflow: hidden;
}

.slide-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  display: block;
}

.slide-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(90deg, rgba(0, 0, 0, 0.3) 0%, transparent 50%);
}

.slide-content {
  position: absolute;
  left: 20px;
  bottom: 16px;
  color: white;
}

.slide-tag {
  display: inline-block;
  background: linear-gradient(135deg, #ff6b6b 0%, #ff4757 100%);
  padding: 2px 10px;
  border-radius: 10px;
  font-size: 11px;
  font-weight: 600;
  margin-bottom: 6px;
}

.slide-title {
  font-size: 20px;
  font-weight: 700;
  margin-bottom: 4px;
  text-shadow: 0 1px 3px rgba(0, 0, 0, 0.3);
}

.slide-description {
  font-size: 13px;
  opacity: 0.95;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.2);
}

/* 轮播按钮 */
.carousel-btn {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  width: 28px;
  height: 28px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.9);
  border: none;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 14px;
  color: #333;
  transition: all 0.2s ease;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
  z-index: 10;
}

.carousel-btn:hover {
  background: #fff;
  transform: translateY(-50%) scale(1.05);
}

.prev-btn {
  left: 10px;
}

.next-btn {
  right: 10px;
}

/* 轮播指示器 */
.carousel-indicators {
  position: absolute;
  bottom: 10px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  gap: 6px;
  z-index: 10;
}

.indicator {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.6);
  cursor: pointer;
  transition: all 0.3s ease;
}

.indicator.active {
  width: 18px;
  border-radius: 3px;
  background: #fff;
}

/* ========== 魔方导航区域 - 优化版 ========== */
.nav-section {
  width: 100%;
  background: #fff;
  padding: 12px 0;
  margin-bottom: 8px;
}

.nav-container {
  width: 100%;
  padding: 0 12px;
}

.nav-grid {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  gap: 8px;
}

.nav-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 12px 8px;
  cursor: pointer;
  transition: all 0.2s ease;
}

.nav-item:active {
  transform: scale(0.95);
}

.nav-icon-bg {
  width: 44px;
  height: 44px;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 6px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.nav-icon {
  font-size: 22px;
}

.nav-name {
  font-size: 12px;
  color: #333;
  font-weight: 500;
}

/* ========== 商品列表区域 - 优惠强化版 ========== */
.products-section {
  width: 100%;
  padding-bottom: 20px;
}

.products-container {
  width: 100%;
  padding: 0 12px;
}

.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 14px 0 10px;
  background: #fff;
  margin: 0 -12px 8px;
  padding-left: 12px;
  padding-right: 12px;
  border-radius: 8px 8px 0 0;
}

.section-title {
  font-size: 16px;
  font-weight: 700;
  color: #333;
  display: flex;
  align-items: center;
}

.title-icon {
  margin-right: 6px;
  font-size: 18px;
}

.section-more {
  font-size: 13px;
  color: #999;
  cursor: pointer;
  display: flex;
  align-items: center;
  transition: color 0.2s ease;
}

.section-more:hover {
  color: #ff6b6b;
}

.more-icon {
  font-size: 12px;
  margin-left: 2px;
}

.products-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 10px;
}

.product-card {
  background: #fff;
  border-radius: 10px;
  overflow: hidden;
  cursor: pointer;
  transition: all 0.2s ease;
  box-shadow: 0 1px 6px rgba(0, 0, 0, 0.05);
}

.product-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.1);
}

.product-image-wrapper {
  width: 100%;
  aspect-ratio: 1;
  overflow: hidden;
  background: #fafafa;
  position: relative;
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

/* 折扣角标 */
.discount-badge {
  position: absolute;
  top: 0;
  left: 0;
  background: linear-gradient(135deg, #ff6b6b 0%, #ff4757 100%);
  padding: 4px 8px 4px 6px;
  border-radius: 0 0 8px 0;
  z-index: 2;
  clip-path: polygon(0 0, 100% 0, 100% 100%, 0 100%, 0 20%);
}

.discount-text {
  color: #fff;
  font-size: 11px;
  font-weight: 700;
}

/* 促销标签 */
.promo-tags {
  position: absolute;
  top: 6px;
  right: 6px;
  display: flex;
  flex-direction: column;
  gap: 4px;
  z-index: 2;
}

.promo-tag {
  display: inline-block;
  padding: 2px 6px;
  border-radius: 3px;
  font-size: 10px;
  font-weight: 600;
  white-space: nowrap;
}

.promo-tag.tag-热卖 {
  background: linear-gradient(135deg, #ff6b6b 0%, #ff4757 100%);
  color: #fff;
}

.promo-tag.tag-爆款 {
  background: linear-gradient(135deg, #ff9f43 0%, #ff6b35 100%);
  color: #fff;
}

.promo-tag.tag-新品 {
  background: linear-gradient(135deg, #4ECDC4 0%, #44A08D 100%);
  color: #fff;
}

.promo-tag.tag-限时 {
  background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
  color: #fff;
}

.promo-tag.tag-限时折扣 {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: #fff;
}

.promo-tag.tag-买一送一 {
  background: linear-gradient(135deg, #11998e 0%, #38ef7d 100%);
  color: #fff;
}

.promo-tag.tag-包邮 {
  background: #fff5f5;
  color: #ff6b6b;
  border: 1px solid #ffcccc;
}

.product-info {
  padding: 10px;
}

.product-name {
  font-size: 13px;
  color: #333;
  font-weight: 500;
  line-height: 1.4;
  margin-bottom: 8px;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
  height: 36px;
}

/* 优惠券标签 */
.coupon-row {
  display: flex;
  align-items: center;
  margin-bottom: 6px;
}

.coupon-label {
  background: linear-gradient(135deg, #ff6b6b 0%, #ff4757 100%);
  color: #fff;
  font-size: 10px;
  padding: 1px 4px;
  border-radius: 2px;
  margin-right: 4px;
  font-weight: 600;
}

.coupon-text {
  font-size: 11px;
  color: #ff6b6b;
  font-weight: 500;
}

/* 价格区域 */
.price-area {
  display: flex;
  align-items: baseline;
  flex-wrap: wrap;
  gap: 4px;
  margin-bottom: 8px;
}

.price-main {
  display: flex;
  align-items: baseline;
}

.price-symbol {
  font-size: 12px;
  color: #ff4757;
  font-weight: 600;
}

.price-value {
  font-size: 20px;
  color: #ff4757;
  font-weight: 700;
}

.price-original {
  font-size: 11px;
  color: #999;
  text-decoration: line-through;
}

.price-save {
  font-size: 11px;
  color: #ff6b6b;
  background: #fff1f0;
  padding: 1px 4px;
  border-radius: 2px;
  font-weight: 500;
}

/* 底部信息 */
.product-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-top: 6px;
  border-top: 1px solid #f0f0f0;
}

.product-sales {
  font-size: 11px;
  color: #999;
}

.product-action {
  font-size: 11px;
  color: #ff6b6b;
  font-weight: 600;
  padding: 2px 8px;
  background: #fff1f0;
  border-radius: 10px;
}

/* ========== 响应式适配 ========== */
/* 平板适配 */
@media (min-width: 768px) and (max-width: 992px) {
  .search-header {
    padding: 12px 20px;
  }
  
  .search-bar {
    max-width: 600px;
    margin: 0 auto;
    height: 40px;
  }
  
  .carousel-slide {
    height: 280px;
  }
  
  .slide-content {
    left: 40px;
    bottom: 24px;
  }
  
  .slide-title {
    font-size: 28px;
  }
  
  .nav-section {
    padding: 20px 0;
  }
  
  .nav-grid {
    grid-template-columns: repeat(5, 1fr);
    max-width: 700px;
    margin: 0 auto;
    gap: 16px;
  }
  
  .nav-item {
    padding: 16px 12px;
  }
  
  .nav-icon-bg {
    width: 52px;
    height: 52px;
  }
  
  .nav-icon {
    font-size: 28px;
  }
  
  .nav-name {
    font-size: 13px;
  }
  
  .products-grid {
    grid-template-columns: repeat(3, 1fr);
    max-width: 720px;
    margin: 0 auto;
    gap: 14px;
  }
  
  .products-container {
    padding: 0 20px;
  }
}

/* PC端适配 */
@media (min-width: 992px) {
  .search-header {
    padding: 14px 24px;
  }
  
  .search-bar {
    max-width: 680px;
    margin: 0 auto;
    height: 44px;
    border-radius: 22px;
  }
  
  .search-input-field {
    font-size: 15px;
  }
  
  .search-btn {
    padding: 8px 24px;
    border-radius: 18px;
  }
  
  .search-btn span {
    font-size: 14px;
  }
  
  .carousel-section {
    margin-top: 72px;
  }
  
  .carousel-slide {
    height: 360px;
  }
  
  .slide-content {
    left: 60px;
    bottom: 36px;
  }
  
  .slide-tag {
    font-size: 12px;
    padding: 4px 14px;
    border-radius: 12px;
    margin-bottom: 10px;
  }
  
  .slide-title {
    font-size: 36px;
    margin-bottom: 8px;
  }
  
  .slide-description {
    font-size: 16px;
  }
  
  .carousel-btn {
    width: 44px;
    height: 44px;
    font-size: 18px;
  }
  
  .prev-btn {
    left: 30px;
  }
  
  .next-btn {
    right: 30px;
  }
  
  .carousel-indicators {
    bottom: 20px;
    gap: 8px;
  }
  
  .indicator {
    width: 8px;
    height: 8px;
  }
  
  .indicator.active {
    width: 28px;
    border-radius: 4px;
  }
  
  .nav-section {
    padding: 24px 0;
    margin-bottom: 12px;
  }
  
  .nav-container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 24px;
  }
  
  .nav-grid {
    grid-template-columns: repeat(10, 1fr);
    gap: 12px;
  }
  
  .nav-item {
    padding: 16px 8px;
  }
  
  .nav-icon-bg {
    width: 48px;
    height: 48px;
    border-radius: 14px;
  }
  
  .nav-icon {
    font-size: 24px;
  }
  
  .nav-name {
    font-size: 12px;
    color: #666;
  }
  
  .products-section {
    padding-bottom: 40px;
  }
  
  .products-container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 24px;
  }
  
  .section-header {
    padding: 20px 0 16px;
    margin: 0;
    background: transparent;
    border-radius: 0;
  }
  
  .section-title {
    font-size: 20px;
  }
  
  .title-icon {
    font-size: 22px;
    margin-right: 8px;
  }
  
  .section-more {
    font-size: 14px;
  }
  
  .products-grid {
    grid-template-columns: repeat(4, 1fr);
    gap: 16px;
  }
  
  .product-card {
    border-radius: 12px;
  }
  
  .product-info {
    padding: 14px;
  }
  
  .product-name {
    font-size: 14px;
    height: 40px;
  }
  
  .price-value {
    font-size: 22px;
  }
  
  .price-symbol {
    font-size: 14px;
  }
  
  .discount-badge {
    padding: 6px 10px 6px 8px;
  }
  
  .discount-text {
    font-size: 12px;
  }
}
</style>
