<template>
  <v-container>
    <v-hover>
      <v-card slot-scope="{ hover }" :class="`elevation-${hover ? 8 : 2} clickable`">
        <v-card-title primary-title size="100%">
          <div>
            <h3 class="headline mb-0">Your Account Information</h3>
          </div>
        </v-card-title>

        <v-card-text>
          <v-simple-table>
            <template v-slot:default>
              <thead>
                <tr>
                  <th class="text-left">Property</th>
                  <th class="text-left">Value</th>
                </tr>
              </thead>
              <tbody>
                <!--Is there a way we can hide the password from being shown here? If so we can try to make an exploit to show it -->
                <tr v-for="(value,property) in bankdata" :key="property">
                  <td>{{property }}</td>
                  <td>{{ value }}</td>
                </tr>
              </tbody>
            </template>
          </v-simple-table>
        </v-card-text>
      </v-card>
    </v-hover>
  </v-container>
</template>
<script>
export default {
  name: "ModuleA",
  props: {},
  methods: {
    clicked: function() {}
  },

  data() {
    return {
      username: "",
      bankdata: {}
    };
  },
  mounted() {
    let instance = this;
    let token = localStorage.getItem('token');
    this.username = localStorage.getItem("user");
    this.axios
      .get(`/users/find/${this.username}`, {
        headers: { "x-access-token": token }
      })
      .then(response => {
        //take this out of the secure site
        console.log(response);
        if (response.status == 200) {
          instance.bankdata = response.data.response[0];
        }
      })
      .catch(err => {
        console.log(err);
      });
  }
};
</script>