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