<template>
  <div class="no-result">
    <img alt="" class="img" src="@/assets/no-result.svg" />
    <p class="text-info">{{ t('no-result') }}</p>
  </div>
</template>

<script lang="ts" setup>
import { useI18n } from 'vue-i18n'

const { t } = useI18n()
</script>

<style lang="postcss" scoped>
.no-result {
  margin-top: 2rem;
  text-align: center;
}

.img {
  display: block;
  margin-left: auto;
  margin-right: auto;
  margin-bottom: 1em;
  width: 17em;
  height: 13em;
}

.text-info {
  margin: auto;
  width: 8em;
  font-style: normal;
  font-weight: 500;
  font-size: 2rem;
  line-height: 150%;
  color: var(--color-brand-txt-base);
}
</style>
