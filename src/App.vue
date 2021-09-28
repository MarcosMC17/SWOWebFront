<template>
<div id="main">

  <div id="header">
    <div>
      <img id="mainLogo" src="./assets/logos/mainLogo.png" alt="Logo">
    </div>
    <div id="coloredLine"></div>
  </div>
  <div id="page">
    <div id="navbar">
      <a class="navbarElement" @click="changeComponent(1)" href="#">Listado de candidato</a>
      <hr>
      <a class="navbarElement" @click="changeComponent(2)" href="#">Gestor de categorias</a>
      <hr/>
      <a class="navbarElement" @click="changeComponent(3)" href="#">Gestor de subcategorias</a>
      <hr/>
      <a class="navbarElement" @click="changeComponent(4)" href="#">Listado de preguntas</a>
    </div>
    <div id="contentArea">
      
      <UserForm v-if="shownComponent == 1"/>
      <CreateCategory v-if="shownComponent == 2"/>
      <CreateSubcategory v-if="shownComponent == 3"/>
      <QuestionsList v-if="shownComponent == 4"/>
    </div>
  </div>
</div>
</template>

<script>

// Component imports
import UserForm from './components/UserForm.vue'
import CreateCategory from './components/CreateCategory.vue'
import CreateSubcategory from './components/CreateSubcategory.vue'
import QuestionsList from './components/QuestionsList.vue'

export default {
  name: 'App',
  components: {
    UserForm,
    CreateCategory,
    CreateSubcategory,
    QuestionsList,
  },
  data() {
    return{
      // This property contains the code of the component that is being shown
      shownComponent: 1,
    }
},
methods:{
  // Changes the shown component to the correspondent component for the given component code (Component codes: UserForm-1, CreateCategory-2, CreateSubcategory-3, QuestionsList-4)
  changeComponent(changeToChangeTo){
    switch (changeToChangeTo){
      case 1:
        this.shownComponent = 1;
        break;
      case 2:
        this.shownComponent = 2;
        break;
      case 3:
        this.shownComponent = 3;
        break;
      case 4:
        this.shownComponent = 4;
        break;
      case 5:
        this.shownComponent = 5;
        break;
    }
  }
}
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  
}

body{
  margin: 0;
}

#header{
  background-color: #131212;
  margin: 0%;
}

#mainLogo{
  height: 40px;
  margin: 20px;
}

#coloredLine{
  background-color: #a51129;
  padding: 7px;
}

#page{
  display: flex;
}


#navbar{
  width: 20%;
  background-color: #f6f6f6;
  border-radius: 2px;
  height: 100%;
  text-align: center;
  padding: 5px;
  margin: 8px;
  border: 1px solid #c6c6c6;
  box-shadow: 0 4px 8px -1px rgb(0 0 0 / 10%);
}

.navbarElement{
  margin: 5px;
  text-decoration: none;
  color: black;
}

.navbarElement:hover{
  color: #d21034;
}

#navbar>hr{
  width: 80%;
}

#contentArea{
  width: 100%;

  height: 100%;
  
}

</style>
