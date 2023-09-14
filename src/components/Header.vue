<script setup>
</script>

<template>
  <header class="header">
    <img class="header__item" src="@/assets/back.svg" alt="" @click="$emit('back')">
    <img class="header__item" src="@/assets/forward.svg" alt="" @click="$emit('forward')">
    <img class="header__item" src="@/assets/label.svg" alt="" @click="$emit('label')">
    <img class="header__item" src="@/assets/text.svg" alt="" @click="$emit('text')">
    <img class="header__item" src="@/assets/image.svg" alt="" @click="$emit('img')">
    <p class="header__text" @click="$emit('copy')">Скопировать HTML</p>
  </header>
</template>

<style scoped lang="sass">
.header
  width: 960px
  display: flex
  gap: 12px
  align-items: center
  padding: 77px 0px 31px 107px
  &__item
    cursor: pointer
    padding: 0px
  &__text
    color: #639EFf
    font-family: Roboto
    font-size: 15px
    line-height: 23px
    padding-left: 8px
    cursor: pointer
</style>