<template>
  <div class="payment-checkout-page">
    <div class="container checkout-container">
      
      <!-- 1. Page Header & Back Button -->
      <div class="checkout-header-block animate-fade-in" v-if="paymentStatus !== 'success'">
        <a href="#shipping-form" class="back-navigation">
          <ArrowLeft :size="16" class="back-icon" />
          <span>Kembali ke Detail Pengiriman</span>
        </a>
        
        <h1 class="main-heading">Pembayaran</h1>
        <p class="sub-heading-text">Selesaikan pembayaran untuk memproses pengiriman Anda.</p>
      </div>

      <!-- Main Columns Grid (Only visible when not successful yet) -->
      <div class="checkout-main-grid animate-fade-in" v-if="paymentStatus !== 'success'">
        
        <!-- 2. Left Column: Pilih Metode Pembayaran Accordion -->
        <div class="payment-methods-card">
          <div class="card-header">
            <CreditCard :size="22" class="header-icon" />
            <h2 class="header-title">Pilih Metode Pembayaran</h2>
          </div>

          <!-- Radio Card Accordion System -->
          <div class="payment-options-list">
            
            <!-- Row 1: Transfer Bank -->
            <div class="accordion-item" :class="{ 'expanded': selectedMethod === 'transfer-bank' }">
              <div 
                class="payment-option-row" 
                :class="{ 'selected': selectedMethod === 'transfer-bank' }"
                @click="selectMethod('transfer-bank', 'bca-transfer')"
              >
                <div class="option-left">
                  <div class="custom-radio">
                    <div class="radio-inner" v-if="selectedMethod === 'transfer-bank'"></div>
                  </div>
                  <div class="method-icon-wrapper">
                    <svg viewBox="0 0 24 24" width="20" height="20" fill="none" stroke="currentColor" stroke-width="2">
                      <rect x="2" y="5" width="20" height="14" rx="2" ry="2"/>
                      <line x1="2" y1="10" x2="22" y2="10"/>
                    </svg>
                  </div>
                  <div class="method-info">
                    <h3 class="method-name">Transfer Bank</h3>
                    <p class="method-desc">Bayar melalui rekening bank</p>
                  </div>
                </div>
                <div class="option-right" v-if="selectedMethod !== 'transfer-bank'">
                  <div class="partner-logos">
                    <!-- BCA -->
                    <svg class="partner-logo" viewBox="0 0 100 35" width="42" height="16">
                      <rect width="100" height="35" rx="6" fill="#00569e"/>
                      <text x="50" y="24" font-family="Arial, sans-serif" font-weight="900" font-style="italic" fill="#ffffff" font-size="19" text-anchor="middle">BCA</text>
                    </svg>
                    <!-- Mandiri -->
                    <svg class="partner-logo" viewBox="0 0 100 35" width="50" height="16">
                      <text x="5" y="24" font-family="Arial, sans-serif" font-weight="900" fill="#003d79" font-size="20">mandırı</text>
                      <path d="M78,11 C84,11 88,14 90,16 C85,15 80,16 75,19 C71,16 70,14 78,11 Z" fill="#ffd700"/>
                    </svg>
                    <!-- BRI -->
                    <svg class="partner-logo" viewBox="0 0 100 35" width="42" height="16">
                      <rect width="24" height="24" x="4" y="5.5" fill="#00529c" rx="4"/>
                      <rect width="12" height="12" x="10" y="11.5" fill="#f58220" rx="2"/>
                      <text x="34" y="23.5" font-family="Arial, sans-serif" font-weight="900" fill="#00529c" font-size="17">BRI</text>
                    </svg>
                    <span class="more-link">Lainnya <span class="chevron-down">∨</span></span>
                  </div>
                </div>
                <div class="option-right accordion-toggle-indicator" v-else>
                  <span class="expanded-chevron">▲</span>
                </div>
              </div>

              <!-- Accordion Content Panel -->
              <div class="accordion-panel" v-if="selectedMethod === 'transfer-bank'">
                <p class="panel-instruction">Pilih rekening bank tujuan transfer:</p>
                <div class="sub-options-grid">
                  <!-- BCA option -->
                  <div 
                    class="sub-option-card" 
                    :class="{ 'active': selectedSubMethod === 'bca-transfer' }"
                    @click="selectedSubMethod = 'bca-transfer'"
                  >
                    <div class="card-logo-row">
                      <svg viewBox="0 0 100 35" width="50" height="18">
                        <rect width="100" height="35" rx="6" fill="#00569e"/>
                        <text x="50" y="24" font-family="Arial, sans-serif" font-weight="900" font-style="italic" fill="#ffffff" font-size="19" text-anchor="middle">BCA</text>
                      </svg>
                      <div class="sub-check" v-if="selectedSubMethod === 'bca-transfer'">✓</div>
                    </div>
                    <span class="bank-name">BCA Transfer</span>
                    <span class="bank-desc">Dicek otomatis</span>
                  </div>
                  <!-- Mandiri option -->
                  <div 
                    class="sub-option-card" 
                    :class="{ 'active': selectedSubMethod === 'mandiri-transfer' }"
                    @click="selectedSubMethod = 'mandiri-transfer'"
                  >
                    <div class="card-logo-row">
                      <svg viewBox="0 0 100 35" width="55" height="18">
                        <text x="5" y="24" font-family="Arial, sans-serif" font-weight="900" fill="#003d79" font-size="20">mandırı</text>
                        <path d="M78,11 C84,11 88,14 90,16 C85,15 80,16 75,19 C71,16 70,14 78,11 Z" fill="#ffd700"/>
                      </svg>
                      <div class="sub-check" v-if="selectedSubMethod === 'mandiri-transfer'">✓</div>
                    </div>
                    <span class="bank-name">Mandiri Transfer</span>
                    <span class="bank-desc">Dicek otomatis</span>
                  </div>
                  <!-- BRI option -->
                  <div 
                    class="sub-option-card" 
                    :class="{ 'active': selectedSubMethod === 'bri-transfer' }"
                    @click="selectedSubMethod = 'bri-transfer'"
                  >
                    <div class="card-logo-row">
                      <svg viewBox="0 0 100 35" width="50" height="18">
                        <rect width="24" height="24" x="4" y="5.5" fill="#00529c" rx="4"/>
                        <rect width="12" height="12" x="10" y="11.5" fill="#f58220" rx="2"/>
                        <text x="34" y="23.5" font-family="Arial, sans-serif" font-weight="900" fill="#00529c" font-size="17">BRI</text>
                      </svg>
                      <div class="sub-check" v-if="selectedSubMethod === 'bri-transfer'">✓</div>
                    </div>
                    <span class="bank-name">BRI Transfer</span>
                    <span class="bank-desc">Dicek otomatis</span>
                  </div>
                </div>

                <div class="bank-transfer-details animate-fade-in" v-if="selectedSubMethod">
                  <div class="info-row">
                    <span class="info-label">Nomor Rekening:</span>
                    <div class="copy-flex">
                      <strong class="account-number">{{ getAccountNumber(selectedSubMethod) }}</strong>
                      <button @click.stop="copyText(getAccountNumber(selectedSubMethod))" class="copy-btn">Salin</button>
                    </div>
                  </div>
                  <div class="info-row mt-2">
                    <span class="info-label">Nama Rekening:</span>
                    <strong class="account-holder">KOLEKTIX QIRIMIN</strong>
                  </div>
                </div>
              </div>
            </div>

            <!-- Row 2: E-Wallet -->
            <div class="accordion-item" :class="{ 'expanded': selectedMethod === 'e-wallet' }">
              <div 
                class="payment-option-row" 
                :class="{ 'selected': selectedMethod === 'e-wallet' }"
                @click="selectMethod('e-wallet', 'gopay')"
              >
                <div class="option-left">
                  <div class="custom-radio">
                    <div class="radio-inner" v-if="selectedMethod === 'e-wallet'"></div>
                  </div>
                  <div class="method-icon-wrapper">
                    <svg viewBox="0 0 24 24" width="20" height="20" fill="none" stroke="currentColor" stroke-width="2">
                      <rect x="2" y="4" width="20" height="16" rx="2" ry="2"/>
                      <path d="M12 4v16M2 10h20M12 14h6"/>
                    </svg>
                  </div>
                  <div class="method-info">
                    <h3 class="method-name">E-Wallet</h3>
                    <p class="method-desc">Bayar menggunakan e-wallet</p>
                  </div>
                </div>
                <div class="option-right" v-if="selectedMethod !== 'e-wallet'">
                  <div class="partner-logos">
                    <!-- OVO -->
                    <svg class="partner-logo" viewBox="0 0 100 35" width="40" height="16">
                      <rect width="100" height="35" rx="10" fill="#4d1b7b"/>
                      <text x="50" y="24" font-family="Arial, sans-serif" font-weight="900" fill="#ffffff" font-size="17" text-anchor="middle">OVO</text>
                    </svg>
                    <!-- DANA -->
                    <svg class="partner-logo" viewBox="0 0 100 35" width="42" height="16">
                      <rect width="100" height="35" rx="6" fill="#108ee9"/>
                      <text x="50" y="24" font-family="Arial, sans-serif" font-weight="900" fill="#ffffff" font-size="16" text-anchor="middle">DANA</text>
                    </svg>
                    <!-- Gopay -->
                    <svg class="partner-logo" viewBox="0 0 100 35" width="48" height="16">
                      <circle cx="15" cy="17.5" r="8" fill="#00aed6"/>
                      <circle cx="15" cy="17.5" r="3" fill="#ffffff"/>
                      <text x="28" y="23.5" font-family="Arial, sans-serif" font-weight="800" fill="#00aed6" font-size="18">gopay</text>
                    </svg>
                    <!-- LinkAja -->
                    <svg class="partner-logo" viewBox="0 0 100 35" width="45" height="16">
                      <rect width="28" height="28" x="5" y="3.5" rx="6" fill="#e1251b"/>
                      <text x="19" y="21.5" font-family="Arial, sans-serif" font-weight="900" fill="#ffffff" font-size="9" text-anchor="middle">Link</text>
                      <text x="52" y="23.5" font-family="Arial, sans-serif" font-weight="800" fill="#e1251b" font-size="15">Aja!</text>
                    </svg>
                  </div>
                </div>
                <div class="option-right accordion-toggle-indicator" v-else>
                  <span class="expanded-chevron">▲</span>
                </div>
              </div>

              <!-- Accordion Content Panel -->
              <div class="accordion-panel" v-if="selectedMethod === 'e-wallet'">
                <p class="panel-instruction">Pilih layanan e-wallet Anda:</p>
                <div class="sub-options-grid">
                  <!-- Gopay -->
                  <div 
                    class="sub-option-card" 
                    :class="{ 'active': selectedSubMethod === 'gopay' }"
                    @click="selectedSubMethod = 'gopay'"
                  >
                    <div class="card-logo-row">
                      <svg viewBox="0 0 100 35" width="55" height="18">
                        <circle cx="15" cy="17.5" r="8" fill="#00aed6"/>
                        <circle cx="15" cy="17.5" r="3" fill="#ffffff"/>
                        <text x="28" y="23.5" font-family="Arial, sans-serif" font-weight="800" fill="#00aed6" font-size="18">gopay</text>
                      </svg>
                      <div class="sub-check" v-if="selectedSubMethod === 'gopay'">✓</div>
                    </div>
                    <span class="bank-name">GoPay</span>
                    <span class="bank-desc">Instan & Mudah</span>
                  </div>
                  <!-- DANA -->
                  <div 
                    class="sub-option-card" 
                    :class="{ 'active': selectedSubMethod === 'dana' }"
                    @click="selectedSubMethod = 'dana'"
                  >
                    <div class="card-logo-row">
                      <svg viewBox="0 0 100 35" width="50" height="18">
                        <rect width="100" height="35" rx="6" fill="#108ee9"/>
                        <text x="50" y="24" font-family="Arial, sans-serif" font-weight="900" fill="#ffffff" font-size="16" text-anchor="middle">DANA</text>
                      </svg>
                      <div class="sub-check" v-if="selectedSubMethod === 'dana'">✓</div>
                    </div>
                    <span class="bank-name">DANA</span>
                    <span class="bank-desc">Proses Cepat</span>
                  </div>
                  <!-- OVO -->
                  <div 
                    class="sub-option-card" 
                    :class="{ 'active': selectedSubMethod === 'ovo' }"
                    @click="selectedSubMethod = 'ovo'"
                  >
                    <div class="card-logo-row">
                      <svg viewBox="0 0 100 35" width="48" height="18">
                        <rect width="100" height="35" rx="10" fill="#4d1b7b"/>
                        <text x="50" y="24" font-family="Arial, sans-serif" font-weight="900" fill="#ffffff" font-size="17" text-anchor="middle">OVO</text>
                      </svg>
                      <div class="sub-check" v-if="selectedSubMethod === 'ovo'">✓</div>
                    </div>
                    <span class="bank-name">OVO</span>
                    <span class="bank-desc">Potongan Admin</span>
                  </div>
                  <!-- LinkAja -->
                  <div 
                    class="sub-option-card" 
                    :class="{ 'active': selectedSubMethod === 'linkaja' }"
                    @click="selectedSubMethod = 'linkaja'"
                  >
                    <div class="card-logo-row">
                      <svg viewBox="0 0 100 35" width="52" height="18">
                        <rect width="28" height="28" x="5" y="3.5" rx="6" fill="#e1251b"/>
                        <text x="19" y="21.5" font-family="Arial, sans-serif" font-weight="900" fill="#ffffff" font-size="9" text-anchor="middle">Link</text>
                        <text x="52" y="23.5" font-family="Arial, sans-serif" font-weight="800" fill="#e1251b" font-size="15">Aja!</text>
                      </svg>
                      <div class="sub-check" v-if="selectedSubMethod === 'linkaja'">✓</div>
                    </div>
                    <span class="bank-name">LinkAja</span>
                    <span class="bank-desc">Dompet Nasional</span>
                  </div>
                </div>

                <!-- Phone number input for E-Wallet payments -->
                <div class="ewallet-phone-form animate-fade-in" v-if="selectedSubMethod">
                  <label class="input-label-text">Nomor Handphone Terdaftar</label>
                  <div class="phone-input-wrapper">
                    <span class="country-prefix">+62</span>
                    <input 
                      type="tel" 
                      v-model="walletPhone" 
                      placeholder="812xxxxxxxx" 
                      class="tel-input-box" 
                    />
                  </div>
                  <p class="phone-disclaimer">Pastikan nomor handphone aktif dan memiliki saldo yang cukup.</p>
                </div>
              </div>
            </div>

            <!-- Row 3: Virtual Account -->
            <div class="accordion-item" :class="{ 'expanded': selectedMethod === 'virtual-account' }">
              <div 
                class="payment-option-row" 
                :class="{ 'selected': selectedMethod === 'virtual-account' }"
                @click="selectMethod('virtual-account', 'bca-va')"
              >
                <div class="option-left">
                  <div class="custom-radio">
                    <div class="radio-inner" v-if="selectedMethod === 'virtual-account'"></div>
                  </div>
                  <div class="method-icon-wrapper">
                    <svg viewBox="0 0 24 24" width="20" height="20" fill="none" stroke="currentColor" stroke-width="2">
                      <path d="M3 21h18M3 10h18M5 6h14M4 10v11M20 10v11M8 14v3M12 14v3M16 14v3"/>
                    </svg>
                  </div>
                  <div class="method-info">
                    <h3 class="method-name">Virtual Account</h3>
                    <p class="method-desc">Bayar menggunakan Virtual Account</p>
                  </div>
                </div>
                <div class="option-right" v-if="selectedMethod !== 'virtual-account'">
                  <div class="partner-logos">
                    <!-- BCA -->
                    <svg class="partner-logo" viewBox="0 0 100 35" width="42" height="16">
                      <rect width="100" height="35" rx="6" fill="#00569e"/>
                      <text x="50" y="24" font-family="Arial, sans-serif" font-weight="900" font-style="italic" fill="#ffffff" font-size="19" text-anchor="middle">BCA</text>
                    </svg>
                    <!-- Mandiri -->
                    <svg class="partner-logo" viewBox="0 0 100 35" width="50" height="16">
                      <text x="5" y="24" font-family="Arial, sans-serif" font-weight="900" fill="#003d79" font-size="20">mandırı</text>
                      <path d="M78,11 C84,11 88,14 90,16 C85,15 80,16 75,19 C71,16 70,14 78,11 Z" fill="#ffd700"/>
                    </svg>
                    <!-- BRI -->
                    <svg class="partner-logo" viewBox="0 0 100 35" width="42" height="16">
                      <rect width="24" height="24" x="4" y="5.5" fill="#00529c" rx="4"/>
                      <rect width="12" height="12" x="10" y="11.5" fill="#f58220" rx="2"/>
                      <text x="34" y="23.5" font-family="Arial, sans-serif" font-weight="900" fill="#00529c" font-size="17">BRI</text>
                    </svg>
                    <!-- BNI -->
                    <svg class="partner-logo" viewBox="0 0 100 35" width="42" height="16">
                      <text x="5" y="24" font-family="Arial, sans-serif" font-weight="900" fill="#005a6f" font-size="18">BNI</text>
                      <circle cx="75" cy="17.5" r="9" fill="#f15a24"/>
                      <text x="75" y="21" font-family="Arial, sans-serif" font-weight="800" fill="#ffffff" font-size="11" text-anchor="middle">46</text>
                    </svg>
                  </div>
                </div>
                <div class="option-right accordion-toggle-indicator" v-else>
                  <span class="expanded-chevron">▲</span>
                </div>
              </div>

              <!-- Accordion Content Panel -->
              <div class="accordion-panel" v-if="selectedMethod === 'virtual-account'">
                <p class="panel-instruction">Pilih bank Virtual Account Anda:</p>
                <div class="sub-options-grid">
                  <!-- BCA VA -->
                  <div 
                    class="sub-option-card" 
                    :class="{ 'active': selectedSubMethod === 'bca-va' }"
                    @click="selectedSubMethod = 'bca-va'"
                  >
                    <div class="card-logo-row">
                      <svg viewBox="0 0 100 35" width="50" height="18">
                        <rect width="100" height="35" rx="6" fill="#00569e"/>
                        <text x="50" y="24" font-family="Arial, sans-serif" font-weight="900" font-style="italic" fill="#ffffff" font-size="19" text-anchor="middle">BCA</text>
                      </svg>
                      <div class="sub-check" v-if="selectedSubMethod === 'bca-va'">✓</div>
                    </div>
                    <span class="bank-name">BCA VA</span>
                    <span class="bank-desc">Verifikasi Instan</span>
                  </div>
                  <!-- Mandiri VA -->
                  <div 
                    class="sub-option-card" 
                    :class="{ 'active': selectedSubMethod === 'mandiri-va' }"
                    @click="selectedSubMethod = 'mandiri-va'"
                  >
                    <div class="card-logo-row">
                      <svg viewBox="0 0 100 35" width="55" height="18">
                        <text x="5" y="24" font-family="Arial, sans-serif" font-weight="900" fill="#003d79" font-size="20">mandırı</text>
                        <path d="M78,11 C84,11 88,14 90,16 C85,15 80,16 75,19 C71,16 70,14 78,11 Z" fill="#ffd700"/>
                      </svg>
                      <div class="sub-check" v-if="selectedSubMethod === 'mandiri-va'">✓</div>
                    </div>
                    <span class="bank-name">Mandiri VA</span>
                    <span class="bank-desc">Verifikasi Instan</span>
                  </div>
                  <!-- BRI VA -->
                  <div 
                    class="sub-option-card" 
                    :class="{ 'active': selectedSubMethod === 'bri-va' }"
                    @click="selectedSubMethod = 'bri-va'"
                  >
                    <div class="card-logo-row">
                      <svg viewBox="0 0 100 35" width="50" height="18">
                        <rect width="24" height="24" x="4" y="5.5" fill="#00529c" rx="4"/>
                        <rect width="12" height="12" x="10" y="11.5" fill="#f58220" rx="2"/>
                        <text x="34" y="23.5" font-family="Arial, sans-serif" font-weight="900" fill="#00529c" font-size="17">BRI</text>
                      </svg>
                      <div class="sub-check" v-if="selectedSubMethod === 'bri-va'">✓</div>
                    </div>
                    <span class="bank-name">BRIVA</span>
                    <span class="bank-desc">Verifikasi Instan</span>
                  </div>
                  <!-- BNI VA -->
                  <div 
                    class="sub-option-card" 
                    :class="{ 'active': selectedSubMethod === 'bni-va' }"
                    @click="selectedSubMethod = 'bni-va'"
                  >
                    <div class="card-logo-row">
                      <svg viewBox="0 0 100 35" width="50" height="18">
                        <text x="5" y="24" font-family="Arial, sans-serif" font-weight="900" fill="#005a6f" font-size="18">BNI</text>
                        <circle cx="75" cy="17.5" r="9" fill="#f15a24"/>
                        <text x="75" y="21" font-family="Arial, sans-serif" font-weight="800" fill="#ffffff" font-size="11" text-anchor="middle">46</text>
                      </svg>
                      <div class="sub-check" v-if="selectedSubMethod === 'bni-va'">✓</div>
                    </div>
                    <span class="bank-name">BNI VA</span>
                    <span class="bank-desc">Verifikasi Instan</span>
                  </div>
                </div>

                <div class="bank-transfer-details animate-fade-in" v-if="selectedSubMethod">
                  <div class="info-row">
                    <span class="info-label">Nomor VA / Pembayaran:</span>
                    <div class="copy-flex">
                      <strong class="account-number">{{ getVaNumber(selectedSubMethod) }}</strong>
                      <button @click.stop="copyText(getVaNumber(selectedSubMethod))" class="copy-btn">Salin</button>
                    </div>
                  </div>
                  <div class="info-row mt-2">
                    <span class="info-label">Nama Transaksi:</span>
                    <strong class="account-holder">KOLEKTIX LOGISTIK</strong>
                  </div>
                </div>
              </div>
            </div>

            <!-- Row 4: Kartu Kredit / Debit -->
            <div class="accordion-item" :class="{ 'expanded': selectedMethod === 'credit-card' }">
              <div 
                class="payment-option-row" 
                :class="{ 'selected': selectedMethod === 'credit-card' }"
                @click="selectMethod('credit-card', 'credit-card-form')"
              >
                <div class="option-left">
                  <div class="custom-radio">
                    <div class="radio-inner" v-if="selectedMethod === 'credit-card'"></div>
                  </div>
                  <div class="method-icon-wrapper">
                    <svg viewBox="0 0 24 24" width="20" height="20" fill="none" stroke="currentColor" stroke-width="2">
                      <rect x="1" y="4" width="22" height="16" rx="2" ry="2"/>
                      <line x1="1" y1="10" x2="23" y2="10"/>
                    </svg>
                  </div>
                  <div class="method-info">
                    <h3 class="method-name">Kartu Kredit / Debit</h3>
                    <p class="method-desc">Bayar menggunakan kartu</p>
                  </div>
                </div>
                <div class="option-right" v-if="selectedMethod !== 'credit-card'">
                  <div class="partner-logos">
                    <!-- VISA -->
                    <svg class="partner-logo" viewBox="0 0 100 35" width="45" height="15">
                      <text x="50" y="26" font-family="Arial, sans-serif" font-weight="900" font-style="italic" fill="#1a1f71" font-size="28" text-anchor="middle">VISA</text>
                    </svg>
                    <!-- Mastercard -->
                    <svg class="partner-logo" viewBox="0 0 100 35" width="38" height="20">
                      <circle cx="38" cy="17.5" r="14" fill="#eb001b" opacity="0.9"/>
                      <circle cx="62" cy="17.5" r="14" fill="#ff5f00" opacity="0.9"/>
                    </svg>
                    <!-- JCB -->
                    <svg class="partner-logo" viewBox="0 0 100 35" width="38" height="16">
                      <rect width="25" height="30" x="8" y="2.5" rx="4" fill="#003780"/>
                      <rect width="25" height="30" x="37" y="2.5" rx="4" fill="#d6001c"/>
                      <rect width="25" height="30" x="66" y="2.5" rx="4" fill="#00843d"/>
                      <text x="50" y="22.5" font-family="Arial, sans-serif" font-weight="900" fill="#ffffff" font-size="15" text-anchor="middle">JCB</text>
                    </svg>
                    <!-- AMEX -->
                    <svg class="partner-logo" viewBox="0 0 100 35" width="50" height="16">
                      <rect width="90" height="30" x="5" y="2.5" fill="#007bc1" rx="4"/>
                      <text x="50" y="21.5" font-family="Arial, sans-serif" font-weight="800" fill="#ffffff" font-size="9" text-anchor="middle" letter-spacing="1">AMEX</text>
                    </svg>
                  </div>
                </div>
                <div class="option-right accordion-toggle-indicator" v-else>
                  <span class="expanded-chevron">▲</span>
                </div>
              </div>

              <!-- Accordion Content Panel -->
              <div class="accordion-panel" v-if="selectedMethod === 'credit-card'">
                <p class="panel-instruction">Masukkan detail kartu kredit/debit Anda:</p>
                
                <div class="credit-card-form animate-fade-in">
                  <div class="input-field">
                    <label class="form-label">Nomor Kartu</label>
                    <div class="input-icon-flex">
                      <input 
                        type="text" 
                        v-model="cardNumber" 
                        placeholder="1234 5678 9012 3456" 
                        class="form-input-text"
                        maxlength="19" 
                      />
                      <div class="card-brand-indicator">
                        <svg viewBox="0 0 100 35" width="35" height="14" v-if="cardNumber.startsWith('4')">
                          <text x="50" y="26" font-family="Arial, sans-serif" font-weight="900" font-style="italic" fill="#1a1f71" font-size="28" text-anchor="middle">VISA</text>
                        </svg>
                        <svg viewBox="0 0 100 35" width="30" height="18" v-else-if="cardNumber.startsWith('5')">
                          <circle cx="38" cy="17.5" r="14" fill="#eb001b" opacity="0.9"/>
                          <circle cx="62" cy="17.5" r="14" fill="#ff5f00" opacity="0.9"/>
                        </svg>
                        <span class="default-card-text" v-else>CARD</span>
                      </div>
                    </div>
                  </div>

                  <div class="form-cols-2 mt-3">
                    <div class="input-field">
                      <label class="form-label">Masa Berlaku</label>
                      <input 
                        type="text" 
                        v-model="cardExpiry" 
                        placeholder="MM / YY" 
                        class="form-input-text" 
                        maxlength="5" 
                      />
                    </div>
                    <div class="input-field">
                      <label class="form-label">CVV / CVV2</label>
                      <input 
                        type="password" 
                        v-model="cardCvv" 
                        placeholder="•••" 
                        class="form-input-text" 
                        maxlength="3" 
                      />
                    </div>
                  </div>

                  <div class="input-field mt-3">
                    <label class="form-label">Nama Pemegang Kartu</label>
                    <input 
                      type="text" 
                      v-model="cardName" 
                      placeholder="Contoh: John Doe" 
                      class="form-input-text" 
                    />
                  </div>
                </div>
              </div>
            </div>

            <!-- Row 5: QRIS -->
            <div class="accordion-item" :class="{ 'expanded': selectedMethod === 'qris' }">
              <div 
                class="payment-option-row" 
                :class="{ 'selected': selectedMethod === 'qris' }"
                @click="selectMethod('qris', 'qris-code')"
              >
                <div class="option-left">
                  <div class="custom-radio">
                    <div class="radio-inner" v-if="selectedMethod === 'qris'"></div>
                  </div>
                  <div class="method-icon-wrapper">
                    <svg viewBox="0 0 24 24" width="20" height="20" fill="none" stroke="currentColor" stroke-width="2">
                      <rect x="3" y="3" width="7" height="7"/>
                      <rect x="14" y="3" width="7" height="7"/>
                      <rect x="14" y="14" width="7" height="7"/>
                      <rect x="3" y="14" width="7" height="7"/>
                      <rect x="9" y="9" width="6" height="6"/>
                    </svg>
                  </div>
                  <div class="method-info">
                    <h3 class="method-name">QRIS</h3>
                    <p class="method-desc">Scan QR untuk pembayaran</p>
                  </div>
                </div>
                <div class="option-right" v-if="selectedMethod !== 'qris'">
                  <div class="partner-logos">
                    <!-- QRIS Logo -->
                    <svg class="partner-logo" viewBox="0 0 100 35" width="55" height="16">
                      <text x="10" y="25" font-family="Arial, sans-serif" font-weight="900" fill="#1c244b" font-size="24">QRIS</text>
                      <rect width="18" height="18" x="72" y="8.5" fill="none" stroke="#1c244b" stroke-width="2"/>
                      <rect width="8" height="8" x="77" y="13.5" fill="#1c244b"/>
                    </svg>
                  </div>
                </div>
                <div class="option-right accordion-toggle-indicator" v-else>
                  <span class="expanded-chevron">▲</span>
                </div>
              </div>

              <!-- Accordion Content Panel -->
              <div class="accordion-panel" v-if="selectedMethod === 'qris'">
                <div class="qris-payment-block animate-fade-in">
                  <p class="panel-instruction text-center">Scan QR Code di bawah menggunakan aplikasi e-wallet Anda:</p>
                  
                  <div class="qris-qr-wrapper">
                    <!-- QRIS Mock Code -->
                    <svg viewBox="0 0 100 100" width="160" height="160" class="qris-qr-code">
                      <rect width="100" height="100" fill="#ffffff" stroke="#e2e8f0" stroke-width="1" rx="8"/>
                      <!-- Corners -->
                      <rect x="8" y="8" width="24" height="24" fill="#1e293b"/>
                      <rect x="12" y="12" width="16" height="16" fill="#ffffff"/>
                      <rect x="16" y="16" width="8" height="8" fill="#1e293b"/>
                      
                      <rect x="68" y="8" width="24" height="24" fill="#1e293b"/>
                      <rect x="72" y="12" width="16" height="16" fill="#ffffff"/>
                      <rect x="76" y="16" width="8" height="8" fill="#1e293b"/>
                      
                      <rect x="8" y="68" width="24" height="24" fill="#1e293b"/>
                      <rect x="12" y="72" width="16" height="16" fill="#ffffff"/>
                      <rect x="16" y="76" width="8" height="8" fill="#1e293b"/>
                      
                      <!-- Alignment -->
                      <rect x="68" y="68" width="8" height="8" fill="#1e293b"/>
                      <rect x="76" y="76" width="8" height="8" fill="#1e293b"/>
                      <rect x="84" y="68" width="8" height="8" fill="#1e293b"/>
                      <rect x="68" y="84" width="8" height="8" fill="#1e293b"/>
                      
                      <!-- Data Modules -->
                      <rect x="36" y="8" width="4" height="12" fill="#1e293b"/>
                      <rect x="44" y="16" width="8" height="4" fill="#1e293b"/>
                      <rect x="36" y="24" width="12" height="4" fill="#1e293b"/>
                      <rect x="8" y="36" width="12" height="4" fill="#1e293b"/>
                      <rect x="16" y="44" width="4" height="8" fill="#1e293b"/>
                      <rect x="24" y="36" width="8" height="8" fill="#1e293b"/>
                      
                      <rect x="36" y="36" width="16" height="16" fill="#1e293b"/>
                      <rect x="40" y="40" width="8" height="8" fill="#ffffff"/>
                      
                      <rect x="56" y="8" width="4" height="20" fill="#1e293b"/>
                      <rect x="60" y="24" width="4" height="8" fill="#1e293b"/>
                      <rect x="56" y="36" width="8" height="4" fill="#1e293b"/>
                      <rect x="60" y="44" width="24" height="4" fill="#1e293b"/>
                      <rect x="80" y="36" width="12" height="4" fill="#1e293b"/>
                      
                      <rect x="36" y="56" width="4" height="8" fill="#1e293b"/>
                      <rect x="44" y="60" width="16" height="4" fill="#1e293b"/>
                      <rect x="52" y="52" width="4" height="16" fill="#1e293b"/>
                      <rect x="8" y="56" width="8" height="4" fill="#1e293b"/>
                      <rect x="20" y="60" width="4" height="8" fill="#1e293b"/>
                      
                      <rect x="68" y="56" width="12" height="4" fill="#1e293b"/>
                      <rect x="84" y="48" width="4" height="12" fill="#1e293b"/>
                      
                      <rect x="36" y="68" width="4" height="16" fill="#1e293b"/>
                      <rect x="44" y="76" width="12" height="8" fill="#1e293b"/>
                      <rect x="60" y="68" width="4" height="4" fill="#1e293b"/>
                      <rect x="60" y="80" width="8" height="4" fill="#1e293b"/>
                      <rect x="8" y="8" width="4" height="4" fill="#1e293b"/>
                      
                      <!-- Center Logo block -->
                      <rect x="40" y="40" width="20" height="20" rx="3" fill="#ffffff" stroke="#1e293b" stroke-width="1.5"/>
                      <text x="50" y="52" font-family="Arial, sans-serif" font-weight="900" fill="#1c244b" font-size="8" text-anchor="middle">QRIS</text>
                    </svg>
                  </div>
                  
                  <div class="qris-steps">
                    <ol>
                      <li>Buka aplikasi Gopay, OVO, DANA, LinkAja, atau Mobile Banking Anda.</li>
                      <li>Pilih opsi <strong>Scan / Bayar</strong> lalu arahkan kamera ke QR Code di atas.</li>
                      <li>Masukkan PIN Anda dan selesaikan pembayaran.</li>
                    </ol>
                  </div>
                </div>
              </div>
            </div>

          </div>

          <!-- Notice Alert Box -->
          <div class="notice-alert-banner">
            <Info :size="20" class="alert-icon" />
            <p class="alert-text">
              Setelah pembayaran berhasil, pesanan Anda akan segera diproses. Pastikan menyelesaikan pembayaran sebelum waktu batas agar pesanan tidak dibatalkan.
            </p>
          </div>

          <!-- Card Footer Security Badges -->
          <div class="card-footer-row">
            <div class="footer-left">
              <Lock :size="14" class="lock-icon" />
              <span>Transaksi aman dan terenkripsi</span>
            </div>
            <div class="footer-right">
              <!-- PCI-DSS -->
              <svg viewBox="0 0 120 30" width="80" height="20" class="compliance-badge">
                <rect width="120" height="30" rx="4" fill="#1e293b"/>
                <text x="60" y="19" font-family="Arial, sans-serif" font-weight="800" fill="#ffffff" font-size="9" text-anchor="middle" letter-spacing="0.5">PCI-DSS COMPLIANT</text>
              </svg>
              <!-- Secure -->
              <svg viewBox="0 0 100 30" width="60" height="20" class="compliance-badge">
                <rect width="100" height="30" rx="4" fill="#0f766e"/>
                <text x="50" y="19" font-family="Arial, sans-serif" font-weight="800" fill="#ffffff" font-size="9" text-anchor="middle">SECURE 256-BIT</text>
              </svg>
              <!-- Verified by VISA -->
              <svg viewBox="0 0 120 30" width="80" height="20" class="compliance-badge">
                <text x="5" y="19" font-family="Arial, sans-serif" font-weight="800" font-style="italic" fill="#1a1f71" font-size="12">Visa</text>
                <text x="35" y="19" font-family="Arial, sans-serif" font-weight="700" fill="#64748b" font-size="9">SECURE</text>
              </svg>
              <!-- Mastercard SecureCode -->
              <svg viewBox="0 0 120 30" width="85" height="20" class="compliance-badge">
                <circle cx="15" cy="15" r="7" fill="#eb001b" opacity="0.8"/>
                <circle cx="24" cy="15" r="7" fill="#ff5f00" opacity="0.8"/>
                <text x="36" y="18" font-family="Arial, sans-serif" font-weight="700" fill="#64748b" font-size="8">Identity Check</text>
              </svg>
            </div>
          </div>
        </div>

        <!-- 3. Right Column: Ringkasan Pengiriman Sidebar -->
        <div class="summary-sidebar-container">
          <div class="summary-sidebar-card">
            <h2 class="sidebar-title">Ringkasan Pengiriman</h2>

            <!-- Visual Route Timeline -->
            <div class="route-timeline">
              <div class="timeline-point origin">
                <div class="point-circle green"></div>
                <div class="point-details">
                  <span class="location-name">{{ originName }}</span>
                  <span class="location-role">Kota Asal</span>
                </div>
              </div>
              
              <div class="timeline-connector"></div>

              <div class="timeline-point destination">
                <div class="point-circle blue">
                  <MapPin :size="10" />
                </div>
                <div class="point-details">
                  <span class="location-name">{{ destinationName }}</span>
                  <span class="location-role">Kota Tujuan</span>
                </div>
              </div>
            </div>

            <!-- Specs List -->
            <div class="specs-grid">
              <div class="spec-item">
                <span class="label">Berat Paket</span>
                <span class="value">{{ formattedWeight }}</span>
              </div>
              <div class="spec-item">
                <span class="label">Dimensi</span>
                <span class="value">{{ dimensionsText }}</span>
              </div>
              <div class="spec-item">
                <span class="label">Layanan</span>
                <span class="value">
                  <span class="service-capsule">{{ serviceLabel }}</span>
                </span>
              </div>
              <div class="spec-item">
                <span class="label">Estimasi Sampai</span>
                <span class="value">{{ serviceEta }}</span>
              </div>
            </div>

            <hr class="card-divider" />

            <!-- Cost Breakdown -->
            <div class="cost-breakdown">
              <div class="cost-item">
                <span class="label">Ongkos Kirim</span>
                <span class="value">{{ formatPrice(ongkosKirim) }}</span>
              </div>
              <div class="cost-item relative-tooltip">
                <span class="label flex-align">
                  Asuransi 
                  <span class="info-trigger" @mouseenter="showInsuranceTooltip = true" @mouseleave="showInsuranceTooltip = false">
                    <Info :size="12" class="info-icon" />
                    <span v-if="showInsuranceTooltip" class="tooltip-bubble">
                      Asuransi paket untuk perlindungan penuh kerusakan/kehilangan hingga Rp 10.000.000.
                    </span>
                  </span>
                </span>
                <span class="value">{{ formatPrice(asuransi) }}</span>
              </div>
              <div class="cost-item">
                <span class="label">Biaya Admin</span>
                <span class="value">{{ formatPrice(biayaAdmin) }}</span>
              </div>
            </div>

            <hr class="card-divider" />

            <!-- Total Payment Row -->
            <div class="total-payment-row">
              <span class="label">Total Pembayaran</span>
              <span class="price">{{ formatPrice(totalPembayaran) }}</span>
            </div>

            <!-- Security Trust Badge -->
            <div class="security-banner">
              <ShieldCheck :size="18" class="shield-icon" />
              <div class="banner-content">
                <strong>Keamanan Terjamin</strong> - Setiap transaksi dilindungi sistem keamanan berlapis.
              </div>
            </div>

            <!-- Support Row -->
            <div class="support-footer">
              <span class="support-label">Butuh bantuan?</span>
              <div class="support-flex">
                <div class="support-whatsapp">
                  <!-- Custom WhatsApp icon -->
                  <svg viewBox="0 0 24 24" width="20" height="20" fill="currentColor" class="wa-icon">
                    <path d="M12.012 2c-5.506 0-9.989 4.478-9.99 9.984a9.96 9.96 0 0 0 1.335 4.975L2 22l5.233-1.371a9.92 9.92 0 0 0 4.773 1.226h.004c5.505 0 9.99-4.477 9.99-9.985C22.004 6.478 17.518 2 12.012 2zm6.155 14.15c-.267.75-1.554 1.376-2.127 1.45-.51.066-1.176.108-3.418-.82-2.866-1.186-4.66-4.11-4.803-4.301-.143-.19-1.157-1.539-1.157-2.93 0-1.39.728-2.073.987-2.352.26-.28.567-.35.755-.35.188 0 .376.002.538.01.168.008.397-.064.623.491.228.558.78 1.902.847 2.038.068.136.113.292.022.473-.09.18-.135.292-.27.45-.136.158-.285.352-.407.472-.136.136-.278.285-.12.558.158.27.7 1.15 1.503 1.865.803.714 1.482.935 1.693 1.026.21.09.332.076.457-.069.124-.144.538-.626.682-.84.143-.21.285-.18.48-.105.195.075 1.238.585 1.45.69.21.106.35.158.403.248.053.09.053.524-.214 1.274z"/>
                  </svg>
                  <span>Hubungi kami melalui WhatsApp atau customer support 24/7.</span>
                </div>
                <div class="headset-badge-icon">
                  <svg viewBox="0 0 24 24" width="18" height="18" fill="none" stroke="currentColor" stroke-width="2">
                    <path d="M3 18v-6a9 9 0 0 1 18 0v6"/>
                    <path d="M21 19a2 2 0 0 1-2 2h-1a2 2 0 0 1-2-2v-3a2 2 0 0 1 2-2h3zM3 19a2 2 0 0 0 2 2h1a2 2 0 0 0 2-2v-3a2 2 0 0 0-2-2H3z"/>
                  </svg>
                </div>
              </div>
            </div>
          </div>
        </div>

      </div>

      <!-- 4. Sticky Bottom Action Bar -->
      <div class="sticky-action-bar animate-fade-in" v-if="paymentStatus !== 'success'">
        <div class="bottom-bar-wrapper">
          <div class="watermark-area">
            <div class="green-check-shield">
              <svg viewBox="0 0 24 24" width="14" height="14" fill="none" stroke="currentColor" stroke-width="3">
                <polyline points="20 6 9 17 4 12"/>
              </svg>
            </div>
            <span>Proses ini diawasi oleh <strong class="brand-kolektik">Kolektik</strong></span>
          </div>

          <button 
            @click="processPayment" 
            class="pay-now-btn"
            :disabled="isProcessing"
          >
            <span v-if="isProcessing" class="spinner"></span>
            <Lock :size="16" v-else class="btn-lock-icon" />
            <span>{{ isProcessing ? 'Memproses Pembayaran...' : 'Bayar Sekarang' }}</span>
            <span class="btn-arrow" v-if="!isProcessing">→</span>
          </button>
        </div>
      </div>

      <!-- 5. Payment Success State Screen -->
      <div class="payment-success-screen animate-fade-in" v-if="paymentStatus === 'success'">
        <div class="success-card">
          <div class="success-icon-container">
            <div class="icon-pulse"></div>
            <div class="checkmark-circle">
              <svg viewBox="0 0 24 24" class="checkmark-svg" fill="none" stroke="currentColor" stroke-width="3">
                <polyline points="20 6 9 17 4 12"/>
              </svg>
            </div>
          </div>

          <h2 class="success-title">Pembayaran Berhasil!</h2>
          <p class="success-subtitle">
            Terima kasih! Pembayaran Anda telah kami terima dan pengiriman Anda sedang segera diproses.
          </p>

          <!-- Booking Code Box -->
          <div class="booking-code-card">
            <span class="code-label">KODE BOOKING</span>
            <div class="code-text">{{ bookingCode }}</div>
          </div>

          <!-- Recap Summary Container -->
          <div class="recap-container">
            <h3 class="recap-title">Rincian Pengiriman</h3>
            
            <div class="recap-grid-info">
              <div class="recap-col">
                <div class="recap-item">
                  <span class="label">Pengirim</span>
                  <span class="val-bold">{{ senderName || 'Nama Pengirim' }}</span>
                  <span class="val-sub">{{ senderPhone || '08xxxxxxxxxx' }}</span>
                </div>
                <div class="recap-item mt-4">
                  <span class="label">Rute Pengiriman</span>
                  <span class="val-bold">{{ originName }} → {{ destinationName }}</span>
                </div>
              </div>
              
              <div class="recap-col">
                <div class="recap-item">
                  <span class="label">Penerima</span>
                  <span class="val-bold">{{ receiverName || 'Nama Penerima' }}</span>
                  <span class="val-sub">{{ receiverPhone || '08xxxxxxxxxx' }}</span>
                </div>
                <div class="recap-item mt-4">
                  <span class="label">Jadwal Penjemputan</span>
                  <span class="val-bold">{{ pickupDate }}</span>
                  <span class="val-sub">Jam {{ pickupTime }}</span>
                </div>
              </div>
            </div>

            <hr class="recap-divider" />

            <div class="recap-bottom-row">
              <div class="recap-service">
                <span class="label">Layanan</span>
                <span class="service-badge">{{ serviceLabel }}</span>
              </div>
              <div class="recap-total">
                <span class="label">Total Pembayaran</span>
                <span class="total-val">{{ formatPrice(totalPembayaran) }}</span>
              </div>
            </div>
          </div>

          <!-- Action Buttons -->
          <div class="actions-row">
            <button @click="backToNewShipment" class="btn btn-outline new-shipment-btn">
              Buat Pengiriman Lain
            </button>
            <button @click="backToHome" class="btn btn-primary home-btn">
              Kembali ke Beranda
            </button>
          </div>
        </div>
      </div>

    </div>
  </div>
</template>

<script>
import { ref, onMounted, computed } from 'vue';
import { ArrowLeft, CreditCard, Lock, ShieldCheck, MapPin, Info, ArrowRight } from '@lucide/vue';

export default {
  name: 'PaymentPage',
  components: {
    ArrowLeft,
    CreditCard,
    Lock,
    ShieldCheck,
    MapPin,
    Info,
    ArrowRight
  },
  setup() {
    // Current selected payment method (accordion row state)
    const selectedMethod = ref('transfer-bank');
    
    // Sub-method selection inside accordion
    const selectedSubMethod = ref('bca-transfer');

    // Credit Card Form states
    const cardNumber = ref('');
    const cardExpiry = ref('');
    const cardCvv = ref('');
    const cardName = ref('');

    // E-Wallet Phone state
    const walletPhone = ref('');

    // Process indicators
    const isProcessing = ref(false);
    const paymentStatus = ref('pending'); // pending, success
    const bookingCode = ref('');
    const showInsuranceTooltip = ref(false);

    // Form inputs state to fall back or display from localStorage
    const activeTab = ref('domestik');
    const senderName = ref('');
    const senderPhone = ref('');
    const senderCity = ref('');
    const senderAddress = ref('');
    const receiverName = ref('');
    const receiverPhone = ref('');
    const receiverCity = ref('');
    const receiverAddress = ref('');
    const senderCountry = ref('');
    const receiverCountry = ref('');
    const packageWeight = ref(1);
    const packageLength = ref(20);
    const packageWidth = ref(15);
    const packageHeight = ref(10);
    const selectedService = ref('instant');
    const pickupDate = ref('22 Juni 2026');
    const pickupTime = ref('14:00 - 16:00');
    
    // Cost breakdown fallbacks
    const ongkosKirim = ref(45000);
    const asuransi = ref(1000);
    const biayaAdmin = ref(1000);

    // Regions mapping list for name extraction
    const regions = [
      { subdistrict: 'Rawasari', city: 'Jakarta Pusat', value: 'rawasari' },
      { subdistrict: 'Selong', city: 'Jakarta Selatan', value: 'selong' },
      { subdistrict: 'Senayan', city: 'Jakarta Selatan', value: 'senayan' },
      { subdistrict: 'Menteng', city: 'Jakarta Pusat', value: 'menteng' },
      { subdistrict: 'Gambir', city: 'Jakarta Pusat', value: 'gambir' },
      { subdistrict: 'Kelapa Gading Barat', city: 'Jakarta Utara', value: 'kelapa-gading' },
      { subdistrict: 'Dago', city: 'Bandung', value: 'dago' },
      { subdistrict: 'Sarijadi', city: 'Bandung', value: 'sarijadi' },
      { subdistrict: 'Pasirkaliki', city: 'Bandung', value: 'pasirkaliki' },
      { subdistrict: 'Gubeng', city: 'Surabaya', value: 'gubeng' },
      { subdistrict: 'Keputih', city: 'Surabaya', value: 'keputih' },
      { subdistrict: 'Tegalsari', city: 'Surabaya', value: 'tegalsari' },
      { subdistrict: 'Klitren', city: 'Yogyakarta', value: 'klitren' },
      { subdistrict: 'Sosromenduran', city: 'Yogyakarta', value: 'sosromenduran' },
      { subdistrict: 'Sekaran', city: 'Semarang', value: 'sekaran' },
      { subdistrict: 'Mugasari', city: 'Semarang', value: 'mugasari' },
      { subdistrict: 'Kesawan', city: 'Medan', value: 'kesawan' },
      { subdistrict: 'Babura', city: 'Medan', value: 'babura' },
      { subdistrict: 'Malimongan Baru', city: 'Makassar', value: 'malimongan' },
      { subdistrict: 'Losari', city: 'Makassar', value: 'losari' },
      { subdistrict: 'Sanur', city: 'Denpasar', value: 'sanur' },
      { subdistrict: 'Renon', city: 'Denpasar', value: 'renon' },
      { subdistrict: 'Talang Semut', city: 'Palembang', value: 'talang-semut' },
      { subdistrict: 'Klandasan Ulu', city: 'Balikpapan', value: 'klandasan' }
    ];

    const countries = [
      { code: 'ID', name: 'Indonesia' },
      { code: 'SG', name: 'Singapura' },
      { code: 'MY', name: 'Malaysia' },
      { code: 'TH', name: 'Thailand' },
      { code: 'PH', name: 'Filipina' },
      { code: 'VN', name: 'Vietnam' },
      { code: 'MM', name: 'Myanmar' },
      { code: 'KH', name: 'Kamboja' },
      { code: 'JP', name: 'Jepang' },
      { code: 'CN', name: 'Cina' },
      { code: 'KR', name: 'Korea Selatan' },
      { code: 'HK', name: 'Hong Kong' },
      { code: 'TW', name: 'Taiwan' },
      { code: 'AU', name: 'Australia' },
      { code: 'NZ', name: 'Selandia Baru' },
      { code: 'IN', name: 'India' },
      { code: 'SA', name: 'Arab Saudi' },
      { code: 'AE', name: 'Uni Emirat Arab' },
      { code: 'US', name: 'Amerika Serikat' },
      { code: 'GB', name: 'Inggris' },
      { code: 'DE', name: 'Jerman' },
      { code: 'FR', name: 'Prancis' },
    ];

    const getCityName = (cityVal) => {
      const region = regions.find(r => r.value === cityVal);
      return region ? region.subdistrict : '';
    };

    const getCountryName = (code) => {
      const c = countries.find(c => c.code === code);
      return c ? c.name : '';
    };

    // Load data from localStorage on mount
    onMounted(() => {
      const saved = localStorage.getItem('kiriminyuk_shipping_details');
      if (saved) {
        try {
          const details = JSON.parse(saved);
          activeTab.value = details.activeTab || 'domestik';
          senderName.value = details.senderName || '';
          senderPhone.value = details.senderPhone || '';
          senderCity.value = details.senderCity || '';
          senderAddress.value = details.senderAddress || '';
          receiverName.value = details.receiverName || '';
          receiverPhone.value = details.receiverPhone || '';
          receiverCity.value = details.receiverCity || '';
          receiverAddress.value = details.receiverAddress || '';
          senderCountry.value = details.senderCountry || '';
          receiverCountry.value = details.receiverCountry || '';
          packageWeight.value = details.packageWeight || 1;
          packageLength.value = details.packageLength || 20;
          packageWidth.value = details.packageWidth || 15;
          packageHeight.value = details.packageHeight || 10;
          selectedService.value = details.selectedService || 'instant';
          pickupDate.value = details.pickupDate || '22 Juni 2026';
          pickupTime.value = details.pickupTime || '14:00 - 16:00';
          
          // Cost breakdowns
          ongkosKirim.value = details.calculatedOngkir || 45000;
        } catch (e) {
          console.error('Failed to parse details', e);
        }
      }
    });

    // Helper functions for bank transfer / VA mock numbers
    const getAccountNumber = (bank) => {
      const numbers = {
        'bca-transfer': '88301081234567',
        'mandiri-transfer': '1370012345678',
        'bri-transfer': '002101001234563'
      };
      return numbers[bank] || '88301081234567';
    };

    const getVaNumber = (bank) => {
      const numbers = {
        'bca-va': '8077708123456789',
        'mandiri-va': '8902208123456789',
        'bri-va': '1280081234567890',
        'bni-va': '8274081234567890'
      };
      return numbers[bank] || '8077708123456789';
    };

    const copyText = (text) => {
      navigator.clipboard.writeText(text).then(() => {
        alert('Berhasil disalin: ' + text);
      });
    };

    const selectMethod = (method, defaultSubMethod) => {
      selectedMethod.value = method;
      selectedSubMethod.value = defaultSubMethod;
    };

    // Computed Origin and Destination Names
    const originName = computed(() => {
      if (activeTab.value === 'domestik') {
        return getCityName(senderCity.value) || 'Senayan';
      } else {
        return getCountryName(senderCountry.value) || 'Indonesia';
      }
    });

    const destinationName = computed(() => {
      if (activeTab.value === 'domestik') {
        return getCityName(receiverCity.value) || 'Pasirkaliki';
      } else {
        return getCountryName(receiverCountry.value) || 'Singapura';
      }
    });

    // Formatted Weight
    const formattedWeight = computed(() => {
      if (activeTab.value === 'domestik') {
        return `${packageWeight.value} Kg`;
      } else {
        return `${packageWeight.value} Gram`;
      }
    });

    // Dimensions
    const dimensionsText = computed(() => {
      return `${packageLength.value} x ${packageWidth.value} x ${packageHeight.value} cm`;
    });

    // Service label
    const serviceLabel = computed(() => {
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

    // Service ETA
    const serviceEta = computed(() => {
      const etas = {
        instant: '2 - 4 Jam',
        sameday: 'Hari ini (6-10 jam)',
        nextday: 'Besok (1 hari)',
        cargo: '2-3 hari',
        'intl-express': '3-5 hari kerja',
        'intl-standard': '7-14 hari kerja',
        'intl-economy': '14-21 hari kerja'
      };
      if (selectedService.value === 'instant') return '2 - 4 Jam';
      return etas[selectedService.value] || '2 - 4 Jam';
    });

    // Calculations
    const totalPembayaran = computed(() => {
      return ongkosKirim.value + asuransi.value + biayaAdmin.value;
    });

    const formatPrice = (val) => {
      return new Intl.NumberFormat('id-ID', {
        style: 'currency',
        currency: 'IDR',
        minimumFractionDigits: 0,
        maximumFractionDigits: 0
      }).format(val);
    };

    // Payment trigger
    const processPayment = () => {
      isProcessing.value = true;
      setTimeout(() => {
        isProcessing.value = false;
        
        // Generate booking code
        const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
        let code = 'KMY-';
        for (let i = 0; i < 8; i++) {
          code += chars.charAt(Math.floor(Math.random() * chars.length));
        }
        bookingCode.value = code;
        paymentStatus.value = 'success';
        
        // Scroll to top
        window.scrollTo({ top: 0, behavior: 'smooth' });
      }, 1500);
    };

    const backToNewShipment = () => {
      localStorage.removeItem('kiriminyuk_shipping_details');
      window.location.hash = '#shipping-form';
      setTimeout(() => {
        window.location.reload();
      }, 50);
    };

    const backToHome = () => {
      localStorage.removeItem('kiriminyuk_shipping_details');
      window.location.hash = '#';
      setTimeout(() => {
        window.location.reload();
      }, 50);
    };

    return {
      selectedMethod,
      selectedSubMethod,
      cardNumber,
      cardExpiry,
      cardCvv,
      cardName,
      walletPhone,
      isProcessing,
      paymentStatus,
      bookingCode,
      showInsuranceTooltip,
      activeTab,
      senderName,
      senderPhone,
      receiverName,
      receiverPhone,
      pickupDate,
      pickupTime,
      ongkosKirim,
      asuransi,
      biayaAdmin,
      originName,
      destinationName,
      formattedWeight,
      dimensionsText,
      serviceLabel,
      serviceEta,
      totalPembayaran,
      formatPrice,
      processPayment,
      backToNewShipment,
      backToHome,
      getAccountNumber,
      getVaNumber,
      copyText,
      selectMethod
    };
  }
};
</script>

<style scoped>
.payment-checkout-page {
  background-color: var(--bg-light);
  min-height: 100vh;
  padding-top: 120px;
  padding-bottom: 140px;
  text-align: left;
}

.checkout-container {
  max-width: var(--container-width);
}

.checkout-header-block {
  margin-bottom: 24px;
}

.back-navigation {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  font-size: 14px;
  font-weight: 500;
  color: var(--text-light);
  margin-bottom: 16px;
  transition: color var(--transition-fast);
}

.back-navigation:hover {
  color: var(--primary);
}

.back-icon {
  margin-top: 1px;
}

.main-heading {
  font-size: 32px;
  font-weight: 800;
  color: #0c2340; /* Navy blue */
  margin-bottom: 6px;
}

.sub-heading-text {
  font-size: 15px;
  color: var(--text-muted);
}

/* 2-column grid layout */
.checkout-main-grid {
  display: grid;
  grid-template-columns: 2fr 1.1fr;
  gap: 32px;
  align-items: start;
}

/* Left Column Styling */
.payment-methods-card {
  background-color: var(--bg-card);
  border: 1px solid var(--border-color);
  border-radius: var(--radius-lg);
  box-shadow: var(--shadow-sm);
  padding: 32px;
}

.payment-methods-card .card-header {
  display: flex;
  align-items: center;
  gap: 12px;
  border-bottom: 1px solid var(--border-color);
  padding-bottom: 20px;
  margin-bottom: 24px;
}

.payment-methods-card .header-icon {
  color: var(--primary);
}

.payment-methods-card .header-title {
  font-size: 18px;
  font-weight: 700;
  color: var(--text-dark);
}

/* Accordion Wrapper */
.payment-options-list {
  display: flex;
  flex-direction: column;
  gap: 16px;
  margin-bottom: 24px;
}

.accordion-item {
  border: 1px solid var(--border-color);
  border-radius: var(--radius-md);
  overflow: hidden;
  transition: all var(--transition-normal);
  background-color: var(--bg-card);
}

.accordion-item.expanded {
  border-color: var(--primary);
  box-shadow: 0 4px 12px rgba(10, 101, 255, 0.05);
}

.payment-option-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 16px 20px;
  cursor: pointer;
  background-color: var(--bg-card);
  min-height: 72px;
  transition: background-color var(--transition-fast);
}

.payment-option-row:hover {
  background-color: #fafafa;
}

.payment-option-row.selected {
  background-color: rgba(10, 101, 255, 0.01);
}

.option-left {
  display: flex;
  align-items: center;
  gap: 16px;
  flex-grow: 1;
}

/* Custom Radio Circle */
.custom-radio {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  border: 2px solid #cbd5e1;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  transition: border-color var(--transition-fast);
}

.payment-option-row:hover .custom-radio {
  border-color: #94a3b8;
}

.payment-option-row.selected .custom-radio {
  border-color: var(--primary);
}

.radio-inner {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  background-color: var(--primary);
}

.method-icon-wrapper {
  color: var(--text-light);
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  width: 36px;
  height: 36px;
  border-radius: var(--radius-sm);
  background-color: var(--bg-light);
}

.payment-option-row.selected .method-icon-wrapper {
  color: var(--primary);
  background-color: var(--primary-light);
}

.method-info {
  display: flex;
  flex-direction: column;
  text-align: left;
}

.method-name {
  font-size: 15px;
  font-weight: 700;
  color: var(--text-dark);
}

.method-desc {
  font-size: 12.5px;
  color: var(--text-muted);
}

.option-right {
  display: flex;
  align-items: center;
}

.partner-logos {
  display: flex;
  align-items: center;
  gap: 12px;
}

.partner-logo {
  object-fit: contain;
  opacity: 0.85;
  transition: opacity 0.2s;
  flex-shrink: 0;
}

.partner-logo:hover {
  opacity: 1;
}

.more-link {
  font-size: 12px;
  font-weight: 600;
  color: var(--text-light);
  display: inline-flex;
  align-items: center;
  gap: 4px;
}

.accordion-toggle-indicator {
  color: var(--text-light);
  font-size: 11px;
  font-weight: bold;
}

.expanded-chevron {
  color: var(--primary);
}

/* Accordion expanded panel */
.accordion-panel {
  padding: 20px;
  background-color: var(--bg-light);
  border-top: 1px solid var(--border-color);
  animation: slideDown 0.3s ease-out forwards;
}

@keyframes slideDown {
  from {
    opacity: 0;
    transform: translateY(-8px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.panel-instruction {
  font-size: 13.5px;
  font-weight: 600;
  color: var(--text-dark);
  margin-bottom: 12px;
}

/* Sub Options Selector Grid */
.sub-options-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(130px, 1fr));
  gap: 12px;
  margin-bottom: 16px;
}

.sub-option-card {
  background-color: white;
  border: 1.5px solid var(--border-color);
  border-radius: var(--radius-sm);
  padding: 14px;
  cursor: pointer;
  display: flex;
  flex-direction: column;
  text-align: left;
  transition: all var(--transition-fast);
}

.sub-option-card:hover {
  border-color: #cbd5e1;
  transform: translateY(-1px);
}

.sub-option-card.active {
  border-color: var(--primary);
  background-color: rgba(10, 101, 255, 0.02);
}

.card-logo-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
  height: 20px;
}

.sub-check {
  width: 16px;
  height: 16px;
  border-radius: 50%;
  background-color: var(--primary);
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 9px;
  font-weight: bold;
}

.bank-name {
  font-size: 13px;
  font-weight: 700;
  color: var(--text-dark);
  margin-bottom: 2px;
}

.bank-desc {
  font-size: 10.5px;
  color: var(--text-light);
}

/* Bank Details Area */
.bank-transfer-details {
  background-color: white;
  border: 1px solid var(--border-color);
  border-radius: var(--radius-sm);
  padding: 16px;
  margin-top: 12px;
}

.info-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: 13.5px;
}

.info-label {
  color: var(--text-muted);
}

.copy-flex {
  display: flex;
  align-items: center;
  gap: 12px;
}

.account-number {
  font-family: var(--font-heading);
  font-size: 15px;
  font-weight: 700;
  color: var(--text-dark);
}

.account-holder {
  font-weight: 700;
  color: var(--text-dark);
}

.copy-btn {
  padding: 4px 10px;
  background-color: var(--primary-light);
  color: var(--primary);
  border-radius: var(--radius-sm);
  font-size: 11.5px;
  font-weight: 700;
  border: none;
  cursor: pointer;
  transition: background-color var(--transition-fast);
}

.copy-btn:hover {
  background-color: var(--primary-light-hover);
}

/* E-Wallet Phone Form */
.ewallet-phone-form {
  background-color: white;
  border: 1px solid var(--border-color);
  border-radius: var(--radius-sm);
  padding: 16px;
  margin-top: 12px;
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.input-label-text {
  font-size: 13px;
  font-weight: 700;
  color: var(--text-dark);
}

.phone-input-wrapper {
  display: flex;
  align-items: center;
  border: 1.5px solid var(--border-color);
  border-radius: var(--radius-sm);
  overflow: hidden;
  height: 44px;
}

.phone-input-wrapper:focus-within {
  border-color: var(--primary);
}

.country-prefix {
  background-color: var(--bg-light);
  color: var(--text-muted);
  font-weight: 700;
  font-size: 14px;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0 12px;
  height: 100%;
  border-right: 1px solid var(--border-color);
}

.tel-input-box {
  flex-grow: 1;
  padding: 0 12px;
  font-size: 14px;
  font-weight: 600;
  color: var(--text-dark);
  height: 100%;
}

.tel-input-box::placeholder {
  color: #94a3b8;
  font-weight: 400;
}

.phone-disclaimer {
  font-size: 11px;
  color: var(--text-light);
  line-height: 1.4;
}

/* Credit Card Form fields */
.credit-card-form {
  background-color: white;
  border: 1px solid var(--border-color);
  border-radius: var(--radius-sm);
  padding: 16px;
  margin-top: 12px;
  display: flex;
  flex-direction: column;
}

.input-field {
  display: flex;
  flex-direction: column;
  gap: 6px;
}

.form-label {
  font-size: 12.5px;
  font-weight: 700;
  color: var(--text-dark);
}

.input-icon-flex {
  display: flex;
  align-items: center;
  border: 1.5px solid var(--border-color);
  border-radius: var(--radius-sm);
  overflow: hidden;
  height: 44px;
  position: relative;
}

.input-icon-flex:focus-within {
  border-color: var(--primary);
}

.form-input-text {
  flex-grow: 1;
  padding: 0 12px;
  font-size: 14px;
  height: 100%;
  font-weight: 600;
}

.form-input-text::placeholder {
  color: #94a3b8;
  font-weight: 400;
}

.card-brand-indicator {
  padding-right: 12px;
  display: flex;
  align-items: center;
}

.default-card-text {
  font-size: 10px;
  font-weight: 800;
  color: var(--text-light);
  background-color: var(--bg-light);
  padding: 2px 6px;
  border-radius: 2px;
}

.form-cols-2 {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
}

/* QRIS Code payment styles */
.qris-payment-block {
  background-color: white;
  border: 1px solid var(--border-color);
  border-radius: var(--radius-sm);
  padding: 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.qris-qr-wrapper {
  margin: 16px 0;
  display: flex;
  justify-content: center;
}

.qris-qr-code {
  box-shadow: var(--shadow-sm);
}

.qris-steps {
  width: 100%;
  text-align: left;
  background-color: var(--bg-light);
  padding: 12px 16px;
  border-radius: var(--radius-sm);
}

.qris-steps ol {
  padding-left: 20px;
  font-size: 12.5px;
  color: var(--text-muted);
  display: flex;
  flex-direction: column;
  gap: 8px;
}

/* Notice Alert Banner */
.notice-alert-banner {
  display: flex;
  gap: 14px;
  padding: 16px 20px;
  background-color: rgba(10, 101, 255, 0.05);
  border-radius: var(--radius-md);
  margin-bottom: 24px;
}

.alert-icon {
  color: var(--primary);
  flex-shrink: 0;
  margin-top: 2px;
}

.alert-text {
  font-size: 13.5px;
  color: var(--text-muted);
  line-height: 1.5;
  text-align: left;
}

/* Card Footer Security Row */
.card-footer-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-top: 1.5px dashed var(--border-color);
  padding-top: 24px;
  margin-top: 8px;
}

.card-footer-row .footer-left {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 13px;
  color: var(--text-light);
  font-weight: 500;
}

.card-footer-row .lock-icon {
  color: var(--text-light);
}

.card-footer-row .footer-right {
  display: flex;
  align-items: center;
  gap: 12px;
}

.compliance-badge {
  flex-shrink: 0;
}

/* Right Column: Ringkasan Pengiriman Sidebar */
.summary-sidebar-container {
  display: flex;
  flex-direction: column;
}

.summary-sidebar-card {
  background-color: var(--bg-card);
  border: 1px solid var(--border-color);
  border-radius: var(--radius-lg);
  box-shadow: var(--shadow-sm);
  padding: 24px;
  position: sticky;
  top: 100px;
  z-index: 10;
}

.sidebar-title {
  font-size: 18px;
  font-weight: 700;
  color: var(--text-dark);
  margin-bottom: 24px;
}

/* Visual Route Timeline */
.route-timeline {
  display: flex;
  flex-direction: column;
  position: relative;
  margin-bottom: 24px;
  padding-left: 4px;
}

.timeline-point {
  display: flex;
  align-items: flex-start;
  gap: 14px;
  z-index: 2;
}

.point-circle {
  width: 22px;
  height: 22px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  margin-top: 2px;
}

.point-circle.green {
  background-color: var(--success);
  border: 4px solid var(--success-light);
  width: 20px;
  height: 20px;
}

.point-circle.blue {
  background-color: var(--primary-light);
  color: var(--primary);
  border: 1.5px solid var(--primary);
  width: 22px;
  height: 22px;
}

.point-details {
  display: flex;
  flex-direction: column;
  text-align: left;
}

.location-name {
  font-size: 14px;
  font-weight: 700;
  color: var(--text-dark);
}

.location-role {
  font-size: 11px;
  color: var(--text-light);
  margin-top: 1px;
}

.timeline-connector {
  position: absolute;
  top: 24px;
  left: 14px;
  width: 2px;
  height: 28px;
  border-left: 2px dashed #cbd5e1;
  z-index: 1;
}

/* Specs List */
.specs-grid {
  display: flex;
  flex-direction: column;
  gap: 12px;
  margin-bottom: 20px;
}

.spec-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: 13.5px;
}

.spec-item .label {
  color: var(--text-light);
}

.spec-item .value {
  font-weight: 600;
  color: var(--text-dark);
}

.service-capsule {
  display: inline-flex;
  padding: 4px 10px;
  background-color: var(--primary-light);
  color: var(--primary);
  font-size: 11px;
  font-weight: 700;
  border-radius: var(--radius-full);
}

.card-divider {
  border: 0;
  border-top: 1px solid var(--border-color);
  margin: 16px 0;
}

/* Cost Breakdown */
.cost-breakdown {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.cost-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: 13.5px;
}

.cost-item .label {
  color: var(--text-muted);
}

.cost-item .value {
  font-weight: 600;
  color: var(--text-dark);
}

.flex-align {
  display: inline-flex;
  align-items: center;
  gap: 6px;
}

.info-trigger {
  display: inline-flex;
  align-items: center;
  cursor: pointer;
  color: var(--text-light);
}

.info-trigger:hover {
  color: var(--primary);
}

.relative-tooltip {
  position: relative;
}

.tooltip-bubble {
  position: absolute;
  bottom: 130%;
  left: 20px;
  width: 220px;
  padding: 8px 12px;
  background-color: var(--text-dark);
  color: white;
  border-radius: var(--radius-sm);
  font-size: 11px;
  line-height: 1.4;
  z-index: 100;
  box-shadow: var(--shadow-md);
  text-align: left;
  font-weight: 400;
}

.tooltip-bubble::after {
  content: '';
  position: absolute;
  top: 100%;
  left: 10px;
  border-width: 5px;
  border-style: solid;
  border-color: var(--text-dark) transparent transparent transparent;
}

/* Total Payment Row */
.total-payment-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.total-payment-row .label {
  font-size: 15px;
  font-weight: 700;
  color: var(--text-dark);
}

.total-payment-row .price {
  font-size: 20px;
  font-weight: 800;
  color: var(--primary);
}

/* Security Trust Banner */
.security-banner {
  display: flex;
  gap: 12px;
  padding: 12px 16px;
  background-color: var(--primary-light);
  border-radius: var(--radius-sm);
  margin-bottom: 24px;
}

.security-banner .shield-icon {
  color: var(--primary);
  flex-shrink: 0;
  margin-top: 1px;
}

.security-banner .banner-content {
  font-size: 12px;
  color: var(--text-muted);
  line-height: 1.5;
  text-align: left;
}

/* Support Row */
.support-footer {
  border-top: 1px solid var(--border-color);
  padding-top: 16px;
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.support-label {
  font-size: 13px;
  font-weight: 700;
  color: var(--text-dark);
  text-align: left;
}

.support-flex {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 16px;
}

.support-whatsapp {
  display: flex;
  align-items: flex-start;
  gap: 10px;
  font-size: 12px;
  color: var(--text-muted);
  line-height: 1.4;
  text-align: left;
}

.wa-icon {
  color: #25d366;
  flex-shrink: 0;
  margin-top: 2px;
}

.headset-badge-icon {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  background-color: var(--primary-light);
  color: var(--primary);
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  box-shadow: 0 2px 6px rgba(10, 101, 255, 0.15);
}

/* Sticky Bottom Action Bar */
.sticky-action-bar {
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  background-color: white;
  border-top: 1px solid var(--border-color);
  box-shadow: 0 -4px 20px rgba(0, 0, 0, 0.05);
  padding: 16px 0;
  z-index: 100;
}

.bottom-bar-wrapper {
  max-width: var(--container-width);
  margin: 0 auto;
  padding: 0 24px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.watermark-area {
  display: flex;
  align-items: center;
  gap: 10px;
  font-size: 13.5px;
  color: var(--text-muted);
}

.green-check-shield {
  width: 18px;
  height: 18px;
  border-radius: 50%;
  background-color: var(--success-light);
  color: var(--success);
  display: flex;
  align-items: center;
  justify-content: center;
  border: 1px solid var(--success);
}

.brand-kolektik {
  color: var(--primary);
  font-weight: 700;
}

.pay-now-btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  padding: 14px 32px;
  background-color: var(--primary);
  color: white;
  font-family: var(--font-heading);
  font-weight: 700;
  font-size: 15px;
  border-radius: var(--radius-md);
  transition: all var(--transition-fast);
  box-shadow: 0 4px 12px rgba(10, 101, 255, 0.25);
  border: none;
  cursor: pointer;
}

.pay-now-btn:hover {
  background-color: var(--primary-hover);
  transform: translateY(-1px);
  box-shadow: 0 6px 16px rgba(10, 101, 255, 0.35);
}

.pay-now-btn:active {
  transform: translateY(0);
}

.pay-now-btn:disabled {
  background-color: #cbd5e1;
  color: #94a3b8;
  box-shadow: none;
  cursor: not-allowed;
}

.btn-lock-icon {
  margin-right: 2px;
}

.btn-arrow {
  font-size: 16px;
  transition: transform 0.2s;
}

.pay-now-btn:hover .btn-arrow {
  transform: translateX(3px);
}

/* Spinner Animation */
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

/* 5. Success Screen */
.payment-success-screen {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 40px 0;
  max-width: 720px;
  margin: 0 auto;
}

.success-card {
  background-color: var(--bg-card);
  border: 1px solid var(--border-color);
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-lg);
  padding: 48px;
  text-align: center;
  width: 100%;
}

.success-icon-container {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 24px;
}

.icon-pulse {
  position: absolute;
  width: 80px;
  height: 80px;
  border-radius: 50%;
  background-color: var(--success-light);
  animation: pulse-success 2s infinite;
  z-index: 1;
}

.checkmark-circle {
  position: relative;
  width: 60px;
  height: 60px;
  border-radius: 50%;
  background-color: var(--success);
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 2;
  box-shadow: 0 4px 12px rgba(34, 197, 94, 0.3);
}

.checkmark-svg {
  width: 32px;
  height: 32px;
}

@keyframes pulse-success {
  0% {
    transform: scale(0.85);
    opacity: 0.5;
  }
  50% {
    transform: scale(1.2);
    opacity: 0.25;
  }
  100% {
    transform: scale(0.85);
    opacity: 0.5;
  }
}

.success-title {
  font-size: 28px;
  font-weight: 800;
  color: var(--text-dark);
  margin-bottom: 12px;
}

.success-subtitle {
  font-size: 15px;
  color: var(--text-muted);
  line-height: 1.6;
  margin-bottom: 32px;
}

/* Booking Code Card */
.booking-code-card {
  background-color: var(--bg-light);
  border: 1px dashed var(--primary);
  border-radius: var(--radius-md);
  padding: 16px 24px;
  display: inline-flex;
  flex-direction: column;
  align-items: center;
  gap: 4px;
  margin-bottom: 32px;
}

.code-label {
  font-size: 11px;
  font-weight: 800;
  color: var(--primary);
  letter-spacing: 1.5px;
}

.code-text {
  font-family: var(--font-heading);
  font-size: 24px;
  font-weight: 800;
  color: var(--text-dark);
  letter-spacing: 0.5px;
}

/* Recap details container */
.recap-container {
  border: 1px solid var(--border-color);
  border-radius: var(--radius-lg);
  padding: 24px 32px;
  margin-bottom: 36px;
  background-color: var(--bg-card);
}

.recap-title {
  font-size: 16px;
  font-weight: 700;
  color: var(--text-dark);
  text-align: left;
  margin-bottom: 20px;
}

.recap-grid-info {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 24px;
  margin-bottom: 24px;
}

.recap-col {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.recap-item {
  display: flex;
  flex-direction: column;
  text-align: left;
}

.recap-item .label {
  font-size: 11px;
  color: var(--text-light);
  font-weight: 600;
  margin-bottom: 4px;
}

.recap-item .val-bold {
  font-size: 14px;
  font-weight: 700;
  color: var(--text-dark);
  line-height: 1.4;
}

.recap-item .val-sub {
  font-size: 12.5px;
  color: var(--text-muted);
  margin-top: 2px;
}

.recap-divider {
  border: 0;
  border-top: 1.5px dashed var(--border-color);
  margin: 0 0 20px 0;
}

.recap-bottom-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.recap-service {
  display: flex;
  flex-direction: column;
  text-align: left;
  gap: 4px;
}

.service-badge {
  display: inline-flex;
  padding: 4px 12px;
  background-color: #e0f2fe;
  color: #0369a1;
  font-size: 11.5px;
  font-weight: 700;
  border-radius: var(--radius-full);
}

.recap-total {
  display: flex;
  flex-direction: column;
  text-align: right;
  gap: 4px;
}

.total-val {
  font-size: 18px;
  font-weight: 800;
  color: var(--text-dark);
}

/* Actions Row success buttons */
.actions-row {
  display: flex;
  gap: 16px;
  justify-content: center;
}

.new-shipment-btn {
  flex-grow: 1;
  max-width: 220px;
}

.home-btn {
  flex-grow: 1;
  max-width: 220px;
}

/* Animations */
.animate-fade-in {
  animation: fadeIn 0.4s ease-out forwards;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(12px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Responsive Overrides */
@media (max-width: 1024px) {
  .payment-checkout-page {
    padding-top: 90px;
    padding-bottom: 120px;
  }

  .checkout-main-grid {
    grid-template-columns: 1fr;
    gap: 24px;
  }

  .summary-sidebar-card {
    position: static;
  }

  .bottom-bar-wrapper {
    flex-direction: column;
    gap: 16px;
    padding: 0 16px;
  }

  .watermark-area {
    font-size: 12.5px;
  }

  .pay-now-btn {
    width: 100%;
    max-width: 100%;
  }
}

@media (max-width: 768px) {
  .payment-methods-card {
    padding: 20px;
  }

  .payment-option-row {
    flex-direction: column;
    align-items: flex-start;
    gap: 16px;
    padding: 16px;
  }

  .option-left {
    width: 100%;
  }

  .option-right {
    width: 100%;
    justify-content: flex-end;
    padding-left: 36px;
  }

  .partner-logos {
    flex-wrap: wrap;
    gap: 10px;
  }

  .card-footer-row {
    flex-direction: column;
    align-items: flex-start;
    gap: 16px;
  }

  .card-footer-row .footer-right {
    flex-wrap: wrap;
    gap: 10px;
  }

  .success-card {
    padding: 24px;
  }

  .recap-container {
    padding: 16px;
  }

  .recap-grid-info {
    grid-template-columns: 1fr;
    gap: 16px;
  }

  .actions-row {
    flex-direction: column;
    width: 100%;
    align-items: center;
  }

  .new-shipment-btn, .home-btn {
    max-width: 100%;
    width: 100%;
  }
  
  .sub-options-grid {
    grid-template-columns: 1fr 1fr;
  }
}
</style>
