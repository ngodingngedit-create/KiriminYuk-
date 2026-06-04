<script setup>
import { ref, onMounted, onUnmounted } from 'vue';
import Header from './components/layout/Header.vue';
import Hero from './components/sections/Hero.vue';
import CekTarif from './components/sections/CekTarif.vue';
import Services from './components/sections/Services.vue';
import HowItWorks from './components/sections/HowItWorks.vue';
import Stats from './components/sections/Stats.vue';
import WhyChooseUs from './components/sections/WhyChooseUs.vue';
import ShippingForm from './components/sections/ShippingForm.vue';
import Payment from './components/sections/Payment.vue';
import MobileNavbar from './components/layout/MobileNavbar.vue';
import Footer from './components/layout/Footer.vue';

const currentView = ref('home');

const updateRoute = () => {
  if (window.location.hash === '#shipping-form') {
    currentView.value = 'shipping-form';
    window.scrollTo({ top: 0, behavior: 'instant' });
  } else if (window.location.hash === '#payment') {
    currentView.value = 'payment';
    window.scrollTo({ top: 0, behavior: 'instant' });
  } else {
    currentView.value = 'home';
  }
};

onMounted(() => {
  updateRoute();
  window.addEventListener('hashchange', updateRoute);
});

onUnmounted(() => {
  window.removeEventListener('hashchange', updateRoute);
});
</script>

<template>
  <div class="app-layout" :class="{ 'has-mobile-nav': currentView === 'home' }">
    <!-- Header/Navbar -->
    <Header :forceSolid="currentView === 'shipping-form' || currentView === 'payment'" />
    
    <!-- Main Content Area -->
    <main>
      <template v-if="currentView === 'home'">
        <!-- Hero Section with tracking card -->
        <Hero />
        
        <!-- Cek Tarif Widget Section -->
        <CekTarif />
        
        <!-- Infinite scrolling logo marquee -->
        <Stats />
        
        <!-- Services (Layanan Kami) Section -->
        <Services />
        
        <!-- How It Works (Cara Kerja) Section -->
        <HowItWorks />
        
        <!-- Social Proof Stats Banner Section -->
        <Stats />
        
        <!-- Why Choose Us & Testimonials Section -->
        <WhyChooseUs />
      </template>
      <template v-else-if="currentView === 'shipping-form'">
        <!-- Shipping Form Component -->
        <ShippingForm />
      </template>
      <template v-else-if="currentView === 'payment'">
        <!-- Payment Component -->
        <Payment />
      </template>
    </main>
    
    <!-- Footer -->
    <Footer v-if="currentView === 'home'" />

    <!-- Mobile Bottom Navigation Bar -->
    <MobileNavbar v-if="currentView === 'home'" />
  </div>
</template>

<style>
/* Main layout container padding offsets to accommodate sticky header */
.app-layout {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

main {
  flex-grow: 1;
}

/* Helper styles for section anchor links */
section {
  scroll-margin-top: 80px;
}

@media (max-width: 1024px) {
  section {
    scroll-margin-top: 70px;
  }
}

@media (max-width: 768px) {
  .app-layout.has-mobile-nav {
    padding-bottom: 68px; /* Safe space offset for fixed bottom mobile navbar */
  }
}
</style>
