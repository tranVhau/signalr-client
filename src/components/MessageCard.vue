<template>
  <div
    class="message-wrapper d-flex"
    :class="{ 'justify-content-end': isSelfMessage }"
  >
    <div class="p-2" :class="isSelfMessage ? 'self-message' : 'other-message'">
      <div class="custom-status-text" :class="{ 'text-end': isSelfMessage }">
        <span class="custom-status-text">{{ this.user }}</span>
        <span class="custom-status-text">{{ this.time | formatTime }}</span>
      </div>
      <slot></slot>
    </div>
  </div>
</template>

<script>
export default {
  props: ["isSelfMessage", "user", "time"],

  filters: {
    formatTime(time) {
      const newTime = new Date(time);
      return `${newTime.getSeconds()}:${newTime.getHours()}:${newTime.getMinutes()}`;
    },
  },
};
</script>
<style lang="scss" scoped>
.message-wrapper {
  padding: 6px 10px;
  .self-message {
    background: rgb(119, 119, 169);
    max-width: 75%;
    border-radius: 8px;
    padding: 6px;
  }

  .other-message {
    background: rgb(212, 212, 216);
    max-width: 75%;
    min-width: 25%;
    border-radius: 8px;
    padding: 6px 10px;
  }
}
</style>
