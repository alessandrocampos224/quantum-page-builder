<!-- ComponentSettings.vue -->
<template>
  <div class="space-y-6">
    <!-- Configurações comuns para todos os componentes -->
    <template v-for="type in ['header', 'content', 'benefits', 'simulator', 'contact-channels', 'cards', 'form']" :key="type">
      <template v-if="component?.type === type">
        <!-- Sistema de Colunas -->
        <div class="space-y-4">
          <h4 class="text-sm font-medium">Sistema de Colunas</h4>
          <div class="space-y-3">
            <div>
              <Label class="mb-1">Largura em Colunas</Label>
              <Select v-model="settings.columnSpan">
                <SelectTrigger>
                  <SelectValue placeholder="Selecione o número de colunas" />
                </SelectTrigger>
                <SelectContent>
                  <SelectItem v-for="n in 12" :key="n" :value="n">{{ n }} {{ n === 1 ? 'coluna' : 'colunas' }}</SelectItem>
                </SelectContent>
              </Select>
            </div>
          </div>
        </div>

        <!-- Aninhamento de Componentes -->
        <div class="space-y-4">
          <h4 class="text-sm font-medium">Aninhamento de Componentes</h4>
          <div class="space-y-3">
            <div class="flex items-center space-x-2">
              <Checkbox id="allowNesting" v-model="settings.allowNesting" />
              <Label for="allowNesting">Permitir componentes aninhados</Label>
            </div>
          </div>
        </div>

        <!-- Container -->
        <div class="space-y-4">
          <h4 class="text-sm font-medium">Container</h4>
          <div class="space-y-3">
            <div>
              <Label class="mb-1">Largura</Label>
              <Select v-model="settings.containerWidth">
                <SelectTrigger>
                  <SelectValue placeholder="Selecione a largura" />
                </SelectTrigger>
                <SelectContent>
                  <SelectItem value="default">Padrão</SelectItem>
                  <SelectItem value="full">Largura Total</SelectItem>
                  <SelectItem value="narrow">Estreito</SelectItem>
                </SelectContent>
              </Select>
            </div>
            <div>
              <Label class="mb-1">Alinhamento do Texto</Label>
              <Select v-model="settings.textAlignment">
                <SelectTrigger>
                  <SelectValue placeholder="Selecione o alinhamento" />
                </SelectTrigger>
                <SelectContent>
                  <SelectItem value="left">Esquerda</SelectItem>
                  <SelectItem value="center">Centro</SelectItem>
                  <SelectItem value="right">Direita</SelectItem>
                </SelectContent>
              </Select>
            </div>
          </div>
        </div>

        <!-- Opção de vincular conteúdo para header, content e cards -->
        <template v-if="type === 'header' || type === 'content' || type === 'cards'">
          <div class="space-y-4">
            <h4 class="text-sm font-medium">Vincular Conteúdo</h4>
            <div class="space-y-3">
              <div>
                <Label class="mb-1">Fonte de Dados</Label>
                <Select v-model="settings.dataSource">
                  <SelectTrigger>
                    <SelectValue placeholder="Selecione a fonte de dados" />
                  </SelectTrigger>
                  <SelectContent>
                    <SelectItem value="static">Conteúdo Estático</SelectItem>
                    <SelectItem value="dynamic">Conteúdo Dinâmico</SelectItem>
                  </SelectContent>
                </Select>
              </div>
              
              <div v-if="settings.dataSource === 'dynamic'">
                <div>
                  <Label class="mb-1">Tipo de Conteúdo</Label>
                  <Select v-model="settings.contentType">
                    <SelectTrigger>
                      <SelectValue placeholder="Selecione o tipo de conteúdo" />
                    </SelectTrigger>
                    <SelectContent>
                      <SelectItem value="single">Item Único</SelectItem>
                      <SelectItem value="category">Categoria</SelectItem>
                      <SelectItem value="posts">Posts</SelectItem>
                    </SelectContent>
                  </Select>
                </div>
                
                <div v-if="settings.contentType === 'single'">
                  <ContentSelector v-model="settings.contentLink" />
                </div>
                
                <div v-if="settings.contentType === 'category'">
                  <Label class="mb-1">Selecionar Categorias</Label>
                  <div class="space-y-2 mt-2">
                    <div v-for="(category, index) in availableCategories" :key="index" class="flex items-center space-x-2">
                      <Checkbox 
                        :id="`category-${index}`" 
                        v-model="settings.selectedCategories" 
                        :value="category.id" 
                        :checked="settings.selectedCategories?.includes(category.id)"
                      />
                      <Label :for="`category-${index}`">{{ category.name }}</Label>
                    </div>
                  </div>
                </div>
                
                <div v-if="settings.contentType === 'posts'">
                  <div>
                    <Label class="mb-1">Filtrar por Categoria</Label>
                    <Select v-model="settings.filterCategory">
                      <SelectTrigger>
                        <SelectValue placeholder="Selecione a categoria" />
                      </SelectTrigger>
                      <SelectContent>
                        <SelectItem value="all">Todas as categorias</SelectItem>
                        <SelectItem v-for="(category, index) in availableCategories" :key="index" :value="category.id">
                          {{ category.name }}
                        </SelectItem>
                      </SelectContent>
                    </Select>
                  </div>
                  <div class="mt-3">
                    <Label class="mb-1">Número de Posts</Label>
                    <Input v-model="settings.postsLimit" type="number" min="1" max="20" />
                  </div>
                </div>
              </div>
            </div>
          </div>
        </template>

        <!-- Configurações específicas do cabeçalho -->
        <template v-if="type === 'header'">
          <div class="space-y-4">
            <h4 class="text-sm font-medium">Cabeçalho</h4>
            <div class="space-y-3">
              <!-- Título -->
              <div v-if="settings.dataSource !== 'dynamic' || !settings.contentLink?.data">
                <Label class="mb-1">Título</Label>
                <Input v-model="settings.title" placeholder="Digite o título" />
              </div>
              <div v-else>
                <Label class="mb-1">Título (Dinâmico)</Label>
                <div class="p-2 bg-gray-50 dark:bg-gray-800 rounded border border-gray-200 dark:border-gray-700 text-sm">
                  {{ settings.contentLink.data.name }}
                </div>
              </div>
              <div>
                <Label class="mb-1">Tamanho do Título</Label>
                <Select v-model="settings.titleSize">
                  <SelectTrigger>
                    <SelectValue placeholder="Selecione o tamanho" />
                  </SelectTrigger>
                  <SelectContent>
                    <SelectItem value="xl">Pequeno</SelectItem>
                    <SelectItem value="2xl">Médio</SelectItem>
                    <SelectItem value="3xl">Grande</SelectItem>
                    <SelectItem value="5xl">Extra Grande</SelectItem>
                  </SelectContent>
                </Select>
              </div>
              <div>
                <Label class="mb-1">Peso do Título</Label>
                <Select v-model="settings.titleWeight">
                  <SelectTrigger>
                    <SelectValue placeholder="Selecione o peso" />
                  </SelectTrigger>
                  <SelectContent>
                    <SelectItem value="normal">Normal</SelectItem>
                    <SelectItem value="medium">Médio</SelectItem>
                    <SelectItem value="semibold">Semi-negrito</SelectItem>
                    <SelectItem value="bold">Negrito</SelectItem>
                  </SelectContent>
                </Select>
              </div>
              <div>
                <Label class="mb-1">Cor do Título</Label>
                <Select v-model="settings.titleColor">
                  <SelectTrigger>
                    <SelectValue placeholder="Selecione a cor" />
                  </SelectTrigger>
                  <SelectContent>
                    <SelectItem value="gray-900">Preto</SelectItem>
                    <SelectItem value="gray-700">Cinza Escuro</SelectItem>
                    <SelectItem value="tenant-primary">Cor Principal</SelectItem>
                  </SelectContent>
                </Select>
              </div>

              <!-- Subtítulo -->
              <div v-if="settings.dataSource !== 'dynamic' || !settings.contentLink?.data">
                <Label class="mb-1">Subtítulo</Label>
                <Input v-model="settings.subtitle" placeholder="Digite o subtítulo" />
              </div>
              <div v-else>
                <Label class="mb-1">Subtítulo (Dinâmico)</Label>
                <div class="p-2 bg-gray-50 dark:bg-gray-800 rounded border border-gray-200 dark:border-gray-700 text-sm">
                  {{ settings.contentLink.data.description || 'Sem descrição' }}
                </div>
              </div>
              <div>
                <Label class="mb-1">Tamanho do Subtítulo</Label>
                <Select v-model="settings.subtitleSize">
                  <SelectTrigger>
                    <SelectValue placeholder="Selecione o tamanho" />
                  </SelectTrigger>
                  <SelectContent>
                    <SelectItem value="lg">Pequeno</SelectItem>
                    <SelectItem value="xl">Médio</SelectItem>
                    <SelectItem value="2xl">Grande</SelectItem>
                  </SelectContent>
                </Select>
              </div>
              <div>
                <Label class="mb-1">Cor do Subtítulo</Label>
                <Select v-model="settings.subtitleColor">
                  <SelectTrigger>
                    <SelectValue placeholder="Selecione a cor" />
                  </SelectTrigger>
                  <SelectContent>
                    <SelectItem value="gray-600">Cinza</SelectItem>
                    <SelectItem value="gray-500">Cinza Claro</SelectItem>
                    <SelectItem value="tenant-primary">Cor Principal</SelectItem>
                  </SelectContent>
                </Select>
              </div>
            </div>
          </div>
        </template>

        <!-- Configurações específicas do bloco de conteúdo -->
        <template v-if="type === 'content'">
          <div class="space-y-4">
            <h4 class="text-sm font-medium">Conteúdo</h4>
            <div class="space-y-3">
              <div v-if="settings.dataSource !== 'dynamic' || !settings.contentLink?.data">
                <Label class="mb-1">Conteúdo</Label>
                <Textarea v-model="settings.content" placeholder="Digite o conteúdo" rows="6" />
              </div>
              <div v-else>
                <Label class="mb-1">Conteúdo (Dinâmico)</Label>
                <div class="p-2 bg-gray-50 dark:bg-gray-800 rounded border border-gray-200 dark:border-gray-700 text-sm max-h-32 overflow-y-auto">
                  {{ settings.contentLink.data.description || 'Sem conteúdo' }}
                </div>
              </div>
              <div>
                <Label class="mb-1">Tamanho do Texto</Label>
                <Select v-model="settings.textSize">
                  <SelectTrigger>
                    <SelectValue placeholder="Selecione o tamanho" />
                  </SelectTrigger>
                  <SelectContent>
                    <SelectItem value="sm">Pequeno</SelectItem>
                    <SelectItem value="base">Médio</SelectItem>
                    <SelectItem value="lg">Grande</SelectItem>
                  </SelectContent>
                </Select>
              </div>
              <div>
                <Label class="mb-1">Cor do Texto</Label>
                <Select v-model="settings.textColor">
                  <SelectTrigger>
                    <SelectValue placeholder="Selecione a cor" />
                  </SelectTrigger>
                  <SelectContent>
                    <SelectItem value="gray-900">Preto</SelectItem>
                    <SelectItem value="gray-700">Cinza Escuro</SelectItem>
                    <SelectItem value="gray-600">Cinza</SelectItem>
                  </SelectContent>
                </Select>
              </div>
            </div>
          </div>
        </template>

        <!-- Configurações específicas para cards -->
        <template v-if="type === 'cards'">
          <div class="space-y-4">
            <h4 class="text-sm font-medium">Cards</h4>
            <div class="space-y-3">
              <!-- Título da seção -->
              <div v-if="settings.dataSource !== 'dynamic' || !settings.contentLink?.data">
                <Label class="mb-1">Título da Seção</Label>
                <Input v-model="settings.title" placeholder="Digite o título da seção" />
              </div>
              <div v-else>
                <Label class="mb-1">Título da Seção (Dinâmico)</Label>
                <div class="p-2 bg-gray-50 dark:bg-gray-800 rounded border border-gray-200 dark:border-gray-700 text-sm">
                  {{ settings.contentLink.data.name }}
                </div>
              </div>
              
              <!-- Subtítulo da seção -->
              <div v-if="settings.dataSource !== 'dynamic' || !settings.contentLink?.data">
                <Label class="mb-1">Subtítulo da Seção</Label>
                <Input v-model="settings.subtitle" placeholder="Digite o subtítulo da seção" />
              </div>
              <div v-else>
                <Label class="mb-1">Subtítulo da Seção (Dinâmico)</Label>
                <div class="p-2 bg-gray-50 dark:bg-gray-800 rounded border border-gray-200 dark:border-gray-700 text-sm">
                  {{ settings.contentLink.data.description || 'Sem descrição' }}
                </div>
              </div>
              
              <!-- Configurações de layout -->
              <div>
                <Label class="mb-1">Número de Colunas</Label>
                <Select v-model="settings.columns">
                  <SelectTrigger>
                    <SelectValue placeholder="Selecione o número de colunas" />
                  </SelectTrigger>
                  <SelectContent>
                    <SelectItem value="1">1 Coluna</SelectItem>
                    <SelectItem value="2">2 Colunas</SelectItem>
                    <SelectItem value="3">3 Colunas</SelectItem>
                    <SelectItem value="4">4 Colunas</SelectItem>
                  </SelectContent>
                </Select>
              </div>
              
              <!-- Cards estáticos (apenas se não for dinâmico) -->
              <div v-if="settings.dataSource !== 'dynamic' || !settings.contentLink?.data">
                <Label class="mb-1">Cards</Label>
                <div v-for="(card, index) in settings.cards" :key="index" class="mb-4 p-3 border border-gray-200 dark:border-gray-700 rounded-md">
                  <div class="flex justify-between items-center mb-2">
                    <h5 class="text-sm font-medium">Card {{ index + 1 }}</h5>
                    <button @click="removeCard(index)" class="text-red-500 hover:text-red-700">
                      <Trash class="h-4 w-4" />
                    </button>
                  </div>
                  <div class="space-y-2">
                    <div>
                      <Label class="mb-1">Título</Label>
                      <Input v-model="card.title" placeholder="Título do card" />
                    </div>
                    <div>
                      <Label class="mb-1">Descrição</Label>
                      <Textarea v-model="card.description" placeholder="Descrição do card" rows="2" />
                    </div>
                    <div>
                      <Label class="mb-1">Ícone</Label>
                      <Select v-model="card.icon">
                        <SelectTrigger>
                          <SelectValue placeholder="Selecione um ícone" />
                        </SelectTrigger>
                        <SelectContent>
                          <SelectItem value="document">Documento</SelectItem>
                          <SelectItem value="calendar">Calendário</SelectItem>
                          <SelectItem value="users">Usuários</SelectItem>
                          <SelectItem value="chart">Gráfico</SelectItem>
                        </SelectContent>
                      </Select>
                    </div>
                    <div>
                      <Label class="mb-1">Link</Label>
                      <div class="grid grid-cols-2 gap-2">
                        <Input v-model="card.link.text" placeholder="Texto do link" />
                        <Input v-model="card.link.url" placeholder="URL" />
                      </div>
                    </div>
                  </div>
                </div>
                <button @click="addCard" class="w-full py-2 px-4 border border-gray-300 dark:border-gray-700 rounded-md text-sm text-gray-700 dark:text-gray-300 hover:bg-gray-50 dark:hover:bg-gray-800 mt-2">
                  <div class="flex items-center justify-center">
                    <Plus class="h-4 w-4 mr-2" />
                    <span>Adicionar Card</span>
                  </div>
                </button>
              </div>
              
              <!-- Mensagem para cards dinâmicos -->
              <div v-else-if="settings.contentLink?.type === 'category'" class="p-4 bg-gray-50 dark:bg-gray-800 rounded-md border border-gray-200 dark:border-gray-700">
                <p class="text-sm text-gray-600 dark:text-gray-400">
                  Os cards serão gerados automaticamente a partir dos posts da categoria selecionada.
                </p>
                <p class="text-xs text-gray-500 dark:text-gray-500 mt-2">
                  Categoria: {{ settings.contentLink.data.name }} ({{ settings.contentLink.data.posts_count }} posts)
                </p>
              </div>
            </div>
          </div>
        </template>

        <!-- Background -->
        <div class="space-y-4">
          <h4 class="text-sm font-medium">Background</h4>
          <div class="space-y-3">
            <div>
              <Label class="mb-1">Cor de Fundo</Label>
              <Select v-model="settings.backgroundColor">
                <SelectTrigger>
                  <SelectValue placeholder="Selecione a cor de fundo" />
                </SelectTrigger>
                <SelectContent>
                  <SelectItem value="white">Branco</SelectItem>
                  <SelectItem value="gray-50">Cinza Claro</SelectItem>
                  <SelectItem value="gray-100">Cinza</SelectItem>
                  <SelectItem value="tenant-primary">Cor Principal</SelectItem>
                  <SelectItem value="tenant-secondary">Cor Secundária</SelectItem>
                </SelectContent>
              </Select>
            </div>
            <div>
              <Label class="mb-1">Imagem de Fundo</Label>
              <div class="flex gap-2">
                <Input v-model="settings.backgroundImage" placeholder="URL da imagem" />
                <Button variant="outline" @click="openMediaLibrary">Escolher</Button>
              </div>
            </div>
            <div v-if="settings.backgroundImage">
              <Label class="mb-1">Opacidade do Overlay</Label>
              <Slider v-model="settings.overlayOpacity" :min="0" :max="1" :step="0.1" />
            </div>
          </div>
        </div>

        <!-- Bordas e Sombras -->
        <div class="space-y-4">
          <h4 class="text-sm font-medium">Bordas e Sombras</h4>
          <div class="space-y-3">
            <div>
              <Label class="mb-1">Sombra</Label>
              <Select v-model="settings.shadow">
                <SelectTrigger>
                  <SelectValue placeholder="Selecione a sombra" />
                </SelectTrigger>
                <SelectContent>
                  <SelectItem value="none">Nenhuma</SelectItem>
                  <SelectItem value="sm">Pequena</SelectItem>
                  <SelectItem value="md">Média</SelectItem>
                  <SelectItem value="lg">Grande</SelectItem>
                  <SelectItem value="xl">Extra Grande</SelectItem>
                </SelectContent>
              </Select>
            </div>
            <div>
              <Label class="mb-1">Estilo da Borda</Label>
              <Select v-model="settings.borderStyle">
                <SelectTrigger>
                  <SelectValue placeholder="Selecione o estilo" />
                </SelectTrigger>
                <SelectContent>
                  <SelectItem value="none">Nenhuma</SelectItem>
                  <SelectItem value="solid">Sólida</SelectItem>
                  <SelectItem value="dashed">Tracejada</SelectItem>
                  <SelectItem value="dotted">Pontilhada</SelectItem>
                </SelectContent>
              </Select>
            </div>
            <div v-if="settings.borderStyle !== 'none'">
              <Label class="mb-1">Espessura da Borda</Label>
              <Select v-model="settings.borderWidth">
                <SelectTrigger>
                  <SelectValue placeholder="Selecione a espessura" />
                </SelectTrigger>
                <SelectContent>
                  <SelectItem value="1">1px</SelectItem>
                  <SelectItem value="2">2px</SelectItem>
                  <SelectItem value="4">4px</SelectItem>
                  <SelectItem value="8">8px</SelectItem>
                </SelectContent>
              </Select>
            </div>
            <div v-if="settings.borderStyle !== 'none'">
              <Label class="mb-1">Cor da Borda</Label>
              <Select v-model="settings.borderColor">
                <SelectTrigger>
                  <SelectValue placeholder="Selecione a cor" />
                </SelectTrigger>
                <SelectContent>
                  <SelectItem value="gray-200">Cinza Claro</SelectItem>
                  <SelectItem value="gray-300">Cinza</SelectItem>
                  <SelectItem value="tenant-primary">Cor Principal</SelectItem>
                  <SelectItem value="tenant-secondary">Cor Secundária</SelectItem>
                </SelectContent>
              </Select>
            </div>
            <div>
              <Label class="mb-1">Arredondamento</Label>
              <Select v-model="settings.borderRadius">
                <SelectTrigger>
                  <SelectValue placeholder="Selecione o arredondamento" />
                </SelectTrigger>
                <SelectContent>
                  <SelectItem value="none">Nenhum</SelectItem>
                  <SelectItem value="sm">Pequeno</SelectItem>
                  <SelectItem value="md">Médio</SelectItem>
                  <SelectItem value="lg">Grande</SelectItem>
                  <SelectItem value="full">Total</SelectItem>
                </SelectContent>
              </Select>
            </div>
          </div>
        </div>

        <!-- Espaçamento -->
        <div class="space-y-4">
          <h4 class="text-sm font-medium">Espaçamento</h4>
          <div class="space-y-3">
            <!-- Espaçamento entre componentes -->
            <div>
              <Label class="mb-1">Espaçamento entre Componentes</Label>
              <Select v-model="settings.spacing">
                <SelectTrigger>
                  <SelectValue placeholder="Selecione o espaçamento" />
                </SelectTrigger>
                <SelectContent>
                  <SelectItem value="compact">Compacto</SelectItem>
                  <SelectItem value="normal">Normal</SelectItem>
                  <SelectItem value="relaxed">Relaxado</SelectItem>
                </SelectContent>
              </Select>
            </div>

            <!-- Padding Vertical -->
            <div>
              <Label class="mb-1">Padding Vertical</Label>
              <Select v-model="settings.paddingY">
                <SelectTrigger>
                  <SelectValue placeholder="Selecione o padding vertical" />
                </SelectTrigger>
                <SelectContent>
                  <SelectItem value="4">Pequeno (py-4)</SelectItem>
                  <SelectItem value="8">Médio (py-8)</SelectItem>
                  <SelectItem value="12">Normal (py-12)</SelectItem>
                  <SelectItem value="16">Grande (py-16)</SelectItem>
                  <SelectItem value="20">Extra Grande (py-20)</SelectItem>
                </SelectContent>
              </Select>
            </div>

            <!-- Padding Horizontal -->
            <div>
              <Label class="mb-1">Padding Horizontal</Label>
              <Select v-model="settings.paddingX">
                <SelectTrigger>
                  <SelectValue placeholder="Selecione o padding horizontal" />
                </SelectTrigger>
                <SelectContent>
                  <SelectItem value="4">Pequeno (px-4)</SelectItem>
                  <SelectItem value="8">Médio (px-8)</SelectItem>
                  <SelectItem value="12">Normal (px-12)</SelectItem>
                  <SelectItem value="16">Grande (px-16)</SelectItem>
                  <SelectItem value="20">Extra Grande (px-20)</SelectItem>
                </SelectContent>
              </Select>
            </div>
          </div>
        </div>

        <!-- Conteúdo específico de cada componente -->
        <template v-if="type === 'benefits'">
          <!-- Conteúdo existente do componente benefits -->
          <div class="space-y-4">
            <div class="flex items-center justify-between">
              <h4 class="text-sm font-medium">Benefícios</h4>
              <button type="button" @click="addBenefit" class="text-sm text-tenant-primary hover:text-tenant-primary-dark">
                Adicionar Benefício
              </button>
            </div>
            <div v-if="settings.items" class="space-y-4">
              <div v-for="(item, index) in settings.items" :key="index" class="p-4 bg-gray-50 dark:bg-gray-800 rounded-lg">
                <div class="flex justify-between items-start mb-3">
                  <h5 class="text-sm font-medium">Benefício {{ index + 1 }}</h5>
                  <button type="button" @click="removeBenefit(index)" class="text-red-500 hover:text-red-600">
                    <Trash class="w-4 h-4" />
                  </button>
                </div>
                <div class="space-y-3">
                  <div>
                    <Label class="mb-1">Título</Label>
                    <Input v-model="item.title" placeholder="Digite o título" />
                  </div>
                  <div>
                    <Label class="mb-1">Descrição</Label>
                    <Textarea v-model="item.description" rows="2" />
                  </div>
                  <div>
                    <Label class="mb-1">Link</Label>
                    <div class="space-y-2">
                      <Input v-model="item.link.text" placeholder="Texto do link" />
                      <Input v-model="item.link.url" placeholder="URL" />
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Layout -->
          <div class="space-y-4">
            <h4 class="text-sm font-medium">Layout</h4>
            <div class="space-y-3">
              <div>
                <Label class="mb-1">Estilo</Label>
                <Select v-model="settings.style">
                  <SelectTrigger>
                    <SelectValue placeholder="Selecione o estilo" />
                  </SelectTrigger>
                  <SelectContent>
                    <SelectItem value="default">Padrão</SelectItem>
                    <SelectItem value="cards">Cards</SelectItem>
                    <SelectItem value="minimal">Minimalista</SelectItem>
                  </SelectContent>
                </Select>
              </div>
              <div>
                <Label class="mb-1">Colunas</Label>
                <Select v-model.number="settings.columns">
                  <SelectTrigger>
                    <SelectValue placeholder="Selecione o número de colunas" />
                  </SelectTrigger>
                  <SelectContent>
                    <SelectItem value="2">2 Colunas</SelectItem>
                    <SelectItem value="3">3 Colunas</SelectItem>
                    <SelectItem value="4">4 Colunas</SelectItem>
                  </SelectContent>
                </Select>
              </div>
            </div>
          </div>
        </template>

        <template v-if="type === 'simulator'">
          <!-- Conteúdo existente do componente simulator -->
          <div class="space-y-4">
            <h4 class="text-sm font-medium">Cabeçalho</h4>
            <div class="space-y-3">
              <div>
                <Label class="mb-1">Título</Label>
                <Input v-model="settings.title" placeholder="Digite o título" />
              </div>
              <div>
                <Label class="mb-1">Descrição</Label>
                <Textarea v-model="settings.description" rows="2" />
              </div>
            </div>
          </div>

          <!-- Campos -->
          <div class="space-y-4">
            <div class="flex items-center justify-between">
              <h4 class="text-sm font-medium">Campos do Formulário</h4>
              <button type="button" @click="addField" class="text-sm text-tenant-primary hover:text-tenant-primary-dark">
                Adicionar Campo
              </button>
            </div>
            <div v-if="settings.fields" class="space-y-4">
              <div v-for="(field, index) in settings.fields" :key="index" class="p-4 bg-gray-50 dark:bg-gray-800 rounded-lg">
                <div class="flex justify-between items-start mb-3">
                  <h5 class="text-sm font-medium">Campo {{ index + 1 }}</h5>
                  <button type="button" @click="removeField(index)" class="text-red-500 hover:text-red-600">
                    <Trash class="w-4 h-4" />
                  </button>
                </div>
                <div class="space-y-3">
                  <div>
                    <Label class="mb-1">Tipo</Label>
                    <Select v-model="field.type">
                      <SelectTrigger>
                        <SelectValue placeholder="Selecione o tipo" />
                      </SelectTrigger>
                      <SelectContent>
                        <SelectItem value="text">Texto</SelectItem>
                        <SelectItem value="number">Número</SelectItem>
                        <SelectItem value="select">Seleção</SelectItem>
                        <SelectItem value="date">Data</SelectItem>
                        <SelectItem value="currency">Moeda</SelectItem>
                      </SelectContent>
                    </Select>
                  </div>
                  <div>
                    <Label class="mb-1">Nome do Campo</Label>
                    <Input v-model="field.name" placeholder="Digite o nome do campo" />
                  </div>
                  <div>
                    <Label class="mb-1">Rótulo</Label>
                    <Input v-model="field.label" placeholder="Digite o rótulo" />
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Botão -->
          <div class="space-y-4">
            <h4 class="text-sm font-medium">Botão de Envio</h4>
            <div class="space-y-3">
              <div>
                <Label class="mb-1">Texto do Botão</Label>
                <Input v-model="settings.submitText" placeholder="Digite o texto do botão" />
              </div>
            </div>
          </div>
        </template>

        <template v-if="type === 'contact-channels'">
          <!-- Conteúdo existente do componente contact-channels -->
          <div class="space-y-4">
            <h4 class="text-sm font-medium">Título</h4>
            <div class="space-y-3">
              <div>
                <Label class="mb-1">Texto</Label>
                <Input v-model="settings.title" placeholder="Digite o título" />
              </div>
            </div>
          </div>

          <!-- Canais -->
          <div class="space-y-4">
            <div class="flex items-center justify-between">
              <h4 class="text-sm font-medium">Canais</h4>
              <button type="button" @click="addChannel" class="text-sm text-tenant-primary hover:text-tenant-primary-dark">
                Adicionar Canal
              </button>
            </div>
            <div v-if="settings.channels" class="space-y-4">
              <div v-for="(channel, index) in settings.channels" :key="index" class="p-4 bg-gray-50 dark:bg-gray-800 rounded-lg">
                <div class="flex justify-between items-start mb-3">
                  <h5 class="text-sm font-medium">Canal {{ index + 1 }}</h5>
                  <button type="button" @click="removeChannel(index)" class="text-red-500 hover:text-red-600">
                    <Trash class="w-4 h-4" />
                  </button>
                </div>
                <div class="space-y-3">
                  <div>
                    <Label class="mb-1">Título</Label>
                    <Input v-model="channel.title" placeholder="Digite o título" />
                  </div>
                  <div>
                    <Label class="mb-1">Descrição</Label>
                    <Textarea v-model="channel.description" rows="2" />
                  </div>
                  <div>
                    <Label class="mb-1">Ícone</Label>
                    <Select v-model="channel.icon">
                      <SelectTrigger>
                        <SelectValue placeholder="Selecione o ícone" />
                      </SelectTrigger>
                      <SelectContent>
                        <SelectItem value="Users">Atendimento Presencial</SelectItem>
                        <SelectItem value="Mail">E-mail</SelectItem>
                        <SelectItem value="Phone">Telefone</SelectItem>
                        <SelectItem value="Calendar">Agendamento</SelectItem>
                        <SelectItem value="Clock">Horário</SelectItem>
                        <SelectItem value="MapPin">Localização</SelectItem>
                        <SelectItem value="Globe">Website</SelectItem>
                        <SelectItem value="MessageSquare">Chat</SelectItem>
                      </SelectContent>
                    </Select>
                  </div>
                  <!-- Detalhes do Canal -->
                  <div class="space-y-2">
                    <div class="flex items-center justify-between">
                      <label class="text-xs font-medium">Detalhes</label>
                      <button type="button" @click="addChannelDetail(channel)" class="text-xs text-tenant-primary hover:text-tenant-primary-dark">
                        Adicionar Detalhe
                      </button>
                    </div>
                    <div v-for="(detail, detailIndex) in channel.details" :key="detailIndex" class="flex items-center space-x-2">
                      <Select v-model="detail.type">
                        <SelectTrigger>
                          <SelectValue placeholder="Selecione o tipo" />
                        </SelectTrigger>
                        <SelectContent>
                          <SelectItem value="address">Endereço</SelectItem>
                          <SelectItem value="phone">Telefone</SelectItem>
                          <SelectItem value="email">E-mail</SelectItem>
                          <SelectItem value="schedule">Horário</SelectItem>
                          <SelectItem value="time">Tempo</SelectItem>
                        </SelectContent>
                      </Select>
                      <Input v-model="detail.value" placeholder="Digite o valor" />
                      <button type="button" @click="removeChannelDetail(channel, detailIndex)" class="text-red-500 hover:text-red-600">
                        <Trash class="w-4 h-4" />
                      </button>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Layout -->
          <div class="space-y-4">
            <h4 class="text-sm font-medium">Layout</h4>
            <div class="space-y-3">
              <div>
                <Label class="mb-1">Estilo</Label>
                <Select v-model="settings.style">
                  <SelectTrigger>
                    <SelectValue placeholder="Selecione o estilo" />
                  </SelectTrigger>
                  <SelectContent>
                    <SelectItem value="default">Padrão</SelectItem>
                    <SelectItem value="cards">Cards</SelectItem>
                    <SelectItem value="minimal">Minimalista</SelectItem>
                  </SelectContent>
                </Select>
              </div>
              <div>
                <Label class="mb-1">Colunas</Label>
                <Select v-model.number="settings.columns">
                  <SelectTrigger>
                    <SelectValue placeholder="Selecione o número de colunas" />
                  </SelectTrigger>
                  <SelectContent>
                    <SelectItem value="2">2 Colunas</SelectItem>
                    <SelectItem value="3">3 Colunas</SelectItem>
                    <SelectItem value="4">4 Colunas</SelectItem>
                  </SelectContent>
                </Select>
              </div>
            </div>
          </div>
        </template>

        <template v-if="type === 'cards'">
          <!-- Título e Subtítulo -->
          <div class="space-y-4">
            <h4 class="text-sm font-medium">Título</h4>
            <div class="space-y-3">
              <div>
                <Label class="mb-1">Texto</Label>
                <Input v-model="settings.title" placeholder="Digite o título" />
              </div>
              <div>
                <Label class="mb-1">Tamanho</Label>
                <Select v-model="settings.titleSize">
                  <SelectTrigger>
                    <SelectValue placeholder="Selecione o tamanho" />
                  </SelectTrigger>
                  <SelectContent>
                    <SelectItem value="xl">Pequeno</SelectItem>
                    <SelectItem value="2xl">Médio</SelectItem>
                    <SelectItem value="3xl">Grande</SelectItem>
                    <SelectItem value="5xl">Extra Grande</SelectItem>
                  </SelectContent>
                </Select>
              </div>
              <div>
                <Label class="mb-1">Peso</Label>
                <Select v-model="settings.titleWeight">
                  <SelectTrigger>
                    <SelectValue placeholder="Selecione o peso" />
                  </SelectTrigger>
                  <SelectContent>
                    <SelectItem value="normal">Normal</SelectItem>
                    <SelectItem value="medium">Médio</SelectItem>
                    <SelectItem value="semibold">Semi-negrito</SelectItem>
                    <SelectItem value="bold">Negrito</SelectItem>
                  </SelectContent>
                </Select>
              </div>
              <div>
                <Label class="mb-1">Cor</Label>
                <Select v-model="settings.titleColor">
                  <SelectTrigger>
                    <SelectValue placeholder="Selecione a cor" />
                  </SelectTrigger>
                  <SelectContent>
                    <SelectItem value="gray-900">Preto</SelectItem>
                    <SelectItem value="gray-700">Cinza Escuro</SelectItem>
                    <SelectItem value="tenant-primary">Cor Principal</SelectItem>
                    <SelectItem value="white">Branco</SelectItem>
                  </SelectContent>
                </Select>
              </div>
            </div>
          </div>

          <div class="space-y-4">
            <h4 class="text-sm font-medium">Subtítulo</h4>
            <div class="space-y-3">
              <div>
                <Label class="mb-1">Texto</Label>
                <Input v-model="settings.subtitle" placeholder="Digite o subtítulo" />
              </div>
              <div>
                <Label class="mb-1">Tamanho</Label>
                <Select v-model="settings.subtitleSize">
                  <SelectTrigger>
                    <SelectValue placeholder="Selecione o tamanho" />
                  </SelectTrigger>
                  <SelectContent>
                    <SelectItem value="base">Pequeno</SelectItem>
                    <SelectItem value="lg">Médio</SelectItem>
                    <SelectItem value="xl">Grande</SelectItem>
                    <SelectItem value="2xl">Extra Grande</SelectItem>
                  </SelectContent>
                </Select>
              </div>
              <div>
                <Label class="mb-1">Peso</Label>
                <Select v-model="settings.subtitleWeight">
                  <SelectTrigger>
                    <SelectValue placeholder="Selecione o peso" />
                  </SelectTrigger>
                  <SelectContent>
                    <SelectItem value="normal">Normal</SelectItem>
                    <SelectItem value="medium">Médio</SelectItem>
                    <SelectItem value="semibold">Semi-negrito</SelectItem>
                  </SelectContent>
                </Select>
              </div>
              <div>
                <Label class="mb-1">Cor</Label>
                <Select v-model="settings.subtitleColor">
                  <SelectTrigger>
                    <SelectValue placeholder="Selecione a cor" />
                  </SelectTrigger>
                  <SelectContent>
                    <SelectItem value="gray-600">Cinza</SelectItem>
                    <SelectItem value="gray-500">Cinza Claro</SelectItem>
                    <SelectItem value="tenant-primary">Cor Principal</SelectItem>
                    <SelectItem value="white">Branco</SelectItem>
                  </SelectContent>
                </Select>
              </div>
            </div>
          </div>

          <!-- Cards -->
          <div class="space-y-4">
            <div class="flex items-center justify-between">
              <h4 class="text-sm font-medium">Cards</h4>
              <button type="button" @click="addCard" class="text-sm text-tenant-primary hover:text-tenant-primary-dark">
                Adicionar Card
              </button>
            </div>
            <div v-if="settings?.cards?.length > 0" class="space-y-4">
              <div v-for="(card, index) in settings.cards" :key="index" class="p-4 bg-gray-50 dark:bg-gray-800 rounded-lg">
                <div class="flex justify-between items-start mb-3">
                  <h5 class="text-sm font-medium">Card {{ index + 1 }}</h5>
                  <button type="button" @click="removeCard(index)" class="text-red-500 hover:text-red-600">
                    <Trash class="w-4 h-4" />
                  </button>
                </div>
                <div class="space-y-3">
                  <div>
                    <Label class="mb-1">Título</Label>
                    <Input v-model="card.title" placeholder="Digite o título" />
                  </div>
                  <div>
                    <Label class="mb-1">Descrição</Label>
                    <Textarea v-model="card.description" rows="2" />
                  </div>
                  <div>
                    <Label class="mb-1">Ícone</Label>
                    <Select v-model="card.icon">
                      <SelectTrigger>
                        <SelectValue placeholder="Selecione o ícone" />
                      </SelectTrigger>
                      <SelectContent>
                        <SelectItem value="document">Documento</SelectItem>
                        <SelectItem value="users">Usuários</SelectItem>
                        <SelectItem value="calendar">Calendário</SelectItem>
                        <SelectItem value="phone">Telefone</SelectItem>
                        <SelectItem value="email">Email</SelectItem>
                        <SelectItem value="location">Localização</SelectItem>
                        <SelectItem value="Clock">Relógio</SelectItem>
                        <SelectItem value="chat">Chat</SelectItem>
                      </SelectContent>
                    </Select>
                  </div>
                  <div>
                    <Label class="mb-1">Link</Label>
                    <div class="space-y-2">
                      <Input v-model="card.link.text" placeholder="Texto do link" />
                      <Input v-model="card.link.url" placeholder="URL" />
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </template>

        <template v-if="type === 'form'">
          <!-- Conteúdo existente do componente form -->
          <div class="space-y-4">
            <div class="flex items-center justify-between">
              <h4 class="text-sm font-medium">Campos do Formulário</h4>
              <button type="button" @click="addFormField" class="text-sm text-tenant-primary hover:text-tenant-primary-dark">
                Adicionar Campo
              </button>
            </div>
            <div v-if="settings.fields" class="space-y-4">
              <div v-for="(field, index) in settings.fields" :key="index" class="p-4 bg-gray-50 dark:bg-gray-800 rounded-lg">
                <div class="flex justify-between items-start mb-3">
                  <h5 class="text-sm font-medium">Campo {{ index + 1 }}</h5>
                  <button type="button" @click="removeFormField(index)" class="text-red-500 hover:text-red-600">
                    <Trash class="w-4 h-4" />
                  </button>
                </div>
                <div class="space-y-3">
                  <div>
                    <Label class="mb-1">Tipo</Label>
                    <Select v-model="field.type">
                      <SelectTrigger>
                        <SelectValue placeholder="Selecione o tipo" />
                      </SelectTrigger>
                      <SelectContent>
                        <SelectItem value="text">Texto</SelectItem>
                        <SelectItem value="number">Número</SelectItem>
                        <SelectItem value="select">Seleção</SelectItem>
                        <SelectItem value="date">Data</SelectItem>
                        <SelectItem value="currency">Moeda</SelectItem>
                      </SelectContent>
                    </Select>
                  </div>
                  <div>
                    <Label class="mb-1">Nome do Campo</Label>
                    <Input v-model="field.name" placeholder="Digite o nome do campo" />
                  </div>
                  <div>
                    <Label class="mb-1">Rótulo</Label>
                    <Input v-model="field.label" placeholder="Digite o rótulo" />
                  </div>
                  <div>
                    <Label class="mb-1">Placeholder</Label>
                    <Input v-model="field.placeholder" placeholder="Digite o placeholder" />
                  </div>
                  <div>
                    <Label class="mb-1">Obrigatório</Label>
                    <Input type="checkbox" v-model="field.required" />
                  </div>
                  <div>
                    <Label class="mb-1">Opções</Label>
                    <div class="space-y-2">
                      <div v-for="(option, optionIndex) in field.options" :key="optionIndex" class="flex items-center space-x-2">
                        <Input v-model="option.label" placeholder="Digite a opção" />
                        <button type="button" @click="removeFieldOption(field, optionIndex)" class="text-red-500 hover:text-red-600">
                          <Trash class="w-4 h-4" />
                        </button>
                      </div>
                      <button type="button" @click="addFieldOption(field)" class="text-xs text-tenant-primary hover:text-tenant-primary-dark">
                        Adicionar Opção
                      </button>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </template>
      </template>
    </template>
  </div>
</template>

<script setup>
import { ref, reactive, watch } from 'vue'
import { Trash, Plus } from 'lucide-vue-next'
import { Input } from './ui/input'
import { Label } from './ui/label'
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from './ui/select'
import { Textarea } from './ui/textarea'
import { Button } from './ui/button'
import { Slider } from './ui/slider'
import { Checkbox } from './ui/checkbox'
import ContentSelector from './ContentSelector.vue'

const props = defineProps({
  component: {
    type: Object,
    required: true,
    default: () => ({
      type: '',
      props: {}
    })
  }
})

const emit = defineEmits(['update'])

// Adicionar propriedades para o sistema de colunas e aninhamento
const columnSettings = {
  columnSpan: 6,
  allowNesting: false,
  contentType: 'single',
  selectedCategories: [],
  filterCategory: 'all',
  postsLimit: 6
}

// Inicializa as configurações com valores padrão baseados no tipo do componente
const initializeSettings = () => {
  const defaultSettings = {
    containerWidth: 'default',
    textAlignment: 'left',
    backgroundColor: 'white',
    backgroundImage: '',
    overlayOpacity: 0,
    shadow: 'none',
    borderStyle: 'none',
    borderWidth: '0',
    borderColor: 'gray-200',
    borderRadius: 'none',
    paddingY: '12',
    paddingX: '4',
    spacing: 'normal',
    style: 'default',
    dataSource: 'static',
    contentLink: null,
    ...columnSettings
  }

  if (props.component?.type === 'header') {
    return {
      ...defaultSettings,
      title: '',
      subtitle: '',
      titleSize: '4xl',
      titleWeight: 'bold',
      titleColor: 'gray-900',
      titleFont: 'font-sans',
      subtitleSize: 'xl',
      subtitleWeight: 'normal',
      subtitleColor: 'gray-600',
      subtitleFont: 'font-sans',
      actions: []
    }
  } else if (props.component?.type === 'content') {
    return {
      ...defaultSettings,
      content: '',
      textSize: 'base',
      textColor: 'gray-900',
      textFont: 'font-sans'
    }
  } else if (props.component?.type === 'benefits') {
    return {
      ...defaultSettings,
      items: [],
      columns: 3
    }
  } else if (props.component?.type === 'simulator') {
    return {
      ...defaultSettings,
      title: '',
      description: '',
      fields: [],
      submitText: 'Calcular'
    }
  } else if (props.component?.type === 'contact-channels') {
    return {
      ...defaultSettings,
      title: '',
      channels: [],
      columns: 3,
      style: 'default'
    }
  } else if (props.component?.type === 'cards') {
    return {
      ...defaultSettings,
      title: '',
      subtitle: '',
      cards: [{
        title: 'Novo Card',
        description: 'Descrição do card',
        icon: 'document',
        link: {
          text: 'Ver mais',
          url: '#'
        }
      }],
      columns: 3,
      spacingVertical: 'default',
      spacingHorizontal: 'default'
    }
  } else if (props.component?.type === 'form') {
    return {
      ...defaultSettings,
      title: '',
      description: '',
      fields: [],
      submitText: 'Enviar'
    }
  } else {
    return defaultSettings
  }
}

// Inicializa as configurações com os valores do componente ou valores padrão
const settings = reactive({
  ...initializeSettings(),
  ...(props.component?.props || {})
})

// Categorias disponíveis (simulação - deve ser carregado da API)
const availableCategories = ref([
  { id: 1, name: 'Audiência Pública' },
  { id: 2, name: 'Prova de Vida' },
  { id: 3, name: 'Auditoria' },
  { id: 4, name: 'Certificação' },
  { id: 5, name: 'Notícias' },
  { id: 6, name: 'Convivência' },
  { id: 7, name: 'Serviços' },
  { id: 8, name: 'Formação' },
  { id: 9, name: 'Informativo' }
])

// Métodos para gerenciar botões de ação
const addAction = () => {
  if (!settings.actions) {
    settings.actions = []
  }
  settings.actions.push({
    text: 'Novo Botão',
    url: '#',
    primary: false
  })
}

const removeAction = (index) => {
  settings.actions.splice(index, 1)
}

// Métodos para gerenciar benefícios
const addBenefit = () => {
  if (!settings.items) {
    settings.items = []
  }
  settings.items.push({
    icon: 'Clock',
    title: 'Novo Benefício',
    description: 'Descrição do benefício',
    link: {
      text: 'Ver mais',
      url: '#'
    }
  })
}

const removeBenefit = (index) => {
  settings.items.splice(index, 1)
}

// Métodos para gerenciar campos do simulador
const addField = () => {
  if (!settings.fields) {
    settings.fields = []
  }
  settings.fields.push({
    type: 'text',
    name: 'novo_campo',
    label: 'Novo Campo'
  })
}

const removeField = (index) => {
  settings.fields.splice(index, 1)
}

// Métodos para gerenciar canais de atendimento
const addChannel = () => {
  if (!settings.channels) {
    settings.channels = []
  }
  settings.channels.push({
    icon: 'Users',
    title: 'Novo Canal',
    description: 'Descrição do canal',
    details: []
  })
}

const removeChannel = (index) => {
  settings.channels.splice(index, 1)
}

const addChannelDetail = (channel) => {
  if (!channel.details) {
    channel.details = []
  }
  channel.details.push({
    type: 'address',
    value: ''
  })
}

const removeChannelDetail = (channel, detailIndex) => {
  channel.details.splice(detailIndex, 1)
}

// Métodos para gerenciar cards
const addCard = () => {
  if (!Array.isArray(settings.cards)) {
    settings.cards = []
  }
  settings.cards.push({
    title: 'Novo Card',
    description: 'Descrição do card',
    icon: 'document',
    link: {
      text: 'Ver mais',
      url: '#'
    }
  })
}

const removeCard = (index) => {
  if (Array.isArray(settings.cards)) {
    settings.cards.splice(index, 1)
  }
}

// Métodos para gerenciar campos do formulário
const addFormField = () => {
  if (!settings.fields) {
    settings.fields = []
  }
  settings.fields.push({
    type: 'text',
    name: 'novo_campo',
    label: 'Novo Campo',
    placeholder: '',
    required: false,
    options: []
  })
}

const removeFormField = (index) => {
  if (settings.fields) {
    settings.fields.splice(index, 1)
  }
}

const addFieldOption = (field) => {
  if (!field.options) {
    field.options = []
  }
  field.options.push({
    label: 'Nova Opção',
    value: field.options.length + 1
  })
}

const removeFieldOption = (field, optionIndex) => {
  if (field.options) {
    field.options.splice(optionIndex, 1)
  }
}

const openMediaLibrary = () => {
  // Implementar integração com biblioteca de mídia
  console.log('Abrir biblioteca de mídia')
}

// Watch para emitir atualizações
watch(settings, (newSettings) => {
  console.log('ComponentSettings - Settings atualizados:', {
    ...newSettings,
    style: newSettings.style // Log específico do estilo
  })
  console.log('ComponentSettings - Tipo do componente:', props.component?.type)
  console.log('ComponentSettings - ID do componente:', props.component?.id)
  emit('update', props.component.id, newSettings)
}, { deep: true })

// Watch para atualizar quando o componente mudar
watch(() => props.component, (newComponent) => {
  if (newComponent) {
    console.log('ComponentSettings - Novo componente:', {
      type: newComponent.type,
      id: newComponent.id,
      props: newComponent.props
    })
    Object.assign(settings, {
      ...initializeSettings(),
      ...(newComponent.props || {})
    })
  }
}, { deep: true })

// Inicializar as configurações quando o componente é montado
if (props.component) {
  Object.assign(settings, {
    ...initializeSettings(),
    ...(props.component.props || {})
  })
}
</script> 