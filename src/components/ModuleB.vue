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
           Balance: ${{userdata[model.username].money}}
        </blockquote>

          <v-text-field
            v-model="model.money"
            :rules="[v => !!v || model.money + ' is required']"
            label="Money"
            required
          ></v-text-field>

          <v-checkbox
            v-model="checkbox"
            :rules="[v => !!v || 'You must agree to continue!']"
            label="Are you SURE?"
            required
          ></v-checkbox>

          <v-btn :disabled="!valid" color="success" class="mr-4" @click="deposit">Deposit</v-btn>

          <v-btn :disabled="!valid" color="success" class="mr-4" @click="withdraw">Withdraw</v-btn>

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
    withdraw() {
      //compare current logged in username to username being deposited/withdrawn, prevent it with a return 0 if the user has a lower userlevel than the user they are changing
        let newVal = parseInt(this.userdata[this.model.username].money) - parseInt(this.model.money)
        if (newVal < 0) {
          alert('U broke')
          return 0;
        }
        let instance = this;
        this.axios
          .post(`/users/find/${this.model.username}/update/money`, {money: newVal})
          .then(response => {
            console.log(instance.model.money);
            console.log(instance.userdata[instance.model.username].money)
            console.log(response);
            instance.$router.go();
          })
    },

    deposit() {
      //prevent level 0 users from putting in money
      //not sure if possible, but maybe try sending a request where allowedVal = 1 as a user with lvl 0? unsure
        let allowedVal = parseInt(this.userdata[this.model.username].userLevel)
        console.log(allowedVal)
        if (allowedVal < 1) {
          alert ('You need to be at least user level 1 to deposit')
          return 0;
        }
        //compare current logged in username to username being deposited/withdrawn, prevent it with a return 0 if the user has a lower userlevel than the user they are changing
        let newVal = parseInt(this.model.money) + parseInt(this.userdata[this.model.username].money)
        let instance = this;
        this.axios
          .post(`/users/find/${this.model.username}/update/money`, {money: newVal})
          .then(response => {
            console.log(instance.model.money);
            console.log(instance.userdata[instance.model.username].money)
            console.log(response);
            instance.$router.go();
          })
    },

    reset() {
      this.$refs.form.reset();
    }
  },
  mounted() {
    let instance = this;
    let token = localStorage.getItem('token');
    this.axios
      .get("/users/all", {
        headers: { "x-access-token": token }
      })
      .then(response => {
        //take this out on the secure site
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