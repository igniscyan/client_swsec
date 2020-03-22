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
            :rules="[v => !!v || 'Item is required']"
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