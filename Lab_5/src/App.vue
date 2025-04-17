<script setup>
import { ref } from 'vue';

const number = ref('');
const category = ref('math');
const fact = ref('Здесь появятся интересные факты');
const isLoading = ref(false);

const fetchFact = async () => {
  if (!number.value) {
    fact.value = 'Введите число';
    return;
  }

  try {
    isLoading.value = true;
    const response = await fetch(`http://numbersapi.com/${number.value}/${category.value}?json`);
    const data = await response.json();

    if (data.found) {
      fact.value = data.text;
    } else {
      fact.value = `${number.value} — ${category.value === 'year' ? 'скучный год' : 'скучное число'}`;
    }
  } catch (error) {
    fact.value = 'Произошла ошибка при получении факта';
    console.error('Error fetching fact:', error);
  } finally {
    isLoading.value = false;
  }
};

const handleSubmit = (e) => {
  e.preventDefault();
  fetchFact();
};

const handleKeyPress = (e) => {
  if (e.key === 'Enter') {
    fetchFact();
  }
};
</script>

<template>
  <div class="app-container">
    <div class="app-card">
      <h1 class="app-title">Интересные числовые факты</h1>
      <p class="app-subtitle">Узнайте удивительные факты о числах</p>
      
      <form @submit.prevent="handleSubmit" class="fact-form">
        <div class="form-group">
          <input
            type="number"
            v-model.number="number"
            @keyup.enter="handleSubmit"
            placeholder="Введите любое число"
            :disabled="isLoading"
            class="form-input"
          >
        </div>
        
        <div class="form-group">
          <select v-model="category" :disabled="isLoading" class="form-select">
            <option value="trivia">Случайный факт</option>
            <option value="math">Математический факт</option>
            <option value="year">Факт о годе</option>
          </select>
        </div>
        
        <button 
          type="submit" 
          class="submit-btn"
          :disabled="isLoading || !number"
        >
          <span v-if="isLoading" class="loader"></span>
          <span v-else>Получить факт</span>
        </button>
      </form>
      
      <div class="fact-container" :class="{ 'error': error, 'loading': isLoading }">
        <div v-if="isLoading" class="loading-state">
          <div class="spinner"></div>
          <p>Загружаем факт...</p>
        </div>
        <div v-else class="fact-content">
          {{ fact }}
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.app-container {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 20px;
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
}

.app-card {
  background: white;
  border-radius: 16px;
  box-shadow: 0 10px 30px rgba(103, 58, 183, 0.1);
  padding: 40px;
  width: 100%;
  max-width: 500px;
}

.app-title {
  color: #673ab7;
  text-align: center;
  font-size: 28px;
  font-weight: 700;
  margin-bottom: 8px;
}

.app-subtitle {
  color: #9e9e9e;
  text-align: center;
  font-size: 16px;
  margin-bottom: 32px;
}

.fact-form {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.form-group {
  display: flex;
  flex-direction: column;
}

.form-input, .form-select {
  padding: 14px 16px;
  border: 2px solid #e0e0e0;
  border-radius: 10px;
  font-size: 16px;
  transition: all 0.3s ease;
  outline: none;
}

.form-input:focus, .form-select:focus {
  border-color: #673ab7;
  box-shadow: 0 0 0 3px rgba(103, 58, 183, 0.2);
}

.submit-btn {
  padding: 14px;
  background: linear-gradient(135deg, #673ab7 0%, #9c27b0 100%);
  color: white;
  border: none;
  border-radius: 10px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
}

.submit-btn:hover:not(:disabled) {
  background: linear-gradient(135deg, #5e35b1 0%, #8e24aa 100%);
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(103, 58, 183, 0.3);
}

.submit-btn:disabled {
  background: #b39ddb;
  cursor: not-allowed;
}

.fact-container {
  margin-top: 24px;
  padding: 20px;
  background: #f3e5f5;
  border-radius: 10px;
  min-height: 100px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-left: 4px solid #9c27b0;
}

.fact-container.error {
  background: #fce4ec;
  border-left-color: #e91e63;
  color: #c2185b;
}

.loading-state {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
  color: #7e57c2;
}

.spinner {
  width: 30px;
  height: 30px;
  border: 3px solid rgba(156, 39, 176, 0.2);
  border-radius: 50%;
  border-top-color: #9c27b0;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

.fact-content {
  font-size: 17px;
  line-height: 1.5;
  color: #4527a0;
  text-align: center;
}


</style>