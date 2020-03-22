<template>
  <div>
      <container>
        <form>
          <v-text-field
          v-model ="username"
          label="Username"
          required
          ></v-text-field>
          
          <v-text-field
           v-model ="password"
          label="Password"
          required
          ></v-text-field>


            <v-btn @click="submit">register</v-btn>

        </form>
      </container>
  </div>
</template>

<script>
export default {
  data(){
    return{
      username: '',
      password: ''
    };
  },
  methods: {
    submit: function(){
       var instance = this;
       this.axios.post('users/register',{
         username: this.username,
         password: this.password
       }).then((response) => {
          if(response.status==200){
          console.log(response);
           localStorage.setItem('token', response.data.token);
           localStorage.setItem('user',response.data.data.username);
           instance.$router.push('/')

          }
          console.log(response);
       }).catch((err) => {
         console.log(err);
       })
    }
  }
}
</script>