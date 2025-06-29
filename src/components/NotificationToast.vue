<template>
  <div :class="['fixed top-4 right-4 p-4 rounded-lg shadow-lg transition-all duration-300 z-50',
                type === 'success' ? 'bg-green-500 text-white' : 
                type === 'error' ? 'bg-red-500 text-white' : 
                'bg-blue-500 text-white']">
    <div class="flex items-center">
      <i :class="['fas mr-2', 
                 type === 'success' ? 'fa-check-circle' : 
                 type === 'error' ? 'fa-exclamation-circle' : 
                 'fa-info-circle']"></i>
      {{ message }}
    </div>
  </div>
</template>

<script>
export default {
  props: {
    type: String,
    message: String
  }
};
</script>