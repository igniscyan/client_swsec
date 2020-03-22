<template>
  <div>
    <ModuleA/>
    <ModuleB/>
    <ModuleC/>
    <ModuleD/>
  </div>
</template>

<script>
// @ is an alias to /src
import ModuleA from '@/components/ModuleA.vue'



export default {
  name: 'Home',
  components: {
    ModuleA
  },
  mounted(){
    if(!localStorage.getItem('token')){
      this.$router.push('/login');
    }
  }
}
</script>
