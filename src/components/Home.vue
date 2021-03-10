<template>
  <div class="home-container">
    <el-button type="info" @click="logout">退出</el-button>
  </div>
</template>

<script>
export default {
  name: 'HomePage',
  data () {
    return {}
  },
  computed: {},
  watch: {},
  created () {
  },
  mounted () {
  },
  methods: {
    logout () {
      window.sessionStorage.clear()
      this.$router.push('/login')
    }
  }
}
</script>

<style scoped>

</style>
