<template>  
  <Wrap :page="page">
    <article class="post">
      <header class="post-header">
        <h1 class="post-title">{{ page.title }}</h1>
      </header>
  
      <div class="post-content">
        <slot name="default"></slot>
      </div>
    </article>
  </Wrap>
</template>

<script>
import Wrap from '../components/Wrap.vue';
export default {
  components: {
    Wrap: Wrap
  },
  props: ['page']
};
</script>