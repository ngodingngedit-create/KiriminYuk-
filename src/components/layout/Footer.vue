<template>
  <footer class="footer" id="kontak">
    <div class="container footer-grid">
      <!-- Info Column -->
      <div class="footer-info-col">
        <a href="#" class="logo-area">
          <!-- Logo biru diubah putih dengan CSS filter untuk footer biru -->
          <img
            src="/logo/logoTeks (1).png"
            alt="KiriminYuk Logo"
            class="footer-logo"
          />
         
        </a>
        <p class="footer-tagline">
          Solusi pengiriman paket kilat dan kargo aman, cepat, dan tepercaya untuk segala kebutuhan bisnis dan personal Anda di seluruh penjuru Nusantara.
        </p>
        <div class="social-links">
          <a href="#" class="social-icon-btn" aria-label="Facebook">
            <svg viewBox="0 0 24 24" width="18" height="18" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <path d="M18 2h-3a5 5 0 0 0-5 5v3H7v4h3v8h4v-8h3l1-4h-4V7a1 1 0 0 1 1-1h3z"></path>
            </svg>
          </a>
          <a href="#" class="social-icon-btn" aria-label="Instagram">
            <svg viewBox="0 0 24 24" width="18" height="18" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <rect x="2" y="2" width="20" height="20" rx="5" ry="5"></rect>
              <path d="M16 11.37A4 4 0 1 1 12.63 8 4 4 0 0 1 16 11.37z"></path>
              <line x1="17.5" y1="6.5" x2="17.51" y2="6.5"></line>
            </svg>
          </a>
          <a href="#" class="social-icon-btn" aria-label="Twitter">
            <svg viewBox="0 0 24 24" width="18" height="18" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <path d="M22 4s-.7 2.1-2 3.4c1.6 10-9.4 17.3-18 11.6 2.2.1 4.4-.6 6-2C3 15.5.5 9.6 3 5c2.2 2.6 5.6 4.1 9 4-.9-4.2 4-6.6 7-3.8 1.1 0 3-1.2 3-1.2z"></path>
            </svg>
          </a>
          <a href="#" class="social-icon-btn" aria-label="Linkedin">
            <svg viewBox="0 0 24 24" width="18" height="18" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <path d="M16 8a6 6 0 0 1 6 6v7h-4v-7a2 2 0 0 0-2-2 2 2 0 0 0-2 2v7h-4v-7a6 6 0 0 1 6-6z"></path>
              <rect x="2" y="9" width="4" height="12"></rect>
              <circle cx="4" cy="4" r="2"></circle>
            </svg>
          </a>
        </div>
      </div>

      <!-- Quick Links -->
      <div class="footer-links-col">
        <h4 class="footer-title">Tautan Cepat</h4>
        <ul class="footer-links">
          <li><a href="#">Beranda</a></li>
          <li><a href="#layanan">Layanan Kami</a></li>
          <li><a href="#cek-tarif">Hitung Ongkir</a></li>
          <li><a href="#tracking">Lacak Pengiriman</a></li>
          <li><a href="#tentang-kami">Tentang Kami</a></li>
        </ul>
      </div>

      <!-- Services -->
      <div class="footer-links-col">
        <h4 class="footer-title">Layanan Pengiriman</h4>
        <ul class="footer-links">
          <li><a href="#layanan">Instant Delivery</a></li>
          <li><a href="#layanan">Same Day Delivery</a></li>
          <li><a href="#layanan">Next Day Delivery</a></li>
          <li><a href="#layanan">Cargo & Logistik</a></li>
          <li><a href="#layanan">Kirim Dokumen</a></li>
          <li><a href="#layanan">E-Commerce Paket</a></li>
        </ul>
      </div>

      <!-- Contact Info -->
      <div class="footer-contact-col">
        <h4 class="footer-title">Hubungi Kami</h4>
        <ul class="footer-contact-info">
          <li>
            <MapPin class="contact-icon" :size="18" />
            <span>Jl. Boulevard Barat Raya No. 12, Kelapa Gading, Jakarta Utara 14240</span>
          </li>
          <li>
            <Phone class="contact-icon" :size="18" />
            <a href="tel:02112345678">021 1234 5678</a>
          </li>
          <li>
            <Mail class="contact-icon" :size="18" />
            <a href="mailto:support@kiriminyuk.com">support@kiriminyuk.com</a>
          </li>
        </ul>
      </div>
    </div>

    <!-- Bottom Copyright -->
    <div class="footer-bottom">
      <div class="container footer-bottom-flex">
        <p>&copy; 2026 KiriminYuk!. Hak Cipta Dilindungi Undang-Undang.</p>
        <div class="footer-bottom-links">
          <a href="#">Kebijakan Privasi</a>
          <span class="divider">|</span>
          <a href="#">Syarat & Ketentuan</a>
        </div>
      </div>
    </div>
  </footer>
</template>

<script>
import { MapPin, Phone, Mail } from '@lucide/vue';

export default {
  name: 'FooterLayout',
  components: {
    MapPin,
    Phone,
    Mail
  }
};
</script>

<style scoped>
.footer {
  background-color: var(--primary); /* Matched exactly to the main primary blue color */
  color: rgba(255, 255, 255, 0.85); /* High contrast light text for readable body */
  padding-top: 60px;
  border-top: none;
}

.footer-grid {
  display: grid;
  grid-template-columns: 2fr 1fr 1.2fr 1.5fr;
  gap: 48px;
  padding-bottom: 48px;
}

.footer-info-col {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.logo-area {
  display: flex;
  align-items: center;
  gap: 10px;
  text-decoration: none;
}

/* Footer Logo PNG */
.footer-logo {
  height: 52px;
  width: auto;
  object-fit: contain;
  flex-shrink: 0;
  /* Logo putih terlihat jelas di background biru */
  filter: brightness(0) invert(1) drop-shadow(0 2px 8px rgba(0,0,0,0.15));
  transition: all var(--transition-fast);
}

.footer-logo:hover {
  transform: scale(1.05);
  filter: brightness(0) invert(1) drop-shadow(0 4px 12px rgba(0,0,0,0.25));
}

.logo-text {
  font-family: var(--font-heading);
  font-weight: 600;
  font-size: 24px;
  letter-spacing: -0.5px;
  position: relative;
  right: 10px;
}

.text-white {
  color: #ffffff;
}

.logo-text span {
  color: #00f3ff; /* Wavy wing highlight in bright cyan for premium readability */
}

.footer-tagline {
  font-size: 14px;
  line-height: 1.6;
  color: rgba(255, 255, 255, 0.85);
  text-align: left;
}

.social-links {
  display: flex;
  gap: 12px;
}

.social-icon-btn {
  width: 38px;
  height: 38px;
  border-radius: var(--radius-full);
  background-color: rgba(255, 255, 255, 0.15); /* Translucent white matching blue bg */
  color: #ffffff;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all var(--transition-fast);
}

.social-icon-btn:hover {
  background-color: #ffffff;
  color: var(--primary); /* Inverse color hover transition */
  transform: translateY(-3px);
}

.footer-title {
  color: #ffffff;
  font-size: 16px;
  font-weight: 700;
  margin-bottom: 24px;
  position: relative;
  padding-bottom: 8px;
  text-align: left;
}

.footer-title::after {
  content: '';
  position: absolute;
  left: 0;
  bottom: 0;
  width: 32px;
  height: 2px;
  background-color: #ffffff; /* White line matching title headers */
}

.footer-links {
  list-style: none;
  display: flex;
  flex-direction: column;
  gap: 12px;
  text-align: left;
}

.footer-links a {
  font-size: 14px;
  color: rgba(255, 255, 255, 0.85);
  transition: all var(--transition-fast);
}

.footer-links a:hover {
  color: #ffffff;
  padding-left: 4px;
}

.footer-contact-info {
  list-style: none;
  display: flex;
  flex-direction: column;
  gap: 16px;
  font-size: 14px;
  text-align: left;
}

.footer-contact-info li {
  display: flex;
  gap: 12px;
  line-height: 1.5;
  color: rgba(255, 255, 255, 0.85);
}

.contact-icon {
  color: #ffffff; /* Clean white contact icons */
  flex-shrink: 0;
}

.footer-contact-info a:hover {
  color: #ffffff;
}

/* Footer Bottom */
.footer-bottom {
  border-top: 1px solid rgba(255, 255, 255, 0.15);
  padding: 24px 0;
  font-size: 13px;
  color: rgba(255, 255, 255, 0.8);
}

.footer-bottom-flex {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.footer-bottom-links {
  display: flex;
  gap: 12px;
}

.footer-bottom-links a {
  color: rgba(255, 255, 255, 0.8);
}

.footer-bottom-links a:hover {
  color: #ffffff;
}

.divider {
  color: rgba(255, 255, 255, 0.25);
}

/* Responsive Grid */
@media (max-width: 1024px) {
  .footer-grid {
    grid-template-columns: 1fr 1fr;
    gap: 40px;
  }

  .footer-logo {
    height: 44px;
  }
}

@media (max-width: 640px) {
  .footer-grid {
    grid-template-columns: 1fr;
    gap: 32px;
  }

  .footer-logo {
    height: 38px;
  }

  .footer-bottom-flex {
    flex-direction: column;
    gap: 12px;
    text-align: center;
  }
}
</style>
