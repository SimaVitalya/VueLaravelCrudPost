<template>
  <div>h</div>
</template>

<script>

export default {
  mounted() {
    // this.$parent.$parent.parentLog(); таким образом мы вызвали родиляля родителя так как щас мы находимся в дочернем компоненте sortmenu а он в свою очередь дочерний компонент Post(а таким образом мы взяли данные из родителя Post)
  },
  methods: {

    logSomeComponent() {
      console.log('its some comp')
    }
  },
};
</script>
