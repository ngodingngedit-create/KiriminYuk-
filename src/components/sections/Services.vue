<template>
  <section class="services-section section-padding" id="layanan">
    <div class="container">
      <!-- Section Header -->
      <div class="services-header-row">
        <div class="services-title-block">
          <span class="badge-tag">LAYANAN KAMI</span>
          <h2 class="services-h2">Layanan Pengiriman Untuk<br class="desktop-only" /> Segala Kebutuhan Anda</h2>
        </div>
        <a href="#all-services" class="see-all-link">
          Lihat Semua Layanan
          <ArrowRight :size="16" />
        </a>
      </div>

      <!-- Services Grid (Desktop: 6 cols, Tablet: 3/2 cols, Mobile: Scrollable Carousel) -->
      <div class="services-scroll-container">
        <div class="services-grid">
          <div 
            v-for="(service, idx) in services" 
            :key="idx" 
            class="service-card"
          >
            <div class="card-icon-wrapper">
              <component :is="service.icon" class="service-card-icon" :size="32" />
            </div>
            <h3 class="service-card-title">{{ service.title }}</h3>
            <p class="service-card-desc">{{ service.description }}</p>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import { ArrowRight, Zap, Clock, Calendar, Package, FileText, ShoppingBag } from '@lucide/vue';

export default {
  name: 'ServicesSection',
  components: {
    ArrowRight
  },
  setup() {
    const services = [
      {
        title: 'Instant Delivery',
        description: 'Pengiriman kilat dalam hitungan jam sampai langsung ke tujuan.',
        icon: Zap
      },
      {
        title: 'Same Day Delivery',
        description: 'Paket sampai di hari yang sama sebelum matahari terbenam.',
        icon: Clock
      },
      {
        title: 'Next Day Delivery',
        description: 'Pengiriman reguler ekonomis yang aman sampai esok hari.',
        icon: Calendar
      },
      {
        title: 'Cargo & Logistics',
        description: 'Solusi tepat untuk pengiriman barang kargo dalam jumlah besar.',
        icon: Package
      },
      {
        title: 'Document Delivery',
        description: 'Layanan kurir khusus pengiriman dokumen penting & rahasia.',
        icon: FileText
      },
      {
        title: 'E-Commerce Delivery',
        description: 'Solusi logistik integratif khusus toko online & pelaku UMKM.',
        icon: ShoppingBag
      }
    ];

    return {
      services
    };
  }
};
</script>

<style scoped>
.services-section {
  background-color: var(--bg-card);
}

.services-header-row {
  display: flex;
  justify-content: space-between;
  align-items: flex-end;
  margin-bottom: 48px;
}

.services-title-block {
  text-align: left;
}

.services-h2 {
  font-size: 36px;
  color: var(--text-dark);
  margin-top: 8px;
}

.see-all-link {
  font-family: var(--font-heading);
  font-weight: 700;
  font-size: 15px;
  color: var(--primary);
  display: inline-flex;
  align-items: center;
  gap: 6px;
  transition: all var(--transition-fast);
  padding-bottom: 4px;
}

.see-all-link:hover {
  color: var(--primary-hover);
  transform: translateX(4px);
}

/* Services Grid */
.services-grid {
  display: grid;
  grid-template-columns: repeat(6, 1fr);
  gap: 20px;
}

.service-card {
  background-color: white;
  border: 1px solid var(--border-color);
  border-radius: var(--radius-lg);
  padding: 32px 24px;
  text-align: center;
  box-shadow: var(--shadow-sm);
  transition: all var(--transition-normal);
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 16px;
  min-height: 250px;
}

.service-card:hover {
  transform: translateY(-8px);
  box-shadow: var(--shadow-lg);
  border-color: var(--primary);
}

.card-icon-wrapper {
  width: 64px;
  height: 64px;
  border-radius: var(--radius-md);
  background-color: var(--primary-light);
  color: var(--primary);
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all var(--transition-fast);
}

.service-card:hover .card-icon-wrapper {
  background-color: var(--primary);
  color: white;
  transform: scale(1.05);
}

.service-card-icon {
  stroke-width: 1.5;
}

.service-card-title {
  font-size: 16px;
  font-weight: 700;
  color: var(--text-dark);
}

.service-card-desc {
  font-size: 13px;
  color: var(--text-light);
  line-height: 1.6;
}

/* Responsive Rules */
@media (max-width: 1200px) {
  .services-grid {
    grid-template-columns: repeat(3, 1fr);
  }
}

@media (max-width: 768px) {
  .services-grid {
    grid-template-columns: repeat(2, 1fr);
  }
  .services-h2 {
    font-size: 28px;
  }
}

@media (max-width: 640px) {
  .services-header-row {
    flex-direction: column;
    align-items: flex-start;
    gap: 16px;
    margin-bottom: 32px;
  }
  
  .see-all-link {
    align-self: flex-start;
  }

  /* Mobile scrollable horizontal layout */
  .services-scroll-container {
    overflow-x: auto;
    margin-left: -24px;
    margin-right: -24px;
    padding-left: 24px;
    padding-right: 24px;
    scrollbar-width: none; /* Firefox */
  }

  .services-scroll-container::-webkit-scrollbar {
    display: none; /* Chrome/Safari */
  }

  .services-grid {
    display: flex;
    gap: 16px;
    width: max-content;
    padding-bottom: 16px;
  }

  .service-card {
    width: 250px;
    flex-shrink: 0;
    min-height: 230px;
    padding: 24px 20px;
  }
}
</style>
