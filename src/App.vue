<template>
    <div id="app">
        <div class="header">
            <img src="static/images/wahpro-wide-logo.png" alt="WAH PRO Remote script Builder">
        </div>
        <div class="container">
            <builder></builder>
        </div>
    </div>
</template>

<script>

import Builder from './components/Builder';

export default {
  name: 'app',
  components: {
    Builder
  }
}
</script>
