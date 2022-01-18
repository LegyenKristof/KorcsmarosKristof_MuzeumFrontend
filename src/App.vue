<template>
  <div>
    <table>
      <thead>
        <th>ID</th>
        <th>Person</th>
        <th>Height</th>
        <th>Price</th>
      </thead>
      <tbody>
        <tr v-for="statue in statues" v-bind:key="statue.id">
          <td>{{statue.id}}</td>
          <td>{{statue.person}}</td>
          <td>{{statue.height}}</td>
          <td>{{statue.price}}</td>
          <td><button @click="deleteStatue(statue.id)">X</button></td>
          <td><button @click="editStatue(statue.id)">Edit</button></td>
        </tr>
        <tr>
          <td>
            <input type="hidden" name="id" v-model="statue.id">
          </td>
          <td>
            <input type="text" name="person" v-model="statue.person">
          </td>
          <td>            
            <input type="number" name="height" v-model="statue.height">
          </td>
          <td>            
            <input type="number" name="price" v-model="statue.price">            
          </td>
          <td>
            <button v-if="!add_new" @click="createStatue" :disabled="saving">Create</button>
          </td>
          <td>
            <button v-if="add_new" @click="saveStatue" :disabled="saving">Save</button>
          </td>
          <td>  
            <button v-if="add_new" @click="cancelStatue" :disabled="saving">Cancel</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>

export default {
  name: 'App',
  components: {

  },
  data() {
    return {
      statues: [],
      statue: {
        id: null,
        person: "",
        height: null,
        price: null
      },
      add_new: false,
      saving: false
    }
  },
  methods: {
    async getStatues(){
      let Response = await fetch('http://127.0.0.1:8000/api/statues')
      let data = await Response.json()
      this.statues = data
    },
    async createStatue(){
      this.saving = true
      await fetch('http://127.0.0.1:8000/api/statues', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Accept': 'application/json'
        },
        body: JSON.stringify(this.statue) 
      })
      await this.getStatues()
      this.saving=false
      this.resetForm()
    },
    resetForm(){
      this.statue = {
        id: null,
        person: "",
        height: null,
        price: null
      }
      this.add_new = false
    },
    async deleteStatue(id){
      await fetch(`http://127.0.0.1:8000/api/statues/${id}`, {
        method: 'DELETE'
      })
      await this.getStatues()
    },
    async editStatue(id) {
      this.add_new = true
      let response = await fetch(`http://127.0.0.1:8000/api/statues/${id}`)
      let data = await response.json()
      this.statue = {...data}
    },
    async saveStatue() {
      this.saving = true
      await fetch(`http://127.0.0.1:8000/api/statues/${this.statue.id}`, {
        method: 'PATCH',
        headers: {
          'Content-Type' : 'application/json',
          'Accept' : 'application/json'
        },
        body: JSON.stringify(this.statue)
      })
      await this.getStatues()
      this.saving = false
      this.resetForm()
    },
    cancelStatue() {    
      this.resetForm()
    }
  },
  mounted() {
    this.getStatues()
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
