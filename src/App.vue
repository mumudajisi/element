<template>
  <div id="app">
    <keep-alive>
      <router-view v-if="$route.meta.path" />
    </keep-alive>
    <router-view v-if="!$route.meta.path" />
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>
