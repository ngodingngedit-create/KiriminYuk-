<template>
  <section class="cek-tarif-section section-padding" id="cek-tarif">
    <div class="container">
      <!-- Section Header -->
      <div class="section-header-left">
        <div class="badge-tag-calculator">
          <Calculator :size="14" class="badge-icon" />
          <span>CEK TARIF</span>
        </div>
        <h2 class="section-title">
          Hitung Ongkos Kirim Secara <span class="highlight-blue">Instan</span>
        </h2>
        <p class="section-description">
          Masukkan detail pengiriman untuk mengetahui estimasi biaya dan waktu pengiriman dengan mudah.
        </p>
      </div>

      <!-- Calculator Card -->
      <div class="calculator-container">
        <!-- Input Form -->
        <form @submit.prevent="calculateRates" class="calculator-form-row">
          <!-- Origin Selector Card -->
          <div class="form-group-card">
            <label for="origin-select">
              <MapPin :size="16" class="field-label-icon" />
              <span>Kota Asal (Origin)</span>
            </label>
            <div class="select-box-wrapper">
              <select id="origin-select" v-model="originCity" class="field-select" :class="{ 'placeholder-selected': !originCity }">
                <option value="" disabled selected>Pilih Kota Asal</option>
                <option v-for="city in cities" :key="'origin-' + city.value" :value="city.value">
                  {{ city.name }}
                </option>
              </select>
            </div>
          </div>

          <!-- Destination Selector Card -->
          <div class="form-group-card">
            <label for="dest-select">
              <MapPin :size="16" class="field-label-icon" />
              <span>Kota Tujuan (Destination)</span>
            </label>
            <div class="select-box-wrapper">
              <select id="dest-select" v-model="destCity" class="field-select" :class="{ 'placeholder-selected': !destCity }">
                <option value="" disabled selected>Pilih Kota Tujuan</option>
                <option v-for="city in cities" :key="'dest-' + city.value" :value="city.value">
                  {{ city.name }}
                </option>
              </select>
            </div>
          </div>

          <!-- Weight Counter Block -->
          <div class="form-group-card weight-counter-card">
            <label>
              <Package :size="16" class="field-label-icon" />
              <span>Berat Paket (kg)</span>
            </label>
            <div class="counter-input-row">
              <button type="button" @click="decreaseWeight" class="counter-btn" aria-label="Decrease weight">
                <Minus :size="14" />
              </button>
              <input 
                type="number" 
                v-model.number="packageWeight" 
                min="1" 
                max="1000" 
                class="counter-input"
                @change="validateWeight"
              />
              <button type="button" @click="increaseWeight" class="counter-btn" aria-label="Increase weight">
                <Plus :size="14" />
              </button>
            </div>
          </div>

          <!-- Action Button -->
          <div class="form-btn-card">
            <button type="submit" class="btn btn-primary btn-calculate-shipping" :disabled="isCalculating">
              <span v-if="!isCalculating">Hitung Ongkos Kirim</span>
              <span v-else class="spinner"></span>
              <ArrowRight v-if="!isCalculating" :size="16" />
            </button>
          </div>
        </form>

        <!-- Shipping Rates Results Area -->
        <div class="rates-results-box" v-if="showResults" :class="{ 'is-collapsed': !isRatesResultsExpanded }">
          <!-- Main Accordion Title & Toggle Button -->
          <div class="results-header-toggle" @click="isRatesResultsExpanded = !isRatesResultsExpanded">
            <h3 class="results-subtitle">Estimasi Ongkos Kirim</h3>
            <button type="button" class="btn-results-collapse" aria-label="Toggle rates results expansion">
              <ChevronDown :size="20" class="results-collapse-arrow" :class="{ 'expanded': isRatesResultsExpanded }" />
            </button>
          </div>

          <!-- Collapsible Container -->
          <div class="results-collapsible-wrapper" :class="{ 'is-expanded': isRatesResultsExpanded }">
            <div class="results-collapsible-content">
              <!-- Accordion Layout -->
              <div class="rates-accordion">
                <div 
                  v-for="rate in shippingRates" 
                  :key="rate.id" 
                  class="accordion-item animate-fade-in"
                  :class="{ 'is-active': activeRateId === rate.id }"
                >
                  <!-- Accordion Header -->
                  <div class="accordion-header" @click="toggleAccordion(rate.id)">
                    <div class="accordion-header-left">
                      <div class="service-icon-bg" :class="rate.iconBgClass">
                        <component :is="rate.icon" :size="18" class="service-icon-glyph" />
                      </div>
                      <div class="service-title-info">
                        <h4 class="service-name">{{ rate.name }}</h4>
                        <span class="service-subtext">{{ rate.subtext }}</span>
                      </div>
                    </div>
                    <div class="accordion-header-right">
                      <div class="accordion-price-eta">
                        <span class="accordion-price">{{ formatRupiah(rate.cost) }}</span>
                        <span class="accordion-eta-badge">{{ rate.eta }}</span>
                      </div>
                      <div class="accordion-arrow-box">
                        <ChevronDown :size="18" class="accordion-arrow" />
                      </div>
                    </div>
                  </div>
                  
                  <!-- Accordion Body -->
                  <div class="accordion-body">
                    <div class="accordion-content">
                      <div class="accordion-details-grid">
                        <div class="detail-col">
                          <span class="detail-label">Estimasi Pengiriman</span>
                          <strong class="detail-value eta-highlight">{{ rate.eta }}</strong>
                        </div>
                        <div class="detail-col">
                          <span class="detail-label">Keunggulan Layanan</span>
                          <p class="detail-value advantage-text">{{ rate.advantage }}</p>
                        </div>
                      </div>
                      <!-- Price row shown only on mobile inside body -->
                      <div class="price-in-body">
                        <span class="detail-label">Ongkos Kirim</span>
                        <span class="body-price-value">{{ formatRupiah(rate.cost) }}</span>
                      </div>
                      <div class="accordion-footer-action">
                        <button @click.stop="selectService(rate)" class="btn btn-primary btn-select-accordion">
                          Pilih Layanan Ini &rarr;
                        </button>
                      </div>
                    </div>
                  </div>
                </div>
              </div>

              <!-- Footer Information Note -->
              <div class="calculator-info-footer">
                <Info :size="16" class="info-footer-icon" />
                <p class="info-footer-text">
                  Biaya sudah termasuk penjemputan dan asuransi dasar. Untuk kebutuhan khusus, silakan hubungi tim kami.
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import { ref } from 'vue';
import { Calculator, MapPin, Package, Info, Zap, Clock, Calendar, Box, Plus, Minus, ArrowRight, ChevronDown } from '@lucide/vue';

export default {
  name: 'CekTarif',
  components: {
    Calculator,
    MapPin,
    Package,
    Info,
    Plus,
    Minus,
    ArrowRight,
    ChevronDown
  },
  setup() {
    const originCity = ref('');
    const destCity = ref('');
    const packageWeight = ref(1);
    const isCalculating = ref(false);
    const showResults = ref(false);
    const activeRateId = ref(null);
    const isRatesResultsExpanded = ref(true);

    // List of cities matching defaults
    const cities = [
      { name: 'Jakarta (DKI Jakarta)', value: 'jakarta' },
      { name: 'Bandung (Jawa Barat)', value: 'bandung' },
      { name: 'Surabaya (Jawa Timur)', value: 'surabaya' },
      { name: 'Medan (Sumatera Utara)', value: 'medan' },
      { name: 'Makassar (Sulawesi Selatan)', value: 'makassar' },
      { name: 'Yogyakarta (DIY Yogyakarta)', value: 'yogyakarta' }
    ];

    // Static default base prices for Jakarta -> Bandung (1 kg)
    const baseRates = [
      {
        id: 'instant',
        name: 'Instant Delivery',
        subtext: 'Sampai di hari yang sama',
        basePrice: 85000,
        eta: 'Hari ini (3-6 jam)',
        advantage: 'Pengiriman super cepat (Prioritas utama)',
        icon: Zap,
        iconBgClass: 'icon-bg-lightblue'
      },
      {
        id: 'sameday',
        name: 'Same Day Delivery',
        subtext: 'Sampai di hari yang sama',
        basePrice: 45000,
        eta: 'Hari ini (6-10 jam)',
        advantage: 'Pengiriman cepat & aman (Dipercepat hari ini)',
        icon: Clock,
        iconBgClass: 'icon-bg-lightgreen'
      },
      {
        id: 'nextday',
        name: 'Next Day Delivery',
        subtext: 'Sampai 1 hari kerja',
        basePrice: 25000,
        eta: 'Besok (1 hari)',
        advantage: 'Lebih hemat (Sampai besok)',
        icon: Calendar,
        iconBgClass: 'icon-bg-lightorange'
      },
      {
        id: 'cargo',
        name: 'Cargo / Reguler',
        subtext: 'Sampai 2-3 hari kerja',
        basePrice: 18000,
        eta: '2-3 hari',
        advantage: 'Ekonomis untuk barang berat/volume besar',
        icon: Box,
        iconBgClass: 'icon-bg-lightpurple'
      }
    ];

    // Reactive rates list loaded after calculation
    const shippingRates = ref([]);

    const toggleAccordion = (id) => {
      if (activeRateId.value === id) {
        activeRateId.value = null;
      } else {
        activeRateId.value = id;
      }
    };

    const increaseWeight = () => {
      packageWeight.value++;
    };

    const decreaseWeight = () => {
      if (packageWeight.value > 1) {
        packageWeight.value--;
      }
    };

    const validateWeight = () => {
      if (!packageWeight.value || packageWeight.value < 1) {
        packageWeight.value = 1;
      }
    };

    // Calculate rates dynamically based on inputs
    const calculateRates = () => {
      if (!originCity.value || !destCity.value) {
        alert('Silakan pilih Kota Asal dan Kota Tujuan terlebih dahulu.');
        return;
      }
      isCalculating.value = true;
      
      setTimeout(() => {
        isCalculating.value = false;
        showResults.value = true;
        isRatesResultsExpanded.value = true;

        // Calculate distance multiplier based on selection
        let distanceMultiplier = 1.0;
        if (originCity.value !== destCity.value) {
          // Mock multiplier logic
          const charDiff = Math.abs(originCity.value.charCodeAt(0) - destCity.value.charCodeAt(0));
          distanceMultiplier = 1.0 + (charDiff * 0.08);
        } else {
          // Same city discount
          distanceMultiplier = 0.55;
        }

        // Apply formula
        shippingRates.value = baseRates.map(rate => {
          let multiplier = distanceMultiplier;
          let weightMultiplier = packageWeight.value;

          // Cargo logic: cheaper per kg for heavier packages, min 10kg conceptually
          if (rate.id === 'cargo') {
            if (packageWeight.value >= 10) {
              weightMultiplier = packageWeight.value * 0.75; // bulk discount
            }
          }

          let finalCost = rate.basePrice * multiplier * weightMultiplier;
          
          // Special case: if origin = Jakarta, destination = Bandung, and weight = 1, force default prices
          if (originCity.value === 'jakarta' && destCity.value === 'bandung' && packageWeight.value === 1) {
            finalCost = rate.basePrice;
          } else {
            // Round to nearest 500
            finalCost = Math.round(finalCost / 500) * 500;
          }

          return {
            ...rate,
            cost: finalCost
          };
        });

        // Auto-expand the first rate card
        if (shippingRates.value.length > 0) {
          activeRateId.value = shippingRates.value[0].id;
        }
      }, 700);
    };

    const formatRupiah = (num) => {
      return 'Rp ' + num.toLocaleString('id-ID');
    };

    const selectService = (service) => {
      alert(`Anda telah memilih layanan ${service.name} dengan ongkos kirim ${formatRupiah(service.cost)}. Hubungi CS kami untuk melanjutkan order.`);
    };

    return {
      originCity,
      destCity,
      packageWeight,
      cities,
      shippingRates,
      isCalculating,
      showResults,
      activeRateId,
      isRatesResultsExpanded,
      toggleAccordion,
      increaseWeight,
      decreaseWeight,
      validateWeight,
      calculateRates,
      formatRupiah,
      selectService
    };
  }
};
</script>

<style scoped>
.cek-tarif-section {
  background-color: var(--bg-light);
}

.section-header-left {
  text-align: left;
  margin-bottom: 40px;
}

.badge-tag-calculator {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  padding: 6px 14px;
  background-color: var(--primary-light);
  color: var(--primary);
  font-weight: 700;
  font-size: 12px;
  border-radius: var(--radius-full);
  margin-bottom: 16px;
  font-family: var(--font-heading);
}

.badge-icon {
  stroke-width: 2.5;
}

.section-title {
  font-size: 38px;
  font-weight: 800;
  line-height: 1.2;
  color: var(--text-dark);
  margin-bottom: 12px;
  letter-spacing: -1px;
}

.section-title .highlight-blue {
  color: var(--primary);
}

.section-description {
  font-size: 15px;
  color: var(--text-muted);
  max-width: 580px;
}

/* Calculator main container card */
.calculator-container {
  background-color: var(--bg-card);
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-lg);
  border: 1px solid var(--border-color);
  overflow: hidden;
  max-width: 1100px;
  margin: 0 auto;
}

/* Horizontal Form Row */
.calculator-form-row {
  display: grid;
  grid-template-columns: 1.1fr 1.1fr 0.9fr 1.1fr;
  gap: 20px;
  padding: 32px;
  background-color: #ffffff;
  border-bottom: 1px solid var(--border-color);
  align-items: flex-end;
}

.form-group-card {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  gap: 8px;
  text-align: left;
  width: 100%;
}

.form-group-card label {
  font-size: 13px;
  font-weight: 700;
  color: var(--text-dark);
  display: flex;
  align-items: center;
  gap: 6px;
}

.field-label-icon {
  color: var(--primary);
  stroke-width: 2.5;
}

.select-box-wrapper {
  position: relative;
  width: 100%;
}

.field-select {
  width: 100%;
  padding: 13px 16px;
  border: 1.5px solid var(--border-color);
  border-radius: var(--radius-md);
  background-color: var(--bg-light);
  font-size: 14px;
  color: var(--text-dark);
  font-weight: 600;
  appearance: none;
  cursor: pointer;
  transition: all var(--transition-fast);
}

.field-select:focus {
  border-color: var(--primary);
  background-color: white;
  box-shadow: 0 0 0 3px var(--primary-light);
}

.select-box-wrapper::after {
  content: '▼';
  font-size: 9px;
  position: absolute;
  right: 16px;
  top: 50%;
  transform: translateY(-50%);
  color: var(--text-light);
  pointer-events: none;
}

/* Weight Counter input card details */
.counter-input-row {
  display: flex;
  align-items: center;
  width: 100%;
  border: 1.5px solid var(--border-color);
  border-radius: var(--radius-md);
  background-color: var(--bg-light);
  padding: 3px;
}

.counter-btn {
  width: 36px;
  height: 36px;
  border-radius: var(--radius-sm);
  display: flex;
  align-items: center;
  justify-content: center;
  color: var(--text-muted);
  background-color: white;
  box-shadow: var(--shadow-sm);
  border: 1px solid var(--border-color);
  transition: all var(--transition-fast);
}

.counter-btn:hover {
  background-color: var(--primary-light);
  color: var(--primary);
  border-color: rgba(10, 101, 255, 0.25);
}

.counter-input {
  flex-grow: 1;
  text-align: center;
  width: 40px;
  font-weight: 700;
  font-size: 15px;
  color: var(--text-dark);
}

/* Chrome, Safari, Edge, Opera: hide spin buttons */
.counter-input::-webkit-outer-spin-button,
.counter-input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

/* Firefox: hide spin buttons */
.counter-input[type=number] {
  -moz-appearance: textfield;
  appearance: textfield;
}

.form-btn-card {
  width: 100%;
}

.btn-calculate-shipping {
  width: 100%;
  padding: 14px 24px;
  font-size: 14px;
  font-weight: 700;
}

.btn-calculate-shipping:hover svg {
  transform: translateX(4px);
}

.btn-calculate-shipping svg {
  transition: transform var(--transition-fast);
}

/* Shipping Rates Results Section */
.rates-results-box {
  padding: 32px 32px 0 32px;
  transition: padding var(--transition-fast);
}

.rates-results-box.is-collapsed {
  padding-bottom: 32px;
}

.results-header-toggle {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-bottom: 20px;
  border-bottom: 1.5px solid var(--border-color);
  margin-bottom: 24px;
  cursor: pointer;
  user-select: none;
  transition: opacity var(--transition-fast);
}

.results-header-toggle:hover {
  opacity: 0.85;
}

.results-subtitle {
  font-size: 18px;
  font-weight: 800;
  color: var(--text-dark);
  text-align: left;
  margin-bottom: 0;
  letter-spacing: -0.3px;
}

.btn-results-collapse {
  background: none;
  border: none;
  cursor: pointer;
  padding: 6px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: var(--primary);
  background-color: var(--primary-light);
  border-radius: var(--radius-full);
  transition: all var(--transition-fast);
}

.btn-results-collapse:hover {
  background-color: rgba(10, 101, 255, 0.15);
}

.results-collapse-arrow {
  transition: transform 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.results-collapse-arrow.expanded {
  transform: rotate(180deg);
}

.results-collapsible-wrapper {
  max-height: 0;
  overflow: hidden;
  transition: max-height 0.4s cubic-bezier(0.4, 0, 0.2, 1), opacity 0.4s ease;
  opacity: 0;
}

.results-collapsible-wrapper.is-expanded {
  max-height: 1500px;
  opacity: 1;
}

.results-collapsible-content {
  display: flex;
  flex-direction: column;
}

/* Accordion Layout styling */
.rates-accordion {
  display: flex;
  flex-direction: column;
  gap: 14px;
  margin-bottom: 28px;
}

.accordion-item {
  background-color: white;
  border: 1px solid var(--border-color);
  border-radius: var(--radius-md);
  overflow: hidden;
  box-shadow: var(--shadow-sm);
  transition: all var(--transition-fast);
}

.accordion-item.is-active {
  border-color: var(--primary);
  box-shadow: 0 8px 24px rgba(10, 101, 255, 0.08);
}

.accordion-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 18px 24px;
  cursor: pointer;
  user-select: none;
  transition: background-color var(--transition-fast);
}

.accordion-header:hover {
  background-color: rgba(10, 101, 255, 0.015);
}

.accordion-header-left {
  display: flex;
  align-items: center;
  gap: 16px;
}

.service-icon-bg {
  width: 40px;
  height: 40px;
  border-radius: var(--radius-md);
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}

.icon-bg-lightblue { background-color: #e0f2fe; color: #0284c7; }
.icon-bg-lightgreen { background-color: #dcfce7; color: #16a34a; }
.icon-bg-lightorange { background-color: #ffedd5; color: #ea580c; }
.icon-bg-lightpurple { background-color: #f3e8ff; color: #7c3aed; }

.service-icon-glyph {
  stroke-width: 2.2;
}

.service-title-info {
  display: flex;
  flex-direction: column;
  text-align: left;
  line-height: 1.35;
}

.service-name {
  font-size: 15px;
  font-weight: 700;
  color: var(--text-dark);
  margin: 0 0 2px 0;
}

.service-subtext {
  font-size: 12px;
  color: var(--text-light);
}

.accordion-header-right {
  display: flex;
  align-items: center;
  gap: 20px;
}

.accordion-price-eta {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  line-height: 1.35;
}

.accordion-price {
  font-size: 16px;
  font-weight: 800;
  color: var(--primary);
}

.accordion-eta-badge {
  font-size: 11px;
  font-weight: 600;
  color: var(--text-muted);
}

.accordion-arrow-box {
  color: var(--text-light);
  display: flex;
  align-items: center;
  justify-content: center;
  transition: transform var(--transition-fast);
}

.accordion-item.is-active .accordion-arrow {
  transform: rotate(180deg);
}

.accordion-body {
  max-height: 0;
  overflow: hidden;
  transition: max-height 0.3s cubic-bezier(0.4, 0, 0.2, 1), border-top var(--transition-fast);
  border-top: 1px solid transparent;
}

.accordion-item.is-active .accordion-body {
  max-height: 250px;
  border-top: 1px solid var(--border-color);
}

.accordion-content {
  padding: 20px 24px;
  background-color: var(--bg-light);
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.accordion-details-grid {
  display: grid;
  grid-template-columns: 1fr 2.5fr;
  gap: 24px;
}

.detail-col {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  text-align: left;
}

.detail-label {
  font-size: 11px;
  font-weight: 700;
  color: var(--text-light);
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 6px;
}

.detail-value {
  font-size: 14px;
  font-weight: 700;
  color: var(--text-dark);
}

.eta-highlight {
  color: var(--primary);
}

.advantage-text {
  font-weight: 500;
  color: var(--text-muted);
  line-height: 1.45;
}

.accordion-footer-action {
  display: flex;
  justify-content: flex-end;
}

.btn-select-accordion {
  padding: 10px 24px;
  font-size: 13px;
  font-weight: 700;
  border-radius: var(--radius-sm);
}

/* Price row inside accordion body — hidden on desktop, shown on mobile */
.price-in-body {
  display: none;
  flex-direction: column;
  gap: 4px;
  padding-top: 12px;
  border-top: 1px solid var(--border-color);
  margin-top: 4px;
}

.body-price-value {
  font-size: 18px;
  font-weight: 800;
  color: var(--primary);
}

/* Select Box Placeholder style override */
.field-select.placeholder-selected {
  color: var(--text-light);
}

/* Banner footnote informational layout */
.calculator-info-footer {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 16px 32px;
  background-color: var(--primary-light);
  margin-left: -32px;
  margin-right: -32px;
  margin-top: 32px;
}

.info-footer-icon {
  color: var(--primary);
  flex-shrink: 0;
  stroke-width: 2.5;
}

.info-footer-text {
  font-size: 13px;
  font-weight: 600;
  color: var(--primary);
  text-align: left;
  line-height: 1.5;
}

.spinner {
  width: 16px;
  height: 16px;
  border: 2px solid rgba(255, 255, 255, 0.3);
  border-radius: 50%;
  border-top-color: white;
  animation: spin 0.8s linear infinite;
  display: inline-block;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

.animate-fade-in {
  animation: fadeIn 0.4s ease-out forwards;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(8px); }
  to { opacity: 1; transform: translateY(0); }
}

/* Responsive viewports stack adjustments */
@media (max-width: 1024px) {
  .calculator-form-row {
    grid-template-columns: 1fr;
    gap: 16px;
    padding: 24px;
  }
  
  .btn-calculate-shipping {
    padding: 14px;
  }

  .rates-results-box {
    padding: 24px 24px 0 24px;
  }

  .calculator-info-footer {
    margin-left: -24px;
    margin-right: -24px;
    padding: 16px 24px;
  }
}

@media (max-width: 768px) {
  .accordion-header {
    padding: 14px 16px;
  }

  .accordion-header-left {
    gap: 10px;
  }

  .service-icon-bg {
    width: 34px;
    height: 34px;
  }

  .service-name {
    font-size: 13px;
    white-space: normal;
    overflow: visible;
    max-width: none;
  }

  .service-subtext {
    font-size: 11px;
  }

  /* Hide price from accordion header on mobile */
  .accordion-price-eta {
    display: none;
  }

  /* Show price inside accordion body on mobile */
  .price-in-body {
    display: flex;
  }

  .accordion-header-right {
    gap: 10px;
  }

  .accordion-content {
    padding: 14px 16px;
  }

  .accordion-details-grid {
    grid-template-columns: 1fr;
    gap: 12px;
  }

  .accordion-footer-action {
    width: 100%;
  }

  .btn-select-accordion {
    width: 100%;
    justify-content: center;
  }
}

@media (max-width: 480px) {
  .accordion-header {
    padding: 12px 14px;
  }

  .accordion-header-left {
    gap: 8px;
    min-width: 0;
  }

  .service-icon-bg {
    width: 30px;
    height: 30px;
    flex-shrink: 0;
  }

  .service-name {
    font-size: 12.5px;
  }

  .service-subtext {
    font-size: 10px;
  }

  .accordion-eta-badge {
    display: none;
  }

  .accordion-arrow-box svg {
    width: 15px;
    height: 15px;
  }

  .body-price-value {
    font-size: 16px;
  }
}

@media (max-width: 640px) {
  .section-title {
    font-size: 28px;
  }
  
  .section-description {
    font-size: 14px;
  }

  /* Fix info footer overflow — use inset card style instead of negative margins */
  .calculator-info-footer {
    margin-left: 0;
    margin-right: 0;
    margin-top: 20px;
    padding: 14px 16px;
    border-radius: var(--radius-md);
    gap: 10px;
  }

  .info-footer-text {
    font-size: 12px;
  }
}
</style>
