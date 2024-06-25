<template>
    <div class="message" :class="type">
      {{ text }}
    </div>
  </template>
  
  <script>
  export default {
    name: 'Message',
    props: {
      text: String,
      type: String
    }
  };
  </script>
  