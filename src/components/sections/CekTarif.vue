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

        <!-- ===== TAB SWITCHER ===== -->
        <div class="tab-switcher">
          <button
            class="tab-btn"
            :class="{ 'tab-btn--active': activeTab === 'domestik' }"
            @click="switchTab('domestik')"
          >
            <Globe :size="16" class="tab-icon" />
            <span>Domestik</span>
          </button>
          <button
            class="tab-btn"
            :class="{ 'tab-btn--active': activeTab === 'internasional' }"
            @click="switchTab('internasional')"
          >
            <Globe :size="16" class="tab-icon" />
            <span>Internasional</span>
          </button>
        </div>

        <!-- ===== DOMESTIK FORM ===== -->
        <form
          v-if="activeTab === 'domestik'"
          @submit.prevent="calculateRates"
          class="calculator-form-row"
        >
          <!-- Origin Selector -->
          <div class="form-group-card">
            <label for="origin-search-input">
              <MapPin :size="16" class="field-label-icon" />
              <span>Kota Asal (Origin)</span>
            </label>
            <div class="custom-select-container">
              <input
                id="origin-search-input"
                type="text"
                v-model="originSearchQuery"
                placeholder="Kota/Kecamatan/Kelurahan/Kode Pos"
                class="field-select autocomplete-input"
                :class="{ 'placeholder-selected': !originSearchQuery }"
                @focus="isOriginFocused = true"
                @blur="handleOriginInputBlur"
                autocomplete="off"
              />
              <div v-if="isOriginFocused && filteredOriginRegions.length > 0" class="autocomplete-dropdown animate-fade-in">
                <div
                  v-for="region in filteredOriginRegions"
                  :key="'orig-reg-' + region.value"
                  class="autocomplete-item"
                  @mousedown.prevent="selectOriginRegion(region)"
                >
                  <MapPin :size="14" class="dropdown-pin-icon" />
                  <div class="region-text">
                    <span class="region-main">{{ region.subdistrict }}, {{ region.district }}</span>
                    <span class="region-sub">{{ region.city }} - {{ region.zip }}</span>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Destination Selector -->
          <div class="form-group-card">
            <label for="dest-search-input">
              <MapPin :size="16" class="field-label-icon" />
              <span>Kota Tujuan (Destination)</span>
            </label>
            <div class="custom-select-container">
              <input
                id="dest-search-input"
                type="text"
                v-model="destSearchQuery"
                placeholder="Kota/Kecamatan/Kelurahan/Kode Pos"
                class="field-select autocomplete-input"
                :class="{ 'placeholder-selected': !destSearchQuery }"
                @focus="isDestFocused = true"
                @blur="handleDestInputBlur"
                autocomplete="off"
              />
              <div v-if="isDestFocused && filteredDestRegions.length > 0" class="autocomplete-dropdown animate-fade-in">
                <div
                  v-for="region in filteredDestRegions"
                  :key="'dest-reg-' + region.value"
                  class="autocomplete-item"
                  @mousedown.prevent="selectDestRegion(region)"
                >
                  <MapPin :size="14" class="dropdown-pin-icon" />
                  <div class="region-text">
                    <span class="region-main">{{ region.subdistrict }}, {{ region.district }}</span>
                    <span class="region-sub">{{ region.city }} - {{ region.zip }}</span>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Weight Counter (Gram) -->
          <div class="form-group-card weight-counter-card">
            <label>
              <Package :size="16" class="field-label-icon" />
              <span>Berat Paket (gram)</span>
            </label>
            <div class="counter-input-row">
              <button type="button" @click="decreaseWeight" class="counter-btn" aria-label="Decrease weight">
                <Minus :size="14" />
              </button>
              <input
                type="number"
                v-model.number="packageWeight"
                min="100"
                max="30000"
                step="100"
                class="counter-input counter-input--gram"
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

        <!-- ===== INTERNASIONAL FORM ===== -->
        <form
          v-else
          @submit.prevent="calculateRates"
          class="calculator-form-row"
        >
          <!-- Country of Origin -->
          <div class="form-group-card">
            <label>
              <MapPin :size="16" class="field-label-icon" />
              <span>Negara Asal (Origin)</span>
            </label>
            <div class="custom-select-container">
              <button
                type="button"
                class="custom-select-trigger"
                :class="{ 'placeholder-selected': !originCountry }"
                @click.stop="toggleOriginDropdown"
              >
                <div class="trigger-content" v-if="originCountry">
                  <img :src="`/flags/${originCountry.toLowerCase()}.png`" class="flag-img-select" alt="" />
                  <span>{{ getCountryName(originCountry) }}</span>
                </div>
                <span v-else class="placeholder">Pilih Negara Asal</span>
                <ChevronDown :size="16" class="select-chevron" :class="{ 'rotated': isOriginDropdownOpen }" />
              </button>

              <div v-if="isOriginDropdownOpen" class="custom-options-container animate-fade-in" @click.stop>
                <div class="search-box-wrapper">
                  <Search :size="14" class="search-icon" />
                  <input
                    type="text"
                    v-model="searchQueryOrigin"
                    placeholder="Cari negara..."
                    class="dropdown-search-input"
                    ref="originSearchInput"
                  />
                </div>
                <div class="options-list">
                  <div
                    v-for="country in filteredOriginCountries"
                    :key="'orig-opt-' + country.code"
                    class="custom-option"
                    :class="{ 'is-selected': originCountry === country.code }"
                    @click="selectOriginCountry(country.code)"
                  >
                    <img :src="`/flags/${country.code.toLowerCase()}.png`" class="flag-img-option" alt="" />
                    <span>{{ country.name }}</span>
                  </div>
                  <div v-if="filteredOriginCountries.length === 0" class="no-options-found">
                    Negara tidak ditemukan
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Destination Country -->
          <div class="form-group-card">
            <label>
              <MapPin :size="16" class="field-label-icon" />
              <span>Negara Tujuan (Destination)</span>
            </label>
            <div class="custom-select-container">
              <button
                type="button"
                class="custom-select-trigger"
                :class="{ 'placeholder-selected': !destCountry }"
                @click.stop="toggleDestDropdown"
              >
                <div class="trigger-content" v-if="destCountry">
                  <img :src="`/flags/${destCountry.toLowerCase()}.png`" class="flag-img-select" alt="" />
                  <span>{{ getCountryName(destCountry) }}</span>
                </div>
                <span v-else class="placeholder">Pilih Negara Tujuan</span>
                <ChevronDown :size="16" class="select-chevron" :class="{ 'rotated': isDestDropdownOpen }" />
              </button>

              <div v-if="isDestDropdownOpen" class="custom-options-container animate-fade-in" @click.stop>
                <div class="search-box-wrapper">
                  <Search :size="14" class="search-icon" />
                  <input
                    type="text"
                    v-model="searchQueryDest"
                    placeholder="Cari negara..."
                    class="dropdown-search-input"
                    ref="destSearchInput"
                  />
                </div>
                <div class="options-list">
                  <div
                    v-for="country in filteredDestCountries"
                    :key="'dest-opt-' + country.code"
                    class="custom-option"
                    :class="{ 'is-selected': destCountry === country.code }"
                    @click="selectDestCountry(country.code)"
                  >
                    <img :src="`/flags/${country.code.toLowerCase()}.png`" class="flag-img-option" alt="" />
                    <span>{{ country.name }}</span>
                  </div>
                  <div v-if="filteredDestCountries.length === 0" class="no-options-found">
                    Negara tidak ditemukan
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Weight Counter (Gram) -->
          <div class="form-group-card weight-counter-card">
            <label>
              <Package :size="16" class="field-label-icon" />
              <span>Berat Paket (gram)</span>
            </label>
            <div class="counter-input-row">
              <button type="button" @click="decreaseWeightGram" class="counter-btn" aria-label="Decrease weight">
                <Minus :size="14" />
              </button>
              <input
                type="number"
                v-model.number="packageWeightGram"
                min="100"
                max="30000"
                step="100"
                class="counter-input counter-input--gram"
                @change="validateWeightGram"
              />
              <button type="button" @click="increaseWeightGram" class="counter-btn" aria-label="Increase weight">
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

        <!-- ===== Shipping Rates Results Area ===== -->
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
                      <div class="service-logo-wrapper">
                        <img
                          v-if="activeTab === 'domestik'"
                          src="/kurir/domestik/jne.png"
                          class="service-logo-img service-logo-img--jne"
                          alt="JNE Express"
                        />
                        <img
                          v-else
                          src="/kurir/internasional/fedex.png"
                          class="service-logo-img service-logo-img--fedex"
                          alt="FedEx Express"
                        />
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
import { ref, computed, onMounted, onUnmounted, nextTick, watch } from 'vue';
import { Calculator, MapPin, Package, Info, Plus, Minus, ArrowRight, ChevronDown, Globe, Search } from '@lucide/vue';

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
    ChevronDown,
    Globe,
    Search
  },
  setup() {
    // ── Tab state ──────────────────────────────────────────
    const activeTab = ref('domestik');

    // ── Domestik form state ────────────────────────────────
    const originCity = ref('');
    const destCity = ref('');
    const originSearchQuery = ref('');
    const destSearchQuery = ref('');
    const isOriginFocused = ref(false);
    const isDestFocused = ref(false);
    const packageWeight = ref(1000); // weight in grams, default 1kg

    // ── Internasional form state ───────────────────────────
    const originCountry = ref('');
    const destCountry = ref('');
    const packageWeightGram = ref(500);

    // ── Custom Dropdown state ──────────────────────────────
    const isOriginDropdownOpen = ref(false);
    const isDestDropdownOpen = ref(false);
    const searchQueryOrigin = ref('');
    const searchQueryDest = ref('');
    const originSearchInput = ref(null);
    const destSearchInput = ref(null);

    // ── Shared state ───────────────────────────────────────
    const isCalculating = ref(false);
    const showResults = ref(false);
    const activeRateId = ref(null);
    const isRatesResultsExpanded = ref(true);
    const shippingRates = ref([]);

    // ── Indonesian Regions list (domestik autocomplete) ────
    const regions = [
      // Jakarta
      { subdistrict: 'Rawasari', district: 'Cempaka Putih', city: 'Jakarta Pusat', zip: '10550', value: 'rawasari' },
      { subdistrict: 'Selong', district: 'Kebayoran Baru', city: 'Jakarta Selatan', zip: '12110', value: 'selong' },
      { subdistrict: 'Senayan', district: 'Kebayoran Baru', city: 'Jakarta Selatan', zip: '12190', value: 'senayan' },
      { subdistrict: 'Menteng', district: 'Menteng', city: 'Jakarta Pusat', zip: '10310', value: 'menteng' },
      { subdistrict: 'Gambir', district: 'Gambir', city: 'Jakarta Pusat', zip: '10110', value: 'gambir' },
      { subdistrict: 'Kelapa Gading Barat', district: 'Kelapa Gading', city: 'Jakarta Utara', zip: '14240', value: 'kelapa-gading' },
      // Bandung
      { subdistrict: 'Dago', district: 'Coblong', city: 'Bandung', zip: '40135', value: 'dago' },
      { subdistrict: 'Sarijadi', district: 'Sukasari', city: 'Bandung', zip: '40151', value: 'sarijadi' },
      { subdistrict: 'Pasirkaliki', district: 'Cicendo', city: 'Bandung', zip: '40171', value: 'pasirkaliki' },
      // Surabaya
      { subdistrict: 'Gubeng', district: 'Gubeng', city: 'Surabaya', zip: '60281', value: 'gubeng' },
      { subdistrict: 'Keputih', district: 'Sukolilo', city: 'Surabaya', zip: '60111', value: 'keputih' },
      { subdistrict: 'Tegalsari', district: 'Tegalsari', city: 'Surabaya', zip: '60262', value: 'tegalsari' },
      // Yogyakarta
      { subdistrict: 'Klitren', district: 'Gondokusuman', city: 'Yogyakarta', zip: '55222', value: 'klitren' },
      { subdistrict: 'Sosromenduran', district: 'Gedongtengen', city: 'Yogyakarta', zip: '55271', value: 'sosromenduran' },
      // Semarang
      { subdistrict: 'Sekaran', district: 'Gunungpati', city: 'Semarang', zip: '50229', value: 'sekaran' },
      { subdistrict: 'Mugasari', district: 'Semarang Selatan', city: 'Semarang', zip: '50249', value: 'mugasari' },
      // Medan
      { subdistrict: 'Kesawan', district: 'Medan Barat', city: 'Medan', zip: '20111', value: 'kesawan' },
      { subdistrict: 'Babura', district: 'Medan Baru', city: 'Medan', zip: '20152', value: 'babura' },
      // Makassar
      { subdistrict: 'Malimongan Baru', district: 'Bontoala', city: 'Makassar', zip: '90151', value: 'malimongan' },
      { subdistrict: 'Losari', district: 'Ujung Pandang', city: 'Makassar', zip: '90111', value: 'losari' },
      // Denpasar
      { subdistrict: 'Sanur', district: 'Denpasar Selatan', city: 'Denpasar', zip: '80228', value: 'sanur' },
      { subdistrict: 'Renon', district: 'Denpasar Selatan', city: 'Renon', zip: '80226', value: 'renon' },
      // Palembang
      { subdistrict: 'Talang Semut', district: 'Bukit Kecil', city: 'Palembang', zip: '30135', value: 'talang-semut' },
      // Balikpapan
      { subdistrict: 'Klandasan Ulu', district: 'Balikpapan Kota', city: 'Balikpapan', zip: '76112', value: 'klandasan' }
    ];

    // ── Country list (internasional) ───────────────────────
    const countries = [
      { code: 'ID', name: 'Indonesia', flag: '🇮🇩' },
      { code: 'SG', name: 'Singapura', flag: '🇸🇬' },
      { code: 'MY', name: 'Malaysia', flag: '🇲🇾' },
      { code: 'TH', name: 'Thailand', flag: '🇹🇭' },
      { code: 'PH', name: 'Filipina', flag: '🇵🇭' },
      { code: 'VN', name: 'Vietnam', flag: '🇻🇳' },
      { code: 'MM', name: 'Myanmar', flag: '🇲🇲' },
      { code: 'KH', name: 'Kamboja', flag: '🇰🇭' },
      { code: 'JP', name: 'Jepang', flag: '🇯🇵' },
      { code: 'CN', name: 'Cina', flag: '🇨🇳' },
      { code: 'KR', name: 'Korea Selatan', flag: '🇰🇷' },
      { code: 'HK', name: 'Hong Kong', flag: '🇭🇰' },
      { code: 'TW', name: 'Taiwan', flag: '🇹🇼' },
      { code: 'AU', name: 'Australia', flag: '🇦🇺' },
      { code: 'NZ', name: 'Selandia Baru', flag: '🇳🇿' },
      { code: 'IN', name: 'India', flag: '🇮🇳' },
      { code: 'SA', name: 'Arab Saudi', flag: '🇸🇦' },
      { code: 'AE', name: 'Uni Emirat Arab', flag: '🇦🇪' },
      { code: 'US', name: 'Amerika Serikat', flag: '🇺🇸' },
      { code: 'GB', name: 'Inggris', flag: '🇬🇧' },
      { code: 'DE', name: 'Jerman', flag: '🇩🇪' },
      { code: 'FR', name: 'Prancis', flag: '🇫🇷' },
      { code: 'NL', name: 'Belanda', flag: '🇳🇱' },
    ];

    // ── Base rates (domestik) ──────────────────────────────
    const baseRatesDomestik = [
      {
        id: 'instant',
        name: 'Instant Delivery',
        subtext: 'Sampai di hari yang sama',
        basePrice: 85000,
        eta: 'Hari ini (3-6 jam)',
        advantage: 'Pengiriman super cepat (Prioritas utama)'
      },
      {
        id: 'sameday',
        name: 'Same Day Delivery',
        subtext: 'Sampai di hari yang sama',
        basePrice: 45000,
        eta: 'Hari ini (6-10 jam)',
        advantage: 'Pengiriman cepat & aman (Dipercepat hari ini)'
      },
      {
        id: 'nextday',
        name: 'Next Day Delivery',
        subtext: 'Sampai 1 hari kerja',
        basePrice: 25000,
        eta: 'Besok (1 hari)',
        advantage: 'Lebih hemat (Sampai besok)'
      },
      {
        id: 'cargo',
        name: 'Cargo / Reguler',
        subtext: 'Sampai 2-3 hari kerja',
        basePrice: 18000,
        eta: '2-3 hari',
        advantage: 'Ekonomis untuk barang berat/volume besar'
      }
    ];

    // ── Base rates (internasional) ─────────────────────────
    const baseRatesInternasional = [
      {
        id: 'intl-express',
        name: 'International Express',
        subtext: 'Pengiriman internasional prioritas',
        basePrice: 250000,
        eta: '3-5 hari kerja',
        advantage: 'Pengiriman ekspres dengan tracking real-time'
      },
      {
        id: 'intl-standard',
        name: 'International Standard',
        subtext: 'Pengiriman internasional standar',
        basePrice: 150000,
        eta: '7-14 hari kerja',
        advantage: 'Ekonomis dengan jaminan keamanan paket'
      },
      {
        id: 'intl-economy',
        name: 'International Economy',
        subtext: 'Pengiriman ekonomi internasional',
        basePrice: 90000,
        eta: '14-21 hari kerja',
        advantage: 'Pilihan paling hemat untuk pengiriman non-urgent'
      }
    ];

    // ── Helper: get country flag emoji (fallback) ──────────
    const getCountryFlag = (code) => {
      const c = countries.find(c => c.code === code);
      return c ? c.flag : '';
    };

    // ── Helper: format region label ────────────────────────
    const getRegionLabel = (region) => {
      return `${region.subdistrict}, ${region.district}, ${region.city} (${region.zip})`;
    };

    // ── Tab switch ─────────────────────────────────────────
    const switchTab = (tab) => {
      activeTab.value = tab;
      showResults.value = false;
      activeRateId.value = null;
      isOriginDropdownOpen.value = false;
      isDestDropdownOpen.value = false;
      isOriginFocused.value = false;
      isDestFocused.value = false;
    };

    // ── Weight controls (Gram - domestik) ───────────────────
    const increaseWeight = () => {
      packageWeight.value = Math.min(30000, packageWeight.value + 100);
    };
    const decreaseWeight = () => {
      packageWeight.value = Math.max(100, packageWeight.value - 100);
    };
    const validateWeight = () => {
      if (!packageWeight.value || packageWeight.value < 100) packageWeight.value = 100;
      packageWeight.value = Math.round(packageWeight.value / 100) * 100;
    };

    // ── Weight controls (Gram - internasional) ────────────
    const increaseWeightGram = () => {
      packageWeightGram.value = Math.min(30000, packageWeightGram.value + 100);
    };
    const decreaseWeightGram = () => {
      packageWeightGram.value = Math.max(100, packageWeightGram.value - 100);
    };
    const validateWeightGram = () => {
      if (!packageWeightGram.value || packageWeightGram.value < 100) packageWeightGram.value = 100;
      packageWeightGram.value = Math.round(packageWeightGram.value / 100) * 100;
    };

    // ── Custom Dropdown actions ────────────────────────────
    const toggleOriginDropdown = () => {
      isOriginDropdownOpen.value = !isOriginDropdownOpen.value;
      isDestDropdownOpen.value = false;
      if (isOriginDropdownOpen.value) {
        searchQueryOrigin.value = '';
        nextTick(() => {
          if (originSearchInput.value) originSearchInput.value.focus();
        });
      }
    };

    const toggleDestDropdown = () => {
      isDestDropdownOpen.value = !isDestDropdownOpen.value;
      isOriginDropdownOpen.value = false;
      if (isDestDropdownOpen.value) {
        searchQueryDest.value = '';
        nextTick(() => {
          if (destSearchInput.value) destSearchInput.value.focus();
        });
      }
    };

    const selectOriginCountry = (code) => {
      originCountry.value = code;
      isOriginDropdownOpen.value = false;
    };

    const selectDestCountry = (code) => {
      destCountry.value = code;
      isDestDropdownOpen.value = false;
    };

    const getCountryName = (code) => {
      const c = countries.find(c => c.code === code);
      return c ? c.name : '';
    };

    const filteredOriginCountries = computed(() => {
      if (!searchQueryOrigin.value) return countries;
      return countries.filter(c => 
        c.name.toLowerCase().includes(searchQueryOrigin.value.toLowerCase()) ||
        c.code.toLowerCase().includes(searchQueryOrigin.value.toLowerCase())
      );
    });

    const filteredDestCountries = computed(() => {
      if (!searchQueryDest.value) return countries;
      return countries.filter(c => 
        c.name.toLowerCase().includes(searchQueryDest.value.toLowerCase()) ||
        c.code.toLowerCase().includes(searchQueryDest.value.toLowerCase())
      );
    });

    const handleOutsideClick = (e) => {
      if (!e.target.closest('.custom-select-container')) {
        isOriginDropdownOpen.value = false;
        isDestDropdownOpen.value = false;
      }
    };

    // ── Domestic Autocomplete Actions ──────────────────────
    const selectOriginRegion = (region) => {
      originCity.value = region.value;
      originSearchQuery.value = getRegionLabel(region);
      isOriginFocused.value = false;
    };

    const selectDestRegion = (region) => {
      destCity.value = region.value;
      destSearchQuery.value = getRegionLabel(region);
      isDestFocused.value = false;
    };

    const handleOriginInputBlur = () => {
      setTimeout(() => {
        isOriginFocused.value = false;
      }, 200);
    };

    const handleDestInputBlur = () => {
      setTimeout(() => {
        isDestFocused.value = false;
      }, 200);
    };

    const filteredOriginRegions = computed(() => {
      const q = originSearchQuery.value.trim().toLowerCase();
      if (!q) return [];
      return regions.filter(r => 
        r.subdistrict.toLowerCase().includes(q) ||
        r.district.toLowerCase().includes(q) ||
        r.city.toLowerCase().includes(q) ||
        r.zip.includes(q)
      ).slice(0, 5);
    });

    const filteredDestRegions = computed(() => {
      const q = destSearchQuery.value.trim().toLowerCase();
      if (!q) return [];
      return regions.filter(r => 
        r.subdistrict.toLowerCase().includes(q) ||
        r.district.toLowerCase().includes(q) ||
        r.city.toLowerCase().includes(q) ||
        r.zip.includes(q)
      ).slice(0, 5);
    });

    // ── Autocomplete Watchers ──────────────────────────────
    watch(originSearchQuery, (newVal) => {
      const selected = regions.find(r => r.value === originCity.value);
      if (selected && newVal === getRegionLabel(selected)) {
        return; // Already selected
      }

      // Check if user typed a 5-digit zip code
      const zipMatch = newVal.trim();
      if (/^\d{5}$/.test(zipMatch)) {
        const found = regions.find(r => r.zip === zipMatch);
        if (found) {
          selectOriginRegion(found);
          return;
        }
      }

      if (!selected || newVal !== getRegionLabel(selected)) {
        originCity.value = '';
      }
    });

    watch(destSearchQuery, (newVal) => {
      const selected = regions.find(r => r.value === destCity.value);
      if (selected && newVal === getRegionLabel(selected)) {
        return; // Already selected
      }

      // Check if user typed a 5-digit zip code
      const zipMatch = newVal.trim();
      if (/^\d{5}$/.test(zipMatch)) {
        const found = regions.find(r => r.zip === zipMatch);
        if (found) {
          selectDestRegion(found);
          return;
        }
      }

      if (!selected || newVal !== getRegionLabel(selected)) {
        destCity.value = '';
      }
    });

    onMounted(() => {
      document.addEventListener('click', handleOutsideClick);
    });

    onUnmounted(() => {
      document.removeEventListener('click', handleOutsideClick);
    });

    // ── Accordion ──────────────────────────────────────────
    const toggleAccordion = (id) => {
      activeRateId.value = activeRateId.value === id ? null : id;
    };

    // ── Calculate ──────────────────────────────────────────
    const calculateRates = () => {
      if (activeTab.value === 'domestik') {
        if (!originCity.value || !destCity.value) {
          alert('Silakan ketik dan pilih daerah asal dan daerah tujuan dari autocomplete.');
          return;
        }
      } else {
        if (!originCountry.value || !destCountry.value) {
          alert('Silakan pilih Negara Asal dan Negara Tujuan terlebih dahulu.');
          return;
        }
      }

      isCalculating.value = true;

      setTimeout(() => {
        isCalculating.value = false;
        showResults.value = true;
        isRatesResultsExpanded.value = true;

        if (activeTab.value === 'domestik') {
          let distanceMultiplier = 1.0;
          if (originCity.value !== destCity.value) {
            const charDiff = Math.abs(originCity.value.charCodeAt(0) - destCity.value.charCodeAt(0));
            distanceMultiplier = 1.0 + (charDiff * 0.08);
          } else {
            distanceMultiplier = 0.55;
          }

          // Domestic calculation based on gram
          const weightKg = packageWeight.value / 1000;

          shippingRates.value = baseRatesDomestik.map(rate => {
            let multiplier = distanceMultiplier;
            let weightMultiplier = weightKg;
            if (rate.id === 'cargo' && weightKg >= 10) {
              weightMultiplier = weightKg * 0.75;
            }
            let finalCost = rate.basePrice * multiplier * weightMultiplier;
            if (originCity.value === 'jakarta' && destCity.value === 'bandung' && weightKg === 1) {
              finalCost = rate.basePrice;
            } else {
              finalCost = Math.round(finalCost / 500) * 500;
            }
            return { ...rate, cost: finalCost };
          });

        } else {
          // Internasional calculation based on gram
          const weightKg = packageWeightGram.value / 1000;
          const isRegional = ['SG', 'MY', 'TH', 'PH', 'VN', 'MM', 'KH'].includes(destCountry.value);
          const regionMultiplier = isRegional ? 1.0 : 2.5;

          shippingRates.value = baseRatesInternasional.map(rate => {
            let finalCost = Math.round((rate.basePrice * weightKg * regionMultiplier) / 500) * 500;
            if (finalCost < rate.basePrice) finalCost = rate.basePrice;
            return { ...rate, cost: finalCost };
          });
        }

        if (shippingRates.value.length > 0) {
          activeRateId.value = shippingRates.value[0].id;
        }
      }, 700);
    };

    // ── Formatters ─────────────────────────────────────────
    const formatRupiah = (num) => 'Rp ' + num.toLocaleString('id-ID');

    const selectService = (service) => {
      alert(`Anda telah memilih layanan ${service.name} dengan ongkos kirim ${formatRupiah(service.cost)}. Hubungi CS kami untuk melanjutkan order.`);
    };

    return {
      activeTab,
      originCity,
      destCity,
      originSearchQuery,
      destSearchQuery,
      isOriginFocused,
      isDestFocused,
      packageWeight,
      originCountry,
      destCountry,
      packageWeightGram,
      isOriginDropdownOpen,
      isDestDropdownOpen,
      searchQueryOrigin,
      searchQueryDest,
      originSearchInput,
      destSearchInput,
      regions,
      countries,
      shippingRates,
      isCalculating,
      showResults,
      activeRateId,
      isRatesResultsExpanded,
      getCountryFlag,
      switchTab,
      toggleAccordion,
      increaseWeight,
      decreaseWeight,
      validateWeight,
      increaseWeightGram,
      decreaseWeightGram,
      validateWeightGram,
      toggleOriginDropdown,
      toggleDestDropdown,
      selectOriginCountry,
      selectDestCountry,
      getCountryName,
      filteredOriginCountries,
      filteredDestCountries,
      selectOriginRegion,
      selectDestRegion,
      handleOriginInputBlur,
      handleDestInputBlur,
      filteredOriginRegions,
      filteredDestRegions,
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

/* ============================
   Calculator Card Container
   ============================ */
.calculator-container {
  background-color: var(--bg-card);
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-lg);
  border: 1px solid var(--border-color);
  overflow: visible; /* Changed from hidden to prevent custom select dropdown clipping */
  max-width: 1100px;
  margin: 0 auto;
  position: relative;
}

/* ============================
   TAB SWITCHER
   ============================ */
.tab-switcher {
  display: flex;
  align-items: center;
  gap: 0;
  padding: 20px 32px 0 32px;
  background-color: #ffffff;
  border-bottom: 1px solid var(--border-color);
  border-top-left-radius: calc(var(--radius-xl) - 1px);
  border-top-right-radius: calc(var(--radius-xl) - 1px);
}

.tab-btn {
  display: inline-flex;
  align-items: center;
  gap: 7px;
  padding: 12px 20px 14px 20px;
  font-size: 14px;
  font-weight: 700;
  font-family: var(--font-heading);
  color: var(--text-light);
  background: transparent;
  border: none;
  border-bottom: 2.5px solid transparent;
  margin-bottom: -1px;
  cursor: pointer;
  transition: color var(--transition-fast), border-color var(--transition-fast);
  white-space: nowrap;
}

.tab-btn:hover {
  color: var(--primary);
}

.tab-btn--active {
  color: var(--primary);
  border-bottom-color: var(--primary);
}

.tab-icon {
  stroke-width: 2;
}

/* ============================
   Horizontal Form Row
   ============================ */
.calculator-form-row {
  display: grid;
  grid-template-columns: 1.1fr 1.1fr 0.9fr 1.1fr;
  gap: 20px;
  padding: 28px 32px 32px 32px;
  background-color: #ffffff;
  align-items: flex-end;
  border-bottom-left-radius: calc(var(--radius-xl) - 1px);
  border-bottom-right-radius: calc(var(--radius-xl) - 1px);
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

/* ============================
   Select Box
   ============================ */
.select-box-wrapper {
  position: relative;
  width: 100%;
}

.field-select {
  width: 100%;
  padding: 13px 36px 13px 16px;
  border: 1.5px solid var(--border-color);
  border-radius: var(--radius-md);
  background-color: var(--bg-light);
  font-size: 11px;
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
  outline: none;
}

.select-box-wrapper::after {
  content: '▼';
  font-size: 9px;
  position: absolute;
  right: 14px;
  top: 50%;
  transform: translateY(-50%);
  color: var(--text-light);
  pointer-events: none;
}

/* ============================
   Country Select with Flag (Custom)
   ============================ */
.custom-select-container {
  position: relative;
  width: 100%;
}

.custom-select-trigger {
  width: 100%;
  padding: 13.5px 16px;
  border: 1.5px solid var(--border-color);
  border-radius: var(--radius-md);
  background-color: var(--bg-light);
  font-size: 14px;
  color: var(--text-dark);
  font-weight: 600;
  cursor: pointer;
  transition: all var(--transition-fast);
  display: flex;
  align-items: center;
  justify-content: space-between;
  text-align: left;
}

.custom-select-trigger:focus {
  border-color: var(--primary);
  background-color: white;
  box-shadow: 0 0 0 3px var(--primary-light);
  outline: none;
}

.trigger-content {
  display: flex;
  align-items: center;
  gap: 10px;
}

.flag-img-select {
  width: 20px;
  height: 14px;
  object-fit: cover;
  border-radius: 2px;
  border: 1px solid rgba(0, 0, 0, 0.1);
  display: block;
}

.placeholder {
  color: var(--text-light);
  font-weight: 600;
}

.select-chevron {
  color: var(--text-light);
  transition: transform var(--transition-fast);
}

.select-chevron.rotated {
  transform: rotate(180deg);
}

.custom-options-container {
  position: absolute;
  top: calc(100% + 6px);
  left: 0;
  width: 100%;
  background-color: white;
  border: 1.5px solid var(--border-color);
  border-radius: var(--radius-md);
  box-shadow: var(--shadow-md);
  z-index: 99;
  max-height: 280px;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

.search-box-wrapper {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 10px 12px;
  border-bottom: 1px solid var(--border-color);
  background-color: white;
}

.search-icon {
  color: var(--text-light);
  flex-shrink: 0;
}

.dropdown-search-input {
  width: 100%;
  border: none;
  font-size: 13px;
  font-weight: 600;
  color: var(--text-dark);
  outline: none;
}

.dropdown-search-input::placeholder {
  color: var(--text-light);
}

.options-list {
  overflow-y: auto;
  max-height: 220px;
  background-color: white;
}

.custom-option {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 11px 16px;
  cursor: pointer;
  transition: background-color var(--transition-fast);
  font-size: 14px;
  font-weight: 600;
  color: var(--text-dark);
  text-align: left;
}

.custom-option:hover {
  background-color: var(--primary-light);
  color: var(--primary);
}

.custom-option.is-selected {
  background-color: var(--primary);
  color: white;
}

.flag-img-option {
  width: 20px;
  height: 14px;
  object-fit: cover;
  border-radius: 2px;
  border: 1px solid rgba(0, 0, 0, 0.15);
  display: block;
}

.no-options-found {
  padding: 14px 16px;
  font-size: 13px;
  color: var(--text-light);
  font-weight: 600;
  text-align: center;
}

/* ============================
   Weight Counter
   ============================ */
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
  flex-shrink: 0;
}

.counter-btn:hover {
  background-color: var(--primary-light);
  color: var(--primary);
  border-color: rgba(10, 101, 255, 0.25);
}

.counter-input {
  flex-grow: 1;
  text-align: center;
  min-width: 0;
  font-weight: 700;
  font-size: 15px;
  color: var(--text-dark);
  background: transparent;
  border: none;
  outline: none;
}

.counter-input--gram {
  font-size: 13px;
}

/* Hide number spin buttons */
.counter-input::-webkit-outer-spin-button,
.counter-input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}
.counter-input[type=number] {
  -moz-appearance: textfield;
  appearance: textfield;
}

/* ============================
   Action Button
   ============================ */
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

/* ============================
   Results Section
   ============================ */
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

/* ============================
   Accordion
   ============================ */
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

.service-logo-wrapper {
  width: 115px;
  height: 50px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  background-color: transparent;
}

.service-logo-img {
  max-width: 100%;
  max-height: 100%;
  object-fit: contain;
  display: block;
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

/* Select placeholder color */
.field-select.placeholder-selected {
  color: var(--text-light);
}

/* ============================
   Info Footer
   ============================ */
.calculator-info-footer {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 16px 32px;
  background-color: var(--primary-light);
  margin-left: -32px;
  margin-right: -32px;
  margin-top: 32px;
  border-bottom-left-radius: calc(var(--radius-xl) - 1px);
  border-bottom-right-radius: calc(var(--radius-xl) - 1px);
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

/* ============================
   Spinner
   ============================ */
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

/* ============================
   RESPONSIVE — Tablet (≤1024px)
   ============================ */
@media (max-width: 1024px) {
  .calculator-form-row {
    grid-template-columns: 1fr 1fr;
    gap: 16px;
    padding: 24px;
  }

  .tab-switcher {
    padding: 16px 24px 0 24px;
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

/* ============================
   RESPONSIVE — Mobile (≤768px)
   ============================ */
@media (max-width: 768px) {
  .calculator-form-row {
    grid-template-columns: 1fr;
    gap: 14px;
    padding: 20px 16px 24px 16px;
  }

  .tab-switcher {
    padding: 14px 16px 0 16px;
  }

  .tab-btn {
    padding: 10px 14px 12px 14px;
    font-size: 13px;
  }

  .accordion-header {
    padding: 14px 16px;
  }

  .accordion-header-left {
    gap: 10px;
  }

  .service-logo-wrapper {
    width: 150px;
    height: 42px;
  }

  .service-name {
    font-size: 13px;
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

  .rates-results-box {
    padding: 20px 16px 0 16px;
  }

  .calculator-info-footer {
    margin-left: -16px;
    margin-right: -16px;
    padding: 14px 16px;
  }
}

/* ============================
   RESPONSIVE — Small Mobile (≤480px)
   ============================ */
@media (max-width: 480px) {
  .tab-btn {
    padding: 8px 12px 10px 12px;
    font-size: 12px;
    gap: 5px;
  }

  .accordion-header {
    padding: 12px 14px;
  }

  .accordion-header-left {
    gap: 8px;
    min-width: 0;
  }

  .service-logo-wrapper {
    width: 80px;
    height: 36px;
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

/* ============================
   RESPONSIVE — Extra Small (≤640px)
   ============================ */
@media (max-width: 640px) {
  .section-title {
    font-size: 28px;
  }

  .section-description {
    font-size: 14px;
  }

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
/* ============================
   Autocomplete Dropdown (Domestik)
   ============================ */
.autocomplete-input {
  cursor: text;
}

.autocomplete-dropdown {
  position: absolute;
  top: calc(100% + 6px);
  left: 0;
  width: 100%;
  background-color: white;
  border: 1.5px solid var(--border-color);
  border-radius: var(--radius-md);
  box-shadow: var(--shadow-md);
  z-index: 100;
  max-height: 250px;
  overflow-y: auto;
  padding: 6px 0;
}

.autocomplete-item {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 10px 16px;
  cursor: pointer;
  transition: background-color var(--transition-fast);
  text-align: left;
}

.autocomplete-item:hover {
  background-color: var(--primary-light);
}

.dropdown-pin-icon {
  color: var(--primary);
  flex-shrink: 0;
}

.region-text {
  display: flex;
  flex-direction: column;
  line-height: 1.35;
}

.region-main {
  font-size: 13px;
  font-weight: 700;
  color: var(--text-dark);
}

.region-sub {
  font-size: 11px;
  color: var(--text-light);
  font-weight: 600;
}
</style>
