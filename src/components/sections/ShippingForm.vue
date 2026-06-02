<template>
  <div class="shipping-dashboard-page">
    <div class="container dashboard-container">
      
      <!-- Page Header & Title -->
      <div class="dashboard-header-block" v-if="activeStep < 4">
        <h1 class="dashboard-title">Buat Pengiriman Baru</h1>
        <p class="dashboard-subtitle">
          Lengkapi detail pengiriman di bawah untuk mendapatkan estimasi biaya dan menjadwalkan penjemputan paket.
        </p>
      </div>

      <!-- Horizontal Multi-Step Progress Tracker -->
      <div class="stepper-wrapper">
        <div class="stepper-container">
          <div 
            v-for="step in steps" 
            :key="step.number" 
            class="step-item"
            :class="{ 
              'active': activeStep === step.number,
              'completed': activeStep > step.number
            }"
            @click="goToStep(step.number)"
          >
            <div class="step-circle-badge">
              <span v-if="activeStep > step.number" class="step-check">✓</span>
              <span v-else>{{ step.number }}</span>
            </div>
            <span class="step-label-text">{{ step.label }}</span>
            
            <!-- Connector Line -->
            <div v-if="step.number < 4" class="step-line-connector"></div>
          </div>
        </div>
      </div>

      <!-- Main Columns Grid (Only visible for steps 1-3) -->
      <div v-if="activeStep < 4" class="dashboard-content-grid">
        
        <!-- Left Column: Multi-Section Input Form -->
        <div class="form-container-column">
          <form @submit.prevent="submitForm" class="shipment-multi-form">
            
            <!-- Section A: Detail Pengirim -->
            <div class="form-card-section" id="section-sender" @focusin="activeStep = 1">
              <div class="section-title-row">
                <div class="section-icon-box">
                  <User :size="20" class="section-icon" />
                </div>
                <h2 class="section-card-heading">Detail Pengirim</h2>
              </div>

              <div class="form-inputs-row grid-3-cols">
                <div class="form-field-group">
                  <label class="field-input-label">Nama Pengirim</label>
                  <input 
                    type="text" 
                    v-model="senderName" 
                    placeholder="Masukkan nama pengirim" 
                    class="form-field-input"
                    :class="{ 'field-error': errors.senderName }"
                    required
                  />
                  <span v-if="errors.senderName" class="error-msg">Nama pengirim wajib diisi</span>
                </div>

                <div class="form-field-group">
                  <label class="field-input-label">Nomor WhatsApp</label>
                  <div class="phone-input-wrapper">
                    <input 
                      type="tel" 
                      v-model="senderPhone" 
                      placeholder="08xxxxxxxxxx" 
                      class="form-field-input"
                      :class="{ 'field-error': errors.senderPhone }"
                      required
                    />
                  </div>
                  <span v-if="errors.senderPhone" class="error-msg">Nomor WhatsApp tidak valid</span>
                </div>

                <div class="form-field-group">
                  <label class="field-input-label">Kota Asal</label>
                  <div class="select-field-wrapper">
                    <MapPin :size="16" class="select-pin-icon" />
                    <select 
                      v-model="senderCity" 
                      class="form-field-select icon-padding"
                      :class="{ 'field-error': errors.senderCity }"
                      required
                    >
                      <option value="" disabled>Pilih kota asal</option>
                      <option v-for="city in cities" :key="'sender-' + city.value" :value="city.value">
                        {{ city.name }}
                      </option>
                    </select>
                  </div>
                  <span v-if="errors.senderCity" class="error-msg">Kota asal wajib dipilih</span>
                </div>
              </div>

              <div class="form-field-group full-width-field mt-4">
                <label class="field-input-label">Alamat Penjemputan</label>
                <textarea 
                  v-model="senderAddress" 
                  placeholder="Masukkan alamat lengkap penjemputan" 
                  rows="3" 
                  class="form-field-textarea"
                  :class="{ 'field-error': errors.senderAddress }"
                  required
                ></textarea>
                <span v-if="errors.senderAddress" class="error-msg">Alamat penjemputan wajib diisi</span>
              </div>
            </div>

            <!-- Section B: Detail Penerima -->
            <div class="form-card-section" id="section-recipient" @focusin="activeStep = 1">
              <div class="section-title-row">
                <div class="section-icon-box">
                  <User :size="20" class="section-icon" />
                </div>
                <h2 class="section-card-heading">Detail Penerima</h2>
              </div>

              <div class="form-inputs-row grid-3-cols">
                <div class="form-field-group">
                  <label class="field-input-label">Nama Penerima</label>
                  <input 
                    type="text" 
                    v-model="receiverName" 
                    placeholder="Masukkan nama penerima" 
                    class="form-field-input"
                    :class="{ 'field-error': errors.receiverName }"
                    required
                  />
                  <span v-if="errors.receiverName" class="error-msg">Nama penerima wajib diisi</span>
                </div>

                <div class="form-field-group">
                  <label class="field-input-label">Nomor WhatsApp</label>
                  <input 
                    type="tel" 
                    v-model="receiverPhone" 
                    placeholder="08xxxxxxxxxx" 
                    class="form-field-input"
                    :class="{ 'field-error': errors.receiverPhone }"
                    required
                  />
                  <span v-if="errors.receiverPhone" class="error-msg">Nomor WhatsApp tidak valid</span>
                </div>

                <div class="form-field-group">
                  <label class="field-input-label">Kota Tujuan</label>
                  <div class="select-field-wrapper">
                    <MapPin :size="16" class="select-pin-icon" />
                    <select 
                      v-model="receiverCity" 
                      class="form-field-select icon-padding"
                      :class="{ 'field-error': errors.receiverCity }"
                      required
                    >
                      <option value="" disabled>Pilih kota tujuan</option>
                      <option v-for="city in cities" :key="'receiver-' + city.value" :value="city.value">
                        {{ city.name }}
                      </option>
                    </select>
                  </div>
                  <span v-if="errors.receiverCity" class="error-msg">Kota tujuan wajib dipilih</span>
                </div>
              </div>

              <div class="form-field-group full-width-field mt-4">
                <label class="field-input-label">Alamat Tujuan</label>
                <textarea 
                  v-model="receiverAddress" 
                  placeholder="Masukkan alamat lengkap tujuan" 
                  rows="3" 
                  class="form-field-textarea"
                  :class="{ 'field-error': errors.receiverAddress }"
                  required
                ></textarea>
                <span v-if="errors.receiverAddress" class="error-msg">Alamat tujuan wajib diisi</span>
              </div>
            </div>

            <!-- Section C: Detail Paket -->
            <div class="form-card-section" id="section-package" @focusin="activeStep = 2">
              <div class="section-title-row">
                <div class="section-icon-box">
                  <Package :size="20" class="section-icon" />
                </div>
                <h2 class="section-card-heading">Detail Paket</h2>
              </div>

              <!-- Package specs layout -->
              <div class="form-inputs-row flex-specs-row">
                <div class="form-field-group w-25">
                  <label class="field-input-label">Jenis Barang</label>
                  <div class="spacer-sublabel"></div>
                  <select v-model="packageType" class="form-field-select" required>
                    <option value="" disabled>Pilih jenis barang</option>
                    <option value="Dokumen">Dokumen</option>
                    <option value="Pakaian">Pakaian</option>
                    <option value="Elektronik">Elektronik</option>
                    <option value="Makanan">Makanan</option>
                    <option value="Lainnya">Lainnya</option>
                  </select>
                </div>

                <div class="form-field-group w-20">
                  <label class="field-input-label">Berat Paket</label>
                  <div class="spacer-sublabel"></div>
                  <div class="input-badge-wrapper">
                    <input 
                      type="number" 
                      v-model.number="packageWeight" 
                      min="1" 
                      class="form-field-input-badge" 
                      required
                    />
                    <span class="input-badge-text">Kg</span>
                  </div>
                </div>

                <!-- Dimensions Group -->
                <div class="form-field-group dimensions-field-group flex-grow">
                  <label class="field-input-label">Dimensi Paket (cm)</label>
                  <div class="dimensions-inputs-subgrid">
                    <div class="dim-sub-col">
                      <span class="dim-sub-label">Panjang</span>
                      <input type="number" v-model.number="packageLength" placeholder="20" min="1" class="form-field-input dim-input-box" />
                    </div>
                    <div class="dim-sub-col">
                      <span class="dim-sub-label">Lebar</span>
                      <input type="number" v-model.number="packageWidth" placeholder="15" min="1" class="form-field-input dim-input-box" />
                    </div>
                    <div class="dim-sub-col">
                      <span class="dim-sub-label">Tinggi</span>
                      <input type="number" v-model.number="packageHeight" placeholder="10" min="1" class="form-field-input dim-input-box" />
                    </div>
                  </div>
                </div>
              </div>

              <!-- Service Selection Radio Grid -->
              <div class="services-radio-group-container mt-6">
                <label class="field-input-label block-label">Pilih Layanan</label>
                <div class="services-cards-grid">
                  
                  <!-- Card 1: Instant -->
                  <div 
                    class="service-radio-card" 
                    :class="{ 'selected': selectedService === 'instant' }"
                    @click="selectedService = 'instant'"
                  >
                    <div class="radio-card-header">
                      <div class="custom-radio-circle">
                        <div class="radio-dot"></div>
                      </div>
                      <Zap :size="18" class="service-card-icon text-orange" />
                    </div>
                    <h3 class="service-card-title">Instant Delivery</h3>
                    <p class="service-card-eta">Sampai 2 - 4 Jam</p>
                    <span class="service-card-price">Rp 45.000</span>
                  </div>

                  <!-- Card 2: Same Day -->
                  <div 
                    class="service-radio-card" 
                    :class="{ 'selected': selectedService === 'sameday' }"
                    @click="selectedService = 'sameday'"
                  >
                    <div class="radio-card-header">
                      <div class="custom-radio-circle">
                        <div class="radio-dot"></div>
                      </div>
                      <Clock :size="18" class="service-card-icon text-green" />
                    </div>
                    <h3 class="service-card-title">Same Day</h3>
                    <p class="service-card-eta">Sampai Hari Ini</p>
                    <span class="service-card-price">Rp 30.000</span>
                  </div>

                  <!-- Card 3: Next Day -->
                  <div 
                    class="service-radio-card" 
                    :class="{ 'selected': selectedService === 'nextday' }"
                    @click="selectedService = 'nextday'"
                  >
                    <div class="radio-card-header">
                      <div class="custom-radio-circle">
                        <div class="radio-dot"></div>
                      </div>
                      <Boxes :size="18" class="service-card-icon text-blue" />
                    </div>
                    <h3 class="service-card-title">Next Day</h3>
                    <p class="service-card-eta">Sampai Besok</p>
                    <span class="service-card-price">Rp 20.000</span>
                  </div>

                  <!-- Card 4: Cargo -->
                  <div 
                    class="service-radio-card" 
                    :class="{ 'selected': selectedService === 'cargo' }"
                    @click="selectedService = 'cargo'"
                  >
                    <div class="radio-card-header">
                      <div class="custom-radio-circle">
                        <div class="radio-dot"></div>
                      </div>
                      <Truck :size="18" class="service-card-icon text-purple" />
                    </div>
                    <h3 class="service-card-title">Cargo</h3>
                    <p class="service-card-eta">2 - 5 Hari</p>
                    <span class="service-card-price">Rp 15.000</span>
                  </div>

                </div>
              </div>
            </div>

            <!-- Section D: Jadwal Pickup -->
            <div class="form-card-section" id="section-pickup" @focusin="activeStep = 3">
              <div class="section-title-row">
                <div class="section-icon-box">
                  <Calendar :size="20" class="section-icon" />
                </div>
                <h2 class="section-card-heading">Jadwal Pickup</h2>
              </div>

              <div class="form-inputs-row grid-3-cols">
                <div class="form-field-group">
                  <label class="field-input-label">Tanggal Pickup</label>
                  <div class="select-field-wrapper">
                    <input 
                      type="text" 
                      v-model="pickupDate" 
                      placeholder="22 Juni 2026" 
                      class="form-field-input calendar-padding" 
                      required
                    />
                    <Calendar :size="16" class="field-calendar-icon" />
                  </div>
                </div>

                <div class="form-field-group">
                  <label class="field-input-label">Jam Pickup</label>
                  <div class="select-field-wrapper">
                    <select v-model="pickupTime" class="form-field-select" required>
                      <option value="09:00 - 11:00">09:00 - 11:00</option>
                      <option value="11:00 - 13:00">11:00 - 13:00</option>
                      <option value="14:00 - 16:00">14:00 - 16:00</option>
                      <option value="16:00 - 18:00">16:00 - 18:00</option>
                    </select>
                  </div>
                </div>

                <div class="form-field-group">
                  <label class="field-input-label">Catatan untuk Kurir (Opsional)</label>
                  <input 
                    type="text" 
                    v-model="courierNotes" 
                    placeholder="Contoh: Patokan rumah warna biru, pagar hitam, dll." 
                    class="form-field-input" 
                  />
                </div>
              </div>
            </div>

          </form>
        </div>

        <!-- Right Column: Sticky Order Summary Sidebar -->
        <div class="sidebar-container-column">
          <div class="sticky-summary-sidebar">
            <h2 class="sidebar-title">Ringkasan Pengiriman</h2>
            
            <!-- Visual Route Timeline -->
            <div class="summary-route-timeline">
              <div class="timeline-point-row origin-point">
                <div class="point-circle green-dot"></div>
                <div class="point-label-block">
                  <span class="point-city">{{ getCityName(senderCity, 'origin') }}</span>
                  <span class="point-sublabel">Kota Asal</span>
                </div>
              </div>
              <div class="timeline-connecting-dashed"></div>
              <div class="timeline-point-row destination-point">
                <div class="point-circle blue-pin">
                  <MapPin :size="10" />
                </div>
                <div class="point-label-block">
                  <span class="point-city">{{ getCityName(receiverCity, 'destination') }}</span>
                  <span class="point-sublabel">Kota Tujuan</span>
                </div>
              </div>
            </div>

            <!-- Specs List -->
            <div class="summary-specs-list">
              <div class="spec-row">
                <span class="spec-label">Berat Paket</span>
                <span class="spec-value">{{ packageWeight || 1 }} Kg</span>
              </div>
              <div class="spec-row">
                <span class="spec-label">Dimensi</span>
                <span class="spec-value">{{ packageLength }} x {{ packageWidth }} x {{ packageHeight }} cm</span>
              </div>
              <div class="spec-row">
                <span class="spec-label">Layanan</span>
                <span class="spec-value">
                  <span class="service-capsule-badge">{{ getServiceLabel }}</span>
                </span>
              </div>
              <div class="spec-row">
                <span class="spec-label">Estimasi Sampai</span>
                <span class="spec-value">{{ getServiceEta }}</span>
              </div>
            </div>

            <hr class="summary-divider" />

            <!-- Cost Breakdown -->
            <div class="summary-costs-breakdown">
              <div class="cost-row">
                <span class="cost-label">Ongkos Kirim</span>
                <span class="cost-value">{{ formatPrice(calculatedOngkir) }}</span>
              </div>
              <div class="cost-row relative-tooltip">
                <span class="cost-label flex-align">
                  Asuransi 
                  <span class="info-tooltip-trigger" @mouseenter="showTooltip = true" @mouseleave="showTooltip = false">
                    <Info :size="13" class="info-trigger-icon" />
                    <span v-if="showTooltip" class="tooltip-box">
                      Asuransi paket untuk perlindungan penuh kerusakan/kehilangan hingga Rp 10.000.000.
                    </span>
                  </span>
                </span>
                <span class="cost-value">Rp 1.000</span>
              </div>
              <div class="cost-row">
                <span class="cost-label">Biaya Admin</span>
                <span class="cost-value">Rp 1.000</span>
              </div>
            </div>

            <hr class="summary-divider" />

            <!-- Total Row -->
            <div class="summary-total-row">
              <span class="total-label">Total</span>
              <span class="total-value">{{ formatPrice(calculatedTotal) }}</span>
            </div>

            <!-- Insurance Trust Banner -->
            <div class="insurance-banner-blue">
              <ShieldCheck :size="20" class="insurance-banner-icon" />
              <div class="insurance-banner-text">
                <strong>Keamanan Terjamin</strong> - Setiap pengiriman Anda diasuransikan hingga 100%.
              </div>
            </div>

            <!-- Submit Button -->
            <button @click="validateAndSubmit" class="btn btn-primary btn-submit-payment-cta">
              Lanjutkan Pembayaran
              <ArrowRight :size="16" />
            </button>

            <!-- Security note -->
            <div class="security-lock-note">
              <Lock :size="12" class="lock-icon" />
              <span>Data Anda aman dan tidak akan dibagikan ke pihak lain.</span>
            </div>
          </div>
        </div>

      </div>

      <!-- STEP 4: Success Confirmation Screen -->
      <div v-else class="confirmation-success-screen animate-fade-in">
        <div class="success-card">
          <div class="success-icon-wrapper">
            <div class="success-icon-pulse"></div>
            <CheckCircle :size="64" class="success-icon-check" />
          </div>

          <h2 class="success-title">Pengiriman Berhasil Dibuat!</h2>
          <p class="success-subtitle">
            Kode booking Anda telah diterbitkan. Kurir kami akan menjemput paket sesuai jadwal yang Anda tentukan.
          </p>

          <!-- Booking Code Box -->
          <div class="booking-code-box">
            <span class="booking-code-label">KODE BOOKING</span>
            <div class="booking-code-text">{{ bookingCode }}</div>
          </div>

          <!-- Recap Summary Container -->
          <div class="recap-summary-card">
            <h3 class="recap-title">Rincian Pengiriman</h3>
            <div class="recap-grid">
              <div class="recap-column">
                <div class="recap-group">
                  <span class="recap-label">Pengirim</span>
                  <strong class="recap-value">{{ senderName }}</strong>
                  <span class="recap-subvalue">{{ senderPhone }}</span>
                </div>
                <div class="recap-group mt-4">
                  <span class="recap-label">Rute Pengiriman</span>
                  <strong class="recap-value">
                    {{ getCityName(senderCity, 'origin') }} → {{ getCityName(receiverCity, 'destination') }}
                  </strong>
                </div>
              </div>
              <div class="recap-column">
                <div class="recap-group">
                  <span class="recap-label">Penerima</span>
                  <strong class="recap-value">{{ receiverName }}</strong>
                  <span class="recap-subvalue">{{ receiverPhone }}</span>
                </div>
                <div class="recap-group mt-4">
                  <span class="recap-label">Jadwal Penjemputan</span>
                  <strong class="recap-value">{{ pickupDate }}</strong>
                  <span class="recap-subvalue">Jam {{ pickupTime }}</span>
                </div>
              </div>
            </div>

            <hr class="recap-divider" />

            <div class="recap-bottom-row">
              <div class="recap-service-tier">
                <span class="recap-label">Layanan</span>
                <span class="recap-badge-cyan">{{ getServiceLabel }}</span>
              </div>
              <div class="recap-total-cost">
                <span class="recap-label">Total Pembayaran</span>
                <strong class="recap-cost-val">{{ formatPrice(calculatedTotal) }}</strong>
              </div>
            </div>
          </div>

          <!-- Action Buttons -->
          <div class="success-actions-row">
            <button @click="resetForm" class="btn btn-outline btn-new-shipment">
              Buat Pengiriman Lain
            </button>
            <a href="#" class="btn btn-primary btn-home-redirect">
              Kembali ke Beranda
            </a>
          </div>
        </div>
      </div>

    </div>

    <!-- Fixed Bottom Bar for Desktop & Mobile -->
    <div v-if="activeStep < 4" class="dashboard-bottom-bar animate-fade-in">
      <div class="container bottom-bar-container">
        <div class="watermark-block">
          <ShieldCheck :size="16" class="watermark-icon" />
          <span class="watermark-text">Proses ini diawasi oleh <strong class="brand-kolektix">Kolektix</strong></span>
        </div>
        <button @click="validateAndSubmit" class="btn btn-primary bottom-bar-btn">
          Lanjutkan Pembayaran
          <ArrowRight :size="15" />
        </button>
      </div>
    </div>

  </div>
</template>

<script>
import { ref, computed } from 'vue';
import { User, Package, Calendar, Zap, Clock, Boxes, Truck, MapPin, ShieldCheck, Lock, Info, CheckCircle, ArrowRight } from '@lucide/vue';

export default {
  name: 'ShippingForm',
  components: {
    User,
    Package,
    Calendar,
    Zap,
    Clock,
    Boxes,
    Truck,
    MapPin,
    ShieldCheck,
    Lock,
    Info,
    CheckCircle,
    ArrowRight
  },
  setup() {
    // Current active stepper step (1, 2, 3, 4)
    const activeStep = ref(1);
    const showTooltip = ref(false);
    const bookingCode = ref('');

    // List of steps for header stepper tracking
    const steps = [
      { number: 1, label: 'Detail Pengiriman' },
      { number: 2, label: 'Detail Paket' },
      { number: 3, label: 'Jadwal Pickup' },
      { number: 4, label: 'Konfirmasi' }
    ];

    // List of cities matching home calculator
    const cities = [
      { name: 'Jakarta (DKI Jakarta)', value: 'jakarta' },
      { name: 'Bandung (Jawa Barat)', value: 'bandung' },
      { name: 'Surabaya (Jawa Timur)', value: 'surabaya' },
      { name: 'Medan (Sumatera Utara)', value: 'medan' },
      { name: 'Makassar (Sulawesi Selatan)', value: 'makassar' },
      { name: 'Yogyakarta (DIY Yogyakarta)', value: 'yogyakarta' }
    ];

    // Form inputs state
    const senderName = ref('');
    const senderPhone = ref('');
    const senderCity = ref('jakarta'); // Default Jakarta
    const senderAddress = ref('');

    const receiverName = ref('');
    const receiverPhone = ref('');
    const receiverCity = ref('bandung'); // Default Bandung
    const receiverAddress = ref('');

    const packageType = ref('');
    const packageWeight = ref(1); // Default 1
    const packageLength = ref(20); // Default 20
    const packageWidth = ref(15); // Default 15
    const packageHeight = ref(10); // Default 10
    const selectedService = ref('instant'); // Default Instant Delivery

    const pickupDate = ref('22 Juni 2026'); // Default value from prompt
    const pickupTime = ref('14:00 - 16:00'); // Default value from prompt
    const courierNotes = ref('');

    // Error messages tracking
    const errors = ref({
      senderName: false,
      senderPhone: false,
      senderCity: false,
      senderAddress: false,
      receiverName: false,
      receiverPhone: false,
      receiverCity: false,
      receiverAddress: false
    });

    // Helper: translate city values to short name
    const getCityName = (cityVal, type) => {
      const city = cities.find(c => c.value === cityVal);
      if (city) {
        return city.name.split(' ')[0];
      }
      return type === 'origin' ? 'Jakarta' : 'Bandung';
    };

    // Helper: Format price to IDR Rupiah
    const formatPrice = (val) => {
      return new Intl.NumberFormat('id-ID', {
        style: 'currency',
        currency: 'IDR',
        minimumFractionDigits: 0,
        maximumFractionDigits: 0
      }).format(val);
    };

    // Interactive Service Labels
    const getServiceLabel = computed(() => {
      const labels = {
        instant: 'Instant Delivery',
        sameday: 'Same Day',
        nextday: 'Next Day',
        cargo: 'Cargo'
      };
      return labels[selectedService.value] || 'Instant Delivery';
    });

    const getServiceEta = computed(() => {
      const etas = {
        instant: '2 - 4 Jam',
        sameday: 'Sampai Hari Ini',
        nextday: 'Sampai Besok',
        cargo: '2 - 5 Hari'
      };
      return etas[selectedService.value] || '2 - 4 Jam';
    });

    // Dynamic cost calculations
    const calculatedOngkir = computed(() => {
      let base = 45000;
      if (selectedService.value === 'instant') base = 45000;
      else if (selectedService.value === 'sameday') base = 30000;
      else if (selectedService.value === 'nextday') base = 20000;
      else if (selectedService.value === 'cargo') base = 15000;

      let weight = packageWeight.value || 1;
      let cost = base * weight;

      // Add extra multiplier fee if cities are different
      if (senderCity.value && receiverCity.value && senderCity.value !== receiverCity.value) {
        const cityPairs = {
          'jakarta-bandung': 0,
          'bandung-jakarta': 0,
          'jakarta-surabaya': 15000,
          'jakarta-medan': 40000,
          'jakarta-makassar': 55000,
          'jakarta-yogyakarta': 10000,
          'bandung-surabaya': 15000,
          'bandung-yogyakarta': 10000
        };
        const key = `${senderCity.value}-${receiverCity.value}`;
        const keyAlt = `${receiverCity.value}-${senderCity.value}`;
        const extra = cityPairs[key] !== undefined ? cityPairs[key] : (cityPairs[keyAlt] !== undefined ? cityPairs[keyAlt] : 25000);
        cost += extra * weight;
      }
      return cost;
    });

    const calculatedTotal = computed(() => {
      return calculatedOngkir.value + 1000 + 1000; // base + admin (1k) + insurance (1k)
    });

    // Generate random booking code on final confirmation
    const generateBookingCode = () => {
      const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
      let code = 'KMY-';
      for (let i = 0; i < 8; i++) {
        code += chars.charAt(Math.floor(Math.random() * chars.length));
      }
      bookingCode.value = code;
    };

    // Client-side validation before checkout
    const validateAndSubmit = () => {
      // Clear errors
      Object.keys(errors.value).forEach(k => errors.value[k] = false);

      let isValid = true;

      if (!senderName.value.trim()) { errors.value.senderName = true; isValid = false; }
      if (!senderPhone.value.trim() || senderPhone.value.length < 9) { errors.value.senderPhone = true; isValid = false; }
      if (!senderCity.value) { errors.value.senderCity = true; isValid = false; }
      if (!senderAddress.value.trim()) { errors.value.senderAddress = true; isValid = false; }

      if (!receiverName.value.trim()) { errors.value.receiverName = true; isValid = false; }
      if (!receiverPhone.value.trim() || receiverPhone.value.length < 9) { errors.value.receiverPhone = true; isValid = false; }
      if (!receiverCity.value) { errors.value.receiverCity = true; isValid = false; }
      if (!receiverAddress.value.trim()) { errors.value.receiverAddress = true; isValid = false; }

      if (!isValid) {
        // Highlight active step as 1 if details are missing
        activeStep.value = 1;
        // Scroll to the first error
        const firstError = document.querySelector('.field-error');
        if (firstError) {
          firstError.scrollIntoView({ behavior: 'smooth', block: 'center' });
        }
        return;
      }

      // If valid, generate booking code and transition to step 4
      generateBookingCode();
      activeStep.value = 4;
      window.scrollTo({ top: 0, behavior: 'smooth' });
    };

    // Stepper navigation helper
    const goToStep = (stepNum) => {
      // Don't let users click Step 4 unless form is valid
      if (stepNum === 4) {
        validateAndSubmit();
      } else {
        activeStep.value = stepNum;
        
        // Scroll to the respective section
        const mappings = {
          1: '#section-sender',
          2: '#section-package',
          3: '#section-pickup'
        };
        const el = document.querySelector(mappings[stepNum]);
        if (el) {
          el.scrollIntoView({ behavior: 'smooth', block: 'center' });
        }
      }
    };

    // Reset shipping form state
    const resetForm = () => {
      senderName.value = '';
      senderPhone.value = '';
      senderCity.value = 'jakarta';
      senderAddress.value = '';
      receiverName.value = '';
      receiverPhone.value = '';
      receiverCity.value = 'bandung';
      receiverAddress.value = '';
      packageType.value = '';
      packageWeight.value = 1;
      packageLength.value = 20;
      packageWidth.value = 15;
      packageHeight.value = 10;
      selectedService.value = 'instant';
      pickupDate.value = '22 Juni 2026';
      pickupTime.value = '14:00 - 16:00';
      courierNotes.value = '';
      
      activeStep.value = 1;
      window.scrollTo({ top: 0, behavior: 'smooth' });
    };

    return {
      activeStep,
      steps,
      cities,
      showTooltip,
      bookingCode,
      senderName,
      senderPhone,
      senderCity,
      senderAddress,
      receiverName,
      receiverPhone,
      receiverCity,
      receiverAddress,
      packageType,
      packageWeight,
      packageLength,
      packageWidth,
      packageHeight,
      selectedService,
      pickupDate,
      pickupTime,
      courierNotes,
      errors,
      getCityName,
      formatPrice,
      getServiceLabel,
      getServiceEta,
      calculatedOngkir,
      calculatedTotal,
      validateAndSubmit,
      goToStep,
      resetForm
    };
  }
};
</script>

<style scoped>
.shipping-dashboard-page {
  background-color: var(--bg-light);
  min-height: 100vh;
  padding-top: 120px;
  padding-bottom: 140px;
  text-align: left;
}

.dashboard-container {
  max-width: var(--container-width);
}

.dashboard-header-block {
  margin-bottom: 32px;
}

.dashboard-title {
  font-size: 32px;
  font-weight: 800;
  color: var(--text-dark);
  margin-bottom: 8px;
}

.dashboard-subtitle {
  font-size: 16px;
  color: var(--text-muted);
}

/* Stepper Tracker styles */
.stepper-wrapper {
  background-color: var(--bg-card);
  border: 1px solid var(--border-color);
  border-radius: var(--radius-lg);
  padding: 24px 32px;
  margin-bottom: 32px;
  box-shadow: var(--shadow-sm);
}

.stepper-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  position: relative;
  max-width: 900px;
  margin: 0 auto;
}

.step-item {
  display: flex;
  align-items: center;
  position: relative;
  z-index: 2;
  cursor: pointer;
  gap: 12px;
}

.step-circle-badge {
  width: 36px;
  height: 36px;
  border-radius: var(--radius-full);
  background-color: var(--bg-light);
  border: 2px solid var(--border-color);
  display: flex;
  align-items: center;
  justify-content: center;
  font-family: var(--font-heading);
  font-weight: 700;
  color: var(--text-light);
  transition: all var(--transition-normal);
  font-size: 15px;
}

.step-label-text {
  font-family: var(--font-heading);
  font-weight: 600;
  font-size: 15px;
  color: var(--text-light);
  white-space: nowrap;
}

.step-line-connector {
  position: absolute;
  top: 18px;
  left: 36px;
  /* Calculated length to connect steps dynamically */
  width: calc(25vw - 40px);
  height: 2px;
  background-color: var(--border-color);
  z-index: -1;
}

/* Active Step styles */
.step-item.active .step-circle-badge {
  background-color: var(--primary);
  border-color: var(--primary);
  color: white;
  box-shadow: 0 4px 10px rgba(10, 101, 255, 0.25);
}

.step-item.active .step-label-text {
  color: var(--text-dark);
  font-weight: 700;
}

/* Completed Step styles */
.step-item.completed .step-circle-badge {
  background-color: var(--primary-light);
  border-color: var(--primary);
  color: var(--primary);
}

.step-item.completed .step-label-text {
  color: var(--primary);
}

.step-check {
  font-size: 16px;
  font-weight: 800;
}

/* Main Dashboard Columns layout */
.dashboard-content-grid {
  display: grid;
  grid-template-columns: 2.3fr 1fr;
  gap: 32px;
  align-items: start;
}

.form-container-column {
  display: flex;
  flex-direction: column;
  gap: 24px;
}

/* Form section cards styling */
.form-card-section {
  background-color: var(--bg-card);
  border: 1px solid var(--border-color);
  border-radius: var(--radius-lg);
  padding: 32px;
  box-shadow: var(--shadow-sm);
  margin-bottom: 24px;
  transition: box-shadow var(--transition-normal);
}

.form-card-section:focus-within {
  box-shadow: var(--shadow-md);
}

.section-title-row {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 24px;
  border-bottom: 1.5px solid var(--bg-light);
  padding-bottom: 16px;
}

.section-icon-box {
  width: 36px;
  height: 36px;
  border-radius: var(--radius-sm);
  background-color: var(--primary-light);
  color: var(--primary);
  display: flex;
  align-items: center;
  justify-content: center;
}

.section-card-heading {
  font-size: 20px;
  font-weight: 700;
  color: var(--text-dark);
}

/* Form fields elements styling */
.form-field-group {
  display: flex;
  flex-direction: column;
  gap: 8px;
  text-align: left;
}

.field-input-label {
  font-family: var(--font-heading);
  font-weight: 600;
  font-size: 14px;
  color: var(--text-dark);
}

.form-field-input, .form-field-select, .form-field-textarea {
  background-color: #ffffff; /* White background as in the image */
  border: 1.5px solid var(--border-color);
  border-radius: var(--radius-sm);
  padding: 12px 16px;
  font-size: 15px;
  color: var(--text-dark);
  transition: all var(--transition-fast);
  width: 100%;
  box-sizing: border-box;
}

.form-field-input, .form-field-select {
  height: 48px;
}

.form-field-input:focus, .form-field-select:focus, .form-field-textarea:focus {
  border-color: var(--primary);
  background-color: white;
  box-shadow: 0 0 0 3px rgba(10, 101, 255, 0.1);
}

.form-field-input::placeholder, .form-field-textarea::placeholder {
  color: #a0aec0;
}

.grid-3-cols {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 16px;
}

/* Icons within inputs selectors */
.select-field-wrapper {
  position: relative;
  display: flex;
  align-items: center;
}

.select-pin-icon {
  position: absolute;
  left: 14px;
  color: var(--primary);
  pointer-events: none;
}

.form-field-select.icon-padding {
  padding-left: 38px;
  appearance: none;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' fill='none' viewBox='0 0 24 24' stroke='%23475569' stroke-width='2'%3E%3Cpath stroke-linecap='round' stroke-linejoin='round' d='M19.5 8.25l-7.5 7.5-7.5-7.5'/%3E%3C/svg%3E");
  background-repeat: no-repeat;
  background-position: right 14px center;
  background-size: 14px;
}

.field-calendar-icon {
  position: absolute;
  right: 14px;
  color: var(--text-light);
  pointer-events: none;
}

.calendar-padding {
  padding-right: 38px;
}

.error-msg {
  color: var(--danger);
  font-size: 12px;
  font-weight: 500;
  margin-top: 2px;
}

.field-error {
  border-color: var(--danger) !important;
  background-color: #fffafb;
}

.mt-4 {
  margin-top: 16px;
}

.mt-6 {
  margin-top: 24px;
}

/* Section C details */
.flex-specs-row {
  display: flex;
  gap: 16px;
  align-items: stretch;
}

.w-25 {
  width: 25%;
}

.w-20 {
  width: 20%;
}

.flex-grow {
  flex-grow: 1;
}

.input-badge-wrapper {
  display: flex;
  position: relative;
  align-items: center;
  height: 48px;
  width: 100%;
}

.form-field-input-badge {
  background-color: #ffffff; /* White background */
  border: 1.5px solid var(--border-color);
  border-radius: var(--radius-sm);
  padding: 12px 60px 12px 16px;
  font-size: 15px;
  color: var(--text-dark);
  transition: all var(--transition-fast);
  width: 100%;
  height: 48px;
  box-sizing: border-box;
}

.form-field-input-badge:focus {
  border-color: var(--primary);
  background-color: white;
  box-shadow: 0 0 0 3px rgba(10, 101, 255, 0.1);
}

.input-badge-text {
  position: absolute;
  right: 1.5px;
  height: calc(48px - 3px);
  display: flex;
  align-items: center;
  padding: 0 16px;
  background-color: var(--bg-light); /* grey background for unit box */
  border-left: 1.5px solid var(--border-color);
  border-top-right-radius: calc(var(--radius-sm) - 1.5px);
  border-bottom-right-radius: calc(var(--radius-sm) - 1.5px);
  font-weight: 700;
  color: var(--text-muted);
  font-size: 14px;
  pointer-events: none;
}

/* Dimension inputs grid */
.dimensions-inputs-subgrid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 16px;
  width: 100%;
}

.dim-sub-col {
  display: flex;
  flex-direction: column;
}

.dim-sub-label {
  display: block;
  font-family: var(--font-body);
  font-size: 13px;
  font-weight: 500;
  color: var(--text-muted);
  margin-bottom: 6px;
  height: 18px;
  line-height: 18px;
  text-align: left;
}

.spacer-sublabel {
  height: 18px;
  margin-bottom: 6px;
  display: block;
}

.dim-input-box {
  background-color: #ffffff; /* White background as in the image */
  border: 1.5px solid var(--border-color);
  border-radius: var(--radius-sm);
  padding: 12px 16px;
  font-size: 15px;
  color: var(--text-dark);
  width: 100%;
  height: 48px;
  box-sizing: border-box;
}


.block-label {
  margin-bottom: 12px;
}

/* Service Card selection grid */
.services-cards-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 16px;
}

.service-radio-card {
  background-color: var(--bg-card);
  border: 1.5px solid var(--border-color);
  border-radius: var(--radius-md);
  padding: 16px;
  cursor: pointer;
  transition: all var(--transition-fast);
  display: flex;
  flex-direction: column;
  position: relative;
}

.service-radio-card:hover {
  border-color: var(--primary);
  transform: translateY(-2px);
  box-shadow: var(--shadow-sm);
}

.service-radio-card.selected {
  border-color: var(--primary);
  background-color: var(--primary-light);
  box-shadow: 0 4px 12px rgba(10, 101, 255, 0.05);
}

.radio-card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 12px;
}

.custom-radio-circle {
  width: 16px;
  height: 16px;
  border-radius: var(--radius-full);
  border: 1.5px solid var(--border-color);
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: white;
}

.service-radio-card.selected .custom-radio-circle {
  border-color: var(--primary);
}

.radio-dot {
  width: 8px;
  height: 8px;
  border-radius: var(--radius-full);
  background-color: transparent;
  transition: background-color var(--transition-fast);
}

.service-radio-card.selected .radio-dot {
  background-color: var(--primary);
}

.service-card-icon {
  padding: 4px;
  background-color: var(--bg-light);
  border-radius: 6px;
  box-sizing: content-box;
}

.service-radio-card.selected .service-card-icon {
  background-color: white;
}

.text-orange { color: #f97316; }
.text-green { color: #22c55e; }
.text-blue { color: #0a65ff; }
.text-purple { color: #8b5cf6; }

.service-card-title {
  font-size: 14px;
  font-weight: 700;
  color: var(--text-dark);
  margin-bottom: 4px;
}

.service-card-eta {
  font-size: 11px;
  color: var(--text-muted);
  margin-bottom: 12px;
}

.service-card-price {
  font-size: 16px;
  font-weight: 800;
  color: var(--text-dark);
  margin-top: auto;
}

.service-radio-card.selected .service-card-price {
  color: var(--primary);
}

/* Sidebar Styling */
.sidebar-container-column {
  position: sticky;
  top: 100px;
}

.sticky-summary-sidebar {
  background-color: var(--bg-card);
  border: 1px solid var(--border-color);
  border-radius: var(--radius-lg);
  padding: 24px;
  box-shadow: var(--shadow-sm);
}

.sidebar-title {
  font-size: 18px;
  font-weight: 700;
  color: var(--text-dark);
  margin-bottom: 20px;
  border-bottom: 1.5px solid var(--bg-light);
  padding-bottom: 12px;
}

/* Summary Route Timeline */
.summary-route-timeline {
  display: flex;
  flex-direction: column;
  position: relative;
  padding-left: 8px;
  margin-bottom: 24px;
}

.timeline-point-row {
  display: flex;
  align-items: flex-start;
  gap: 16px;
  position: relative;
  z-index: 2;
}

.point-circle {
  width: 14px;
  height: 14px;
  border-radius: var(--radius-full);
  margin-top: 4px;
  flex-shrink: 0;
}

.green-dot {
  background-color: var(--success);
  box-shadow: 0 0 0 4px rgba(34, 197, 94, 0.15);
}

.blue-pin {
  background-color: var(--primary);
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 0 0 4px rgba(10, 101, 255, 0.15);
}

.point-label-block {
  display: flex;
  flex-direction: column;
  text-align: left;
}

.point-city {
  font-size: 15px;
  font-weight: 700;
  color: var(--text-dark);
  line-height: 1.2;
}

.point-sublabel {
  font-size: 11px;
  color: var(--text-light);
  margin-top: 2px;
}

.timeline-connecting-dashed {
  position: absolute;
  left: 14px;
  top: 16px;
  bottom: 16px;
  width: 2px;
  border-left: 2px dashed var(--border-color);
  z-index: 1;
}

/* Specs layout */
.summary-specs-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
  margin-bottom: 20px;
}

.spec-row {
  display: flex;
  justify-content: space-between;
  font-size: 13px;
}

.spec-label {
  color: var(--text-light);
  font-weight: 500;
}

.spec-value {
  color: var(--text-dark);
  font-weight: 700;
}

.service-capsule-badge {
  background-color: var(--primary-light);
  color: var(--primary);
  padding: 4px 10px;
  border-radius: var(--radius-full);
  font-size: 11px;
  font-weight: 700;
}

.summary-divider {
  border: none;
  border-top: 1px solid var(--border-color);
  margin: 16px 0;
}

/* Cost rows */
.summary-costs-breakdown {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.cost-row {
  display: flex;
  justify-content: space-between;
  font-size: 14px;
}

.cost-label {
  color: var(--text-muted);
  font-weight: 500;
}

.flex-align {
  display: flex;
  align-items: center;
  gap: 4px;
}

.cost-value {
  color: var(--text-dark);
  font-weight: 600;
}

.relative-tooltip {
  position: relative;
}

.info-tooltip-trigger {
  display: inline-flex;
  cursor: pointer;
  position: relative;
}

.info-trigger-icon {
  color: var(--text-light);
}

.tooltip-box {
  position: absolute;
  bottom: 120%;
  left: 50%;
  transform: translateX(-50%);
  background-color: var(--text-dark);
  color: white;
  padding: 8px 12px;
  border-radius: var(--radius-sm);
  font-size: 11px;
  font-weight: 500;
  width: 200px;
  line-height: 1.4;
  box-shadow: var(--shadow-lg);
  z-index: 10;
  pointer-events: none;
  text-align: center;
}

.tooltip-box::after {
  content: '';
  position: absolute;
  top: 100%;
  left: 50%;
  transform: translateX(-50%);
  border-width: 5px;
  border-style: solid;
  border-color: var(--text-dark) transparent transparent transparent;
}

/* Total row */
.summary-total-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.total-label {
  font-size: 16px;
  font-weight: 800;
  color: var(--text-dark);
}

.total-value {
  font-size: 22px;
  font-weight: 800;
  color: var(--primary);
}

/* Trust Banner */
.insurance-banner-blue {
  background-color: var(--primary-light);
  border: 1px solid rgba(10, 101, 255, 0.15);
  border-radius: var(--radius-md);
  padding: 12px 16px;
  display: flex;
  gap: 12px;
  align-items: flex-start;
  margin-bottom: 20px;
  text-align: left;
}

.insurance-banner-icon {
  color: var(--primary);
  flex-shrink: 0;
  margin-top: 1px;
}

.insurance-banner-text {
  font-size: 12px;
  color: var(--text-muted);
  line-height: 1.4;
}

.insurance-banner-text strong {
  color: var(--primary);
}

.btn-submit-payment-cta {
  width: 100%;
  padding: 14px 20px;
  font-size: 16px;
  border-radius: var(--radius-md);
}

.security-lock-note {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 6px;
  color: var(--text-light);
  font-size: 11px;
  margin-top: 16px;
}

/* Step 4: Confirmation screen styling */
.confirmation-success-screen {
  background-color: var(--bg-card);
  border: 1px solid var(--border-color);
  border-radius: var(--radius-lg);
  padding: 48px;
  box-shadow: var(--shadow-sm);
  max-width: 650px;
  margin: 0 auto;
  text-align: center;
}

.success-icon-wrapper {
  position: relative;
  width: 80px;
  height: 80px;
  margin: 0 auto 24px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.success-icon-pulse {
  position: absolute;
  width: 100%;
  height: 100%;
  background-color: var(--success-light);
  border-radius: var(--radius-full);
  animation: pulse-ring 1.5s infinite;
  z-index: 1;
}

.success-icon-check {
  color: var(--success);
  position: relative;
  z-index: 2;
}

@keyframes pulse-ring {
  0% { transform: scale(0.9); opacity: 1; }
  100% { transform: scale(1.3); opacity: 0; }
}

.success-title {
  font-size: 24px;
  font-weight: 800;
  color: var(--text-dark);
  margin-bottom: 12px;
}

.success-subtitle {
  font-size: 15px;
  color: var(--text-muted);
  line-height: 1.5;
  max-width: 500px;
  margin: 0 auto 24px;
}

.booking-code-box {
  background: var(--bg-light);
  border: 1.5px dashed var(--primary);
  border-radius: var(--radius-md);
  padding: 16px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  margin-bottom: 32px;
}

.booking-code-label {
  font-size: 11px;
  font-weight: 700;
  color: var(--text-light);
  letter-spacing: 1px;
}

.booking-code-text {
  font-family: var(--font-heading);
  font-size: 28px;
  font-weight: 800;
  color: var(--primary);
  letter-spacing: 2px;
  margin-top: 4px;
}

.recap-summary-card {
  border: 1.5px solid var(--border-color);
  border-radius: var(--radius-md);
  padding: 24px;
  text-align: left;
  margin-bottom: 32px;
}

.recap-title {
  font-size: 16px;
  font-weight: 700;
  color: var(--text-dark);
  margin-bottom: 16px;
}

.recap-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 24px;
}

.recap-group {
  display: flex;
  flex-direction: column;
}

.recap-label {
  font-size: 11px;
  font-weight: 700;
  color: var(--text-light);
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 4px;
}

.recap-value {
  font-size: 14px;
  color: var(--text-dark);
  font-weight: 700;
}

.recap-subvalue {
  font-size: 13px;
  color: var(--text-muted);
  margin-top: 2px;
}

.recap-divider {
  border: none;
  border-top: 1px solid var(--border-color);
  margin: 20px 0;
}

.recap-bottom-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.recap-badge-cyan {
  background-color: var(--primary-light);
  color: var(--primary);
  padding: 6px 12px;
  border-radius: var(--radius-full);
  font-size: 12px;
  font-weight: 700;
}

.recap-cost-val {
  font-size: 18px;
  color: var(--primary);
  font-weight: 800;
}

.success-actions-row {
  display: flex;
  gap: 16px;
  justify-content: center;
}

.btn-new-shipment {
  padding: 12px 24px;
  font-size: 15px;
}

.btn-home-redirect {
  padding: 12px 24px;
  font-size: 15px;
}

/* Animations */
.animate-fade-in {
  animation: fadeIn 0.4s ease-out forwards;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}

/* Responsive CSS for Tablet and Mobile devices */
@media (max-width: 1024px) {
  .shipping-dashboard-page {
    padding-top: 96px;
    padding-bottom: 60px;
  }

  .stepper-line-connector {
    width: calc(20vw - 30px);
  }

  .dashboard-content-grid {
    grid-template-columns: 1fr;
    gap: 24px;
  }

  .sidebar-container-column {
    position: static;
  }

  .grid-3-cols {
    grid-template-columns: 1fr;
    gap: 16px;
  }

  .flex-specs-row {
    flex-direction: column;
    gap: 16px;
  }

  .w-25, .w-20 {
    width: 100%;
  }

  .services-cards-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (max-width: 640px) {
  .dashboard-title {
    font-size: 26px;
  }

  .stepper-wrapper {
    padding: 16px;
  }

  .step-label-text {
    display: none; /* Hide labels on small mobile to fit timeline */
  }

  .stepper-container {
    justify-content: space-around;
  }

  .step-line-connector {
    width: 15vw;
    left: 28px;
    top: 14px;
  }

  .step-circle-badge {
    width: 30px;
    height: 30px;
    font-size: 13px;
  }

  .services-cards-grid {
    grid-template-columns: 1fr;
  }

  .confirmation-success-screen {
    padding: 24px 16px;
  }

  .recap-grid {
    grid-template-columns: 1fr;
    gap: 16px;
  }

  .success-actions-row {
    flex-direction: column;
    width: 100%;
  }

  .btn-new-shipment, .btn-home-redirect {
    width: 100%;
  }
}

/* Bottom Bar styles */
.dashboard-bottom-bar {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  background: #ffffff;
  border-top: 1px solid var(--border-color);
  box-shadow: 0 -4px 20px rgba(0, 0, 0, 0.05);
  z-index: 100;
  padding: 16px 0;
}

.bottom-bar-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  max-width: var(--container-width);
  margin: 0 auto;
  padding-left: 24px;
  padding-right: 24px;
  width: 100%;
}

.watermark-block {
  display: flex;
  align-items: center;
  gap: 8px;
  color: var(--text-muted);
}

.watermark-icon {
  color: var(--success);
}

.watermark-text {
  font-size: 13px;
  font-weight: 500;
}

.brand-kolektix {
  color: var(--primary);
  font-weight: 700;
}

.bottom-bar-btn {
  padding: 12px 24px;
  font-size: 15px;
  font-weight: 700;
  display: inline-flex;
  align-items: center;
  gap: 8px;
}

/* Responsive bottom bar styles */
@media (max-width: 768px) {
  .bottom-bar-container {
    padding-left: 16px;
    padding-right: 16px;
  }
}

@media (max-width: 640px) {
  .dashboard-bottom-bar {
    padding: 12px 0;
  }
  
  .watermark-text {
    font-size: 11px;
  }
  
  .bottom-bar-btn {
    padding: 10px 16px;
    font-size: 13px;
    gap: 6px;
  }

  .watermark-icon {
    width: 14px;
    height: 14px;
  }
}
</style>
