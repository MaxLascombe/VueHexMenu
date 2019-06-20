# Vue Hexagon Menu

## Usage

```html
<template>
  <div id="app">
    <HexMenu id="main-menu" border-color='white'>
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
```

## Attributes

* __:animate-speed__ Speed of open animation (ms) (Default: 150)

* __border-color__ Color of hexagon border (Default: 'white')

* __:height__ Height of hexagon (Default: 100)

* __id__ HexMenu element id.

* __open-fill__ Color of hexagon fill when open (Default: 'white')

* __:stroke-width__ Width of hexagon border (Default: 1)

* __:width__ Width of content of menu (Default: 300)
