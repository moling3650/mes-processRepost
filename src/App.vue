<template>
  <div id="app">
    <transition name="slide-left">
      <keep-alive>
        <router-view :process="currentProcess"></router-view>
      </keep-alive>
    </transition>
  </div>
</template>

<script>
  export default {
    name: 'app',
    data () {
      return {
        routeMap: ['/', '/chart', '/charts'],
        rm_index: 0,
        processList: [],
        pl_index: 0
      }
    },
    computed: {
      currentProcess () {
        return (this.processList.length <= this.pl_index) ? {} : this.processList[this.pl_index]
      }
    },
    methods: {
      changePage () {
        this.rm_index = (this.rm_index + 1) % this.routeMap.length
        if (this.rm_index === 0) {
          this.pl_index = (this.pl_index + 1) % this.processList.length
        }
        this.$router.push(this.routeMap[this.rm_index])
      }
    },
    created () {
      this.$http.get('/DataAPI/ProduceReport/processInOutNg.ashx?ActType=GetProcessCode').then(res => {
        this.processList = res.data.ProcessList
        if (this.processList.length > 0) {
          setInterval(this.changePage, 5000)
        }
      }).catch(err => console.error(err))
    }
  }
</script>

<style>
  #app {
    position: relative;
    height: 100%;
    text-align: center;
  }

  .slide-left-enter-active, .slide-left-leave-active {
    transition: all .5s;
  }

  .slide-left-enter {
    transform: translateX(100%);
    opacity: 0;
  }

  .slide-left-leave-active {
    transform: translateX(-100%);
    opacity: 0;
  }
</style>
