<template>
  <div id="userForm">
    <h1 id="pageTitle">Candidatos</h1>
    <!-- Open Modal Window -->
    <a href="#openModal"><button class="submit">Añadir nuevo candidato</button></a>
    <!-- Modal Window -->
    <div id="openModal" class="modalWindow">
      <div>
        <label for="userName">Nombre:</label><br/>
        <input type="text" id="appName" required v-model="appName"><br/>
        <label for="appPos">Posición:</label><br/>
        <input type="text" id="appPos" required v-model="appPos"><br/>
        <label for="intName">Entrevistador:</label><br/>
        <input type="text" id="IntName" required v-model="intName"><br/>
        <label for="intPos">Posicion del entrevistador:</label><br/>
        <input type="text" id="intPos" required v-model="intPos"><br/>
        <label for="codingScore">Puntuacion en el Coding Test:</label><br/>
        <input type="text" id="codingScore" required v-model="codingScore"><br/>
        <label for="intDate">Fecha del test:</label><br/>
        <input type="date" id="intDate" required v-model="intDate"><br/>
        <div id="divButtons">
        <a href="#ok" title="Ok" class="ok"><button class="submit" @click="closeWindow('cancel')">Cancelar</button></a>
        <a href="#ok" title="Ok" class="ok"><button id="botAdd" class="submit" @click="closeWindow('save')">Añadir</button></a>
        </div>
      </div>
    </div>
    <!-- End of the modal window -->
    <table id="listQ">
      <thead>
        <tr>
          <th>ID</th>
          <th>Fecha Entrevista</th>
          <th>Nombre Candidato</th>
          <th>Posicion del candidato</th>
          <th>Entrevistador</th>
          <th>Pos.Entrevistador</th>
          <th>Punt.Test</th>
          <th>Stack</th>
          <th></th>
        </tr>
      </thead>
        <tr class="json" v-for="object in json1" :key="object">
          <td>{{object.AppID}}</td>
          <td>{{object.IntDate}}</td>
          <td>{{object.AppName}}</td>
          <td>{{object.AppPosition}}</td>
          <td>{{object.IntName}}</td>
          <td>{{object.IntPosition}}</td>
          <td>{{object.CodingScore}}</td>
          <td>{{object.stack}}</td>
          <div>
          <a href="#openModal"><button class="botQues" id="botEdit" @click="chargeData(object.AppID)"><img src="../assets/logos/editIcon.png" alt="Editar"></button></a>
          <button class="botQues" id="botDelete" @click="deleteARow(object.AppID)"><img src="../assets/logos/deleteIcon.png" alt="Eliminar"></button>
          </div>
        </tr>
    </table>
  </div>

</template>

<script>

// REST
import axios from "axios";

export default {

  created(){    
      // Charges the rows from the database into the table
      try{
       axios.get("http://localhost:3000/applicants").then(response =>{
         console.log(response.data);
         this.json1 = response.data;
         })
         .catch(e => {
           this.errors.push(e)
         })
     }
     catch{
       console.log("Error en el get");
     }
    },
  name: 'UserForm',
  data() {
    return{
      editMode: false,
      AppID: "",
      appName: "",
      appPos : "",
      intName: "",
      intPos: "",
      codingScore: "",
      intDate: "",
      stack: "",
      // json1: [{
      //       "AppID" : 1,
      //       "IntDate" : "2021-09-22",
      //       "AppName" : "Pepe Perez",
      //       "AppPosition" : "Junior Developer",
      //       "IntName" : "Lino",
      //       "IntPosition" : "Solution Architect",
      //       "CodingScore" : 70,
      //       "stack" : "Python"
      //       }],
      json1: "",
    }
  },

  methods:{
    // Opens the modal window with the edit mode actived and the data charged in the form
    chargeData(AppID){
      this.editMode = true;
      console.log(AppID);
      let jsonArray = this.json1;
      for(let object in jsonArray){
        this.AppID = AppID
        if(jsonArray[object].AppID == this.AppID){
          this.appName = jsonArray[object].AppName;
          this.appPos = jsonArray[object].AppPosition;
          this.intName = jsonArray[object].IntName;
          this.intPos = jsonArray[object].IntPosition;
          this.codingScore = jsonArray[object].CodingScore;
          this.intDate = jsonArray[object].IntDate;
          this.stack = jsonArray[object].stack;
          break;
        }
      }
    },

    // This method adds a new row to the database
    chargeNew(){
      try{
       axios.post("http://localhost:3000/applicants", {IntDate : this.intDate, AppName : this.appName, AppPosition : this.appPos, IntName : this.intName, IntPosition : this.intPos, CodingScore : this.codingScore, stack : this.stack}).then(response =>{
         console.log(response);
         })
         .catch(e => {
           this.errors.push(e);
         })

     }
     catch{
       console.log("Error en el post");
     }
       this.cleanData();
    },

    // This method update the row in the database with the correspondent given ID via PUT call
    updateDB(){
      try{
        axios.put("http://localhost:3000/applicants", {AppID : this.AppID, IntDate : this.intDate, AppName : this.appName, AppPosition : this.appPos, IntName : this.intName, IntPosition : this.intPos, CodingScore : this.codingScore, stack : this.stack});
      }
      catch{
        
        console.log("Error en el put");
      }

      this.cleanData();
    },

    // This method deletes the row with the specified ID from the database
      execDelete(){
          try{
            axios.delete('http://localhost:3000/applicants', {AppID : this.AppID});
          }
          catch{
            
            console.log("Error en el delete");
          } 
        },
    


    // This method closes the modal window opened by the method 'chargeData' or by the 'Create new element' button. If the button used to close is cancel, just closes the window, if the button is save, checks if the edit mode is on, if it's on, updates the row in the database, if not, creates a new row 
    closeWindow(buttonType){
      if(buttonType == "cancel"){
        this.cleanData();
      }
      else if(buttonType == "save"){
        if(this.editMode){
          this.updateDB();
        }
        else{
          this.chargeNew();
        }
      }
    },

    // This method cleans the data properties used in the new/edit form of the rows of the database
    cleanData(){
      this.AppID = "";
        this.appName = "";
        this.appPos = "";
        this.intName = "";
        this.intPos = "";
        this.codingScore = "";
        this.intDate = "";
        this.stack = "";
        this.editMode = false;
    },

    // This method deletes the row from the database correspondent to the given ID,but needs a confirmation (works with "execDelete()" method)
    deleteARow(AppID){
      if (confirm('¿Estas seguro de que quieres borrar este registro de la base de datos?')) {
        this.execDelete(AppID);
      }
    }
}
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  #userForm{
    margin-top: 5%;
    margin-left: 3%;
    margin-right: 3%;
    display: grid;
  }

  form{
    display: grid;
  }

  input{
    margin: 2px 0px 2px 0px;
    height: 3em;
    border-radius: 2px;
    border: 1px solid black;
    padding-left: 10px;
  }

  .submit{
    background-color: #3c3c3b;
    color: white;
    padding: 15px;
    width:35% ;
    border-radius: 30px;
    border: 0px;
    transition: 0.3s;
    float: right;
  }

  .submit:hover{
    transition: 0.3s;
    background-color: #3c3c3b;
    text-shadow: 0 0 10px #000000;
  }

  .submit{
    background-color: #a51129;
    text-shadow: 0 0 10px #000000;
    color: white;
    padding: 15px;
    width:35%;
    border-radius: 2px;
    border: 0px;
    transition: 0.3s;
    
  }


.botQues{
    transition: 0.3s;
    background-color: #3c3c3b;
    text-shadow: 0 0 10px #000000;
    border: 0px;
    border-radius: 2px;
    width: 3em;
    height: 2em;
    text-align: center;
}

.botQues:hover{
  transition: 0.3s;
  background-color: #a51129;
  text-shadow: 0 0 10px #000000;
}



#botEdit{
  background-color: #3c3c3b;
  margin-top: 10px;
}

#botEdit:hover{
  background-color: #4A9462;
}



#botAdd{
  background-color: #4A9462;
  margin-right: 10px;
}

#botAdd:hover{
  transition: 0.3s;
  background-color: #3c3c3b;
  margin-right: 10px;
}

#pageTitle{
  margin: 0;
}

/* For Modal Window  */
.modalWindow {
position: fixed;
top: 0;
right: 0;
bottom: 0;
left: 0;
background: rgba(0,0,0,0.2);
z-index: 99999;
opacity:0;
pointer-events: none;

}
.modalWindow:target {
opacity:1;
pointer-events: auto;
}
.modalWindow > div {
width: 500px;
position: relative;
margin: 10% auto;
background: #fff;
display: grid;
padding: 10px;
box-shadow: 0 4px 8px -1px rgb(0 0 0 / 10%);
}

.openModal{
  width: fit-content;
  
}
</style>
