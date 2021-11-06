<template>
  <div>
    <button type="button" class="btn" :class="addClasses" @click="clicked">
      {{ text }}
    </button>
  </div>
</template>

<script>
export default {
  data() {
    return {}
  },
  props: {
    text: String,
    addClasses: String,
  },
  methods: {
    clicked() {
      this.$emit('is-clicked');
    }
  }
}
</script>

<style lang="scss" scoped>
.btn {
  display: block;
  margin: 30px auto;
  background: #7b02e7;
  outline: none;
  width: 200px;
  height: 40px;
  border-radius: 4px;
  line-height: 36px;
  color: #FFF;
  font-size: 1em;
  white-space: nowrap;
  text-align: center;
  border: none;
  cursor: pointer;
  transform: translateY(0);
  box-shadow: 0 0 0 0 rgba(0,0,0,0);
  transition: background .3s ease, transform .4s ease, box-shadow .5s;

  &:hover {
    background: darken(#7b02e7, 10%);
    transform: translateY(-3px);
    box-shadow: 0 5px 30px 0 rgba(0,0,0, 0.1);
    transition: background .3s ease, transform .4s ease, box-shadow .5s;
  }

  &.btn-red {
    background: #e7024e;
    font-size: .8em;
    max-width: 80px;

    &:hover {
      background: lighten(#e7024e, 10%);
    }
  }
  &.btn-small {
    line-height: 23px;
    height: 25px;
  }
  &.disabled {
    opacity: 0.5;
    pointer-events: none;
  }
}
</style>
