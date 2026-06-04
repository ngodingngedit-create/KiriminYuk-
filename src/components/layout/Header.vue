<template>
  <header class="header" :class="{ 'header-scrolled': isScrolled || forceSolid }">
    <div class="container navbar-container">
      <!-- Logo Section -->
      <a href="#" class="logo-area">
        <!-- Logo saat scrolled/solid: gunakan logo biru -->
        <img
          v-if="isScrolled || forceSolid"
          src="/logo/logoTeks (2).png"
          alt="KiriminYuk Logo"
          class="navbar-logo navbar-logo-blue"
        />
        <!-- Logo saat glass/transparan: gunakan logo putih -->
        <img
          v-else
          src="/logo/logoTeks (1).png"
          alt="KiriminYuk Logo"
          class="navbar-logo navbar-logo-white"
        />
       
      </a>

      <!-- Desktop Navigation Menu -->
      <nav class="desktop-nav">
        <ul class="nav-links">
          <li><a href="#" class="nav-link" :class="{ 'active': activeLink === 'beranda', 'text-white-link': !isScrolled && !forceSolid }" @click="activeLink = 'beranda'">Beranda</a></li>
          <li class="dropdown-item">
            <a href="#layanan" class="nav-link" :class="{ 'text-white-link': !isScrolled && !forceSolid }">
              Layanan
              <ChevronDown class="dropdown-icon" :size="14" />
            </a>
            <div class="dropdown-menu">
              <a href="#layanan">Instant Delivery</a>
              <a href="#layanan">Same Day Delivery</a>
              <a href="#layanan">Next Day Delivery</a>
              <a href="#layanan">Cargo & Logistik</a>
            </div>
          </li>
          <li><a href="#cek-tarif" class="nav-link" :class="{ 'text-white-link': !isScrolled && !forceSolid }">Cek Tarif</a></li>
          <li><a href="#tracking" class="nav-link" :class="{ 'text-white-link': !isScrolled && !forceSolid }">Tracking</a></li>
          <li><a href="#tentang-kami" class="nav-link" :class="{ 'text-white-link': !isScrolled && !forceSolid }">Tentang Kami</a></li>
          <li><a href="#kontak" class="nav-link" :class="{ 'text-white-link': !isScrolled && !forceSolid }">Kontak</a></li>
        </ul>
      </nav>

      <!-- Right Header Actions (Desktop) -->
      <div class="header-actions">
        <!-- Interactive Language Switcher dropdown/accordion -->
        <div class="lang-selector-wrapper">
          <button @click="toggleLang" class="lang-trigger-btn" :class="{ 'lang-btn-glass': !isScrolled && !forceSolid }">
            <template v-if="selectedLang === 'id'">
              <svg viewBox="0 0 3 2" width="18" height="12" class="flag-icon">
                <rect width="3" height="1" fill="#FF0000" />
                <rect y="1" width="3" height="1" fill="#FFFFFF" />
              </svg>
              <span>Indonesia</span>
            </template>
            <template v-else>
              <svg viewBox="0 0 60 30" width="18" height="12" class="flag-icon">
                <rect width="60" height="30" fill="#00247d"/>
                <path d="M0,0 L60,30 M60,0 L0,30" stroke="#fff" stroke-width="6"/>
                <path d="M0,0 L60,30 M60,0 L0,30" stroke="#cf142b" stroke-width="4"/>
                <path d="M30,0 L30,30 M0,15 L60,15" stroke="#fff" stroke-width="10"/>
                <path d="M30,0 L30,30 M0,15 L60,15" stroke="#cf142b" stroke-width="6"/>
              </svg>
              <span>English</span>
            </template>
            <ChevronDown class="chevron-lang" :class="{ 'rotate': isLangOpen }" :size="12" />
          </button>

          <!-- Accordion panel -->
          <div v-if="isLangOpen" class="lang-dropdown-menu">
            <button @click="changeLang('id')" class="lang-option" :class="{ 'active': selectedLang === 'id' }">
              <svg viewBox="0 0 3 2" width="18" height="12" class="flag-icon">
                <rect width="3" height="1" fill="#FF0000" />
                <rect y="1" width="3" height="1" fill="#FFFFFF" />
              </svg>
              <span>Indonesia</span>
            </button>
            <button @click="changeLang('en')" class="lang-option" :class="{ 'active': selectedLang === 'en' }">
              <svg viewBox="0 0 60 30" width="18" height="12" class="flag-icon">
                <rect width="60" height="30" fill="#00247d"/>
                <path d="M0,0 L60,30 M60,0 L0,30" stroke="#fff" stroke-width="6"/>
                <path d="M0,0 L60,30 M60,0 L0,30" stroke="#cf142b" stroke-width="4"/>
                <path d="M30,0 L30,30 M0,15 L60,15" stroke="#fff" stroke-width="10"/>
                <path d="M30,0 L30,30 M0,15 L60,15" stroke="#cf142b" stroke-width="6"/>
              </svg>
              <span>English</span>
            </button>
          </div>
        </div>
        
        <!-- Primary Solid Blue Button -->
        <a href="#shipping-form" class="btn btn-primary btn-nav-cta">
          Pesan Sekarang
          <ArrowRight class="btn-arrow" :size="15" />
        </a>
      </div>

      <!-- Hamburger Menu Button (Mobile) -->
      <button class="hamburger-btn" :class="{ 'text-white-btn': !isScrolled && !forceSolid }" @click="toggleDrawer" aria-label="Toggle Menu">
        <Menu v-if="!isDrawerOpen" :size="24" />
        <X v-else :size="24" />
      </button>
    </div>

    <!-- Mobile Drawer Navigation -->
    <div class="mobile-drawer-overlay" :class="{ 'open': isDrawerOpen }" @click="closeDrawer"></div>
    <div class="mobile-drawer" :class="{ 'open': isDrawerOpen }">
      <div class="drawer-header">
        <a href="#" class="logo-area" @click="closeDrawer">
          <!-- Logo di drawer mobile selalu biru karena background drawer putih -->
          <img
            src="/logo/logo (1).png"
            alt="KiriminYuk Logo"
            class="navbar-logo navbar-logo-blue drawer-logo"
          />
          <div class="logo-text-block">
            <span class="logo-text">Kirimin<span>Yuk!</span></span>
            <span class="logo-subtext">DELIVERY</span>
          </div>
        </a>
        <button class="close-drawer-btn" @click="closeDrawer">
          <X :size="24" />
        </button>
      </div>

      <nav class="mobile-nav">
        <ul class="mobile-nav-links">
          <li><a href="#" class="mobile-nav-link" @click="closeDrawer">Beranda</a></li>
          <li><a href="#layanan" class="mobile-nav-link" @click="closeDrawer">Layanan</a></li>
          <li><a href="#cek-tarif" class="mobile-nav-link" @click="closeDrawer">Cek Tarif</a></li>
          <li><a href="#tracking" class="mobile-nav-link" @click="closeDrawer">Tracking</a></li>
          <li><a href="#tentang-kami" class="mobile-nav-link" @click="closeDrawer">Tentang Kami</a></li>
          <li><a href="#kontak" class="mobile-nav-link" @click="closeDrawer">Kontak</a></li>
        </ul>

        <div class="drawer-footer">
          <!-- Language selector inside mobile drawer -->
          <div class="lang-selector-wrapper-drawer">
            <button @click="toggleLang" class="lang-trigger-btn-drawer">
              <template v-if="selectedLang === 'id'">
                <svg viewBox="0 0 3 2" width="18" height="12" class="flag-icon">
                  <rect width="3" height="1" fill="#FF0000" />
                  <rect y="1" width="3" height="1" fill="#FFFFFF" />
                </svg>
                <span>Indonesia</span>
              </template>
              <template v-else>
                <svg viewBox="0 0 60 30" width="18" height="12" class="flag-icon">
                  <rect width="60" height="30" fill="#00247d"/>
                  <path d="M0,0 L60,30 M60,0 L0,30" stroke="#fff" stroke-width="6"/>
                  <path d="M0,0 L60,30 M60,0 L0,30" stroke="#cf142b" stroke-width="4"/>
                  <path d="M30,0 L30,30 M0,15 L60,15" stroke="#fff" stroke-width="10"/>
                  <path d="M30,0 L30,30 M0,15 L60,15" stroke="#cf142b" stroke-width="6"/>
                </svg>
                <span>English</span>
              </template>
              <ChevronDown class="chevron-lang" :class="{ 'rotate': isLangOpen }" :size="12" />
            </button>

            <!-- Expanded panel mobile -->
            <div v-if="isLangOpen" class="lang-dropdown-menu-drawer">
              <button @click="changeLang('id')" class="lang-option-drawer" :class="{ 'active': selectedLang === 'id' }">
                <svg viewBox="0 0 3 2" width="18" height="12" class="flag-icon">
                  <rect width="3" height="1" fill="#FF0000" />
                  <rect y="1" width="3" height="1" fill="#FFFFFF" />
                </svg>
                <span>Indonesia</span>
              </button>
              <button @click="changeLang('en')" class="lang-option-drawer" :class="{ 'active': selectedLang === 'en' }">
                <svg viewBox="0 0 60 30" width="18" height="12" class="flag-icon">
                  <rect width="60" height="30" fill="#00247d"/>
                  <path d="M0,0 L60,30 M60,0 L0,30" stroke="#fff" stroke-width="6"/>
                  <path d="M0,0 L60,30 M60,0 L0,30" stroke="#cf142b" stroke-width="4"/>
                  <path d="M30,0 L30,30 M0,15 L60,15" stroke="#fff" stroke-width="10"/>
                  <path d="M30,0 L30,30 M0,15 L60,15" stroke="#cf142b" stroke-width="6"/>
                </svg>
                <span>English</span>
              </button>
            </div>
          </div>

          <a href="#shipping-form" class="btn btn-primary btn-drawer-cta" @click="closeDrawer">
            Pesan Sekarang
            <ArrowRight :size="15" />
          </a>
        </div>
      </nav>
    </div>
  </header>
</template>

<script>
import { ref, onMounted, onUnmounted } from 'vue';
import { ChevronDown, Menu, X, ArrowRight } from '@lucide/vue';

export default {
  name: 'HeaderLayout',
  components: {
    ChevronDown,
    Menu,
    X,
    ArrowRight
  },
  props: {
    forceSolid: {
      type: Boolean,
      default: false
    }
  },
  setup() {
    const isScrolled = ref(false);
    const isDrawerOpen = ref(false);
    const activeLink = ref('beranda');
    const selectedLang = ref('id');
    const isLangOpen = ref(false);

    const handleScroll = () => {
      isScrolled.value = window.scrollY > 20;
    };

    const toggleDrawer = () => {
      isDrawerOpen.value = !isDrawerOpen.value;
      if (isDrawerOpen.value) {
        document.body.style.overflow = 'hidden';
        document.body.classList.add('drawer-open');
      } else {
        document.body.style.overflow = '';
        document.body.classList.remove('drawer-open');
      }
    };

    const closeDrawer = () => {
      isDrawerOpen.value = false;
      document.body.style.overflow = '';
      document.body.classList.remove('drawer-open');
    };

    const toggleLang = () => {
      isLangOpen.value = !isLangOpen.value;
    };

    const changeLang = (lang) => {
      selectedLang.value = lang;
      isLangOpen.value = false;
      // Option to emit event or call translations
    };

    onMounted(() => {
      window.addEventListener('scroll', handleScroll);
    });

    onUnmounted(() => {
      window.removeEventListener('scroll', handleScroll);
    });

    return {
      isScrolled,
      isDrawerOpen,
      activeLink,
      selectedLang,
      isLangOpen,
      toggleDrawer,
      closeDrawer,
      toggleLang,
      changeLang
    };
  }
};
</script>

<style scoped>
/* Floating Header Container */
.header {
  position: fixed;
  top: 20px;
  left: 50%;
  transform: translateX(-50%);
  width: calc(100% - 48px);
  max-width: var(--container-width);
  /* Top style: Glassmorphism */
  background: rgba(255, 255, 255, 0.08);
  backdrop-filter: blur(16px) saturate(120%);
  -webkit-backdrop-filter: blur(16px) saturate(120%);
  border: 1px solid rgba(255, 255, 255, 0.15);
  border-radius: var(--radius-lg);
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.1);
  z-index: 100;
  transition: all var(--transition-normal);
  height: 84px;
  display: flex;
  align-items: center;
}

.header-scrolled {
  top: 0;
  width: 100%;
  max-width: 100%;
  border-radius: 0;
  border-top: none;
  border-left: none;
  border-right: none;
  /* Scrolled style: clean solid white */
  background-color: var(--bg-card);
  border-bottom: 1px solid var(--border-color);
  height: 76px;
  box-shadow: var(--shadow-lg);
  backdrop-filter: none;
}

.navbar-container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 100%;
  padding-left: 24px;
  padding-right: 24px;
}

/* Logo Design */
.logo-area {
  display: flex;
  align-items: center;
  gap: 10px;
  text-decoration: none;
}

/* Logo PNG image - Desktop */
.navbar-logo {
  height: 48px;
  width: auto;
  object-fit: contain;
  flex-shrink: 0;
  transition: all var(--transition-fast);
  display: block;
}

.navbar-logo-white {
  /* Logo putih untuk mode glass/transparan */
  filter: drop-shadow(0 2px 8px rgba(0,0,0,0.15));
}

.navbar-logo-blue {
  /* Logo biru untuk mode solid/scrolled */
  filter: drop-shadow(0 2px 6px rgba(10, 101, 255, 0.12));
}

.logo-text-block {
  display: flex;
  flex-direction: column;
  text-align: left;
  line-height: 1.1;
}

.logo-text {
  font-family: var(--font-heading);
  font-weight: 700;
  font-size: 22px;
  color: var(--text-dark);
  letter-spacing: -0.5px;
  position: relative;
  right: 10px;
}

.logo-text.text-white {
  color: #ffffff;
}

.logo-text span {
  color: var(--primary);
}

.logo-text.text-white span {
  color: #00f3ff; /* cyan highlight for high visibility on dark/glass mode */
}

.logo-subtext {
  font-size: 9px;
  font-weight: 700;
  color: var(--text-light);
  letter-spacing: 2px;
  margin-top: 1px;
}

/* Desktop navigation links */
.desktop-nav {
  display: block;
}

.nav-links {
  display: flex;
  align-items: center;
  list-style: none;
  gap: 24px;
}

.nav-link {
  font-weight: 600;
  font-size: 14px;
  color: var(--text-dark);
  display: flex;
  align-items: center;
  gap: 4px;
  padding: 6px 0;
  border-bottom: 2px solid transparent;
  font-family: var(--font-body);
}

.text-white-link {
  color: rgba(255, 255, 255, 0.9) !important;
}

.text-white-link:hover, .text-white-link.active {
  color: #00f3ff !important;
}

.nav-link:hover, .nav-link.active {
  color: var(--primary);
}

.dropdown-icon {
  transition: transform var(--transition-fast);
  color: var(--text-light);
}

.dropdown-item {
  position: relative;
}

.dropdown-item:hover .dropdown-icon {
  transform: rotate(180deg);
}

.dropdown-menu {
  position: absolute;
  top: 100%;
  left: 50%;
  transform: translateX(-50%) translateY(10px);
  background-color: var(--bg-card);
  min-width: 180px;
  border-radius: var(--radius-md);
  box-shadow: var(--shadow-lg);
  border: 1px solid var(--border-color);
  opacity: 0;
  visibility: hidden;
  transition: all var(--transition-fast);
  padding: 8px 0;
  z-index: 101;
}

.dropdown-item:hover .dropdown-menu {
  opacity: 1;
  visibility: visible;
  transform: translateX(-50%) translateY(0);
}

.dropdown-menu a {
  display: block;
  padding: 10px 20px;
  font-size: 13px;
  font-weight: 600;
  color: var(--text-muted);
  text-align: left;
}

.dropdown-menu a:hover {
  background-color: var(--primary-light);
  color: var(--primary);
}

/* Header Actions */
.header-actions {
  display: flex;
  align-items: center;
  gap: 16px;
}

/* Language selector accordion/dropdown */
.lang-selector-wrapper {
  position: relative;
}

.lang-trigger-btn {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 10px 16px;
  border: 1.5px solid var(--primary);
  border-radius: var(--radius-md);
  color: var(--text-dark);
  font-weight: 700;
  font-size: 14px;
  background-color: white;
  transition: all var(--transition-fast);
  font-family: var(--font-heading);
}

.lang-btn-glass {
  border-color: rgba(255, 255, 255, 0.35);
  background-color: rgba(255, 255, 255, 0.08);
  color: #ffffff;
}

.lang-trigger-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
}

.lang-btn-glass:hover {
  background-color: rgba(255, 255, 255, 0.15);
  border-color: rgba(255, 255, 255, 0.5);
  color: #ffffff;
}

.flag-icon {
  border-radius: 2px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  flex-shrink: 0;
}

.chevron-lang {
  color: var(--text-light);
  transition: transform var(--transition-fast);
}

.lang-btn-glass .chevron-lang {
  color: rgba(255, 255, 255, 0.6);
}

.chevron-lang.rotate {
  transform: rotate(180deg);
}

/* Lang Dropdown Accordion Panel */
.lang-dropdown-menu {
  position: absolute;
  top: 100%;
  right: 0;
  margin-top: 8px;
  background-color: var(--bg-card);
  border: 1px solid var(--border-color);
  border-radius: var(--radius-md);
  box-shadow: var(--shadow-lg);
  min-width: 150px;
  padding: 6px;
  display: flex;
  flex-direction: column;
  gap: 4px;
  z-index: 101;
}

.lang-option {
  display: flex;
  align-items: center;
  gap: 8px;
  width: 100%;
  padding: 8px 12px;
  font-size: 13px;
  font-weight: 700;
  color: var(--text-dark);
  border-radius: var(--radius-sm);
  transition: all var(--transition-fast);
}

.lang-option:hover {
  background-color: var(--primary-light);
  color: var(--primary);
}

.lang-option.active {
  background-color: var(--primary);
  color: white;
}

.btn-nav-cta {
  padding: 11px 22px;
  font-size: 14px;
}

.btn-arrow {
  transition: transform var(--transition-fast);
}

.btn-nav-cta:hover .btn-arrow {
  transform: translateX(4px);
}

/* Hamburger menu icon */
.hamburger-btn {
  display: none;
  color: var(--text-dark);
}

.text-white-btn {
  color: #ffffff !important;
}

/* Mobile drawer and overlay completely hidden on desktop */
.mobile-drawer-overlay, .mobile-drawer {
  display: none;
}

/* Responsiveness overrides */
@media (max-width: 1024px) {
  .header {
    top: 10px;
    height: 58px;
    width: calc(100% - 20px);
    /* Mobile top style starts as clean glass too */
    background: rgba(255, 255, 255, 0.15);
    backdrop-filter: blur(12px);
    border: 1px solid rgba(255, 255, 255, 0.2);
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.05);
  }
  
  .header-scrolled {
    top: 0;
    width: 100%;
    border-radius: 0;
    background-color: var(--bg-card);
    border-bottom: 1px solid var(--border-color);
  }
  
  .navbar-container {
    padding-left: 14px;
    padding-right: 14px;
  }

  /* Logo PNG - Mobile responsif */
  .navbar-logo {
    height: 36px;
  }

  .drawer-logo {
    height: 40px;
  }

  .logo-text {
    font-size: 16px;
  }

  .logo-subtext {
    font-size: 8px;
  }

  .desktop-nav {
    display: none;
  }
  
  .header-actions {
    display: none;
  }
  
  .hamburger-btn {
    display: flex;
    align-items: center;
    justify-content: center;
  }

  /* Show and position correctly on mobile */
  .mobile-drawer-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    background-color: rgba(15, 23, 42, 0.4);
    backdrop-filter: blur(4px);
    z-index: 998;
    opacity: 0;
    visibility: hidden;
    transition: all var(--transition-normal);
    display: block;
  }

  .mobile-drawer-overlay.open {
    opacity: 1;
    visibility: visible;
  }

  .mobile-drawer {
    position: fixed;
    top: 0;
    right: 0;
    width: 300px;
    max-width: 80vw;
    height: 100vh;
    background-color: var(--bg-card);
    box-shadow: var(--shadow-xl);
    z-index: 999;
    transform: translateX(100%);
    transition: transform var(--transition-normal) cubic-bezier(0.16, 1, 0.3, 1);
    display: flex;
    flex-direction: column;
    padding: 24px;
  }

  .mobile-drawer.open {
    transform: translateX(0);
  }

  .close-drawer-btn {
    color: var(--text-dark);
  }

  .mobile-nav {
    display: flex;
    flex-direction: column;
    flex-grow: 1;
  }

  .mobile-nav-links {
    list-style: none;
    display: flex;
    flex-direction: column;
    gap: 16px;
    margin-top: 24px;
  }

  .mobile-nav-link {
    font-family: var(--font-heading);
    font-size: 16px;
    font-weight: 700;
    color: var(--text-dark);
    display: block;
    padding: 10px 0;
    border-bottom: 1px solid var(--border-color);
    text-align: left;
  }

  .mobile-nav-link:hover {
    color: var(--primary);
    padding-left: 8px;
  }

  .drawer-footer {
    margin-top: auto;
    display: flex;
    flex-direction: column;
    gap: 20px;
    border-top: 1px solid var(--border-color);
    padding-top: 24px;
  }

  /* Mobile Drawer Language Accordion styling */
  .lang-selector-wrapper-drawer {
    width: 100%;
    text-align: left;
  }

  .lang-trigger-btn-drawer {
    display: flex;
    align-items: center;
    justify-content: space-between;
    width: 100%;
    padding: 12px 16px;
    border: 1.5px solid var(--border-color);
    border-radius: var(--radius-md);
    color: var(--text-dark);
    font-weight: 700;
    font-size: 15px;
  }

  .lang-dropdown-menu-drawer {
    border: 1.5px solid var(--border-color);
    border-top: none;
    border-bottom-left-radius: var(--radius-md);
    border-bottom-right-radius: var(--radius-md);
    background-color: var(--bg-light);
    display: flex;
    flex-direction: column;
    padding: 8px;
    gap: 4px;
    margin-top: -2px;
  }

  .lang-option-drawer {
    display: flex;
    align-items: center;
    gap: 10px;
    width: 100%;
    padding: 10px 14px;
    font-size: 14px;
    font-weight: 700;
    color: var(--text-dark);
    border-radius: var(--radius-sm);
  }

  .lang-option-drawer.active {
    background-color: var(--primary);
    color: white;
  }

  .btn-drawer-cta {
    width: 100%;
  }
}
</style>

<!-- Global (non-scoped) rules: hide bottom bar when mobile drawer is open -->
<style>
/* Bottom bar smooth hide when mobile drawer opens */
.dashboard-bottom-bar {
  transition: transform 0.3s cubic-bezier(0.4, 0, 0.2, 1),
              opacity  0.3s ease;
}

body.drawer-open .dashboard-bottom-bar {
  transform: translateY(100%);
  opacity: 0;
  pointer-events: none;
}
</style>
