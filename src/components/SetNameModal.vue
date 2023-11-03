<template>
  <div class="modal-bounder">
    <div class="modal-wrapper">
      <h5>set your name</h5>
      <input type="text" v-model="username" />
      <div class="confirm-btn" @click="setUsernameHandler">OK</div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      username: "",
    };
  },

  methods: {
    setUsernameHandler() {
      const tag = new Date();
      this.$emit(
        "setUsernameHandler",
        this.username
          ? this.username.trim()
          : "Anonymous" +
              tag.getMilliseconds() +
              tag.getSeconds() +
              tag.getMinutes() +
              tag.getHours()
      );
    },
  },
};
</script>

<style lang="scss" scoped>
.modal-bounder {
  position: absolute;
  display: flex;
  justify-content: center;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.251);

  .modal-wrapper {
    text-align: center;
    background: rgb(0, 107, 169);
    margin: auto;
    padding: 32px 40px;
    border-radius: 8px;

    h5 {
      color: white;
      font-weight: bolder;
    }

    input {
      border-radius: 6px;
      border: none;
      padding: 2px;
      margin: 26px;
      text-align: center;
      font-weight: 600;
      font-size: large;
    }

    .confirm-btn {
      background: rgb(242, 244, 244);
      padding: 2px 10px;
      border-radius: 4px;
      border: none;
      cursor: pointer;
      width: 40%;
      margin: auto;
    }
  }
}
</style>
