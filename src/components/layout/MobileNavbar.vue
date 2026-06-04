<template>
  <nav class="mobile-navigation-bar" :class="{ 'visible': isMounted }">
    <!-- Home / Beranda -->
    <div 
      class="mobile-nav-item" 
      :class="{ 'active': activeTab === 'home' }" 
      @click="navigateTo('home')"
    >
      <div class="active-indicator"></div>
      <div class="icon-container">
        <img 
          src="/logo/logo (2).png" 
          alt="Home" 
          class="home-logo-icon" 
          :class="{ 'active-logo': activeTab === 'home' }" 
        />
      </div>
      <span class="nav-label">Beranda</span>
    </div>

    <!-- Ongkir -->
    <div 
      class="mobile-nav-item" 
      :class="{ 'active': activeTab === 'ongkir' }" 
      @click="navigateTo('ongkir')"
    >
      <div class="active-indicator"></div>
      <div class="icon-container">
        <Calculator :size="20" class="nav-icon" />
      </div>
      <span class="nav-label">Ongkir</span>
    </div>

    <!-- Tracking -->
    <div 
      class="mobile-nav-item" 
      :class="{ 'active': activeTab === 'tracking' }" 
      @click="navigateTo('tracking')"
    >
      <div class="active-indicator"></div>
      <div class="icon-container">
        <Package :size="20" class="nav-icon" />
      </div>
      <span class="nav-label">Tracking</span>
    </div>

    <!-- Search / Cari -->
    <div 
      class="mobile-nav-item" 
      :class="{ 'active': activeTab === 'search' }" 
      @click="navigateTo('search')"
    >
      <div class="active-indicator"></div>
      <div class="icon-container">
        <Search :size="20" class="nav-icon" />
      </div>
      <span class="nav-label">Cari</span>
    </div>
  </nav>
</template>

<script>
import { ref, onMounted, onUnmounted, nextTick } from 'vue';
import { Calculator, Package, Search } from '@lucide/vue';

export default {
  name: 'MobileNavbar',
  components: {
    Calculator,
    Package,
    Search
  },
  setup() {
    const activeTab = ref('home');
    const isMounted = ref(false);

    // Sync active tab state with the current URL hash
    const updateActiveTab = () => {
      const hash = window.location.hash;
      if (hash === '#cek-tarif') {
        activeTab.value = 'ongkir';
      } else if (hash === '#tracking') {
        activeTab.value = 'tracking';
      } else if (hash === '#search') {
        activeTab.value = 'search';
      } else {
        activeTab.value = 'home';
      }
    };

    const navigateTo = (tab) => {
      activeTab.value = tab;
      if (tab === 'home') {
        window.location.hash = '#';
      } else if (tab === 'ongkir') {
        window.location.hash = '#cek-tarif';
      } else if (tab === 'tracking') {
        window.location.hash = '#tracking';
      } else if (tab === 'search') {
        // Search scrolls to resi input and focuses it
        window.location.hash = '#tracking';
        nextTick(() => {
          const input = document.querySelector('.glass-input-clear');
          if (input) {
            input.focus();
            input.scrollIntoView({ behavior: 'smooth', block: 'center' });
          }
        });
      }
    };

    onMounted(() => {
      updateActiveTab();
      window.addEventListener('hashchange', updateActiveTab);
      
      // Delay mounting state toggle to trigger smooth slide-up entry transition
      setTimeout(() => {
        isMounted.value = true;
      }, 50);
    });

    onUnmounted(() => {
      window.removeEventListener('hashchange', updateActiveTab);
    });

    return {
      activeTab,
      isMounted,
      navigateTo
    };
  }
};
</script>

<style scoped>
/* Bottom mobile navigation container in website's primary blue */
.mobile-navigation-bar {
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 68px;
  background-color: var(--primary); /* primary royal blue */
  border-top-left-radius: 20px;
  border-top-right-radius: 20px;
  box-shadow: 0 -8px 24px rgba(15, 23, 42, 0.222), 0 -2px 8px rgba(15, 23, 42, 0.06); /* strong elevated shadow */
  display: flex;
  justify-content: center; /* center the items */
  gap: 4px; /* closer gap between buttons */
  align-items: center;
  z-index: 999;
  padding-bottom: env(safe-area-inset-bottom);
  border: none;
  
  /* Initial hidden state off-screen for entry transition */
  transform: translateY(105%);
  transition: transform 0.35s cubic-bezier(0.16, 1, 0.3, 1), box-shadow 0.3s ease;
}

.mobile-navigation-bar.visible {
  transform: translateY(0);
}

.mobile-nav-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 68px; /* tighter width to keep them close together */
  flex-grow: 0; /* prevent stretching */
  height: 100%;
  cursor: pointer;
  position: relative;
  padding-top: 10px;
  transition: all var(--transition-fast);
}

/* Horizontal pill indicator line at the top */
.active-indicator {
  position: absolute;
  top: 0;
  width: 24px;
  height: 4px;
  background-color: transparent;
  border-bottom-left-radius: 4px;
  border-bottom-right-radius: 4px;
  transition: background-color var(--transition-fast), width 0.3s ease;
}

.mobile-nav-item.active .active-indicator {
  background-color: #ffffff; /* white indicator on blue background */
  width: 36px;
}

.icon-container {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 4px;
  height: 22px;
}

/* Home logo image icon styling - color silhouetting via CSS filters */
.home-logo-icon {
  height: 20px;
  width: auto;
  object-fit: contain;
  filter: brightness(0) invert(1) opacity(0.55); /* translucent white silhouette */
  transition: all var(--transition-fast);
}

.home-logo-icon.active-logo {
  filter: brightness(0) invert(1); /* solid white silhouette when active */
  opacity: 1;
}

/* Lucide icon styling */
.nav-icon {
  color: rgba(255, 255, 255, 0.55); /* translucent white inactive state */
  transition: color var(--transition-fast), transform 0.2s ease;
}

.mobile-nav-item:hover .nav-icon {
  color: rgba(255, 255, 255, 0.8);
}

.mobile-nav-item.active .nav-icon {
  color: #ffffff; /* solid white active state */
  transform: scale(1.1);
}

/* Tab text labels */
.nav-label {
  font-family: var(--font-body);
  font-size: 11px;
  font-weight: 500;
  color: rgba(255, 255, 255, 0.55);
  transition: color var(--transition-fast), font-weight var(--transition-fast);
}

.mobile-nav-item:hover .nav-label {
  color: rgba(255, 255, 255, 0.8);
}

.mobile-nav-item.active .nav-label {
  color: #ffffff;
  font-weight: 700;
}

/* Hide navigation bar on desktop */
@media (min-width: 769px) {
  .mobile-navigation-bar {
    display: none;
  }
}
</style>

<style>
/* Global rules to hide mobile navbar smoothly when the drawer menu is open */
body.drawer-open .mobile-navigation-bar {
  transform: translateY(105%) !important;
}
</style>
