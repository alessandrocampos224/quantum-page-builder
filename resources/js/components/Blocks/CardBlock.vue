<!-- CardBlock.vue -->
<template>
  <div 
    :class="[
      'card-block relative',
      `bg-${backgroundColor}`,
      `shadow-${shadow}`,
      `border-${borderStyle}`,
      `border-${borderWidth}`,
      `border-${borderColor}`,
      `rounded-${borderRadius}`,
      `py-${paddingY}`,
      `px-${paddingX}`,
      backgroundImage ? 'bg-cover bg-center' : '',
      overlayOpacity ? 'relative' : '',
      textAlignment === 'center' ? 'text-center' : '',
      textAlignment === 'right' ? 'text-right' : '',
    ]"
    :style="{
      backgroundImage: backgroundImage ? `url(${backgroundImage})` : null,
    }"
  >
    <!-- Overlay com opacidade -->
    <div 
      v-if="overlayOpacity"
      class="absolute inset-0 bg-black"
      :style="{ opacity: overlayOpacity }"
    ></div>

    <div :class="[
      'relative z-10',
      containerWidth === 'full' ? '' : 'container mx-auto',
      containerWidth === 'narrow' ? 'max-w-4xl' : ''
    ]">
      <div v-if="title || subtitle" class="mb-8">
        <h2 :class="[
          'text-' + titleSize,
          'font-' + titleWeight,
          'text-' + titleColor,
          titleFont,
          'leading-tight'
        ]">
          {{ title }}
        </h2>
        <p 
          v-if="subtitle"
          :class="[
            'mt-4',
            'text-' + subtitleSize,
            'font-' + subtitleWeight,
            'text-' + subtitleColor,
            subtitleFont
          ]"
        >
          {{ subtitle }}
        </p>
      </div>

      <div :class="[
        'grid',
        {
          'gap-4': spacing === 'compact',
          'gap-8': spacing === 'normal',
          'gap-12': spacing === 'relaxed'
        },
        {
          'grid-cols-1 md:grid-cols-2 lg:grid-cols-3': columns === 3,
          'grid-cols-1 md:grid-cols-2': columns === 2,
          'grid-cols-1 md:grid-cols-2 lg:grid-cols-4': columns === 4,
          'grid-cols-1': columns === 1
        }
      ]">
        <div v-for="(card, index) in safeCards" :key="index" 
          class="bg-white dark:bg-gray-800 overflow-hidden shadow-lg rounded-lg hover:shadow-xl transition-shadow duration-300 ease-in-out"
        >
          <div class="p-6">
            <div class="flex items-center mb-4">
              <component 
                v-if="card?.icon" 
                :is="getIconComponent(card.icon)"
                class="h-6 w-6 text-primary-600 dark:text-primary-400 mr-3"
              />
              <h3 class="text-xl font-semibold text-gray-900 dark:text-gray-100">
                {{ card?.title || 'Sem título' }}
              </h3>
            </div>
            
            <p class="text-gray-600 dark:text-gray-400 mb-4">
              {{ card?.description || 'Sem descrição' }}
            </p>

            <div v-if="card?.link?.text" class="mt-4">
              <a 
                :href="card.link.url"
                class="text-primary-600 dark:text-primary-400 hover:text-primary-700 dark:hover:text-primary-300 font-medium inline-flex items-center"
              >
                {{ card.link.text }}
                <svg class="w-5 h-5 ml-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7" />
                </svg>
              </a>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'
import {
  FileText,
  Users,
  Calendar,
  Phone,
  Mail,
  MapPin,
  Clock,
  MessageSquare
} from 'lucide-vue-next'

const props = defineProps({
  // Container
  containerWidth: {
    type: String,
    default: 'default' // 'default', 'full', 'narrow'
  },
  textAlignment: {
    type: String,
    default: 'left' // 'left', 'center', 'right'
  },
  // Background
  backgroundColor: {
    type: String,
    default: 'white' // 'white', 'gray-50', 'gray-100', etc
  },
  backgroundImage: {
    type: String,
    default: ''
  },
  overlayOpacity: {
    type: Number,
    default: 0 // 0 a 1
  },
  // Bordas e Sombras
  shadow: {
    type: String,
    default: 'none' // 'none', 'sm', 'md', 'lg', 'xl'
  },
  borderStyle: {
    type: String,
    default: 'none' // 'none', 'solid', 'dashed', 'dotted'
  },
  borderWidth: {
    type: String,
    default: '0' // '0', '1', '2', '4', '8'
  },
  borderColor: {
    type: String,
    default: 'gray-200'
  },
  borderRadius: {
    type: String,
    default: 'none' // 'none', 'sm', 'md', 'lg', 'full'
  },
  // Espaçamento
  paddingY: {
    type: String,
    default: '12' // '4', '8', '12', '16', '20'
  },
  paddingX: {
    type: String,
    default: '4' // '4', '8', '12', '16', '20'
  },
  // Título
  title: {
    type: String,
    default: ''
  },
  titleSize: {
    type: String,
    default: '4xl' // 'xl', '2xl', '3xl', '4xl', '5xl'
  },
  titleWeight: {
    type: String,
    default: 'bold' // 'normal', 'medium', 'semibold', 'bold'
  },
  titleColor: {
    type: String,
    default: 'gray-900'
  },
  titleFont: {
    type: String,
    default: 'font-sans' // 'font-sans', 'font-serif', 'font-mono'
  },
  // Subtítulo
  subtitle: {
    type: String,
    default: ''
  },
  subtitleSize: {
    type: String,
    default: 'xl'
  },
  subtitleWeight: {
    type: String,
    default: 'normal'
  },
  subtitleColor: {
    type: String,
    default: 'gray-600'
  },
  subtitleFont: {
    type: String,
    default: 'font-sans'
  },
  // Cards
  cards: {
    type: Array,
    default: () => []
  },
  columns: {
    type: Number,
    default: 3
  },
  // Espaçamento entre cards
  spacing: {
    type: String,
    default: 'normal' // 'compact', 'normal', 'relaxed'
  }
})

// Computed property para garantir que cards seja sempre um array
const safeCards = computed(() => {
  return Array.isArray(props.cards) ? props.cards : []
})

const getIconComponent = (iconName) => {
  if (!iconName) return FileText
  
  const icons = {
    'document': FileText,
    'users': Users,
    'calendar': Calendar,
    'phone': Phone,
    'email': Mail,
    'location': MapPin,
    'clock': Clock,
    'chat': MessageSquare
  }
  return icons[iconName] || FileText
}
</script>

<style scoped>
.card-block {
  @apply py-12;
}

/* Estilos */
.style-default {
  @apply bg-gray-50 dark:bg-gray-900;
}

.style-highlighted {
  @apply bg-white dark:bg-gray-800;
}

.style-bordered {
  @apply border border-gray-200 dark:border-gray-700 rounded-lg;
}

/* Layouts */
.layout-default {
  @apply w-full;
}

.layout-centered {
  @apply text-center;
}

.layout-narrow {
  @apply max-w-4xl mx-auto;
}

.layout-wide {
  @apply max-w-7xl mx-auto;
}

/* Espaçamentos Verticais */
.spacing-v-default {
  @apply py-12;
}

.spacing-v-compact {
  @apply py-8;
}

.spacing-v-comfortable {
  @apply py-16;
}

.spacing-v-spacious {
  @apply py-20;
}

/* Espaçamentos Horizontais */
.spacing-h-default {
  @apply px-4 sm:px-6 lg:px-8;
}

.spacing-h-compact {
  @apply px-4;
}

.spacing-h-comfortable {
  @apply px-6 sm:px-8 lg:px-10;
}

.spacing-h-spacious {
  @apply px-8 sm:px-10 lg:px-12;
}
</style> 