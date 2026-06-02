<template>
  <section class="how-it-works-section section-padding" id="cara-kerja">
    <div class="container">
      <!-- Section Header -->
      <div class="section-header-left">
        <div class="badge-tag-how">
          <span>CARA KERJA</span>
        </div>
        <h2 class="section-title">Kirim Paket Jadi Lebih Mudah</h2>
        <p class="section-description">
          4 langkah sederhana untuk pengalaman pengiriman yang cepat, aman, dan terpercaya.
        </p>
      </div>

      <!-- Step-by-Step Process Flow -->
      <div class="steps-flow-container">
        <div class="steps-grid">
          <!-- Step 1 -->
          <div class="step-card">
            <div class="step-badge-wrapper">
              <div class="step-number-circle">1</div>
              <div class="step-icon-outer">
                <FileText :size="32" class="step-icon-glyph" />
              </div>
            </div>
            <h3 class="step-card-title">Buat Pesanan</h3>
            <p class="step-card-desc">
              Isi detail pengiriman dengan mudah melalui website atau aplikasi kami.
            </p>
          </div>

          <!-- Connector 1-2 -->
          <div class="step-connector">
            <div class="connector-line"></div>
            <div class="connector-arrow-circle">
              <ChevronRight :size="14" class="connector-chevron" />
            </div>
            <div class="connector-line"></div>
          </div>

          <!-- Step 2 -->
          <div class="step-card">
            <div class="step-badge-wrapper">
              <div class="step-number-circle">2</div>
              <div class="step-icon-outer">
                <PackageCheck :size="32" class="step-icon-glyph" />
              </div>
            </div>
            <h3 class="step-card-title">Kurir Menjemput</h3>
            <p class="step-card-desc">
              Kurir kami akan menjemput paket langsung di lokasi Anda.
            </p>
          </div>

          <!-- Connector 2-3 -->
          <div class="step-connector">
            <div class="connector-line"></div>
            <div class="connector-arrow-circle">
              <ChevronRight :size="14" class="connector-chevron" />
            </div>
            <div class="connector-line"></div>
          </div>

          <!-- Step 3 -->
          <div class="step-card">
            <div class="step-badge-wrapper">
              <div class="step-number-circle">3</div>
              <div class="step-icon-outer">
                <Truck :size="32" class="step-icon-glyph" />
              </div>
            </div>
            <h3 class="step-card-title">Paket Dikirim</h3>
            <p class="step-card-desc">
              Paket dikirim melalui rute tercepat dengan sistem logistik terpercaya.
            </p>
          </div>

          <!-- Connector 3-4 -->
          <div class="step-connector">
            <div class="connector-line"></div>
            <div class="connector-arrow-circle">
              <ChevronRight :size="14" class="connector-chevron" />
            </div>
            <div class="connector-line"></div>
          </div>

          <!-- Step 4 -->
          <div class="step-card">
            <div class="step-badge-wrapper">
              <div class="step-number-circle">4</div>
              <div class="step-icon-outer">
                <PackageCheck :size="32" class="step-icon-glyph" />
              </div>
            </div>
            <h3 class="step-card-title">Sampai Tujuan</h3>
            <p class="step-card-desc">
              Paket diterima dengan aman oleh penerima tepat waktu.
            </p>
          </div>
        </div>
      </div>

      <!-- Value Proposition / Feature Card -->
      <div class="features-proposition-card">
        <div class="features-proposition-grid">
          <!-- Item 1: Aman & Terpercaya -->
          <div class="feature-prop-item">
            <div class="feature-prop-icon-bg">
              <ShieldCheck :size="24" class="feature-prop-icon" />
            </div>
            <div class="feature-prop-text">
              <h4 class="feature-prop-title">Aman & Terpercaya</h4>
              <p class="feature-prop-subtext">Keamanan paket Anda adalah prioritas kami.</p>
            </div>
          </div>

          <!-- Divider -->
          <div class="feature-prop-divider"></div>

          <!-- Item 2: Tepat Waktu -->
          <div class="feature-prop-item">
            <div class="feature-prop-icon-bg">
              <Clock :size="24" class="feature-prop-icon" />
            </div>
            <div class="feature-prop-text">
              <h4 class="feature-prop-title">Tepat Waktu</h4>
              <p class="feature-prop-subtext">Kami berkomitmen mengirim tepat waktu.</p>
            </div>
          </div>

          <!-- Divider -->
          <div class="feature-prop-divider"></div>

          <!-- Item 3: Jangkauan Luas -->
          <div class="feature-prop-item">
            <div class="feature-prop-icon-bg">
              <MapPin :size="24" class="feature-prop-icon" />
            </div>
            <div class="feature-prop-text">
              <h4 class="feature-prop-title">Jangkauan Luas</h4>
              <p class="feature-prop-subtext">Layanan tersedia di lebih dari 150 kota di Indonesia.</p>
            </div>
          </div>

          <!-- Divider -->
          <div class="feature-prop-divider"></div>

          <!-- Item 4: Support 24/7 -->
          <div class="feature-prop-item">
            <div class="feature-prop-icon-bg">
              <Headphones :size="24" class="feature-prop-icon" />
            </div>
            <div class="feature-prop-text">
              <h4 class="feature-prop-title">Support 24/7</h4>
              <p class="feature-prop-subtext">Tim kami siap membantu kapan pun Anda butuh.</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import { FileText, PackageCheck, Truck, ShieldCheck, Clock, MapPin, Headphones, ChevronRight } from '@lucide/vue';

export default {
  name: 'HowItWorksSection',
  components: {
    FileText,
    PackageCheck,
    Truck,
    ShieldCheck,
    Clock,
    MapPin,
    Headphones,
    ChevronRight
  }
};
</script>

<style scoped>
.how-it-works-section {
  background-color: var(--bg-light);
  border-top: 1px solid var(--border-color);
  position: relative;
}

.section-header-left {
  text-align: left;
  margin-bottom: 56px;
}

.badge-tag-how {
  display: inline-flex;
  align-items: center;
  padding: 6px 14px;
  background-color: var(--primary-light);
  color: var(--primary);
  font-weight: 700;
  font-size: 12px;
  border-radius: var(--radius-full);
  margin-bottom: 16px;
  font-family: var(--font-heading);
}

.section-title {
  font-size: 38px;
  font-weight: 800;
  line-height: 1.2;
  color: var(--text-dark);
  margin-bottom: 12px;
  letter-spacing: -1px;
}

.section-description {
  font-size: 15px;
  color: var(--text-muted);
  max-width: 580px;
  margin: 0;
}

.steps-flow-container {
  max-width: 1100px;
  margin: 0 auto;
}

.steps-grid {
  display: grid;
  grid-template-columns: 1fr auto 1fr auto 1fr auto 1fr;
  align-items: center;
  gap: 12px;
}

.step-card {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  transition: transform var(--transition-normal);
}

.step-card:hover {
  transform: translateY(-4px);
}

.step-badge-wrapper {
  position: relative;
  margin-bottom: 20px;
}

.step-number-circle {
  width: 26px;
  height: 26px;
  border-radius: var(--radius-full);
  background-color: var(--primary);
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 700;
  font-size: 12px;
  position: absolute;
  top: -8px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 5;
  box-shadow: 0 4px 8px rgba(10, 101, 255, 0.25);
}

.step-icon-outer {
  width: 90px;
  height: 90px;
  border-radius: var(--radius-full);
  background-color: #f0f6ff;
  display: flex;
  align-items: center;
  justify-content: center;
  color: var(--primary);
  border: 1.5px solid rgba(10, 101, 255, 0.06);
  transition: all var(--transition-normal);
}

.step-card:hover .step-icon-outer {
  background-color: #e0edff;
  border-color: rgba(10, 101, 255, 0.2);
  box-shadow: 0 8px 24px rgba(10, 101, 255, 0.1);
}

.step-icon-glyph {
  stroke-width: 1.5;
}

.step-card-title {
  font-size: 16px;
  font-weight: 800;
  color: var(--text-dark);
  margin-bottom: 8px;
  font-family: var(--font-heading);
}

.step-card-desc {
  font-size: 13px;
  color: var(--text-muted);
  line-height: 1.5;
  max-width: 220px;
  margin: 0 auto;
}

/* Step Connectors */
.step-connector {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 70px;
  flex-shrink: 0;
}

.connector-line {
  flex-grow: 1;
  border-top: 2.2px dashed rgba(10, 101, 255, 0.2);
  height: 0;
}

.connector-arrow-circle {
  width: 26px;
  height: 26px;
  border-radius: var(--radius-full);
  border: 1.5px solid var(--border-color);
  background-color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  color: var(--primary);
  flex-shrink: 0;
  box-shadow: var(--shadow-sm);
  margin: 0 2px;
}

.connector-chevron {
  stroke-width: 3.5;
}

/* Features Highlight bottom card styling */
.features-proposition-card {
  margin-top: 64px;
  background-color: white;
  border: 1px solid var(--border-color);
  border-radius: var(--radius-lg);
  box-shadow: var(--shadow-sm);
  padding: 32px;
}

.features-proposition-grid {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 100%;
}

.feature-prop-item {
  display: flex;
  align-items: center;
  gap: 16px;
  flex: 1;
  padding: 8px 12px;
  text-align: left;
  transition: transform var(--transition-normal);
}

.feature-prop-item:hover {
  transform: translateY(-4px);
}

.feature-prop-icon-bg {
  width: 48px;
  height: 48px;
  border-radius: var(--radius-full);
  background-color: #f0f6ff;
  display: flex;
  align-items: center;
  justify-content: center;
  color: var(--primary);
  flex-shrink: 0;
  border: 1.5px solid rgba(10, 101, 255, 0.05);
}

.feature-prop-icon {
  stroke-width: 2.2;
}

.feature-prop-text {
  display: flex;
  flex-direction: column;
  line-height: 1.35;
}

.feature-prop-title {
  font-size: 14px;
  font-weight: 800;
  color: var(--text-dark);
  margin: 0 0 4px 0;
}

.feature-prop-subtext {
  font-size: 12px;
  color: var(--text-muted);
  margin: 0;
}

.feature-prop-divider {
  width: 1px;
  height: 40px;
  background-color: var(--border-color);
  flex-shrink: 0;
}

/* Responsive configurations */
@media (max-width: 1024px) {
  .steps-grid {
    gap: 8px;
  }
  
  .step-connector {
    width: 40px;
  }
  
  .feature-prop-item {
    gap: 12px;
    padding: 4px;
  }
  
  .feature-prop-title {
    font-size: 13px;
  }
  
  .feature-prop-subtext {
    font-size: 11px;
  }
}

@media (max-width: 768px) {
  .steps-grid {
    grid-template-columns: 1fr;
    gap: 40px;
  }
  
  .step-connector {
    display: none;
  }
  
  .features-proposition-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 24px;
  }
  
  .feature-prop-divider {
    display: none;
  }
  
  .features-proposition-card {
    padding: 24px;
  }
}

@media (max-width: 520px) {
  .features-proposition-grid {
    grid-template-columns: 1fr;
    gap: 20px;
  }
  
  .section-title {
    font-size: 28px;
  }
  
  .section-description {
    font-size: 14px;
  }
}
</style>
