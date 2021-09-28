<template>
  <div id="QuestionList">
    <h1 id="pageTitle">Preguntas</h1>
    <!-- Open Modal Window -->
    <a href="#openModal"><button class="submit">Añadir nueva pregunta</button></a>
    <!-- Modal Window -->
    <div id="openModal" class="modalWindow">
      <div>
        <label for="userName">Enunciado:</label><br/>
        <input type="text" id="appName" required v-model="Question"><br/>
        <label for="appPos">Respuesta sugerida:</label><br/>
        <input type="text" id="appPos" required v-model="Sample"><br/>
        <label for="intName">Categoria:</label><br/>
        <select id="cate" name="cate">
          <option  v-for="cat in json2" :key="cat">{{cat.CatName}}</option>  
        </select><br/>
        <label for="intPos">Subcategoria:</label><br/>
        <select id="subcate" name="subcate">
          <option  v-for="subcat in json3" :key="subcat">{{subcat.CatName}}</option>
          </select><br/>

        <div id="divButtons">
      <a href="#ok" title="Ok" class="ok"><button class="submit" @click="closeWindow('cancel')">Cancelar</button></a>
        <a href="#ok" title="Ok" class="ok"><button id="botAdd" class="submit" @click="closeWindow('save')">Añadir</button></a>
        </div>
      </div>
  </div>
    <table id="listQ">
      <thead>
        <tr>
          <th>ID</th>
          <th>Categoria</th>
          <th>Subcategoria</th>
          <th class="absorbing-column">Enunciado</th>
          <th></th>
        </tr>
      </thead>
        <tr class="json" v-for="object in json1" :key="object">
          <td>{{object.QId}}</td>
          <td>{{object.IdCat}}</td>
          <td>{{object.IdSubcat}}</td>
          <td>{{object.Question}}</td>
          <div>
          <a href="#openModal"><button class="botQues" id="botEdit" @click="chargeData(object.QId)"><img src="../assets/logos/editIcon.png" alt="Editar"></button></a>
          <button class="botQues" id="botDelete" @click="deleteARow(object.QId)"><img src="../assets/logos/deleteIcon.png" alt="Eliminar"></button>
          </div>
        </tr>
    </table>
  </div>
</template>

<script>

//REST
import axios from "axios";

export default {
  // Charges the rows from the database into the table
  created(){    
      
      try{

        axios.get("http://localhost:3000/questions").then(response =>{
        console.log(response.data);
        this.json1 = response.data;
         })
         .catch(e => {
           this.errors.push(e)
         })

         axios.get("http://localhost:3000/categories").then(response =>{
        console.log(response.data);
        this.json2 = response.data;
         })
         .catch(e => {
           this.errors.push(e)
         })

         axios.get("http://localhost:3000/subcategories").then(response =>{
        console.log(response.data);
        this.json3 = response.data;
         })
         .catch(e => {
           this.errors.push(e)
         })

     }
     catch{
       console.log("Error en el get");
     }
    },
  name: 'QuestionsList',
  props: {
    // msg: String
  },
  data() {
    return{
      editMode : false,
      QId: "",
      Question : "",
      Sample : "",
      IdCat : "",
      IdSubcat : "",
      json1 : this.json1,
      json2 : this.json2,
      json3 : this.json3,
    }
  },
  methods:{
      // This method enter the program in edit mode and open a modal window with the selected row to edit
      chargeData(QId){
      this.editMode = true;
      console.log(QId);
      let jsonArray = this.json1;
      for(let object in jsonArray){
        this.QId = QId;
        if(jsonArray[object].QId == this.QId){
          this.Question = jsonArray[object].Question;
          this.Sample = jsonArray[object].Sample;
          this.IdCat = jsonArray[object].IdCat;
          this.IdSubcat = jsonArray[object].IdSubcat;
          break;
        }
      }
    },

    // This method adds a new row to the database
    chargeNew(){
      try{
       axios.post("http://localhost:3000/questions", {Question : this.Question, IdCat : this.IdCat, IdSubCat : this.IdSubcat, Sample : this.Sample}).then(response =>{
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
        axios.put("http://localhost:3000/questions", {QId : this.QId, Question : this.Question, IdCat : this.IdCat, IdSubCat : this.IdSubcat, Sample : this.Sample});
      }
      catch{
        
        console.log("Error en el put");
      }

      this.cleanData();
    },

    // This method deletes the row with the specified ID from the database
      execDelete(){
          try{
            axios.delete('http://localhost:3000/questions', { QId: this.QId});
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
      this.QId = "";
      this.Question = "";
      this.Sample = "";
      this.IdCat = "";
      this.IdSubcat = "";
      this.editMode = false;
    },

    // This method deletes the row from the database correspondent to the given ID,but needs a confirmation (works with "execDelete()" method)
    deleteARow(QId){
      if (confirm('¿Estas seguro de que quieres borrar este registro de la base de datos?')) {
        this.execDelete(QId);
      }
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  #QuestionList{
    margin-top: 5%;
    margin-left: 3%;
    margin-right: 3%;
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
  background-color: #3c3c3b;
  transition: 0.3s;
}



  select{
    height: 3em;
    margin: 5px 0px 10px 0px;
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
  
</style>
