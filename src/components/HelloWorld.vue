<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <b-container class="bv-example-row">
      <b-row>
          <b-col>No</b-col>
          <b-col>Fridge No</b-col>
          <b-col cols="8">Name</b-col>
      </b-row>
      <b-row v-bind:class="{line: item.type === 2}" v-for="(item, index) in fridges">
          <b-col>{{ index+1 }}</b-col>
          <b-col>{{ item.type }}</b-col>
          <b-col cols="8">{{ item.name }}</b-col>
      </b-row>
    </b-container>
    <b-btn v-b-modal.modal1>Movement</b-btn>

    <!-- Modal Component -->
    <b-modal id="modal1" title="Movement" ok-title="Apply" @ok="changeApply" @cancel="cancelApply">
      <h3>Move groceries between Fridge 1 and Fridge 2</h3>
      <b-row>        
        <b-col>
          <b-list-group>
            <b-list-group-item 
              class="item"
              v-bind:class="{active: selectedID===item.id}"
              v-for="item in fridges1"
              v-on:click="selectItem(item.id)"
            >
              {{ item.name }}
            </b-list-group-item>
          </b-list-group>
        </b-col>
        <b-col cols="2">
          <b-btn v-on:click="changeItem">&laquo; &raquo;</b-btn>
        </b-col>
        <b-col>
          <b-list-group>
            <b-list-group-item
              class="item"
              v-bind:class="{active: selectedID===item.id}"
              v-for="item in fridges2"
              v-on:click="selectItem(item.id)"
            >
              {{ item.name }}
            </b-list-group-item>
          </b-list-group>
        </b-col>
      </b-row>
    </b-modal>
  </div>
</template>

<script>

export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: 'Welcome to My Vue.js App',
      fridges: JSON.parse(localStorage.getItem('fridge')),
      fridges1: [],
      fridges2: [],
      selectedID: null,
      tempFridges: JSON.parse(localStorage.getItem('fridge'))
    }
  },
  beforeCreate () {
    if(!localStorage.getItem('fridge')) {
      var fridges = [
        {id:'1', type:1, name:"food1"},
        {id:'2', type:2, name:"food2"},
        {id:'3', type:1, name:"food3"},
        {id:'4', type:2, name:"food4"},
        {id:'5', type:2, name:"food5"},
        {id:'6', type:2, name:"food6"},
        {id:'7', type:1, name:"food7"},
      ];
      localStorage.removeItem('fridge');
      localStorage.setItem('fridge', JSON.stringify(fridges));
    }
  },
  created () {
    var self = this;
    self.fridges1 = self.fridges.filter((fridge)=>fridge.type === 1);
    self.fridges2 = self.fridges.filter((fridge)=>fridge.type === 2);
  },
  methods: {
    selectItem (id) {
      var self = this;
      self.selectedID = id;
    },
    changeItem () {
      var self = this;
      if(!self.selectedID) {
        alert("Select a Item!");
        return;
      }

      self.tempFridges.map(fridge => {
        if (fridge.id === self.selectedID) {
          fridge.type = fridge.type === 1 ? 2 : 1;
        }
        return fridge;
      });

      self.fridges1 = self.tempFridges.filter((fridge)=>fridge.type === 1);
      self.fridges2 = self.tempFridges.filter((fridge)=>fridge.type === 2);
    },
    changeApply () {
      var self = this;
      self.selectedID = null;
      localStorage.setItem('fridge', JSON.stringify(self.tempFridges));
      self.fridges = JSON.parse(localStorage.getItem('fridge'));
    },
    cancelApply () {
      var self = this;
      self.tempFridges = JSON.parse(localStorage.getItem('fridge'));
      self.selectedID = null;
      self.fridges1 = self.fridges.filter((fridge)=>fridge.type === 1);
      self.fridges2 = self.fridges.filter((fridge)=>fridge.type === 2);
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
#modal1 {
  min-width: 600px;
}
#modal1 h3 {
  font-size: 13px;
}
.item {
  cursor: pointer;
}
.active {
  background-color: grey;
  color: white;
}
.line {
  background-color: blue;
  color: white;
}
</style>
