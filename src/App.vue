<template>
  <div id="app" class="uk-container">
    <div class="tm-header uk-visible@m" uk-header>

    </div>
    <div class="uk-section uk-section-large">
      <router-view/>
    </div>
  </div>
</template>

<script>
import Vue from 'vue'
import ECharts from 'vue-echarts'
import "echarts/lib/component/legend";
import "echarts/lib/component/tooltip";

Vue.component('v-chart', ECharts)

export default {

}
</script>