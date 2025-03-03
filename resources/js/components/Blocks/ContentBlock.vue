<!-- ContentBlock.vue -->
<template>
  <div 
    :class="[
      'content-block relative',
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
      <div class="prose dark:prose-invert max-w-none">
        <div v-if="useMarkdown" v-html="renderedContent"></div>
        <div v-else class="whitespace-pre-wrap">{{ displayContent }}</div>
      </div>
    </div>

    <!-- Área para componentes aninhados -->
    <div v-if="allowNesting" class="nested-components-container mt-8 border-t border-gray-200 dark:border-gray-700 pt-6">
      <slot></slot>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'
import { marked } from 'marked'

const props = defineProps({
  content: {
    type: String,
    default: ''
  },
  useMarkdown: {
    type: Boolean,
    default: true
  },
  // Fonte de dados
  dataSource: {
    type: String,
    default: 'static' // 'static', 'dynamic'
  },
  contentLink: {
    type: Object,
    default: null
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
  // Novas propriedades para o sistema de colunas e aninhamento
  columnSpan: {
    type: [Number, String],
    default: 12
  },
  allowNesting: {
    type: Boolean,
    default: false
  },
  // Novas propriedades para listagens dinâmicas
  contentType: {
    type: String,
    default: 'single'
  },
  selectedCategories: {
    type: Array,
    default: () => []
  },
  filterCategory: {
    type: [String, Number],
    default: 'all'
  },
  postsLimit: {
    type: [Number, String],
    default: 6
  }
})

// Conteúdo dinâmico ou estático
const displayContent = computed(() => {
  if (props.dataSource === 'dynamic' && props.contentLink?.data) {
    return props.contentLink.data.description || ''
  }
  return props.content
})

const renderedContent = computed(() => {
  const contentToRender = displayContent.value
  if (!contentToRender) return ''
  return marked(contentToRender)
})
</script>

<style scoped>
/* Estilos da prosa */
:deep(.prose) {
  @apply max-w-none;
}

:deep(.prose h1) {
  @apply text-3xl font-bold text-gray-900 dark:text-white mt-8 mb-4;
}

:deep(.prose h2) {
  @apply text-2xl font-semibold text-gray-900 dark:text-white mt-6 mb-4;
}

:deep(.prose h3) {
  @apply text-xl font-semibold text-gray-900 dark:text-white mt-4 mb-2;
}

:deep(.prose p) {
  @apply text-base text-gray-700 dark:text-gray-300 leading-relaxed mb-4;
}

:deep(.prose ul) {
  @apply list-disc list-inside text-gray-700 dark:text-gray-300 mb-4;
}

:deep(.prose ol) {
  @apply list-decimal list-inside text-gray-700 dark:text-gray-300 mb-4;
}

:deep(.prose a) {
  @apply text-blue-600 hover:underline;
}

:deep(.prose blockquote) {
  @apply border-l-4 border-gray-200 dark:border-gray-700 pl-4 italic text-gray-600 dark:text-gray-400;
}

:deep(.prose code) {
  @apply bg-gray-100 dark:bg-gray-800 rounded px-1 py-0.5 text-sm font-mono text-gray-800 dark:text-gray-200;
}

:deep(.prose pre) {
  @apply bg-gray-100 dark:bg-gray-800 rounded p-4 overflow-x-auto;
}

:deep(.prose img) {
  @apply rounded-lg shadow-lg max-w-full h-auto;
}

:deep(.prose table) {
  @apply min-w-full divide-y divide-gray-200 dark:divide-gray-700;
}

:deep(.prose th) {
  @apply px-6 py-3 bg-gray-50 dark:bg-gray-800 text-left text-xs font-medium text-gray-500 dark:text-gray-400 uppercase tracking-wider;
}

:deep(.prose td) {
  @apply px-6 py-4 whitespace-nowrap text-sm text-gray-900 dark:text-gray-100;
}

.content-block {
  @apply w-full;
}
</style> 