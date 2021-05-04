
<template>
  <Currency/>
</template>

<script setup>
  import Currency from './components/Currency.vue'
</script>

<style lang="scss">
  /**
  * Pseudo Elements
  */

  ::-webkit-scrollbar {
    width: 24px;
    height: 24px;
  }

  ::-webkit-scrollbar-thumb {
    border: 8px solid transparent;
    border-radius: 12px;
    box-shadow: inset 0 0 0 4px var(--primary);
  }

  :root {
    --primary: #00ad7c;
    --foreground: #fff;
    --red: #ff4a61;
    --dark-300: #2c3139;
    --dark-400: #22262d;
    --dark-450: #1e2127;
    --dark-500: #191c21;
  }

  /**
  * 1. (iOS Safari) Enable momentum scrolling on overflow elements
  */

  *,
  *::after,
  *::before {
    box-sizing: inherit;
  }

  *:not(svg *) {
    position: relative;
    margin: 0;
    padding: 0;
    border: 0;
    color: inherit;
    font: inherit;
    text-align: inherit;
    text-decoration: inherit;
    -webkit-overflow-scrolling: touch;
  }

  /**
  * 1. (WebKit/Blink) (Non-standard) Scrollbars overlay content
  * 2. (Touch devices) Prevent tap delay
  */

  html {
    overflow: overlay; /* 1. */
    touch-action: manipulation; /* 2. */
    box-sizing: border-box;
  }

  body {
    color: var(--foreground);
    font: 400 1em/1 sans-serif;
    background: var(--dark-500);
  }

  /**
  * NOTE: This sets the maximum site width when using this viewport meta tag.
  * Mobile devices will scale down from this size.
  * <meta name="viewport" content="width=device-width,maximum-scale=5">
  */

  @media (max-width: 375px) {
    body {
      width: 375px;
    }
  }
</style>
