<template>
  <v-container>
    <v-hover>
      <v-row align="center">
        <v-form ref="form" v-model="valid">
          <v-select
            v-model="model.username"
            :items="items"
            :rules="[v => !!v || 'Item is required']"
            label="Username"
            required
          ></v-select>

        <blockquote v-if='model.username' class='blockquote'>
           User level: {{userdata[model.username].userlevel}}
        </blockquote>

          <v-text-field
            v-model="model.userLevel"
            :rules="[v => !!v || 'Item is required']"
            label="User Level"
            required
          ></v-text-field>

          <v-checkbox
            v-model="checkbox"
            :rules="[v => !!v || 'You must agree to continue!']"
            label="Are you SURE?"
            required
          ></v-checkbox>

          <v-btn :disabled="!valid" color="success" class="mr-4" @click="setLevel">Set</v-btn>

          <v-btn color="error" class="mr-4" @click="reset">Reset Form</v-btn>
        </v-form>
      </v-row>
    </v-hover>
  </v-container>
</template>
<script>
export default {
  data: () => ({
    valid: true,
    name: "",
    items: [],
    checkbox: false,
    model: {},
    userdata: {}
  }),

  methods: {
    setLevel() {
        let newVal = parseInt(this.model.userLevel)
        let thisUser = localStorage.getItem('user')
        let isMe = thisUser == this.model.username
        let instance = this;
        this.axios
          .post(`/users/find/${this.model.username}/update/level`, {userLevel: newVal})
          .then(response => {
            console.log(response);
            if (isMe) {
                localStorage.setItem('userLevel', newVal)
            }
            instance.$router.go();
          })
    },

    reset() {
      this.$refs.form.reset();
    }
  },
  mounted() {
    let instance = this;
    this.axios
      .get("/users/all")
      .then(response => {
        console.log(response);
        if (response.status == 200) {
          for (let i of response.data) {
            instance.items.push(i.username);
            instance.userdata[(i.username)]=i;
          }
        }
      })
      .catch(err => {
        console.log(err);
      });
  }
};
</script>