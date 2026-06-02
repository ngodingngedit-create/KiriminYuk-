<template>
  <section class="hero-section" id="beranda">
    <!-- Smooth fading background slideshow -->
    <div class="hero-slides-wrapper">
      <div 
        v-for="(img, idx) in backgroundImages" 
        :key="idx" 
        class="hero-slide-bg"
        :style="{ backgroundImage: `url(${img})`, opacity: idx === activeBgIndex ? 1 : 0 }"
      ></div>
    </div>
    
    <!-- Dark Vignette Overlay for full-background legibility -->
    <div class="hero-overlay"></div>

    <div class="container hero-container">
      <div class="hero-grid">
        <!-- Left Column: Content -->
        <div class="hero-content">
          <!-- Translucent Badge -->
          

          <!-- Headline H1 in white for photo background readability -->
          <h1 class="hero-title-premium">
            Membawa Inovasi untuk <br />
            Perjalanan Pengiriman Anda.
          </h1>

          <p class="hero-description-premium">
            Dari pelacakan real-time hingga rute logistik cerdas, kami membantu Anda mengirimkan paket dengan lebih aman, cepat, dan transparan ke seluruh Indonesia.
          </p>

          <!-- CTA Buttons (Blue theme with high-contrast elements) -->
          <div class="hero-ctas">
            <a href="#shipping-form" class="btn btn-primary hero-btn-main">
              Pesan Sekarang
              <ArrowRight :size="18" />
            </a>
            <a href="#cek-tarif" class="btn-outline-glass">
              <span>Cek Tarif</span>
              <Calculator :size="18" class="calc-icon" />
            </a>
          </div>
        </div>

        <!-- Right Column: Visuals & Overlapping Floating Lacak Card -->
        <div class="hero-right-col">
          <!-- Functional slider arrows (slides bg images!) -->
          

          <!-- Glassmorphic Tracking Card (High Contrast & Clear Input) -->
          <div class="glass-tracking-card" id="tracking">
            <!-- Card Header Row -->
            <div class="card-header-row">
              <div class="green-status-dot animate-pulse-cyan"></div>
              <h3 class="card-title">Lacak Pengiriman</h3>
            </div>
            
            <p class="card-desc">
              Masukkan nomor resi Anda untuk memantau status pengiriman paket secara real-time.
            </p>

            <form @submit.prevent="handleTrack" class="tracking-form">
              <div class="input-row">
                <input 
                  type="text" 
                  v-model="resiNumber" 
                  placeholder="Masukkan nomor resi" 
                  class="glass-input-clear"
                  required
                />
                <button type="submit" class="btn btn-primary tracking-btn-glass" :disabled="isLoading">
                  <span v-if="!isLoading">Lacak</span>
                  <span v-else class="spinner"></span>
                  <ArrowRight v-if="!isLoading" :size="15" />
                </button>
              </div>
            </form>

            <!-- Interactive Tracking Result Summary (Glassmorphic) -->
            <div v-if="trackingResult" class="tracking-result-panel animate-fade-in">
              <div class="result-header">
                <span class="status-badge" :class="trackingResult.statusClass">
                  {{ trackingResult.status }}
                </span>
                <span class="resi-label">{{ trackingResult.resi }}</span>
              </div>
              
              <!-- Timeline -->
              <div class="tracking-timeline">
                <div 
                  v-for="(step, idx) in trackingResult.steps" 
                  :key="idx" 
                  class="timeline-item"
                  :class="{ 'active': idx === 0 }"
                >
                  <div class="timeline-bullet"></div>
                  <div class="timeline-content">
                    <span class="timeline-time">{{ step.time }}</span>
                    <h4 class="timeline-title">{{ step.title }}</h4>
                    <p class="timeline-desc">{{ step.desc }}</p>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import { ref, onMounted, onUnmounted } from 'vue';
import { Clock, Navigation, FileText, Headphones, ArrowRight, MapPin, Calculator } from '@lucide/vue';

export default {
  name: 'HeroSection',
  components: {
    Clock,
    Navigation,
    FileText,
    Headphones,
    ArrowRight,
    MapPin,
    Calculator
  },
  setup() {
    const resiNumber = ref('');
    const isLoading = ref(false);
    const trackingResult = ref(null);
    
    // Rotating background images list
    const backgroundImages = [
      '/images/delivery-van.png',
      '/images/warehouse.png',
      '/images/courier.png'
    ];
    const activeBgIndex = ref(0);
    let slideTimer = null;

    const nextSlide = () => {
      activeBgIndex.value = (activeBgIndex.value + 1) % backgroundImages.length;
    };

    const prevSlide = () => {
      activeBgIndex.value = (activeBgIndex.value - 1 + backgroundImages.length) % backgroundImages.length;
    };

    const handleTrack = () => {
      if (!resiNumber.value.trim()) return;
      isLoading.value = true;
      trackingResult.value = null;

      // Simulate API call delay
      setTimeout(() => {
        const query = resiNumber.value.trim().toUpperCase();
        isLoading.value = false;

        // Custom Demo Resi Logic
        if (query === 'KMY-12345') {
          trackingResult.value = {
            resi: 'KMY-12345',
            status: 'Dalam Pengiriman',
            statusClass: 'status-shipping',
            steps: [
              { time: '02 Jun 2026, 17:30', title: 'Paket Sedang Dikirim', desc: 'Kurir [Rian] sedang membawa paket menuju alamat penerima.' },
              { time: '02 Jun 2026, 09:15', title: 'Tiba di HUB Jakarta Pusat', desc: 'Paket telah disortir dan siap didistribusikan.' },
              { time: '01 Jun 2026, 14:00', title: 'Diberangkatkan dari Surabaya', desc: 'Paket dalam perjalanan darat menuju Jakarta.' },
              { time: '01 Jun 2026, 10:00', title: 'Paket Berhasil Di-pickup', desc: 'Kurir menjemput paket dari pengirim.' }
            ]
          };
        } else {
          // Dynamic mock generation based on any tracking number input
          trackingResult.value = {
            resi: query,
            status: 'Resi Aktif',
            statusClass: 'status-active',
            steps: [
              { time: '02 Jun 2026, 15:45', title: 'Paket Diterima di Gudang Origin', desc: 'Paket telah terdaftar di database logistik KiriminYuk!.' },
              { time: '02 Jun 2026, 12:00', title: 'Manifest Dibuat', desc: 'Detail pengiriman telah disiapkan oleh sistem.' }
            ]
          };
        }
      }, 1000);
    };

    onMounted(() => {
      // Auto cycle slides every 6 seconds
      slideTimer = setInterval(nextSlide, 6000);
    });

    onUnmounted(() => {
      if (slideTimer) clearInterval(slideTimer);
    });

    return {
      resiNumber,
      isLoading,
      trackingResult,
      backgroundImages,
      activeBgIndex,
      nextSlide,
      prevSlide,
      handleTrack
    };
  }
};
</script>

<style scoped>
.hero-section {
  position: relative;
  padding-top: 156px; /* Offset for floating header */
  padding-bottom: 80px;
  min-height: 100vh;
  display: flex;
  align-items: center;
  overflow: hidden;
  background-color: #0b1329;
}

/* Background image slideshow track */
.hero-slides-wrapper {
  position: absolute;
  inset: 0;
  z-index: 0;
}

.hero-slide-bg {
  position: absolute;
  inset: 0;
  background-size: cover;
  background-position: center;
  transition: opacity 1.5s ease-in-out; /* Smooth fade animation */
  z-index: 0;
}

/* Vignette overlay shading background images for readability */
.hero-overlay {
  position: absolute;
  inset: 0;
  background: linear-gradient(
    135deg,
    rgba(11, 19, 41, 0.85) 0%,
    rgba(11, 19, 41, 0.6) 60%,
    rgba(11, 19, 41, 0.75) 100%
  );
  z-index: 1;
}

.hero-container {
  position: relative;
  z-index: 2; /* above background layers and shadow */
  width: 100%;
}

.hero-grid {
  display: grid;
  grid-template-columns: 1.25fr 0.75fr;
  gap: 48px;
  align-items: center;
}

/* Left Column styling */
.hero-content {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  text-align: left;
}

/* Translucent Capsule Badge */
.badge-tag-translucent {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 8px 16px;
  background-color: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.15);
  border-radius: var(--radius-full);
  color: #ffffff;
  font-weight: 600;
  font-size: 13px;
  margin-bottom: 24px;
  font-family: var(--font-heading);
}

.badge-dot {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background-color: #00f3ff; /* bright cyan dot accent */
  box-shadow: 0 0 8px #00f3ff;
}

/* Farmora Style H1 Heading */
.hero-title-premium {
  font-family: var(--font-heading);
  font-size: 58px;
  font-weight: 800;
  line-height: 1.15;
  letter-spacing: -1.5px;
  color: #ffffff;
  margin-bottom: 24px;
}

.hero-description-premium {
  font-size: 16px;
  color: rgba(255, 255, 255, 0.85);
  line-height: 1.75;
  margin-bottom: 36px;
  max-width: 520px;
}

/* CTA Buttons */
.hero-ctas {
  display: flex;
  gap: 16px;
}

.hero-btn-main {
  padding: 13px 26px;
  font-size: 15px;
}

.hero-btn-main:hover svg {
  transform: translateX(4px);
}

.hero-btn-main svg {
  transition: transform var(--transition-fast);
}

/* White glass outline button */
.btn-outline-glass {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  padding: 13px 26px;
  font-family: var(--font-heading);
  font-weight: 600;
  border-radius: var(--radius-md);
  transition: all var(--transition-fast);
  white-space: nowrap;
  border: 1.5px solid rgba(255, 255, 255, 0.35);
  background-color: rgba(255, 255, 255, 0.08);
  color: #ffffff;
}

.btn-outline-glass:hover {
  border-color: #ffffff;
  background-color: rgba(255, 255, 255, 0.15);
  transform: translateY(-2px);
}

.calc-icon {
  color: #ffffff;
}

/* Right Column styling */
.hero-right-col {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  height: 100%;
  justify-content: flex-end;
}




/* Simple Clean Glassmorphic Tracking Card */
.glass-tracking-card {
  background: rgba(255, 255, 255, 0.08);
  backdrop-filter: blur(20px);
  -webkit-backdrop-filter: blur(20px);
  border: 1px solid rgba(255, 255, 255, 0.18);
  border-radius: 16px;
  padding: 28px 24px;
  width: 100%;
  max-width: 420px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.25);
  text-align: left;
  margin-top: 40px;
}

.card-header-row {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 10px;
}

.green-status-dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background-color: #22d3a5;
  flex-shrink: 0;
}

@keyframes pulse-cyan {
  0%, 100% { opacity: 1; }
  50%       { opacity: 0.4; }
}

.animate-pulse-cyan {
  animation: pulse-cyan 2s ease-in-out infinite;
}

.card-title {
  color: #ffffff;
  font-size: 16px;
  font-weight: 700;
  font-family: var(--font-heading);
}

.card-desc {
  font-size: 12.5px;

  color: rgba(255, 255, 255, 0.72);
  line-height: 1.65;
  margin-bottom: 22px;
}

.input-row {
  display: flex;
  gap: 10px;
  width: 100%;
}

/* Solid white input field for high-contrast tracking entry */
.glass-input-clear {
  flex-grow: 1;
  padding: 13px 16px;
  font-size: 14px;
  background-color: rgba(255, 255, 255, 0.97);
  border: 1.5px solid rgba(255, 255, 255, 0.2);
  border-radius: 10px;
  color: var(--text-dark);
  font-weight: 600;
  transition: all 0.25s ease;
  font-family: var(--font-body);
}

.glass-input-clear::placeholder {
  color: #94a3b8;
  font-weight: 400;
}

.glass-input-clear:focus {
  border-color: #00f3ff;
  box-shadow: 0 0 0 3px rgba(0, 243, 255, 0.2);
  outline: none;
}

.tracking-btn-glass {
  padding: 13px 20px;
  border-radius: 10px;
  font-size: 14px;
  font-weight: 700;
  letter-spacing: 0.2px;
  background: linear-gradient(135deg, #0a65ff 0%, #0052cc 100%);
  box-shadow: 0 4px 16px rgba(10, 101, 255, 0.4);
  transition: box-shadow 0.25s ease, transform 0.2s ease;
}

.tracking-btn-glass:hover {
  box-shadow: 0 6px 24px rgba(10, 101, 255, 0.55);
  transform: translateY(-1px);
}

/* Result panel */
.tracking-result-panel {
  margin-top: 16px;
  padding-top: 16px;
  border-top: 1px solid rgba(255, 255, 255, 0.15);
}

.result-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}

.status-badge {
  font-size: 10px;
  font-weight: 700;
  padding: 4px 8px;
  border-radius: var(--radius-full);
  text-transform: uppercase;
}

.status-shipping {
  background-color: rgba(10, 101, 255, 0.2);
  color: #00f3ff;
}

.status-active {
  background-color: rgba(34, 197, 94, 0.2);
  color: #22c55e;
}

.resi-label {
  font-size: 12px;
  font-weight: 600;
  color: rgba(255, 255, 255, 0.65);
}

.tracking-timeline {
  display: flex;
  flex-direction: column;
  gap: 16px;
  max-height: 150px;
  overflow-y: auto;
  padding-right: 4px;
}

.timeline-item {
  display: flex;
  gap: 12px;
  position: relative;
}

.timeline-bullet {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background-color: rgba(255, 255, 255, 0.25);
  margin-top: 6px;
  flex-shrink: 0;
  z-index: 2;
}

.timeline-item.active .timeline-bullet {
  background-color: #00f3ff;
  box-shadow: 0 0 8px #00f3ff;
}

.timeline-item::after {
  content: '';
  position: absolute;
  left: 3px;
  top: 14px;
  bottom: -18px;
  width: 2px;
  background-color: rgba(255, 255, 255, 0.15);
  z-index: 1;
}

.timeline-item:last-child::after {
  display: none;
}

.timeline-content {
  text-align: left;
}

.timeline-time {
  font-size: 10px;
  color: rgba(255, 255, 255, 0.5);
  display: block;
  margin-bottom: 2px;
}

.timeline-title {
  font-size: 13px;
  font-weight: 700;
  color: #ffffff;
}

.timeline-item.active .timeline-title {
  color: #00f3ff;
}

.timeline-desc {
  font-size: 11px;
  color: rgba(255, 255, 255, 0.7);
  line-height: 1.4;
}

.spinner {
  width: 14px;
  height: 14px;
  border: 2px solid rgba(255, 255, 255, 0.3);
  border-radius: 50%;
  border-top-color: white;
  animation: spin 0.8s linear infinite;
  display: inline-block;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

/* Responsiveness overrides */
@media (max-width: 1024px) {
  .hero-section {
    padding-top: 120px;
    padding-bottom: 60px;
    min-height: auto;
  }
  
  .hero-grid {
    grid-template-columns: 1fr;
    gap: 40px;
  }
  
  .hero-content {
    align-items: center;
    text-align: center;
  }
  
  .hero-title-premium {
    font-size: 42px;
  }
  
  .hero-description-premium {
    margin-left: auto;
    margin-right: auto;
  }
  
  .hero-right-col {
    align-items: center;
  }
  
  
  
  .glass-tracking-card {
    max-width: 100%;
    margin-top: 20px;
  }
}

@media (max-width: 640px) {
  .hero-section {
    padding-top: 100px;
  }
  
  .hero-title-premium {
    font-size: 32px;
    letter-spacing: -1px;
  }
  
  .hero-description-premium {
    font-size: 14px;
  }
  
  .hero-ctas {
    flex-direction: column;
    width: 100%;
    gap: 12px;
  }
  
  .hero-btn-main, .btn-outline-glass {
    width: 100%;
    justify-content: center;
  }
  
  .input-row {
    flex-direction: column;
    gap: 12px;
  }
  
  .glass-input-clear {
    width: 100%;
  }
  
  .tracking-btn-glass {
    width: 100%;
  }
}
</style>
