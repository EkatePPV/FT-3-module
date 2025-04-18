<template>
  <div class="app">
    <div class="header">
      <h1 class="title">✨ Миксер эмодзи ✨</h1>
      <p class="subtitle">Создайте уникальную комбинацию из двух эмодзи</p>
    </div>

    <div v-if="isLoading" class="loading-state">
      <div class="spinner"></div>
      <p>Загружаем коллекцию эмодзи...</p>
    </div>

    <div v-else-if="error" class="error-state">
      <p>😕 Ошибка: {{ error }}</p>
      <button @click="fetchEmojis" class="retry-btn">Попробовать снова</button>
    </div>

    <div v-else class="emoji-selector">
      <div class="emoji-picker first-picker">
        <h2 class="picker-title">Первый эмодзи</h2>
        <EmojiList
          :emojis="emojis"
          @selectEmoji="(emoji) => selectEmoji(0, emoji)"
          :selected-emoji="selectedSmiles[0]"
          :key="'first-'+selectedSmiles[0]?.name"
        />
      </div>

      <div class="emoji-picker second-picker">
        <h2 class="picker-title">Второй эмодзи</h2>
        <EmojiList
          :emojis="emojis"
          @selectEmoji="(emoji) => selectEmoji(1, emoji)"
          :selected-emoji="selectedSmiles[1]"
          :key="'second-'+selectedSmiles[1]?.name"
        />
      </div>
    </div>

    <EmojiMixer
      :firstEmoji="selectedSmiles[0]"
      :secondEmoji="selectedSmiles[1]"
      :key="mixerKey"
      class="mixer-container"
    />
  </div>
</template>

<script>
import { ref, onMounted, computed } from 'vue'
import EmojiList from './components/EmojiList.vue'
import EmojiMixer from './components/EmojiMixer.vue'

export default {
  components: {
    EmojiList,
    EmojiMixer
  },
  setup() {
    const emojis = ref([])
    const isLoading = ref(true)
    const error = ref(null)
    const selectedSmiles = ref([null, null])

    const mixerKey = computed(() => {
      return `${selectedSmiles.value[0]?.name || 'null'}-${selectedSmiles.value[1]?.name || 'null'}`
    })

    const fetchEmojis = async () => {
      try {
        isLoading.value = true
        error.value = null
        const response = await fetch('https://emojihub.yurace.pro/api/all')
        if (!response.ok) throw new Error('Ошибка загрузки')
        emojis.value = await response.json()
      } catch (err) {
        error.value = err.message
      } finally {
        isLoading.value = false
      }
    }

    const selectEmoji = (index, emoji) => {
      const newArray = [...selectedSmiles.value]
      newArray[index] = emoji
      selectedSmiles.value = newArray
    }

    onMounted(fetchEmojis)

    return {
      emojis,
      isLoading,
      error,
      selectedSmiles,
      selectEmoji,
      mixerKey,
      fetchEmojis
    }
  }
}
</script>

<style scoped>
.app {
  max-width: 1200px;
  margin: 0 auto;
  padding: 2rem;
  font-family: 'Segoe UI', 'Helvetica Neue', sans-serif;
}

.header {
  text-align: center;
  margin-bottom: 3rem;
}

.title {
  font-size: 2.5rem;
  font-weight: 700;
  color: #6a5acd;
  margin-bottom: 0.5rem;
  background: linear-gradient(45deg, #6a5acd, #9370db);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.subtitle {
  font-size: 1.1rem;
  color: #777;
  margin-top: 0;
}

.emoji-selector {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 2rem;
  margin-bottom: 3rem;
}

.emoji-picker {
  background: white;
  border-radius: 16px;
  padding: 1.5rem;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
  transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
  border: 1px solid #f0f0f0;
}

.emoji-picker:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.12);
}

.first-picker {
  border-top: 4px solid #9370db;
}

.second-picker {
  border-top: 4px solid #6a5acd;
}

.picker-title {
  font-size: 1.3rem;
  color: #555;
  margin-top: 0;
  margin-bottom: 1.5rem;
  text-align: center;
}

.loading-state {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 3rem;
  background: #f9f9f9;
  border-radius: 16px;
  color: #666;
}

.spinner {
  width: 50px;
  height: 50px;
  border: 5px solid rgba(106, 90, 205, 0.2);
  border-radius: 50%;
  border-top-color: #6a5acd;
  animation: spin 1s linear infinite;
  margin-bottom: 1rem;
}

.error-state {
  padding: 2rem;
  background: #fff0f0;
  border-radius: 16px;
  color: #d33;
  text-align: center;
}

.retry-btn {
  margin-top: 1rem;
  padding: 0.7rem 1.5rem;
  background: #6a5acd;
  color: white;
  border: none;
  border-radius: 8px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s;
}

.retry-btn:hover {
  background: #5a4abd;
  transform: translateY(-2px);
}

.mixer-container {
  margin-top: 2rem;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

@media (max-width: 768px) {
  .emoji-selector {
    grid-template-columns: 1fr;
  }
  
  .header {
    margin-bottom: 2rem;
  }
  
  .title {
    font-size: 2rem;
  }
}
</style>