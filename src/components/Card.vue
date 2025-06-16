<template>
  <div
    class="card"
    :class="{ flipped, matched }"
    @click="$emit('click')"
  >
    <span v-if="flipped || matched">{{ card.icon }}</span>
  </div>
</template>

<script>
export default {
  props: ['card', 'flipped', 'matched'],
  emits: ['click'],
};
</script>

<style scoped>
.card {
  width: 70px;
  height: 70px;
  background: #3498db;
  margin: 5px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 2em;
  border-radius: 8px;
  cursor: pointer;
  transition: transform 0.3s, background 0.3s;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
  user-select: none;
}
.card.flipped, .card.matched {
  background: #fff;
  transform: rotateY(180deg);
  color: #222;
}
</style> 