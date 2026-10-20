<script setup lang="ts">
const props = defineProps<{
  initial?: number
}>()

const count = ref(props.initial ?? 0)

function inc() {
  count.value += 1
}

function dec() {
  count.value -= 1
}
</script>

<template>
  <div>
    {{ count }}
    <button class="inc" @click="inc">
      +
    </button>
    <button class="dec" @click="dec">
      -
    </button>
  </div>
</template>
