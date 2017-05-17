<template lang="html">
  <div class="Home">
    <div class="row" v-for="(match,index) in listmatch" >
      <div class="col s12 m4 l2" ></div>
      <div class="col s12 m4 l10">
        <div class="row">
        <div class="col s12 m6">
          <div class="card teal darken-1">
            <div class="card-content white-text">
              <iframe width="300" height="300" frameborder="0" style="border:0" :src="url+match.place_id"  allowfullscreen>
             </iframe>
              <span class="card-title">{{match.name}}</span>
              <div class="left-align">
                <h5>posted by {{match.creator.local.username}}</h5  >
                <p>{{match.place}}</p>
                <p>{{match.address}}</p>
                <p>{{match.phone}}</p>
                <p>{{match.matchTime}}</p>
                <p>available : {{match.openStatus}}</p>
              </div>
            </div>
            <div v-if="match.openStatus">
              <div v-if="match.creator.local.username !== username">
                <div class="card-action">
                  <a href="javascript:void(0)" @click="challangeTeam(match._id,index)" >Challenge this team</a>
                </div>
              </div>
              <div v-else>
                <div class="card-action">
                  <a href="javascript:void(0)"  >waiting challanger</a>
                  </div>
              </div>

            </div>
            <div v-else>
              <div class="card-action">
                <a href="javascript:void(0)" >Booked</a>
                </div>
            </div>
          </div>
        </div>
      </div>
      </div>
      <div class="col s12 m4 l2"></div>
    </div>
  </div>

</template>

<script>

export default {
  name:'home',
  data(){
    return {
      listmatch:[],
      database:'',
      username:'',
      url:"https://www.google.com/maps/embed/v1/place?key=AIzaSyCRXpSjbVOqMEOvV5AeOIk2Ivp8YnLU9To&q=place_id:"
    }
  },
  methods:{
    getlistmatch() {
            console.log('asdasdasdasdasdasd');
        var self = this
        this.axios.get('http://localhost:3000/matches', {

        }).then(function(response) {
          console.log(response.data);
            self.listmatch = response.data
            console.log(self.listBlogs);
        })
    },
    challangeTeam(idmatch,index){
      var self = this

      self.listmatch[index].openStatus=false;
      let userid = window.localStorage.getItem('id')
      this.axios.put(`http://localhost:3000/matching/${idmatch}`, {
        userId : userid
      }).then(function(response) {
        console.log(response);
        self.database.ref('room/' + idmatch).set({
          status: false
        });
    })
  }
},
  created(){
    let self=this;
    let token = window.localStorage.getItem('token')
    if(token!==null && token!==undefined){
      console.log('=============');
      console.log(self.username);
      this.database = firebase.database();
      self.getlistmatch();
      self.username = window.localStorage.getItem('username')

      var room = this.database.ref('room/');
      room.on('value', function(snapshot) {
        self.getlistmatch();
      });
    }else{
      console.log('KELUARLO');
      self.$router.push('/')
    }


  }


}
</script>

<style lang="css">

</style>
