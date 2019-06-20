<template>
  <div :id="id" :class="{'hex-menu': true, 'open': isOpen}" :style="{width: wFromH(height) + 'px', height: height + 'px', '--animate-speed': animateS}">
    <svg :style="{width: (width+strokeWidth+2*s)+'px', height: '500px', position: 'relative'}">
      <polygon :id="id+'hex-menu-hex'" :points="getCoords" :style="{'stroke': borderColor, 'stroke-width': strokeWidth+'px', 'fill': getFill, cursor: (isOpen)?'auto':'pointer'}" @click="openMenu">
        <animate ref='hexOpen' attributeName="points" :dur="animateMS" :from='closedCoords' :to='openCoords' begin='indefinite' />
        <animate ref='hexClose' attributeName="points" :dur="animateMS" :from='openCoords' :to='closedCoords' begin='indefinite' />
      </polygon>
      Sorry, your browser does not support inline SVG.
    </svg>
    <div :id="id+'menu-container'" class="menu-container" :style="{width: (this.isOpen) ? width+'px' : Math.round(wFromH(height)/2-2*s)+'px', height: innerHeight, top: Math.round(strokeWidth/2+s)+'px', left: (this.isOpen) ? Math.round(strokeWidth/2+s)+'px': Math.round(strokeWidth/2+s+wFromH(height)/4)+'px', opacity: (rectOpac+1)%2, visibility: (rectOpac)?'hidden':'visible'}">
      <div :id="id+'menu-content'" class="menu-content" :style="{'width': width+'px'}">
        <slot></slot>
      </div>
    </div>
    <div :id="id+'icon'" class='icon'>
      <slot name='icon'></slot>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HexMenu',
  props: {
    id: String,
    height: { default: 100, type: Number },
    width: { default: 300, type: Number },
    strokeWidth: { default: 1, type: Number },
    animateSpeed: { default: 150, type: Number },
    borderColor: { default: 'white', type: String },
    openFill: { default: 'white', type: String }
  },
  data () {
    return {
      isOpen: false,
      animationDone: true,
      menuHeight: 300
    }
  },
  computed: {
    s: function () { return Math.round(Math.sqrt(2)*this.strokeWidth/2); },
    innerHeight: function () { return (this.isOpen) ? this.menuHeight+'px' : (this.height-2*this.strokeWidth)+'px'; },
    contentWidth: function () { return (this.isOpen) ? this.width : 0; },
    contentLeft: function () {return (this.isOpen) ? this.strokeWidth : 0; },
    getFill: function () { return (this.isOpen) ? this.openFill : 'black'; },
    parentBackground: function () { return this.parentElement; },
    animateMS: function () { return this.animateSpeed + 'ms'; },
    animateS: function () { return this.animateSpeed/1000 + 's'; },
    getCoords: function () {
      var open = (this.animationDone) ? this.isOpen : !this.isOpen;
      if (!open)
        return this.closedCoords;
      else
        return this.openCoords;
    },
    openCoords: function () {
      var menuH = this.menuHeight;
      var w = this.width;
      var s = this.s;
      var TL = s + ',' + s;
      var TR = this.strokeWidth+w+s + ',' + s;
      var R = this.strokeWidth+w+s + ',' + Math.round(menuH/2);
      var BR = this.strokeWidth+w+s + ',' + (this.strokeWidth+menuH+s);
      var BL = s + ',' + (this.strokeWidth+menuH+s);
      var L = s + ',' + Math.round(menuH/2);

      return TL+' '+TR+' '+R+' '+BR+' '+BL+' '+L;
    },
    closedCoords: function () {
      var h = Math.round(this.height / 2);
      var r = Math.round(this.wFromH(this.height)/2);
      var s = this.s;

      var TL = (Math.round(r/2)+s) + ',' + s;
      var TR = (Math.round(3*r/2)-s) + ',' + s;
      var R = (2*r-s) + ',' + h;
      var BR = (Math.round(3*r/2)-s) + ',' + (2*h-s);
      var BL = (Math.round(r/2)+s) + ',' + (2*h-s);
      var L = s + ',' + h;

      return TL+' '+TR+' '+R+' '+BR+' '+BL+' '+L;
    },
    rectOpac: function () { return (this.isOpen) ? 0 : 1; }
  },
  methods: {
    toggleMenu: function () {
      if (!this.isOpen) { this.openMenu() }
      else { this.closeMenu(); }
    },
    openMenu: function () {
      if (!this.isOpen) {
        this.$refs.hexOpen.beginElement();
        this.animationDone = false;
        setTimeout(() => this.animationDone = true, this.animateSpeed);
        this.isOpen = true;
      }
    },
    closeMenu: function () {
      if (this.isOpen) {
        this.$refs.hexClose.beginElement();
        this.animationDone = false;
        setTimeout(() => this.animationDone = true, this.animateSpeed);
        this.isOpen = false;
      }
    },
    wFromH: function (h) {
      return Math.round(2*h/Math.sqrt(3));
    }
  },
  mounted () {
    var clickOutside = function(e) {
      var poly = e.target.closest("#"+this.id+"hex-menu-hex");
      var content = e.target.closest("#"+this.id+"menu-container");
      var icon = e.target.closest("#"+this.id+"icon");
      if (poly == undefined && content == undefined && icon == undefined)
        this.closeMenu();
    }
    var clickOutsideMe = clickOutside.bind(this);
    document.addEventListener("click", clickOutsideMe);
  },
  beforeUpdate() {
    this.menuHeight = document.getElementById(this.id+"menu-content").offsetHeight;
  }
}
</script>

<style scoped>
  .hex-menu {color: white; display: block; z-index: 10; position: relative; pointer-events: none;}
  .hex-menu polygon, .menu-content {pointer-events: auto;}
  .menu-container {position: absolute; overflow: hidden; transition-property: width, height, left, opacity, visibility; transition-duration: var(--animate-speed); transition-timing-function: linear;}
  .menu-content {color: black;}
  .icon {position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); transition: opacity var(--animate-speed);}
  .open > .icon {opacity: 0;}
  polygon {transition: fill var(--animate-speed);}
</style>
