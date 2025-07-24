<template>
  <button 
    class="recycle-btn"
    @click="handleRecycle"
    :disabled="recyclableCount === 0"
  >
    回收({{ activeFilter }}: {{ recyclableCount }})
  </button>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  filteredCards: {
    type: Array,
    required: true
  },
  activeFilter: {
    type: String,
    required: true
  }
})

const emit = defineEmits(['recycle'])

// 计算可回收的卡片数量（未质押的卡片）
const recyclableCount = computed(() => {
  return props.filteredCards.filter(card => 
    card.id !== 0 && !card.isStaked && card.berry_consumption >= 0
  ).length
})

const handleRecycle = () => {
  // 获取可回收卡片的 raw_index
  const recyclableCards = props.filteredCards
    .filter(card => card.id !== 0 && !card.isStaked && card.berry_consumption >= 0)
    .map(card => card.raw_index)
  
  if (recyclableCards.length > 0) {
    emit('recycle', {
      indices: recyclableCards,
      filteredCards: props.filteredCards,
      activeFilter: props.activeFilter
    })
  }
}
</script>

<style scoped>
.recycle-btn {
  min-width: 60px;
  width: fit-content;
  flex-grow: 0;
  background: #28a745;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 10px;
  font-weight: bold;
  transition: background-color 0.2s;
}

.recycle-btn:hover {
  background: #218838;
}

.recycle-btn:disabled {
  background: #6c757d;
  cursor: not-allowed;
}
</style>