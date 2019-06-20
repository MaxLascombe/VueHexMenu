# Vue Hexagon Menu

## Usage

```html
<template>
  <div id="app">
    <HexMenu id="main-menu" :height=60 border-color='white'>
      <span slot='icon'>MENU</span>
      <a href="/home">Home</a>
      <a href="/info">Information</a>
      <a href="/settings">Settings</a>
    </HexMenu>
  </div>
</template>

<script>
import HexMenu from '{path-to-folder}/HexMenu/HexMenu.vue'

export default {
  name: 'app',
  components: { 
    HexMenu 
  }
}
</script>
