<script setup>

const props = defineProps([
  'title',
  'description',
  'url',
  'og'
])

</script>

<template>
  <meta name="viewport" content="width=device-width, shrink-to-fit=no">
  <title>{{ title }}</title>
  <meta name="description" content="{{ description }}">
  <link rel="canonical" href="{{ url }}">

  <!-- OpenGraph - https://ogp.me -->
  <meta property="og:title" content="{{ title }}">
  <meta property="og:site_name" content="{{ title }}">
  <meta property="og:description" content="{{ description }}">
  <meta property="og:url" content="{{ url }}">
  <meta property="og:type" content="website">
  <meta property="og:image" content="{{ og }}">

  <!-- Twitter (using large preview images) -->
  <meta name="twitter:title" content="{{ title }}">
  <meta name="twitter:description" content="{{ description }}">
  <meta name="twitter:url" content="{{ url }}">
  <meta name="twitter:domain" content="{{ url }}">
  <meta name="twitter:image" content="{{ og }}">
  <meta name="twitter:card" content="summary_large_image">

</template>
