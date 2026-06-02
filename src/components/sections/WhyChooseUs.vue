<template>
  <section class="why-choose-us-section section-padding" id="tentang-kami">
    <div class="container">
      <!-- Left-Aligned Section Header -->
      <div class="section-header-left">
        <div class="badge-tag-why">
          <Star :size="12" class="badge-icon-star" />
          <span>MENGAPA MEMILIH KIRIMINYUK?</span>
        </div>
        <h2 class="benefits-heading">Pengiriman Terpercaya Untuk Anda</h2>
        <p class="benefits-description">
          Kami berkomitmen memberikan layanan terbaik dengan jaringan luas, teknologi canggih, dan tim profesional.
        </p>
      </div>

      <!-- Testimonials Container: Infinite Scrolling Marquee -->
      <div class="testimonials-marquee-wrapper">
        <div class="testimonials-marquee-track">
          <!-- First Set of Testimonials -->
          <div class="testimonials-marquee-content">
            <div 
              v-for="t in testimonials" 
              :key="'set1-' + t.id" 
              class="testimonial-card-modern"
            >
              <div class="stars-row-gold">
                <Star v-for="s in t.stars" :key="'star-set1-' + t.id + '-' + s" :size="16" fill="#facc15" color="#facc15" />
              </div>
              <blockquote class="quote-text-modern">
                "{{ t.quote }}"
              </blockquote>
              <div class="author-profile-modern">
                <img :src="t.avatar" :alt="t.name" class="author-avatar-circle" />
                <div class="author-meta-block">
                  <h4 class="author-name-bold">{{ t.name }}</h4>
                  <span class="author-occupation-subtitle">{{ t.occupation }}</span>
                </div>
              </div>
            </div>
          </div>
          <!-- Second Set of Testimonials (Duplicate for Seamless Loop) -->
          <div class="testimonials-marquee-content" aria-hidden="true">
            <div 
              v-for="t in testimonials" 
              :key="'set2-' + t.id" 
              class="testimonial-card-modern"
            >
              <div class="stars-row-gold">
                <Star v-for="s in t.stars" :key="'star-set2-' + t.id + '-' + s" :size="16" fill="#facc15" color="#facc15" />
              </div>
              <blockquote class="quote-text-modern">
                "{{ t.quote }}"
              </blockquote>
              <div class="author-profile-modern">
                <img :src="t.avatar" :alt="t.name" class="author-avatar-circle" />
                <div class="author-meta-block">
                  <h4 class="author-name-bold">{{ t.name }}</h4>
                  <span class="author-occupation-subtitle">{{ t.occupation }}</span>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Bottom Support CTA Card -->
      <div class="help-card-row-wrapper">
        <div class="help-card-royal-horizontal">
          <div class="help-card-left">
            <div class="help-icon-outer-white">
              <Headphones :size="28" class="help-icon-glyph-blue" />
            </div>
            <div class="help-text-block">
              <h3 class="help-title-white">Butuh Bantuan?</h3>
              <p class="help-desc-white">
                Tim customer service kami siap membantu segala kebutuhan pengiriman Anda 24 jam sehari.
              </p>
            </div>
          </div>
          <div class="help-card-right">
            <a href="#kontak" class="btn-help-solid-white">
              Hubungi Kami <ArrowRight :size="16" />
            </a>
            
            <div class="cta-divider-row-horizontal">
              <div class="cta-divider-line"></div>
              <span class="cta-divider-text">atau</span>
              <div class="cta-divider-line"></div>
            </div>

            <!-- WhatsApp Link -->
            <a 
              href="https://wa.me/6281234567890" 
              target="_blank" 
              class="whatsapp-link-btn"
            >
              <svg viewBox="0 0 24 24" class="whatsapp-icon-svg" xmlns="http://www.w3.org/2000/svg">
                <path d="M.057 24l1.687-6.163c-1.041-1.804-1.588-3.849-1.587-5.946C.06 5.348 5.397.01 12.008.01c3.202.001 6.212 1.246 8.477 3.514 2.266 2.268 3.507 5.28 3.505 8.484-.004 6.657-5.34 11.997-11.953 11.997-2.005-.001-3.973-.502-5.724-1.457L0 24zm6.59-4.846c1.6.95 3.188 1.449 4.825 1.451 5.436.002 9.858-4.37 9.861-9.809.002-2.633-1.02-5.109-2.88-6.97C16.59 1.96 14.12 .939 11.999.939 6.566.939 2.147 5.313 2.144 10.75c-.002 1.712.453 3.382 1.32 4.874L2.43 21.09l5.65-1.472zM17.15 14.39c-.28-.14-1.65-.81-1.91-.9-.26-.1-.45-.14-.64.14-.19.28-.73.9-.9 1.09-.17.19-.34.21-.62.07-1.16-.58-1.92-1.03-2.68-2.33-.2-.34.2-.32.57-1.06.06-.12.03-.23-.01-.33-.05-.1-.45-1.09-.62-1.5-.16-.39-.33-.33-.45-.33h-.39c-.14 0-.38.05-.58.27-.2.22-.77.75-.77 1.83s.79 2.13.9 2.27c.11.14 1.55 2.37 3.76 3.32.53.22.94.36 1.26.46.53.17 1.01.14 1.39.09.42-.06 1.65-.67 1.88-1.33.23-.66.23-1.22.16-1.33-.07-.12-.25-.19-.53-.33z"/>
              </svg>
              <span>Chat via WhatsApp &rarr;</span>
            </a>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import { Star, Headphones, ArrowRight } from '@lucide/vue';

export default {
  name: 'WhyChooseUsSection',
  components: {
    Star,
    Headphones,
    ArrowRight
  },
  setup() {
    const testimonials = [
      {
        id: 'budi',
        stars: 5,
        quote: 'Pengiriman cepat dan aman. Tracking-nya akurat banget, recommended!',
        name: 'Budi Santoso',
        occupation: 'Pemilik Toko Online',
        avatar: '/images/avatar-budi.png'
      },
      {
        id: 'rina',
        stars: 5,
        quote: 'Layanan sangat membantu bisnis saya, paket selalu sampai tepat waktu.',
        name: 'Rina Aprilia',
        occupation: 'Owner Retail Store',
        avatar: '/images/avatar-rina.png'
      },
      {
        id: 'hendra',
        stars: 5,
        quote: 'Tarifnya sangat bersahabat dibanding yang lain. Customer service-nya juga responsif sekali membantu kendala saya.',
        name: 'Hendra Wijaya',
        occupation: 'Digital Marketer',
        avatar: '/images/avatar-budi.png'
      },
      {
        id: 'siti',
        stars: 5,
        quote: 'Sangat mudah buat order massal di platform ini. Jemput paket gratis tanpa minimal order. Bisnis fashion saya jadi lancar!',
        name: 'Siti Rahma',
        occupation: 'Fashion Brand Owner',
        avatar: '/images/avatar-rina.png'
      }
    ];

    return {
      testimonials
    };
  }
};
</script>

<style scoped>
.why-choose-us-section {
  background-color: var(--bg-light);
  border-top: 1px solid var(--border-color);
}

/* Left-Aligned Header Layout */
.section-header-left {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  text-align: left;
  margin-bottom: 40px;
}

.badge-tag-why {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  padding: 6px 14px;
  background-color: var(--primary-light);
  color: var(--primary);
  font-weight: 700;
  font-size: 11px;
  border-radius: var(--radius-full);
  margin-bottom: 16px;
  align-self: flex-start;
}

.badge-icon-star {
  color: var(--primary);
  fill: var(--primary);
}

.benefits-heading {
  font-size: 36px;
  font-weight: 800;
  line-height: 1.25;
  letter-spacing: -1px;
  margin-top: 0;
  margin-bottom: 16px;
  color: var(--text-dark);
}

.benefits-description {
  font-size: 15px;
  color: var(--text-muted);
  line-height: 1.6;
  margin-top: 0;
  margin-bottom: 0;
  max-width: 600px;
}

/* Testimonials Marquee Track styling */
.testimonials-marquee-wrapper {
  width: 100%;
  overflow: hidden;
  margin-bottom: 48px;
  position: relative;
}

.testimonials-marquee-track {
  display: flex;
  width: max-content;
  animation: testimonialMarquee 40s linear infinite;
  gap: 24px;
  padding-right: 24px;
}

.testimonials-marquee-wrapper:hover .testimonials-marquee-track {
  animation-play-state: paused;
}

.testimonials-marquee-content {
  display: flex;
  gap: 24px;
  flex-shrink: 0;
}

.testimonial-card-modern {
  background-color: white;
  border: 1px solid var(--border-color);
  border-radius: var(--radius-lg);
  padding: 24px;
  box-shadow: var(--shadow-sm);
  display: flex;
  flex-direction: column;
  gap: 16px;
  text-align: left;
  transition: all var(--transition-normal);
  width: 380px;
  min-height: 195px;
  flex-shrink: 0;
}

.testimonial-card-modern:hover {
  transform: translateY(-4px);
  box-shadow: var(--shadow-md);
  border-color: rgba(10, 101, 255, 0.15);
}

.stars-row-gold {
  display: flex;
  gap: 3px;
}

.quote-text-modern {
  font-size: 14px;
  line-height: 1.6;
  color: var(--text-muted);
  font-style: italic;
  margin: 0;
}

.author-profile-modern {
  display: flex;
  align-items: center;
  gap: 12px;
  border-top: 1px solid var(--border-color);
  padding-top: 14px;
  margin-top: auto;
}

.author-avatar-circle {
  width: 40px;
  height: 40px;
  border-radius: var(--radius-full);
  object-fit: cover;
  border: 2.2px solid var(--primary-light);
}

.author-meta-block {
  display: flex;
  flex-direction: column;
  line-height: 1.25;
}

.author-name-bold {
  font-size: 14px;
  font-weight: 700;
  color: var(--text-dark);
  margin: 0;
}

.author-occupation-subtitle {
  font-size: 12px;
  color: var(--text-light);
}

/* Help Card Bottom Row horizontal styling */
.help-card-row-wrapper {
  width: 100%;
  max-width: 1100px;
  margin: 0 auto;
}

.help-card-royal-horizontal {
  background-color: var(--primary);
  background-image: linear-gradient(135deg, var(--primary) 0%, #0050d1 100%);
  color: white;
  border-radius: var(--radius-lg);
  padding: 28px 40px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 40px;
  box-shadow: 0 12px 30px rgba(10, 101, 255, 0.1);
  text-align: left;
}

.help-card-left {
  display: flex;
  align-items: center;
  gap: 24px;
  flex: 1;
}

.help-icon-outer-white {
  width: 56px;
  height: 56px;
  border-radius: var(--radius-full);
  background-color: white;
  color: var(--primary);
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
  flex-shrink: 0;
}

.help-icon-glyph-blue {
  stroke-width: 2.2;
}

.help-text-block {
  display: flex;
  flex-direction: column;
  gap: 6px;
}

.help-title-white {
  color: white;
  font-size: 22px;
  font-weight: 800;
  letter-spacing: -0.5px;
  margin: 0;
}

.help-desc-white {
  font-size: 13px;
  line-height: 1.5;
  color: rgba(255, 255, 255, 0.85);
  max-width: 480px;
  margin: 0;
}

.help-card-right {
  display: flex;
  align-items: center;
  gap: 24px;
  flex-shrink: 0;
}

.btn-help-solid-white {
  background-color: white;
  color: var(--primary);
  font-weight: 700;
  padding: 13px 28px;
  font-size: 14px;
  border-radius: var(--radius-md);
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.05);
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  transition: all var(--transition-fast) ease;
  cursor: pointer;
  text-decoration: none;
}

.btn-help-solid-white:hover {
  background-color: var(--primary-light);
  transform: translateY(-2px);
  box-shadow: 0 6px 14px rgba(0, 0, 0, 0.1);
}

.btn-help-solid-white svg {
  transition: transform var(--transition-fast);
}

.btn-help-solid-white:hover svg {
  transform: translateX(4px);
}

.cta-divider-row-horizontal {
  display: flex;
  align-items: center;
  width: 60px;
  gap: 8px;
  flex-shrink: 0;
}

.cta-divider-line {
  flex-grow: 1;
  height: 1px;
  background-color: rgba(255, 255, 255, 0.2);
}

.cta-divider-text {
  font-size: 12px;
  color: rgba(255, 255, 255, 0.7);
  text-transform: lowercase;
}

.whatsapp-link-btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 6px;
  color: white;
  font-weight: 700;
  font-size: 13px;
  transition: opacity var(--transition-fast);
  text-decoration: none;
  flex-shrink: 0;
}

.whatsapp-link-btn:hover {
  opacity: 0.9;
}

.whatsapp-icon-svg {
  width: 16px;
  height: 16px;
  fill: #25d366;
}

/* Keyframes for seamless scrolling */
@keyframes testimonialMarquee {
  0% {
    transform: translateX(0);
  }
  100% {
    transform: translateX(-50%);
  }
}

/* Responsiveness adjustments */
@media (max-width: 1024px) {
  .help-card-royal-horizontal {
    gap: 32px;
    padding: 24px 30px;
  }
}

@media (max-width: 900px) {
  .help-card-royal-horizontal {
    flex-direction: column;
    padding: 32px 24px;
    gap: 24px;
    text-align: center;
  }
  
  .help-card-left {
    flex-direction: column;
    text-align: center;
    gap: 16px;
  }
  
  .help-desc-white {
    max-width: 100%;
  }
  
  .help-card-right {
    flex-direction: column;
    width: 100%;
    gap: 16px;
  }
  
  .btn-help-solid-white {
    width: 100%;
    justify-content: center;
  }
  
  .cta-divider-row-horizontal {
    width: 100%;
  }
}

@media (max-width: 768px) {
  .benefits-heading {
    font-size: 28px;
  }
  
  .testimonial-card-modern {
    width: 290px;
    padding: 20px;
  }

  .testimonials-marquee-track {
    gap: 16px;
    padding-right: 16px;
  }

  .testimonials-marquee-content {
    gap: 16px;
  }
}
</style>
