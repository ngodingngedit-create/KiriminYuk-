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
          
          <!-- Tab Switcher -->
          <div class="tab-switcher">
            <button
              type="button"
              class="tab-btn"
              :class="{ 'tab-btn--active': activeTab === 'domestik' }"
              @click="switchTab('domestik')"
            >
              <Globe :size="16" class="tab-icon" />
              <span>Domestik</span>
            </button>
            <button
              type="button"
              class="tab-btn"
              :class="{ 'tab-btn--active': activeTab === 'internasional' }"
              @click="switchTab('internasional')"
            >
              <Globe :size="16" class="tab-icon" />
              <span>Internasional</span>
            </button>
          </div>

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

                <!-- Domestik: Autocomplete Kota Asal -->
                <div class="form-field-group" v-if="activeTab === 'domestik'">
                  <label class="field-input-label">Kota/Kecamatan/Kode Pos Asal</label>
                  <div class="custom-select-container">
                    <!-- <MapPin :size="16" class="select-pin-icon" /> -->
                    <input 
                      type="text" 
                      v-model="originSearchQuery" 
                      placeholder="Ketik kota/kecamatan/kode pos" 
                      class="form-field-input icon-padding autocomplete-input"
                      :class="{ 'field-error': errors.senderCity }"
                      @focus="isOriginFocused = true"
                      @blur="handleOriginBlur"
                      autocomplete="off"
                    />
                    <div v-if="isOriginFocused && filteredOriginRegions.length > 0" class="autocomplete-dropdown animate-fade-in">
                      <div 
                        v-for="region in filteredOriginRegions" 
                        :key="'orig-reg-' + region.value" 
                        class="autocomplete-item"
                        @mousedown.prevent="selectOriginRegion(region)"
                      >
                        <!-- <MapPin :size="14" class="dropdown-pin-icon" /> -->
                        <div class="region-text">
                          <span class="region-main">{{ region.subdistrict }}, {{ region.district }}</span>
                          <span class="region-sub">{{ region.city }} - {{ region.zip }}</span>
                        </div>
                      </div>
                    </div>
                  </div>
                  <span v-if="errors.senderCity" class="error-msg">Kota asal wajib diketik dan dipilih</span>
                </div>

                <!-- Internasional: Country of Origin -->
                <div class="form-field-group" v-else>
                  <label class="field-input-label">Negara Asal</label>
                  <div class="custom-select-container">
                    <button
                      type="button"
                      class="custom-select-trigger"
                      :class="{ 'placeholder-selected': !senderCountry, 'field-error': errors.senderCity }"
                      @click.stop="toggleSenderCountryDropdown"
                    >
                      <div class="trigger-content" v-if="senderCountry">
                        <img :src="`/flags/${senderCountry.toLowerCase()}.png`" class="flag-img-select" alt="" />
                        <span>{{ getCountryName(senderCountry) }}</span>
                      </div>
                      <span v-else class="placeholder">Pilih Negara Asal</span>
                      <ChevronDown :size="16" class="select-chevron" :class="{ 'rotated': isSenderCountryDropdownOpen }" />
                    </button>

                    <div v-if="isSenderCountryDropdownOpen" class="custom-options-container animate-fade-in" @click.stop>
                      <div class="search-box-wrapper">
                        <Search :size="14" class="search-icon" />
                        <input
                          type="text"
                          v-model="searchQuerySender"
                          placeholder="Cari negara..."
                          class="dropdown-search-input"
                          ref="senderCountrySearchInput"
                        />
                      </div>
                      <div class="options-list">
                        <div
                          v-for="country in filteredSenderCountries"
                          :key="'sender-opt-' + country.code"
                          class="custom-option"
                          :class="{ 'is-selected': senderCountry === country.code }"
                          @click="selectSenderCountry(country.code)"
                        >
                          <img :src="`/flags/${country.code.toLowerCase()}.png`" class="flag-img-option" alt="" />
                          <span>{{ country.name }}</span>
                        </div>
                        <div v-if="filteredSenderCountries.length === 0" class="no-options-found">
                          Negara tidak ditemukan
                        </div>
                      </div>
                    </div>
                  </div>
                  <span v-if="errors.senderCity" class="error-msg">Negara asal wajib dipilih</span>
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

                <!-- Domestik: Autocomplete Kota Tujuan -->
                <div class="form-field-group" v-if="activeTab === 'domestik'">
                  <label class="field-input-label">Kota/Kecamatan/Kode Pos Tujuan</label>
                  <div class="custom-select-container">
                    <!-- <MapPin :size="16" class="select-pin-icon" /> -->
                    <input 
                      type="text" 
                      v-model="destSearchQuery" 
                      placeholder="Ketik kota/kecamatan/kode pos" 
                      class="form-field-input icon-padding autocomplete-input"
                      :class="{ 'field-error': errors.receiverCity }"
                      @focus="isDestFocused = true"
                      @blur="handleDestBlur"
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
                  <span v-if="errors.receiverCity" class="error-msg">Kota tujuan wajib diketik dan dipilih</span>
                </div>

                <!-- Internasional: Destination Country -->
                <div class="form-field-group" v-else>
                  <label class="field-input-label">Negara Tujuan</label>
                  <div class="custom-select-container">
                    <button
                      type="button"
                      class="custom-select-trigger"
                      :class="{ 'placeholder-selected': !receiverCountry, 'field-error': errors.receiverCity }"
                      @click.stop="toggleReceiverCountryDropdown"
                    >
                      <div class="trigger-content" v-if="receiverCountry">
                        <img :src="`/flags/${receiverCountry.toLowerCase()}.png`" class="flag-img-select" alt="" />
                        <span>{{ getCountryName(receiverCountry) }}</span>
                      </div>
                      <span v-else class="placeholder">Pilih Negara Tujuan</span>
                      <ChevronDown :size="16" class="select-chevron" :class="{ 'rotated': isReceiverCountryDropdownOpen }" />
                    </button>

                    <div v-if="isReceiverCountryDropdownOpen" class="custom-options-container animate-fade-in" @click.stop>
                      <div class="search-box-wrapper">
                        <Search :size="14" class="search-icon" />
                        <input
                          type="text"
                          v-model="searchQueryReceiver"
                          placeholder="Cari negara..."
                          class="dropdown-search-input"
                          ref="receiverCountrySearchInput"
                        />
                      </div>
                      <div class="options-list">
                        <div
                          v-for="country in filteredReceiverCountries"
                          :key="'receiver-opt-' + country.code"
                          class="custom-option"
                          :class="{ 'is-selected': receiverCountry === country.code }"
                          @click="selectReceiverCountry(country.code)"
                        >
                          <img :src="`/flags/${country.code.toLowerCase()}.png`" class="flag-img-option" alt="" />
                          <span>{{ country.name }}</span>
                        </div>
                        <div v-if="filteredReceiverCountries.length === 0" class="no-options-found">
                          Negara tidak ditemukan
                        </div>
                      </div>
                    </div>
                  </div>
                  <span v-if="errors.receiverCity" class="error-msg">Negara tujuan wajib dipilih</span>
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
                    <span class="input-badge-text">{{ activeTab === 'domestik' ? 'Kg' : 'Gram' }}</span>
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
                  <template v-if="activeTab === 'domestik'">
                    <div 
                      v-for="service in servicesDomestik"
                      :key="service.id"
                      class="service-radio-card" 
                      :class="{ 'selected': selectedService === service.id }"
                      @click="selectedService = service.id"
                    >
                      <div class="radio-card-header">
                        <div class="custom-radio-circle">
                          <div class="radio-dot"></div>
                        </div>
                        <img src="/kurir/domestik/jne.png" class="service-card-logo-img service-card-logo-img--jne" alt="JNE Express" />
                      </div>
                      <h3 class="service-card-title">{{ service.name }}</h3>
                      <p class="service-card-eta">{{ service.eta }}</p>
                      <span class="service-card-price">{{ formatPrice(calculateServiceCost(service.id, true)) }}</span>
                    </div>
                  </template>

                  <template v-else>
                    <div 
                      v-for="service in servicesInternasional"
                      :key="service.id"
                      class="service-radio-card" 
                      :class="{ 'selected': selectedService === service.id }"
                      @click="selectedService = service.id"
                    >
                      <div class="radio-card-header">
                        <div class="custom-radio-circle">
                          <div class="radio-dot"></div>
                        </div>
                        <img src="/kurir/internasional/fedex.png" class="service-card-logo-img service-card-logo-img--fedex" alt="FedEx Express" />
                      </div>
                      <h3 class="service-card-title">{{ service.name }}</h3>
                      <p class="service-card-eta">{{ service.eta }}</p>
                      <span class="service-card-price">{{ formatPrice(calculateServiceCost(service.id, false)) }}</span>
                    </div>
                  </template>
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
                       <!-- Placeholder when no route selected -->
            <div v-if="!isRouteSelected" class="summary-placeholder-box">
              <MapPin :size="24" class="placeholder-icon animate-bounce" />
              <p>Tentukan lokasi asal dan tujuan untuk melihat ringkasan biaya</p>
            </div>

            <!-- Visual Route Timeline & Details Wrapper -->
            <div class="summary-details-wrapper" :class="{ 'is-visible': isRouteSelected }">
              <!-- Visual Route Timeline -->
              <div class="summary-route-timeline">
                <div class="timeline-point-row origin-point">
                  <div class="point-circle green-dot"></div>
                  <div class="point-label-block">
                    <span class="point-city">{{ activeTab === 'domestik' ? getCityName(senderCity, 'origin') : getCountryName(senderCountry) }}</span>
                    <span class="point-sublabel">{{ activeTab === 'domestik' ? 'Kota Asal' : 'Negara Asal' }}</span>
                  </div>
                </div>
                <div class="timeline-connecting-dashed"></div>
                <div class="timeline-point-row destination-point">
                  <div class="point-circle blue-pin">
                    <MapPin :size="10" />
                  </div>
                  <div class="point-label-block">
                    <span class="point-city">{{ activeTab === 'domestik' ? getCityName(receiverCity, 'destination') : getCountryName(receiverCountry) }}</span>
                    <span class="point-sublabel">{{ activeTab === 'domestik' ? 'Kota Tujuan' : 'Negara Tujuan' }}</span>
                  </div>
                </div>
              </div>

              <!-- Specs List -->
              <div class="summary-specs-list">
                <div class="spec-row">
                  <span class="spec-label">Berat Paket</span>
                  <span class="spec-value">{{ packageWeight || (activeTab === 'domestik' ? 1 : 500) }} {{ activeTab === 'domestik' ? 'Kg' : 'Gram' }}</span>
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
            </div>

            <!-- Submit Button -->
            <!-- <button 
              @click="validateAndSubmit" 
              :disabled="!isRouteSelected"
              class="btn btn-primary btn-submit-payment-cta"
              :class="{ 'btn-disabled': !isRouteSelected }"
            >
              Lanjutkan Pembayaran
              <ArrowRight :size="16" />
            </button> -->

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
        <button 
          @click="validateAndSubmit" 
          :disabled="!isRouteSelected"
          class="btn btn-primary bottom-bar-btn"
          :class="{ 'btn-disabled': !isRouteSelected }"
        >
          Lanjutkan Pembayaran
          <ArrowRight :size="15" />
        </button>
      </div>
    </div>

  </div>
</template>

<script>
import { ref, computed, onMounted, onUnmounted, nextTick, watch } from 'vue';
import { User, Package, Calendar, MapPin, ShieldCheck, Lock, Info, CheckCircle, ArrowRight, Globe, ChevronDown, Search, Minus, Plus } from '@lucide/vue';

export default {
  name: 'ShippingForm',
  components: {
    User,
    Package,
    Calendar,
    MapPin,
    ShieldCheck,
    Lock,
    Info,
    CheckCircle,
    ArrowRight,
    Globe,
    ChevronDown,
    Search,
    Minus,
    Plus
  },
  setup() {
    // Current active stepper step (1, 2, 3, 4)
    const activeStep = ref(1);
    const showTooltip = ref(false);
    const bookingCode = ref('');

    // Tab state: 'domestik' or 'internasional'
    const activeTab = ref('domestik');

    // List of steps for header stepper tracking
    const steps = [
      { number: 1, label: 'Detail Pengiriman' },
      { number: 2, label: 'Detail Paket' },
      { number: 3, label: 'Jadwal Pickup' },
      { number: 4, label: 'Konfirmasi' }
    ];

    // List of regions for autocomplete lookup
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

    // List of countries for international dropdown selection
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
    ];

    const servicesDomestik = [
      {
        id: 'instant',
        name: 'Instant Delivery',
        subtext: 'Sampai di hari yang sama',
        basePrice: 85000,
        eta: 'Hari ini (3-6 jam)'
      },
      {
        id: 'sameday',
        name: 'Same Day Delivery',
        subtext: 'Sampai di hari yang sama',
        basePrice: 45000,
        eta: 'Hari ini (6-10 jam)'
      },
      {
        id: 'nextday',
        name: 'Next Day Delivery',
        subtext: 'Sampai 1 hari kerja',
        basePrice: 25000,
        eta: 'Besok (1 hari)'
      },
      {
        id: 'cargo',
        name: 'Cargo / Reguler',
        subtext: 'Sampai 2-3 hari kerja',
        basePrice: 18000,
        eta: '2-3 hari'
      }
    ];

    const servicesInternasional = [
      {
        id: 'intl-express',
        name: 'International Express',
        subtext: 'Pengiriman internasional prioritas',
        basePrice: 250000,
        eta: '3-5 hari kerja'
      },
      {
        id: 'intl-standard',
        name: 'International Standard',
        subtext: 'Pengiriman internasional standar',
        basePrice: 150000,
        eta: '7-14 hari kerja'
      },
      {
        id: 'intl-economy',
        name: 'International Economy',
        subtext: 'Pengiriman ekonomi internasional',
        basePrice: 90000,
        eta: '14-21 hari kerja'
      }
    ];


    // Form inputs state
    const senderName = ref('');
    const senderPhone = ref('');
    const senderCity = ref(''); // Start empty
    const originSearchQuery = ref(''); // Start empty
    const isOriginFocused = ref(false);
    const senderAddress = ref('');

    const receiverName = ref('');
    const receiverPhone = ref('');
    const receiverCity = ref(''); // Start empty
    const destSearchQuery = ref(''); // Start empty
    const isDestFocused = ref(false);
    const receiverAddress = ref('');

    // International Country states
    const senderCountry = ref(''); // Start empty
    const receiverCountry = ref(''); // Start empty
    const isSenderCountryDropdownOpen = ref(false);
    const isReceiverCountryDropdownOpen = ref(false);
    const searchQuerySender = ref('');
    const searchQueryReceiver = ref('');
    const senderCountrySearchInput = ref(null);
    const receiverCountrySearchInput = ref(null);

    const packageType = ref('');
    const packageWeight = ref(1); // Default weight value (1 for Domestik, updated to 500 for Intl)
    const packageLength = ref(20);
    const packageWidth = ref(15);
    const packageHeight = ref(10);
    const selectedService = ref('instant');

    const pickupDate = ref('22 Juni 2026');
    const pickupTime = ref('14:00 - 16:00');
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

    // ── Helper functions ──────────────────────────────
    const getRegionLabel = (region) => {
      return `${region.subdistrict}, ${region.district}, ${region.city} (${region.zip})`;
    };

    const getCityName = (cityVal, type) => {
      if (!cityVal) return '';
      if (activeTab.value === 'domestik') {
        const region = regions.find(r => r.value === cityVal);
        if (region) {
          return region.subdistrict;
        }
        return '';
      } else {
        const countryVal = type === 'origin' ? senderCountry.value : receiverCountry.value;
        if (!countryVal) return '';
        const country = countries.find(c => c.code === countryVal);
        return country ? country.name : '';
      }
    };

    const getCityKey = (cityVal) => {
      const region = regions.find(r => r.value === cityVal);
      if (!region) return '';
      const name = region.city.toLowerCase();
      if (name.includes('jakarta')) return 'jakarta';
      if (name.includes('bandung')) return 'bandung';
      if (name.includes('surabaya')) return 'surabaya';
      if (name.includes('medan')) return 'medan';
      if (name.includes('makassar')) return 'makassar';
      if (name.includes('yogyakarta')) return 'yogyakarta';
      return name;
    };

    const getCountryName = (code) => {
      const c = countries.find(c => c.code === code);
      return c ? c.name : '';
    };

    const formatPrice = (val) => {
      return new Intl.NumberFormat('id-ID', {
        style: 'currency',
        currency: 'IDR',
        minimumFractionDigits: 0,
        maximumFractionDigits: 0
      }).format(val);
    };

    // ── Tab controls ──────────────────────────────
    const switchTab = (tab) => {
      activeTab.value = tab;
      if (tab === 'domestik') {
        packageWeight.value = 1;
        selectedService.value = 'instant';
        senderCity.value = '';
        originSearchQuery.value = '';
        receiverCity.value = '';
        destSearchQuery.value = '';
      } else {
        packageWeight.value = 500;
        selectedService.value = 'intl-express';
        senderCountry.value = '';
        receiverCountry.value = '';
      }
      isSenderCountryDropdownOpen.value = false;
      isReceiverCountryDropdownOpen.value = false;
      isOriginFocused.value = false;
      isDestFocused.value = false;
    };

    // ── Domestic Autocomplete Actions ──────────────────────
    const selectOriginRegion = (region) => {
      senderCity.value = region.value;
      originSearchQuery.value = getRegionLabel(region);
      isOriginFocused.value = false;
    };

    const selectDestRegion = (region) => {
      receiverCity.value = region.value;
      destSearchQuery.value = getRegionLabel(region);
      isDestFocused.value = false;
    };

    const handleOriginBlur = () => {
      setTimeout(() => {
        isOriginFocused.value = false;
      }, 200);
    };

    const handleDestBlur = () => {
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

    // ── Country Dropdowns Actions ──────────────────────
    const toggleSenderCountryDropdown = () => {
      isSenderCountryDropdownOpen.value = !isSenderCountryDropdownOpen.value;
      isReceiverCountryDropdownOpen.value = false;
      if (isSenderCountryDropdownOpen.value) {
        searchQuerySender.value = '';
        nextTick(() => {
          if (senderCountrySearchInput.value) senderCountrySearchInput.value.focus();
        });
      }
    };

    const toggleReceiverCountryDropdown = () => {
      isReceiverCountryDropdownOpen.value = !isReceiverCountryDropdownOpen.value;
      isSenderCountryDropdownOpen.value = false;
      if (isReceiverCountryDropdownOpen.value) {
        searchQueryReceiver.value = '';
        nextTick(() => {
          if (receiverCountrySearchInput.value) receiverCountrySearchInput.value.focus();
        });
      }
    };

    const selectSenderCountry = (code) => {
      senderCountry.value = code;
      isSenderCountryDropdownOpen.value = false;
    };

    const selectReceiverCountry = (code) => {
      receiverCountry.value = code;
      isReceiverCountryDropdownOpen.value = false;
    };

    const filteredSenderCountries = computed(() => {
      if (!searchQuerySender.value) return countries;
      return countries.filter(c => 
        c.name.toLowerCase().includes(searchQuerySender.value.toLowerCase()) ||
        c.code.toLowerCase().includes(searchQuerySender.value.toLowerCase())
      );
    });

    const filteredReceiverCountries = computed(() => {
      if (!searchQueryReceiver.value) return countries;
      return countries.filter(c => 
        c.name.toLowerCase().includes(searchQueryReceiver.value.toLowerCase()) ||
        c.code.toLowerCase().includes(searchQueryReceiver.value.toLowerCase())
      );
    });

    const handleOutsideClick = (e) => {
      if (!e.target.closest('.custom-select-container')) {
        isSenderCountryDropdownOpen.value = false;
        isReceiverCountryDropdownOpen.value = false;
      }
    };

    // ── Autocomplete Search Query Watchers ──────────────
    watch(originSearchQuery, (newVal) => {
      const selected = regions.find(r => r.value === senderCity.value);
      if (selected && newVal === getRegionLabel(selected)) {
        return; // Already selected
      }

      // Check for 5-digit zip code
      const zipMatch = newVal.trim();
      if (/^\d{5}$/.test(zipMatch)) {
        const found = regions.find(r => r.zip === zipMatch);
        if (found) {
          selectOriginRegion(found);
          return;
        }
      }

      if (!selected || newVal !== getRegionLabel(selected)) {
        senderCity.value = '';
      }
    });

    watch(destSearchQuery, (newVal) => {
      const selected = regions.find(r => r.value === receiverCity.value);
      if (selected && newVal === getRegionLabel(selected)) {
        return; // Already selected
      }

      // Check for 5-digit zip code
      const zipMatch = newVal.trim();
      if (/^\d{5}$/.test(zipMatch)) {
        const found = regions.find(r => r.zip === zipMatch);
        if (found) {
          selectDestRegion(found);
          return;
        }
      }

      if (!selected || newVal !== getRegionLabel(selected)) {
        receiverCity.value = '';
      }
    });

    onMounted(() => {
      document.addEventListener('click', handleOutsideClick);
      
      // Load saved details from localStorage if they exist
      const saved = localStorage.getItem('kiriminyuk_shipping_details');
      if (saved) {
        try {
          const details = JSON.parse(saved);
          senderName.value = details.senderName || '';
          senderPhone.value = details.senderPhone || '';
          senderCity.value = details.senderCity || '';
          originSearchQuery.value = details.originSearchQuery || '';
          senderAddress.value = details.senderAddress || '';
          receiverName.value = details.receiverName || '';
          receiverPhone.value = details.receiverPhone || '';
          receiverCity.value = details.receiverCity || '';
          destSearchQuery.value = details.destSearchQuery || '';
          receiverAddress.value = details.receiverAddress || '';
          senderCountry.value = details.senderCountry || '';
          receiverCountry.value = details.receiverCountry || '';
          packageType.value = details.packageType || '';
          packageWeight.value = details.packageWeight || 1;
          packageLength.value = details.packageLength || 20;
          packageWidth.value = details.packageWidth || 15;
          packageHeight.value = details.packageHeight || 10;
          selectedService.value = details.selectedService || 'instant';
          pickupDate.value = details.pickupDate || '22 Juni 2026';
          pickupTime.value = details.pickupTime || '14:00 - 16:00';
          courierNotes.value = details.courierNotes || '';
          activeTab.value = details.activeTab || 'domestik';
        } catch (e) {
          console.error('Failed to parse saved shipping details', e);
        }
      }
    });

    onUnmounted(() => {
      document.removeEventListener('click', handleOutsideClick);
    });

    // ── Service Cost Calculation ───────────────────────────
    const calculateServiceCost = (serviceId, isDomestik) => {
      if (isDomestik) {
        const service = servicesDomestik.find(s => s.id === serviceId);
        if (!service) return 0;

        let distanceMultiplier = 1.0;
        if (senderCity.value && receiverCity.value) {
          if (senderCity.value !== receiverCity.value) {
            const charDiff = Math.abs(senderCity.value.charCodeAt(0) - receiverCity.value.charCodeAt(0));
            distanceMultiplier = 1.0 + (charDiff * 0.08);
          } else {
            distanceMultiplier = 0.55;
          }
        }

        const weightKg = packageWeight.value || 1;
        let weightMultiplier = weightKg;
        if (serviceId === 'cargo' && weightKg >= 10) {
          weightMultiplier = weightKg * 0.75;
        }

        let finalCost = service.basePrice * distanceMultiplier * weightMultiplier;

        // Specially handle Jakarta-Bandung 1kg fallback just like CekTarif
        const isJktBdg = (senderCity.value === 'rawasari' || getCityKey(senderCity.value) === 'jakarta') && 
                         (receiverCity.value === 'dago' || getCityKey(receiverCity.value) === 'bandung');
        const isBdgJkt = (senderCity.value === 'dago' || getCityKey(senderCity.value) === 'bandung') && 
                         (receiverCity.value === 'rawasari' || getCityKey(receiverCity.value) === 'jakarta');

        if ((isJktBdg || isBdgJkt) && weightKg === 1) {
          finalCost = service.basePrice;
        } else {
          finalCost = Math.round(finalCost / 500) * 500;
        }
        return finalCost;
      } else {
        const service = servicesInternasional.find(s => s.id === serviceId);
        if (!service) return 0;

        const weightKg = (packageWeight.value || 500) / 1000;
        const dest = receiverCountry.value || 'SG';
        const isRegional = ['SG', 'MY', 'TH', 'PH', 'VN', 'MM', 'KH'].includes(dest);
        const regionMultiplier = isRegional ? 1.0 : 2.5;

        let finalCost = Math.round((service.basePrice * weightKg * regionMultiplier) / 500) * 500;
        if (finalCost < service.basePrice) finalCost = service.basePrice;
        return finalCost;
      }
    };

    // ── Computed Order Specs ──────────────────────────────
    const getServiceLabel = computed(() => {
      const labels = {
        instant: 'Instant Delivery',
        sameday: 'Same Day Delivery',
        nextday: 'Next Day Delivery',
        cargo: 'Cargo / Reguler',
        'intl-express': 'International Express',
        'intl-standard': 'International Standard',
        'intl-economy': 'International Economy'
      };
      return labels[selectedService.value] || 'Instant Delivery';
    });

    const getServiceEta = computed(() => {
      const etas = {
        instant: 'Hari ini (3-6 jam)',
        sameday: 'Hari ini (6-10 jam)',
        nextday: 'Besok (1 hari)',
        cargo: '2-3 hari',
        'intl-express': '3-5 hari kerja',
        'intl-standard': '7-14 hari kerja',
        'intl-economy': '14-21 hari kerja'
      };
      return etas[selectedService.value] || 'Hari ini (3-6 jam)';
    });

    // Dynamic cost calculations
    const calculatedOngkir = computed(() => {
      if (activeTab.value === 'domestik') {
        if (!senderCity.value || !receiverCity.value) return 0;
      } else {
        if (!senderCountry.value || !receiverCountry.value) return 0;
      }
      return calculateServiceCost(selectedService.value, activeTab.value === 'domestik');
    });

    const calculatedTotal = computed(() => {
      if (calculatedOngkir.value === 0) return 0;
      return calculatedOngkir.value + 1000 + 1000; // base + admin (1k) + insurance (1k)
    });

    const isRouteSelected = computed(() => {
      if (activeTab.value === 'domestik') {
        return !!senderCity.value && !!receiverCity.value;
      } else {
        return !!senderCountry.value && !!receiverCountry.value;
      }
    });

    // Generate booking code
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
      
      if (activeTab.value === 'domestik') {
        if (!senderCity.value) { errors.value.senderCity = true; isValid = false; }
      } else {
        if (!senderCountry.value) { errors.value.senderCity = true; isValid = false; }
      }
      
      if (!senderAddress.value.trim()) { errors.value.senderAddress = true; isValid = false; }

      if (!receiverName.value.trim()) { errors.value.receiverName = true; isValid = false; }
      if (!receiverPhone.value.trim() || receiverPhone.value.length < 9) { errors.value.receiverPhone = true; isValid = false; }
      
      if (activeTab.value === 'domestik') {
        if (!receiverCity.value) { errors.value.receiverCity = true; isValid = false; }
      } else {
        if (!receiverCountry.value) { errors.value.receiverCity = true; isValid = false; }
      }
      
      if (!receiverAddress.value.trim()) { errors.value.receiverAddress = true; isValid = false; }

      if (!isValid) {
        // Highlight active step as 1 if details are missing
        activeStep.value = 1;
        // Scroll to the first error
        nextTick(() => {
          const firstError = document.querySelector('.field-error');
          if (firstError) {
            firstError.scrollIntoView({ behavior: 'smooth', block: 'center' });
          }
        });
        return;
      }

      // Save details to localStorage
      const details = {
        senderName: senderName.value,
        senderPhone: senderPhone.value,
        senderCity: senderCity.value,
        originSearchQuery: originSearchQuery.value,
        senderAddress: senderAddress.value,
        receiverName: receiverName.value,
        receiverPhone: receiverPhone.value,
        receiverCity: receiverCity.value,
        destSearchQuery: destSearchQuery.value,
        receiverAddress: receiverAddress.value,
        senderCountry: senderCountry.value,
        receiverCountry: receiverCountry.value,
        packageType: packageType.value,
        packageWeight: packageWeight.value,
        packageLength: packageLength.value,
        packageWidth: packageWidth.value,
        packageHeight: packageHeight.value,
        selectedService: selectedService.value,
        pickupDate: pickupDate.value,
        pickupTime: pickupTime.value,
        courierNotes: courierNotes.value,
        activeTab: activeTab.value,
        calculatedOngkir: calculatedOngkir.value,
        calculatedTotal: calculatedTotal.value,
        serviceLabel: getServiceLabel.value,
        serviceEta: getServiceEta.value
      };
      localStorage.setItem('kiriminyuk_shipping_details', JSON.stringify(details));
      
      // Navigate to payment page
      window.location.hash = '#payment';
    };

    // Stepper navigation helper
    const goToStep = (stepNum) => {
      if (stepNum === 4) {
        validateAndSubmit();
      } else {
        activeStep.value = stepNum;
        
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
      localStorage.removeItem('kiriminyuk_shipping_details');
      senderName.value = '';
      senderPhone.value = '';
      senderCity.value = 'rawasari';
      originSearchQuery.value = 'Rawasari, Cempaka Putih, Jakarta Pusat (10550)';
      senderAddress.value = '';
      receiverName.value = '';
      receiverPhone.value = '';
      receiverCity.value = 'dago';
      destSearchQuery.value = 'Dago, Coblong, Bandung (40135)';
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
      senderCountry.value = 'ID';
      receiverCountry.value = 'SG';
      
      activeTab.value = 'domestik';
      activeStep.value = 1;
      window.scrollTo({ top: 0, behavior: 'smooth' });
    };

    return {
      activeStep,
      steps,
      regions,
      countries,
      showTooltip,
      bookingCode,
      servicesDomestik,
      servicesInternasional,
      calculateServiceCost,
      activeTab,
      isRouteSelected,
      senderName,
      senderPhone,
      senderCity,
      originSearchQuery,
      isOriginFocused,
      senderAddress,
      receiverName,
      receiverPhone,
      receiverCity,
      destSearchQuery,
      isDestFocused,
      receiverAddress,
      senderCountry,
      receiverCountry,
      isSenderCountryDropdownOpen,
      isReceiverCountryDropdownOpen,
      searchQuerySender,
      searchQueryReceiver,
      senderCountrySearchInput,
      receiverCountrySearchInput,
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
      getCountryName,
      formatPrice,
      switchTab,
      selectOriginRegion,
      selectDestRegion,
      handleOriginBlur,
      handleDestBlur,
      filteredOriginRegions,
      filteredDestRegions,
      toggleSenderCountryDropdown,
      toggleReceiverCountryDropdown,
      selectSenderCountry,
      selectReceiverCountry,
      filteredSenderCountries,
      filteredReceiverCountries,
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

.service-card-logo-img {
  height: 36px;
  max-width: 95px;
  object-fit: contain;
  display: block;
}

.service-card-logo-img--jne {
  max-width: 80px;
}

.service-card-logo-img--fedex {
  max-width: 85px;
}

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

/* ============================
   TAB SWITCHER
   ============================ */
.tab-switcher {
  display: flex;
  align-items: center;
  gap: 0;
  padding: 0 24px;
  background-color: #ffffff;
  border: 1px solid var(--border-color);
  border-radius: var(--radius-lg);
  margin-bottom: 8px;
  box-shadow: var(--shadow-sm);
}

.tab-btn {
  display: inline-flex;
  align-items: center;
  gap: 7px;
  padding: 14px 20px 16px 20px;
  font-size: 15px;
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
   Custom dropdowns (Internasional) & Autocomplete (Domestik)
   ============================ */
.custom-select-container {
  position: relative;
  width: 100%;
}

.custom-select-trigger {
  width: 100%;
  padding: 12px 16px;
  border: 1.5px solid var(--border-color);
  border-radius: var(--radius-sm);
  background-color: #ffffff;
  font-size: 15px;
  color: var(--text-dark);
  font-weight: 500;
  cursor: pointer;
  transition: all var(--transition-fast);
  display: flex;
  align-items: center;
  justify-content: space-between;
  text-align: left;
  height: 48px;
  box-sizing: border-box;
}

.custom-select-trigger:focus {
  border-color: var(--primary);
  box-shadow: 0 0 0 3px rgba(10, 101, 255, 0.1);
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
  color: #a0aec0;
}

.select-chevron {
  color: var(--text-light);
  transition: transform var(--transition-fast);
  margin-left: auto;
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
  border-radius: var(--radius-sm);
  box-shadow: var(--shadow-md);
  z-index: 100;
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

/* Autocomplete Dropdown (Domestik) */
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
  border-radius: var(--radius-sm);
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

@media (max-width: 640px) {
  .tab-switcher {
    padding: 0 16px;
  }
  .tab-btn {
    padding: 12px 14px 14px 14px;
    font-size: 14px;
  }
}

/* ============================
   Order Summary Details & Placeholder transitions
   ============================ */
.summary-placeholder-box {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
  padding: 32px 16px;
  border: 1.5px dashed var(--border-color);
  border-radius: var(--radius-md);
  color: var(--text-light);
  background-color: var(--bg-light);
  margin-bottom: 20px;
  transition: all 0.4s ease;
}

.placeholder-icon {
  color: var(--text-light);
  margin-bottom: 12px;
  opacity: 0.6;
}

.summary-placeholder-box p {
  font-size: 13px;
  font-weight: 500;
  line-height: 1.4;
  margin: 0;
}

.summary-details-wrapper {
  max-height: 0;
  opacity: 0;
  overflow: hidden;
  transition: max-height 0.6s cubic-bezier(0.4, 0, 0.2, 1), opacity 0.4s ease;
}

.summary-details-wrapper.is-visible {
  max-height: 800px;
  opacity: 1;
}

.btn-disabled {
  background-color: #cbd5e1 !important;
  border-color: #cbd5e1 !important;
  color: #94a3b8 !important;
  cursor: not-allowed !important;
  box-shadow: none !important;
  transform: none !important;
}
</style>
