<!-- HeaderBlock.vue -->
<template>
  <header 
    :class="[
      'header-block relative',
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
      <h1 :class="[
        {
          'text-xl': titleSize === 'xl',
          'text-2xl': titleSize === '2xl',
          'text-3xl': titleSize === '3xl',
          'text-5xl': titleSize === '5xl',
          'font-normal': titleWeight === 'normal',
          'font-medium': titleWeight === 'medium',
          'font-semibold': titleWeight === 'semibold',
          'font-bold': titleWeight === 'bold',
          'text-gray-900': titleColor === 'gray-900',
          'text-gray-700': titleColor === 'gray-700',
          'text-tenant-primary': titleColor === 'tenant-primary'
        },
        titleFont,
        'leading-tight'
      ]">
        {{ title }}
      </h1>
      
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

      <!-- Botões de ação -->
      <div 
        v-if="actions && actions.length > 0"
        :class="[
          'mt-8',
          'flex',
          textAlignment === 'center' ? 'justify-center' : '',
          textAlignment === 'right' ? 'justify-end' : '',
          'gap-4'
        ]"
      >
        <a
          v-for="(action, index) in actions"
          :key="index"
          :href="action.url"
          :class="[
            'px-6 py-3 rounded-md font-medium transition-all duration-200',
            action.primary ? 'bg-tenant-primary text-white hover:bg-tenant-primary-dark' : 'border border-tenant-primary text-tenant-primary hover:bg-tenant-primary hover:text-white'
          ]"
        >
          {{ action.text }}
        </a>
      </div>
    </div>
  </header>
</template>

<script setup>
const props = defineProps({
  title: {
    type: String,
    required: true
  },
  subtitle: {
    type: String,
    default: ''
  },
  // Opções de Container
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
  // Botões de ação
  actions: {
    type: Array,
    default: () => []
  }
})
</script>

<style scoped>
.header-block {
  @apply py-12;
}

/* Estilos */
.style-default {
  @apply bg-white dark:bg-gray-800;
}

.style-highlighted {
  @apply bg-gray-50 dark:bg-gray-800;
}

.style-bordered {
  @apply border-b border-gray-200 dark:border-gray-700;
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