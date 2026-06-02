<template>
  <section class="stats-banner-section">
    <div class="marquee-wrapper">
      <div class="marquee-track">
        <!-- First Set -->
        <div class="marquee-content">
          <div class="marquee-item">
            <Package :size="22" class="marquee-icon" />
            <span class="marquee-logo">Kirimin<span>Yuk!</span></span>
          </div>
          <div class="marquee-item">
            <Zap :size="22" class="marquee-icon" />
            <span class="marquee-logo">Kirimin<span>Yuk!</span></span>
          </div>
          <div class="marquee-item">
            <Truck :size="22" class="marquee-icon" />
            <span class="marquee-logo">Kirimin<span>Yuk!</span></span>
          </div>
          <div class="marquee-item">
            <Package :size="22" class="marquee-icon" />
            <span class="marquee-logo">Kirimin<span>Yuk!</span></span>
          </div>
          <div class="marquee-item">
            <Zap :size="22" class="marquee-icon" />
            <span class="marquee-logo">Kirimin<span>Yuk!</span></span>
          </div>
          <div class="marquee-item">
            <Truck :size="22" class="marquee-icon" />
            <span class="marquee-logo">Kirimin<span>Yuk!</span></span>
          </div>
          <div class="marquee-item">
            <Package :size="22" class="marquee-icon" />
            <span class="marquee-logo">Kirimin<span>Yuk!</span></span>
          </div>
          <div class="marquee-item">
            <Zap :size="22" class="marquee-icon" />
            <span class="marquee-logo">Kirimin<span>Yuk!</span></span>
          </div>
        </div>

        <!-- Duplicate Set for Seamless Loop -->
        <div class="marquee-content" aria-hidden="true">
          <div class="marquee-item">
            <Package :size="22" class="marquee-icon" />
            <span class="marquee-logo">Kirimin<span>Yuk!</span></span>
          </div>
          <div class="marquee-item">
            <Zap :size="22" class="marquee-icon" />
            <span class="marquee-logo">Kirimin<span>Yuk!</span></span>
          </div>
          <div class="marquee-item">
            <Truck :size="22" class="marquee-icon" />
            <span class="marquee-logo">Kirimin<span>Yuk!</span></span>
          </div>
          <div class="marquee-item">
            <Package :size="22" class="marquee-icon" />
            <span class="marquee-logo">Kirimin<span>Yuk!</span></span>
          </div>
          <div class="marquee-item">
            <Zap :size="22" class="marquee-icon" />
            <span class="marquee-logo">Kirimin<span>Yuk!</span></span>
          </div>
          <div class="marquee-item">
            <Truck :size="22" class="marquee-icon" />
            <span class="marquee-logo">Kirimin<span>Yuk!</span></span>
          </div>
          <div class="marquee-item">
            <Package :size="22" class="marquee-icon" />
            <span class="marquee-logo">Kirimin<span>Yuk!</span></span>
          </div>
          <div class="marquee-item">
            <Zap :size="22" class="marquee-icon" />
            <span class="marquee-logo">Kirimin<span>Yuk!</span></span>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import { Package, Zap, Truck } from '@lucide/vue';

export default {
  name: 'StatsBannerSection',
  components: {
    Package,
    Zap,
    Truck
  }
};
</script>

<style scoped>
.stats-banner-section {
  background-color: var(--primary);
  color: white;
  padding: 24px 0;
  overflow: hidden;
  position: relative;
  border-top: 1px solid rgba(255, 255, 255, 0.08);
  border-bottom: 1px solid rgba(255, 255, 255, 0.08);
}

.marquee-wrapper {
  display: flex;
  overflow: hidden;
  width: 100%;
}

.marquee-track {
  display: flex;
  width: max-content;
  animation: marquee 25s linear infinite;
  gap: 40px;
  padding-right: 40px;
}

.marquee-wrapper:hover .marquee-track {
  animation-play-state: paused;
}

.marquee-content {
  display: flex;
  align-items: center;
  gap: 40px;
  flex-shrink: 0;
}

.marquee-item {
  display: flex;
  align-items: center;
  gap: 16px;
}

.marquee-icon {
  color: #00f3ff;
  stroke-width: 2.2;
}

.marquee-logo {
  font-family: var(--font-heading);
  font-weight: 800;
  font-size: 26px;
  color: #ffffff;
  letter-spacing: -0.5px;
  user-select: none;
}

.marquee-logo span {
  color: #00f3ff;
}

@keyframes marquee {
  0% {
    transform: translateX(0);
  }
  100% {
    transform: translateX(-50%);
  }
}

@media (max-width: 768px) {
  .stats-banner-section {
    padding: 18px 0;
  }

  .marquee-logo {
    font-size: 20px;
  }

  .marquee-track {
    gap: 30px;
    padding-right: 30px;
  }

  .marquee-content {
    gap: 30px;
  }

  .marquee-item {
    gap: 12px;
  }
}
</style>
