<template>
  <div>
    <ModuleA />
    <v-container>
      <v-row>
        <v-col>
          <ModuleB />
        </v-col>
        <v-col>
          <ModuleC v-if="userLevel>1"/>
        </v-col>
      </v-row>
    </v-container>
    <ModuleD />
  </div>
</template>

<script>
// @ is an alias to /src
import ModuleA from "@/components/ModuleA.vue";
import ModuleB from "@/components/ModuleB.vue";
import ModuleC from "@/components/ModuleC.vue";

export default {
  name: "Home",
  components: {
    ModuleA,
    ModuleB,
    ModuleC
  },
  data() {
    return {
      userLevel: null
    }
  },
  mounted() {
    if (!localStorage.getItem("token")) {
      this.$router.push("/login");
    }
    this.userLevel = localStorage.getItem("userLevel")
  }
};
</script>
