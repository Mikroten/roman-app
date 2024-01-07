<template>
  <div class="modal" v-if="show">
    <div class="modal-content">
      <slot></slot>
      <button @click="$emit('close')">Close</button>
    </div>
  </div>
</template>

<script setup>
defineProps({
  show: Boolean
});
</script>

<style scoped>
.modal {
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;

}

.modal-content {
  background-color: white;
  padding: 20px;
  border-radius: 5px;
  width: 200px;
}

.modal-content h2, p {
  margin: 0;
}

button{
  margin-top: 10px;
  height: 30px;
  border: 3px solid black;
  background: white;
  border-radius: 7px;
  cursor: pointer;
  margin-left: 10px;
}
</style>
