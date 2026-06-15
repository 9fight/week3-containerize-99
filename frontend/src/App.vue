<template>
  <div class="stockpro-page">
    <div class="site-effects" aria-hidden="true">
      <span v-for="n in 24" :key="n" :class="`spark spark-${n}`"></span>
      <span class="mesh mesh-a"></span>
      <span class="mesh mesh-b"></span>
    </div>

    <header class="site-nav">
      <a class="brand" href="#" aria-label="StockPro">
        <span class="brand-logo"><Package /></span>
        <span>
          <strong>StockPro</strong>
          <small>Inventory SaaS</small>
        </span>
      </a>

      <nav class="nav-links" aria-label="Main navigation">
        <a href="#dashboard">แดชบอร์ด</a>
        <a href="#products">สินค้า</a>
        <a href="#insights">สรุป</a>
      </nav>

      <button class="fusions-btn nav-action" @click="openAdd">
        <UserRoundPlus />
        <span>เริ่มเพิ่มสินค้า</span>
      </button>
    </header>

    <main>
      <section class="hero" id="dashboard">
        <div class="hero-copy">
          <p class="micro-copy">StockPro inventory command center</p>
          <h1 class="hero-title">
            <span class="headline-main">ระบบจัดการสต็อก</span>
            <span class="headline-line">
              <span class="headline-prefix">ที่</span>
              <span class="neon-word neon-purple">ดูง่าย</span>
              <span class="neon-word neon-pink">ใช้งานไว</span>
            </span>
          </h1>
          <p class="hero-desc">
            คุมสินค้า มูลค่าสต็อก และรายการใกล้หมดในหน้าเดียว พร้อม search, filter และ CRUD ครบสำหรับงานส่ง Week 3
          </p>
          <div class="hero-actions">
            <button class="fusions-btn" @click="openAdd">
              <UserRoundPlus />
              <span>เพิ่มสินค้าใหม่</span>
            </button>
            <a class="fusions-btn ghost" href="#products">
              <Star />
              <span>ดูรายการสินค้า</span>
            </a>
          </div>
          <div class="hero-proof">
            <span><CircleCheck /> API พร้อม</span>
            <span><CircleCheck /> Docker พร้อม</span>
            <span><CircleCheck /> UI พร้อมใช้งาน</span>
          </div>
        </div>

        <div class="hero-visual">
          <div class="mock-window">
            <div class="mock-bar">
              <span></span><span></span><span></span>
              <p>stockpro.app/dashboard</p>
            </div>
            <div class="mock-content">
              <div class="mock-panel">
                <div class="mock-title">
                  <Boxes />
                  <span>Inventory Pulse</span>
                </div>
                <strong>{{ stats.totalItems.toLocaleString() }}</strong>
                <small>items in stock</small>
                <div class="mock-wave">
                  <i v-for="n in 12" :key="n" :style="{ height: `${24 + ((n * 13) % 52)}px` }"></i>
                </div>
              </div>
              <div class="mock-list">
                <div v-for="p in previewProducts" :key="p.id" class="mock-row">
                  <span :class="stockClass(p.stock)"></span>
                  <div>
                    <strong>{{ p.name }}</strong>
                    <small>{{ p.category }} · {{ p.stock }} ชิ้น</small>
                  </div>
                  <b>฿{{ Number(p.price).toLocaleString() }}</b>
                </div>
              </div>
            </div>
          </div>
          <div class="floating-card float-a">
            <TrendingUp />
            <span>฿{{ Math.round(stats.totalValue).toLocaleString() }}</span>
          </div>
          <div class="floating-card float-b">
            <AlertTriangle />
            <span>{{ stats.lowStock }} low stock</span>
          </div>
        </div>
      </section>

      <section class="metric-strip" id="insights" aria-label="Inventory metrics">
        <div class="metric-card">
          <Package />
          <strong>{{ stats.total }}</strong>
          <span>สินค้าทั้งหมด</span>
        </div>
        <div class="metric-card">
          <BadgeDollarSign />
          <strong>฿{{ Math.round(stats.totalValue).toLocaleString() }}</strong>
          <span>มูลค่าสต็อก</span>
        </div>
        <div class="metric-card">
          <Boxes />
          <strong>{{ stats.totalItems.toLocaleString() }}</strong>
          <span>จำนวนรวม</span>
        </div>
        <div class="metric-card danger">
          <AlertTriangle />
          <strong>{{ stats.lowStock }}</strong>
          <span>ใกล้หมด</span>
        </div>
      </section>

      <section class="faq-section" aria-label="Frequently asked questions">
        <div class="section-head faq-head">
          <div>
            <p class="micro-copy">Quick help</p>
            <h2>คำถามที่พบบ่อย</h2>
          </div>
          <span class="count-chip">เลือกคำถามเพื่อดูคำตอบ</span>
        </div>

        <div class="faq-grid">
          <div class="faq-chat">
            <div class="chat-row bot">
              <div class="chat-avatar">
                <BotMessageSquare />
              </div>
              <div class="chat-bubble">
                <strong>StockPro Help</strong>
                <p>เลือกคำถามด้านซ้าย แล้วระบบจะตอบให้ทันที</p>
              </div>
            </div>

            <div class="chat-row user">
              <div class="chat-bubble">
                <strong>{{ activeFaq.question }}</strong>
                <p>อยากรู้เรื่องนี้</p>
              </div>
            </div>

            <div class="chat-row bot">
              <div class="chat-avatar">
                <MessageSquareQuote />
              </div>
              <div class="chat-bubble answer">
                <strong>คำตอบ</strong>
                <p>{{ activeFaq.answer }}</p>
              </div>
            </div>

            <div class="chat-tips">
              <span v-for="tip in activeFaq.tips" :key="tip">
                <CircleCheck />
                {{ tip }}
              </span>
            </div>
          </div>

          <div class="faq-list">
            <button
              v-for="faq in faqItems"
              :key="faq.id"
              type="button"
              class="faq-item"
              :class="{ active: selectedFaqId === faq.id }"
              @click="selectedFaqId = faq.id"
            >
              <MessagesSquare />
              <span>
                <strong>{{ faq.question }}</strong>
                <small>{{ faq.short }}</small>
              </span>
            </button>
          </div>
        </div>
      </section>

      <section class="products-section" id="products">
        <div class="section-head">
          <div>
            <p class="micro-copy">Product workspace</p>
            <h2>จัดการสินค้า</h2>
          </div>
          <button class="fusions-btn compact" @click="openAdd">
            <Plus />
            <span>เพิ่มสินค้า</span>
          </button>
        </div>

        <div class="alert-low" v-if="stats.lowStock > 0">
          <AlertTriangle />
          <span>มีสินค้า <strong>{{ stats.lowStock }} รายการ</strong> ที่มีสต็อกน้อยกว่า 10 ชิ้น</span>
        </div>

        <div class="tool-surface">
          <label class="control search-control">
            <Search />
            <input
              v-model="search"
              @input="debounceFetch"
              placeholder="ค้นหาชื่อสินค้า หรือคำอธิบาย"
            />
          </label>
          <label class="control">
            <SlidersHorizontal />
            <select v-model="catFilter" @change="fetchProducts">
              <option value="">ทุกหมวดหมู่</option>
              <option v-for="c in categories" :key="c" :value="c">{{ c }}</option>
            </select>
          </label>
          <span class="count-chip">{{ filtered.length }} / {{ products.length }} รายการ</span>
        </div>

        <div class="state-box" v-if="loading">
          <LoaderCircle class="spin" />
          <span>กำลังโหลดข้อมูลสินค้า...</span>
        </div>

        <div class="state-box" v-else-if="filtered.length === 0">
          <ArchiveX />
          <strong>ไม่พบสินค้า</strong>
          <span>{{ search || catFilter ? 'ลองเปลี่ยนคำค้นหาหรือหมวดหมู่' : 'กดเพิ่มสินค้าเพื่อเริ่มต้น' }}</span>
        </div>

        <div class="product-grid" v-else>
          <article
            v-for="p in filtered"
            :key="p.id"
            class="product-card"
            :class="{ 'card-low': p.stock > 0 && p.stock < 10, 'card-out': p.stock <= 0 }"
          >
            <div class="card-orb" aria-hidden="true"></div>
            <div class="product-top">
              <span class="cat-badge" :class="catClass(p.category)">{{ p.category }}</span>
              <span class="stock-pill" :class="stockClass(p.stock)">{{ stockLabel(p.stock) }}</span>
            </div>
            <h3>{{ p.name }}</h3>
            <p>{{ p.description || 'ไม่มีคำอธิบายสินค้า' }}</p>
            <div class="price-row">
              <strong>฿{{ parseFloat(p.price).toLocaleString('th-TH', { minimumFractionDigits: 2 }) }}</strong>
              <span>{{ p.stock.toLocaleString() }} ชิ้น</span>
            </div>
            <div class="stock-track">
              <i :class="stockClass(p.stock)" :style="{ width: Math.min((p.stock / 80) * 100, 100) + '%' }"></i>
            </div>
            <div class="card-actions">
              <button class="mini-btn dark" @click="openEdit(p)">
                <Pencil />
                <span>แก้ไข</span>
              </button>
              <button class="mini-btn light" @click="confirmDelete = p">
                <Trash2 />
                <span>ลบ</span>
              </button>
            </div>
          </article>
        </div>
      </section>
    </main>

    <div class="overlay" v-if="showModal" @click.self="showModal = false">
      <div class="modal">
        <div class="modal-head">
          <span><component :is="editingId ? Pencil : PackagePlus" /></span>
          <div>
            <p class="micro-copy">{{ editingId ? 'Edit product' : 'Create product' }}</p>
            <h2>{{ editingId ? 'แก้ไขสินค้า' : 'เพิ่มสินค้าใหม่' }}</h2>
          </div>
        </div>

        <form @submit.prevent="saveProduct">
          <label class="form-field">
            <span>ชื่อสินค้า *</span>
            <input v-model="form.name" placeholder="เช่น MacBook Air M3" required />
          </label>
          <div class="form-grid">
            <label class="form-field">
              <span>หมวดหมู่ *</span>
              <input v-model="form.category" list="cat-list" placeholder="เช่น Electronics" required />
              <datalist id="cat-list">
                <option v-for="c in categories" :value="c" :key="c" />
              </datalist>
            </label>
            <label class="form-field">
              <span>ราคา (บาท) *</span>
              <input v-model="form.price" type="number" min="0" step="0.01" placeholder="0.00" required />
            </label>
          </div>
          <label class="form-field">
            <span>จำนวนสต็อก (ชิ้น) *</span>
            <input v-model="form.stock" type="number" min="0" placeholder="0" required />
          </label>
          <label class="form-field">
            <span>คำอธิบายสินค้า</span>
            <textarea v-model="form.description" rows="3" placeholder="รายละเอียดเพิ่มเติม (ถ้ามี)"></textarea>
          </label>
          <div class="modal-actions">
            <button type="button" class="modal-btn cancel" @click="showModal = false">
              <X />
              <span>ยกเลิก</span>
            </button>
            <button type="submit" class="fusions-btn compact">
              <Save />
              <span>{{ editingId ? 'บันทึก' : 'เพิ่มสินค้า' }}</span>
            </button>
          </div>
        </form>
      </div>
    </div>

    <div class="overlay" v-if="confirmDelete" @click.self="confirmDelete = null">
      <div class="modal confirm">
        <div class="confirm-icon"><Trash2 /></div>
        <h2>ยืนยันการลบสินค้า</h2>
        <p>คุณต้องการลบ <strong>{{ confirmDelete?.name }}</strong> ออกจากระบบหรือไม่?</p>
        <div class="modal-actions centered">
          <button class="modal-btn cancel" @click="confirmDelete = null">
            <X />
            <span>ยกเลิก</span>
          </button>
          <button class="modal-btn danger" @click="deleteProduct(confirmDelete.id)">
            <Trash2 />
            <span>ลบเลย</span>
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import {
  AlertTriangle,
  ArchiveX,
  BadgeDollarSign,
  Boxes,
  CircleCheck,
  BotMessageSquare,
  LoaderCircle,
  MessageSquareQuote,
  MessagesSquare,
  Package,
  PackagePlus,
  Pencil,
  Plus,
  Save,
  Search,
  SlidersHorizontal,
  Star,
  Trash2,
  TrendingUp,
  UserRoundPlus,
  X
} from '@lucide/vue'

const products = ref([])
const loading = ref(true)
const search = ref('')
const catFilter = ref('')
const showModal = ref(false)
const editingId = ref(null)
const confirmDelete = ref(null)
const selectedFaqId = ref('search')
const form = ref({ name: '', category: '', price: '', stock: 0, description: '' })

let debounceTimer = null

const faqItems = [
  {
    id: 'search',
    question: 'ค้นหาสินค้ายังไง?',
    short: 'ใช้ search bar ด้านบน',
    answer: 'พิมพ์ชื่อสินค้า หรือคำอธิบายลงในช่องค้นหา ระบบจะกรองรายการให้ทันที',
    tips: ['ค้นได้ทั้งชื่อและคำอธิบาย', 'พิมพ์แล้วรอระบบกรองอัตโนมัติ', 'ใช้คู่กับตัวเลือกหมวดหมู่ได้']
  },
  {
    id: 'add',
    question: 'เพิ่มสินค้าใหม่ทำยังไง?',
    short: 'กดปุ่มเพิ่มสินค้า',
    answer: 'กดปุ่ม เพิ่มสินค้าใหม่ หรือ เพิ่มสินค้า จากนั้นกรอกชื่อ หมวดหมู่ ราคา สต็อก และคำอธิบายแล้วกดบันทึก',
    tips: ['ฟอร์มรองรับแก้ไขข้อมูลเดิมได้', 'ราคาใส่ทศนิยมได้', 'สต็อกต้องเป็นตัวเลข']
  },
  {
    id: 'low-stock',
    question: 'สินค้าใกล้หมดดูตรงไหน?',
    short: 'เช็กการ์ดสีแดงและ alert',
    answer: 'ดูจากการ์ดสรุปสต็อกใกล้หมด และการ์ดสินค้าแต่ละใบที่มีแถบหรือสถานะสีแดง',
    tips: ['ต่ำกว่า 10 ชิ้นจะถูกนับเป็นใกล้หมด', 'สต็อกหมดจะแสดงเป็นหมดแล้ว', 'มี alert ด้านบนรายการสินค้า']
  },
  {
    id: 'delete',
    question: 'ลบสินค้าแล้วกู้คืนได้ไหม?',
    short: 'ลบถาวรจากระบบ',
    answer: 'การลบในหน้านี้เป็นการลบออกจากรายการทันที จึงควรตรวจชื่อสินค้าให้ดีก่อนยืนยัน',
    tips: ['มีหน้าต่างยืนยันก่อนลบ', 'กด ยกเลิก เพื่อไม่ลบข้อมูล', 'ควรสำรองข้อมูลก่อนใช้งานจริง']
  }
]

const filtered = computed(() => {
  const s = search.value.toLowerCase()
  return products.value.filter(p => {
    const matchS = !s || p.name.toLowerCase().includes(s) || (p.description || '').toLowerCase().includes(s)
    const matchC = !catFilter.value || p.category === catFilter.value
    return matchS && matchC
  })
})

const previewProducts = computed(() => filtered.value.slice(0, 4))

const categories = computed(() =>
  [...new Set(products.value.map(p => p.category))].sort()
)

const stats = computed(() => ({
  total: products.value.length,
  lowStock: products.value.filter(p => p.stock < 10).length,
  totalItems: products.value.reduce((s, p) => s + p.stock, 0),
  totalValue: products.value.reduce((s, p) => s + parseFloat(p.price) * p.stock, 0)
}))

const activeFaq = computed(() => faqItems.find(item => item.id === selectedFaqId.value) || faqItems[0])

function debounceFetch() {
  clearTimeout(debounceTimer)
  debounceTimer = setTimeout(fetchProducts, 280)
}

async function fetchProducts() {
  loading.value = true
  const q = new URLSearchParams()
  if (search.value) q.set('search', search.value)
  if (catFilter.value) q.set('category', catFilter.value)
  try {
    const res = await fetch(`/api/products?${q}`)
    products.value = await res.json()
  } catch {
    products.value = []
  } finally {
    loading.value = false
  }
}

function openAdd() {
  editingId.value = null
  form.value = { name: '', category: '', price: '', stock: 0, description: '' }
  showModal.value = true
}

function openEdit(p) {
  editingId.value = p.id
  form.value = { name: p.name, category: p.category, price: p.price, stock: p.stock, description: p.description || '' }
  showModal.value = true
}

async function saveProduct() {
  const body = {
    name: form.value.name,
    category: form.value.category,
    price: parseFloat(form.value.price),
    stock: parseInt(form.value.stock),
    description: form.value.description
  }
  const url = editingId.value ? `/api/products/${editingId.value}` : '/api/products'
  const method = editingId.value ? 'PUT' : 'POST'
  await fetch(url, { method, headers: { 'Content-Type': 'application/json' }, body: JSON.stringify(body) })
  showModal.value = false
  fetchProducts()
}

async function deleteProduct(id) {
  await fetch(`/api/products/${id}`, { method: 'DELETE' })
  confirmDelete.value = null
  fetchProducts()
}

function stockClass(s) {
  if (s <= 0) return 'out'
  if (s < 10) return 'low'
  if (s < 30) return 'mid'
  return 'high'
}

function stockLabel(s) {
  if (s <= 0) return 'หมด'
  if (s < 10) return 'ใกล้หมด'
  if (s < 30) return 'ปานกลาง'
  return 'พร้อมขาย'
}

function catClass(cat) {
  const m = {
    Electronics: 'c-purple',
    Clothing: 'c-pink',
    Footwear: 'c-violet',
    Food: 'c-orange',
    Sports: 'c-green',
    Books: 'c-blue',
    Furniture: 'c-slate',
    Bags: 'c-orange',
    Accessories: 'c-green',
    Tools: 'c-slate'
  }
  return m[cat] || 'c-purple'
}

onMounted(fetchProducts)
</script>

<style scoped>
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
svg { width: 18px; height: 18px; stroke-width: 2.15; }
a { color: inherit; text-decoration: none; }
button, input, select, textarea { font: inherit; }

.stockpro-page {
  min-height: 100vh;
  position: relative;
  overflow-x: hidden;
  color: #09090b;
  background:
    radial-gradient(circle at 14% 8%, rgba(97, 95, 255, .09), transparent 28%),
    radial-gradient(circle at 83% 2%, rgba(236, 108, 255, .08), transparent 30%),
    linear-gradient(180deg, #fff 0%, #fafafa 45%, #fff 100%);
}

.site-effects { position: fixed; inset: 0; pointer-events: none; overflow: hidden; z-index: 0; }
.mesh {
  position: absolute;
  border-radius: 999px;
  filter: blur(44px);
  opacity: .42;
  animation: floatMesh 9s ease-in-out infinite;
}
.mesh-a { width: 260px; height: 260px; left: -90px; top: 170px; background: #c4b4ff; }
.mesh-b { width: 300px; height: 300px; right: -120px; top: 70px; background: #fae8ff; animation-delay: 1.4s; }
.spark {
  position: absolute;
  width: 5px;
  height: 5px;
  border-radius: 999px;
  background: #625fff;
  opacity: .45;
  animation: drift 7s ease-in-out infinite;
}
.spark:nth-child(3n) { background: #ec6cff; }
.spark:nth-child(4n) { background: #fe6e00; }
.spark:nth-child(5n) { background: #00bb7f; }
.spark-1 { left: 7%; top: 16%; }.spark-2 { left: 22%; top: 9%; animation-delay: .4s; }
.spark-3 { left: 42%; top: 18%; animation-delay: .8s; }.spark-4 { left: 68%; top: 12%; animation-delay: 1.2s; }
.spark-5 { right: 8%; top: 22%; animation-delay: 1.6s; }.spark-6 { left: 11%; top: 44%; animation-delay: 2s; }
.spark-7 { left: 34%; top: 52%; animation-delay: 2.4s; }.spark-8 { right: 23%; top: 44%; animation-delay: 2.8s; }
.spark-9 { right: 7%; top: 58%; animation-delay: 3.2s; }.spark-10 { left: 8%; bottom: 18%; animation-delay: 3.6s; }
.spark-11 { left: 29%; bottom: 12%; animation-delay: 4s; }.spark-12 { left: 51%; bottom: 19%; animation-delay: 4.4s; }
.spark-13 { right: 18%; bottom: 13%; animation-delay: 4.8s; }.spark-14 { right: 5%; bottom: 26%; animation-delay: 5.2s; }
.spark-15 { left: 3%; top: 72%; animation-delay: 5.6s; }.spark-16 { left: 58%; top: 7%; animation-delay: 6s; }
.spark-17 { left: 79%; top: 35%; animation-delay: .2s; }.spark-18 { left: 18%; top: 31%; animation-delay: .7s; }
.spark-19 { left: 47%; top: 39%; animation-delay: 1.1s; }.spark-20 { right: 31%; bottom: 35%; animation-delay: 1.5s; }
.spark-21 { left: 64%; bottom: 8%; animation-delay: 1.9s; }.spark-22 { left: 37%; bottom: 31%; animation-delay: 2.3s; }
.spark-23 { right: 43%; top: 26%; animation-delay: 2.7s; }.spark-24 { right: 12%; bottom: 42%; animation-delay: 3.1s; }
@keyframes drift { 50% { transform: translateY(-18px) scale(1.3); opacity: .85; } }
@keyframes floatMesh { 50% { transform: translate(18px, -18px) scale(1.08); } }

.site-nav, main { position: relative; z-index: 2; }
.site-nav {
  position: fixed;
  top: 16px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 50;
  width: min(1180px, calc(100% - 32px));
  height: 74px;
  margin: 0 auto;
  padding: 0 14px 0 18px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 16px;
  border: 1px solid rgba(228, 228, 231, .85);
  border-radius: 24px;
  background: rgba(255, 255, 255, .76);
  box-shadow: 0 20px 60px rgba(9, 9, 11, .08);
  backdrop-filter: blur(18px);
}
.brand { display: flex; align-items: center; gap: 11px; }
.brand-logo {
  width: 42px;
  height: 42px;
  display: grid;
  place-items: center;
  border-radius: 16px;
  color: #fff;
  background: #09090b;
  box-shadow: 0 12px 28px rgba(9, 9, 11, .18);
}
.brand strong { display: block; font-size: 1rem; font-weight: 900; letter-spacing: 0; }
.brand small { display: block; color: #71717b; font-size: .72rem; font-weight: 700; }
.nav-links { display: flex; align-items: center; gap: 6px; padding: 5px; border-radius: 999px; background: #f4f4f5; }
.nav-links a {
  padding: 8px 14px;
  border-radius: 999px;
  color: #52525c;
  font-size: .86rem;
  font-weight: 800;
  transition: color .18s ease, background .18s ease;
}
.nav-links a:hover { color: #09090b; background: #fff; }

main {
  width: min(1180px, calc(100% - 32px));
  margin: 0 auto;
  padding: 112px 0 54px;
}
.hero {
  min-height: 560px;
  display: grid;
  grid-template-columns: minmax(560px, 1.08fr) minmax(390px, .92fr);
  gap: 44px;
  align-items: center;
}
.micro-copy {
  color: #4f39f6;
  font-size: .79rem;
  font-weight: 900;
  text-transform: uppercase;
  letter-spacing: .11em;
}
.hero h1 {
  max-width: 760px;
  margin-top: 14px;
  font-size: clamp(3rem, 4.25vw, 4.7rem);
  line-height: 1.04;
  letter-spacing: 0;
  font-weight: 900;
  text-wrap: balance;
  word-break: keep-all;
}

.hero-title {
  display: grid;
  gap: 4px;
}

.headline-main {
  display: block;
  white-space: nowrap;
  letter-spacing: -.01em;
}

.headline-line {
  display: inline-flex;
  align-items: center;
  flex-wrap: wrap;
  gap: .16em;
  width: fit-content;
  line-height: .98;
}

.headline-prefix {
  color: #09090b;
  font-weight: 900;
  margin-right: .02em;
}

.headline-dot {
  width: .13em;
  height: .13em;
  margin: 0 .08em;
  border-radius: 999px;
  background: linear-gradient(135deg, #ec6cff, #fcbb00);
  box-shadow: 0 0 24px rgba(236, 108, 255, .55);
}

.neon-word {
  position: relative;
  display: inline-block;
  color: transparent;
  -webkit-background-clip: text;
  background-clip: text;
  letter-spacing: -.015em;
  text-shadow: 0 0 28px rgba(141, 84, 255, .28);
}

.neon-word::after {
  content: "";
  position: absolute;
  left: .05em;
  right: .05em;
  bottom: -.08em;
  height: .1em;
  border-radius: 999px;
  filter: blur(10px);
  opacity: .8;
}

.neon-purple {
  background-image: linear-gradient(100deg, #4f39f6, #8d54ff, #ec6cff);
}

.neon-purple::after {
  background: linear-gradient(90deg, #4f39f6, #8d54ff, #ec6cff);
}

.neon-pink {
  background-image: linear-gradient(100deg, #ec6cff, #ff6568, #fcbb00);
  text-shadow: 0 0 30px rgba(236, 108, 255, .26);
}

.neon-pink::after {
  background: linear-gradient(90deg, #ec6cff, #ff6568, #fcbb00);
}
.hero-desc {
  max-width: 640px;
  margin-top: 26px;
  color: #52525c;
  font-size: 1.04rem;
  line-height: 1.8;
  text-wrap: pretty;
}
.hero-actions { display: flex; flex-wrap: wrap; gap: 18px; margin-top: 32px; }
.fusions-btn, .mini-btn, .modal-btn {
  position: relative;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  border: 0;
  border-radius: 999px;
  cursor: pointer;
  font-weight: 900;
  letter-spacing: 0;
  transition: transform .18s ease, box-shadow .18s ease, background .18s ease;
}
.fusions-btn {
  min-height: 58px;
  padding: 0 28px;
  color: #fff;
  isolation: isolate;
  background: radial-gradient(circle at 26% 0%, rgba(255,255,255,.14), transparent 34%), #171717;
  box-shadow: 0 1px 0 rgba(255,255,255,.12) inset, 0 20px 44px rgba(9,9,11,.18);
}
.fusions-btn::after, .mini-btn.dark::after, .modal-btn.danger::after {
  content: "";
  position: absolute;
  z-index: -1;
  left: 16px;
  right: 16px;
  bottom: -8px;
  height: 14px;
  border-radius: 999px;
  background: linear-gradient(90deg, #ec6cff, #ff6568, #fcbb00, #00d294, #54a2ff, #8d54ff);
  filter: blur(9px);
  opacity: .84;
  transform: scaleX(.92);
  transition: opacity .18s ease, transform .18s ease, filter .18s ease;
}
.fusions-btn:hover, .mini-btn:hover, .modal-btn:hover { transform: translateY(-2px); }
.fusions-btn:hover::after, .mini-btn.dark:hover::after, .modal-btn.danger:hover::after { opacity: 1; transform: scaleX(1); filter: blur(11px); }
.fusions-btn.ghost { color: #09090b; background: #fff; border: 1px solid #e4e4e7; }
.fusions-btn.ghost::after { opacity: .46; }
.fusions-btn.compact { min-height: 46px; padding: 0 18px; }
.nav-action { min-height: 44px; padding: 0 18px; }
.hero-proof { display: flex; flex-wrap: wrap; gap: 12px; margin-top: 28px; }
.hero-proof span {
  display: inline-flex;
  align-items: center;
  gap: 7px;
  padding: 8px 11px;
  border: 1px solid #e4e4e7;
  border-radius: 999px;
  color: #3f3f46;
  background: rgba(255,255,255,.74);
  font-size: .82rem;
  font-weight: 800;
}
.hero-proof svg { color: #00bb7f; }

.hero-visual { position: relative; min-height: 450px; display: grid; place-items: center; }
.mock-window {
  width: min(100%, 600px);
  border: 1px solid rgba(228,228,231,.9);
  border-radius: 28px;
  overflow: hidden;
  background: rgba(255,255,255,.82);
  box-shadow: 0 36px 100px rgba(9,9,11,.12);
  backdrop-filter: blur(18px);
  transform: rotate(-1deg);
}
.mock-bar {
  height: 48px;
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 0 16px;
  border-bottom: 1px solid #f4f4f5;
  background: #fff;
}
.mock-bar span { width: 10px; height: 10px; border-radius: 999px; background: #ff6568; }
.mock-bar span:nth-child(2) { background: #fcbb00; }
.mock-bar span:nth-child(3) { background: #00d294; }
.mock-bar p { margin-left: 8px; color: #71717b; font-size: .8rem; font-weight: 800; }
.mock-content { display: grid; grid-template-columns: .86fr 1.14fr; gap: 14px; padding: 16px; }
.mock-panel, .mock-row {
  border: 1px solid #e4e4e7;
  border-radius: 22px;
  background: #fff;
}
.mock-panel { padding: 18px; }
.mock-title { display: flex; align-items: center; gap: 8px; color: #4f39f6; font-weight: 900; }
.mock-panel strong { display: block; margin-top: 26px; font-size: 2.4rem; line-height: 1; }
.mock-panel small { display: block; margin-top: 6px; color: #71717b; font-weight: 800; }
.mock-wave { height: 92px; display: flex; align-items: end; gap: 6px; margin-top: 20px; }
.mock-wave i {
  width: 9px;
  border-radius: 999px;
  background: linear-gradient(180deg, #8d54ff, #625fff);
  opacity: .92;
}
.mock-list { display: grid; gap: 10px; }
.mock-row { display: grid; grid-template-columns: 10px 1fr auto; gap: 10px; align-items: center; padding: 12px; }
.mock-row > span { width: 10px; height: 44px; border-radius: 999px; background: #00bb7f; }
.mock-row > span.mid { background: #fcbb00; }.mock-row > span.low { background: #fb2c36; }.mock-row > span.out { background: #9f9fa9; }
.mock-row strong { display: block; font-size: .86rem; max-width: 160px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
.mock-row small { display: block; color: #71717b; font-size: .75rem; font-weight: 700; }
.mock-row b { color: #4f39f6; font-size: .82rem; }
.floating-card {
  position: absolute;
  display: inline-flex;
  align-items: center;
  gap: 9px;
  padding: 12px 14px;
  border: 1px solid #e4e4e7;
  border-radius: 999px;
  background: rgba(255,255,255,.9);
  box-shadow: 0 18px 44px rgba(9,9,11,.12);
  font-weight: 900;
}
.float-a { top: 28px; right: 0; color: #4f39f6; }
.float-b { left: 0; bottom: 40px; color: #e40014; }

.metric-strip {
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));
  gap: 14px;
  margin-top: 18px;
}
.metric-card {
  min-height: 132px;
  padding: 20px;
  border: 1px solid #e4e4e7;
  border-radius: 28px;
  background: rgba(255,255,255,.88);
  box-shadow: 0 20px 60px rgba(9,9,11,.06);
}
.metric-card svg { color: #4f39f6; width: 24px; height: 24px; }
.metric-card strong { display: block; margin-top: 18px; font-size: 1.75rem; line-height: 1; }
.metric-card span { display: block; margin-top: 8px; color: #71717b; font-size: .86rem; font-weight: 800; }
.metric-card.danger svg, .metric-card.danger strong { color: #e40014; }

.faq-section {
  margin-top: 22px;
  padding: 22px;
  border: 1px solid #e4e4e7;
  border-radius: 28px;
  background: rgba(255,255,255,.88);
  box-shadow: 0 20px 60px rgba(9,9,11,.06);
}

.faq-head { margin-bottom: 16px; }

.faq-grid {
  display: grid;
  grid-template-columns: minmax(0, 1.15fr) minmax(300px, .85fr);
  gap: 14px;
}

.faq-chat {
  display: grid;
  gap: 12px;
  padding: 18px;
  border: 1px solid #e4e4e7;
  border-radius: 24px;
  background: linear-gradient(180deg, #ffffff, #fafafa);
}

.chat-row {
  display: flex;
  align-items: flex-start;
  gap: 12px;
}

.chat-row.user {
  justify-content: flex-end;
}

.chat-avatar {
  width: 42px;
  height: 42px;
  flex: 0 0 42px;
  display: grid;
  place-items: center;
  border-radius: 14px;
  color: #fff;
  background: #171717;
}

.chat-bubble {
  max-width: min(100%, 520px);
  padding: 14px 16px;
  border: 1px solid #e4e4e7;
  border-radius: 20px;
  background: #fff;
  box-shadow: 0 12px 28px rgba(9,9,11,.05);
}

.chat-row.user .chat-bubble {
  background: #171717;
  color: #fff;
  border-color: #171717;
}

.chat-bubble strong {
  display: block;
  font-size: .88rem;
  line-height: 1.35;
}

.chat-bubble p {
  margin-top: 6px;
  font-size: .92rem;
  line-height: 1.7;
}

.chat-bubble.answer strong {
  color: #4f39f6;
}

.chat-row.user .chat-bubble strong,
.chat-row.user .chat-bubble p {
  color: #fff;
}

.chat-tips {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  margin-top: 4px;
}

.chat-tips span {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 9px 12px;
  border: 1px solid #e4e4e7;
  border-radius: 999px;
  color: #3f3f46;
  background: #fff;
  font-size: .82rem;
  font-weight: 800;
}

.chat-tips svg { width: 16px; height: 16px; color: #00bb7f; }

.faq-list {
  display: grid;
  gap: 10px;
}

.faq-item {
  width: 100%;
  display: grid;
  grid-template-columns: 36px 1fr;
  gap: 12px;
  align-items: center;
  padding: 14px;
  border: 1px solid #e4e4e7;
  border-radius: 20px;
  background: #fafafa;
  text-align: left;
  cursor: pointer;
  transition: border-color .18s ease, background .18s ease, transform .18s ease;
}

.faq-item:hover {
  border-color: #c7d2ff;
  transform: translateY(-1px);
  background: #fff;
}

.faq-item.active {
  border-color: #615fff;
  background: #eef2ff;
}

.faq-item svg {
  width: 18px;
  height: 18px;
  color: #4f39f6;
}

.faq-item strong {
  display: block;
  color: #09090b;
  font-size: .92rem;
  line-height: 1.35;
}

.faq-item small {
  display: block;
  margin-top: 4px;
  color: #71717b;
  font-size: .8rem;
  line-height: 1.45;
}

.products-section { margin-top: 72px; }
.section-head { display: flex; align-items: end; justify-content: space-between; gap: 18px; margin-bottom: 18px; }
.section-head h2 { margin-top: 4px; font-size: clamp(2rem, 4vw, 3.25rem); line-height: 1; font-weight: 900; letter-spacing: 0; }
.alert-low {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 16px;
  padding: 14px 16px;
  border: 1px solid #ffcaca;
  border-radius: 20px;
  color: #bf000f;
  background: #fef2f2;
  font-weight: 800;
}
.tool-surface {
  display: grid;
  grid-template-columns: minmax(260px, 1fr) 230px auto;
  gap: 12px;
  align-items: center;
  padding: 14px;
  border: 1px solid #e4e4e7;
  border-radius: 28px;
  background: rgba(255,255,255,.82);
  box-shadow: 0 20px 60px rgba(9,9,11,.06);
}
.control {
  min-height: 48px;
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 0 15px;
  border: 1px solid #e4e4e7;
  border-radius: 999px;
  background: #fafafa;
  color: #71717b;
}
.control:focus-within { border-color: #615fff; box-shadow: 0 0 0 4px rgba(97,95,255,.12); background: #fff; }
.control input, .control select { width: 100%; border: 0; outline: 0; color: #09090b; background: transparent; }
.count-chip {
  justify-self: end;
  padding: 12px 14px;
  border-radius: 999px;
  color: #4f39f6;
  background: #eef2ff;
  font-size: .84rem;
  font-weight: 900;
  white-space: nowrap;
}
.state-box {
  min-height: 260px;
  display: grid;
  place-items: center;
  align-content: center;
  gap: 10px;
  color: #71717b;
  text-align: center;
}
.state-box svg { width: 42px; height: 42px; color: #625fff; }
.state-box strong { color: #09090b; font-size: 1.1rem; }
.spin { animation: spin 1s linear infinite; }
@keyframes spin { to { transform: rotate(360deg); } }

.product-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(270px, 1fr));
  gap: 16px;
  margin-top: 16px;
}
.product-card {
  position: relative;
  min-height: 320px;
  overflow: hidden;
  padding: 18px;
  border: 1px solid #e4e4e7;
  border-radius: 30px;
  background: rgba(255,255,255,.9);
  box-shadow: 0 20px 60px rgba(9,9,11,.06);
  transition: transform .2s ease, box-shadow .2s ease, border-color .2s ease;
}
.product-card:hover { transform: translateY(-5px); border-color: #c7d2ff; box-shadow: 0 30px 80px rgba(9,9,11,.1); }
.product-card.card-low { border-color: #ffcaca; }
.product-card.card-out { opacity: .72; }
.card-orb {
  position: absolute;
  width: 160px;
  height: 160px;
  right: -70px;
  top: -70px;
  border-radius: 999px;
  background: radial-gradient(circle, rgba(141,84,255,.16), transparent 70%);
}
.product-top { display: flex; align-items: center; justify-content: space-between; gap: 10px; position: relative; z-index: 1; }
.cat-badge, .stock-pill {
  display: inline-flex;
  align-items: center;
  min-height: 28px;
  padding: 0 10px;
  border-radius: 999px;
  font-size: .72rem;
  font-weight: 900;
}
.c-purple { color: #4f39f6; background: #eef2ff; }.c-pink { color: #c4005c; background: #fce7f3; }
.c-violet { color: #7008e7; background: #f5f3ff; }.c-orange { color: #c53c00; background: #fff7ed; }
.c-green { color: #007956; background: #ecfdf5; }.c-blue { color: #155dfc; background: #eff6ff; }
.c-slate { color: #52525c; background: #f4f4f5; }
.stock-pill.high { color: #007956; background: #ecfdf5; }.stock-pill.mid { color: #b75000; background: #fffbeb; }
.stock-pill.low { color: #bf000f; background: #fef2f2; }.stock-pill.out { color: #71717b; background: #f4f4f5; }
.product-card h3 {
  position: relative;
  z-index: 1;
  min-height: 54px;
  margin-top: 18px;
  color: #09090b;
  font-size: 1.08rem;
  line-height: 1.35;
  font-weight: 900;
}
.product-card p {
  position: relative;
  z-index: 1;
  min-height: 48px;
  margin-top: 10px;
  color: #71717b;
  font-size: .86rem;
  line-height: 1.55;
}
.price-row {
  display: flex;
  align-items: end;
  justify-content: space-between;
  gap: 12px;
  margin-top: 20px;
}
.price-row strong { color: #4f39f6; font-size: 1.25rem; font-weight: 900; }
.price-row span { color: #52525c; font-size: .86rem; font-weight: 900; }
.stock-track { height: 8px; overflow: hidden; margin-top: 14px; border-radius: 999px; background: #f4f4f5; }
.stock-track i { display: block; min-width: 4px; height: 100%; border-radius: 999px; background: #00bb7f; }
.stock-track i.mid { background: #f99c00; }.stock-track i.low { background: #fb2c36; }.stock-track i.out { width: 2% !important; background: #9f9fa9; }
.card-actions { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-top: 18px; }
.mini-btn { min-height: 42px; padding: 0 12px; font-size: .84rem; }
.mini-btn.dark { color: #fff; isolation: isolate; background: #171717; }
.mini-btn.dark::after { bottom: -6px; height: 10px; opacity: .62; }
.mini-btn.light { color: #bf000f; background: #fef2f2; border: 1px solid #ffe2e2; }

.overlay {
  position: fixed;
  inset: 0;
  z-index: 50;
  display: grid;
  place-items: center;
  padding: 18px;
  background: rgba(9, 9, 11, .52);
  backdrop-filter: blur(10px);
}
.modal {
  width: min(520px, 100%);
  max-height: 92vh;
  overflow-y: auto;
  padding: 24px;
  border: 1px solid rgba(228,228,231,.9);
  border-radius: 32px;
  background: #fff;
  box-shadow: 0 40px 110px rgba(9,9,11,.28);
}
.modal-head { display: flex; align-items: center; gap: 14px; margin-bottom: 20px; }
.modal-head > span, .confirm-icon {
  width: 48px;
  height: 48px;
  display: grid;
  place-items: center;
  border-radius: 18px;
  color: #fff;
  background: #171717;
}
.modal h2 { font-size: 1.5rem; font-weight: 900; letter-spacing: 0; }
.form-field { display: grid; gap: 7px; margin-bottom: 14px; }
.form-field span { color: #3f3f46; font-size: .85rem; font-weight: 900; }
.form-field input, .form-field textarea {
  width: 100%;
  min-height: 46px;
  padding: 10px 13px;
  border: 1px solid #e4e4e7;
  border-radius: 18px;
  outline: 0;
  resize: vertical;
  background: #fafafa;
}
.form-field input:focus, .form-field textarea:focus { border-color: #615fff; background: #fff; box-shadow: 0 0 0 4px rgba(97,95,255,.12); }
.form-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
.modal-actions { display: flex; justify-content: flex-end; gap: 10px; margin-top: 20px; }
.modal-actions.centered { justify-content: center; }
.modal-btn { min-height: 46px; padding: 0 16px; }
.modal-btn.cancel { color: #3f3f46; background: #f4f4f5; }
.modal-btn.danger { color: #fff; isolation: isolate; background: #171717; }
.modal-btn.danger::after { bottom: -6px; height: 10px; background: linear-gradient(90deg, #fb64b6, #ff6568, #fcbb00); }
.confirm { max-width: 420px; text-align: center; }
.confirm-icon { margin: 0 auto 16px; color: #fff; }
.confirm p { margin-top: 10px; color: #71717b; line-height: 1.6; }

@media (max-width: 980px) {
  .hero { grid-template-columns: 1fr; min-height: auto; }
  .hero h1 {
    max-width: 100%;
    font-size: clamp(2.7rem, 9vw, 4.7rem);
  }
  .headline-main { white-space: normal; }
  .hero-visual { min-height: 420px; }
  .metric-strip { grid-template-columns: repeat(2, minmax(0, 1fr)); }
  .faq-grid { grid-template-columns: 1fr; }
  .tool-surface { grid-template-columns: 1fr; }
  .count-chip { justify-self: start; }
}

@media (max-width: 720px) {
  .site-nav {
    width: calc(100% - 16px);
    height: auto;
    padding: 14px;
    align-items: flex-start;
    flex-direction: column;
    border-radius: 22px;
  }
  .nav-links { width: 100%; justify-content: space-between; overflow-x: auto; }
  .nav-action { width: 100%; }
  main { padding-top: 136px; }
  .hero h1 {
    font-size: clamp(2.45rem, 13vw, 4rem);
    line-height: 1.08;
  }
  .hero-title { gap: 8px; }
  .headline-line { gap: .12em; }
  .hero-actions, .section-head { align-items: stretch; flex-direction: column; }
  .fusions-btn, .fusions-btn.ghost { width: 100%; }
  .mock-content { grid-template-columns: 1fr; }
  .hero-visual { min-height: auto; }
  .floating-card { position: static; margin-top: 12px; }
  .metric-strip, .form-grid { grid-template-columns: 1fr; }
  .faq-section { padding: 16px; }
  .chat-row {
    gap: 10px;
  }
  .chat-avatar { width: 38px; height: 38px; flex-basis: 38px; }
  .chat-bubble { padding: 12px 14px; }
  .faq-item { grid-template-columns: 32px 1fr; }
  .modal-actions { flex-direction: column; }
  .modal-btn, .modal .fusions-btn { width: 100%; }
}
</style>
