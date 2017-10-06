<template>
    <div id="app">
        <app-header></app-header>
        <div class="container">
            <builder></builder>
        </div>
    </div>
</template>

<script>

import Builder from './components/Builder';
import Header from './components/Header';

export default {
    name: 'app',
    components: {
        Builder,
        appHeader: Header
    }
}
</script>
