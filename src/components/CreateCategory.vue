<template>
    <div id="newCat">
        <h1 id="pageTitle">Categorias</h1>
        <!-- Open Modal Window -->
        <a href="#openModal"><button class="submit">Añadir nueva categoria</button></a>
        <!-- Modal Window -->
        <div id="openModal" class="modalWindow">
            <div>
                <label for="catNameField">Nombre de la categoria:</label><br/>
                <input type="text" id="catNameField" required v-model="newCategoryName"><br/>
                <div id="divButtons">
                <a href="#ok" title="Ok" class="ok"><button class="submit" @click="closeWindow('cancel')">Cancelar</button></a>
                <a href="#ok" title="Ok" class="ok"><button id="botAdd" class="submit" @click="closeWindow('save')">Añadir</button></a>
                </div>
            </div>
         </div>
        <table>
      <thead>
        <tr>
          <th>ID</th>
          <th>Categoria</th>
          <th></th>
        </tr>
      </thead>
        <tr class="json" v-for="object in json1" :key="object">
          <td>{{object.IdCat}}</td>
          <td>{{object.CatName}}</td>
          <div>
          <a href="#openModal"><button class="botQues" id="botEdit" @click="chargeData(object.IdCat)"><img src="../assets/logos/editIcon.png" alt="Editar"></button></a>
          <button class="botQues" id="botDelete" @click="deleteARow(object.IdCat)"><img src="../assets/logos/deleteIcon.png" alt="Eliminar"></button>
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
       axios.get("http://localhost:3000/categories").then(response =>{
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

  name: 'CreateCategory',
  data(){
      return{
        IdCat: "",
          newCategoryName: "",
          editMode: false,
          // json1: [{
          //   "IdCat": 1,
          //   "CatName": "Java",
          //   },
          //   {
          //   "IdCat": 2,
          //       "CatName": "JS",
          //   },
          //   {
          //   "IdCat": 3,
          //       "CatName": "Python",
          //   },
          //   {
          //   "IdCat": 4,
          //       "CatName": "POO",
          //   },
          //   {
          //   "IdCat": 5,
          //       "CatName": "PHP",
          //   }],
          json1: this.json1,
      }
  },
    methods:{
      // Opens the modal window with the edit mode actived and the data charged in the form
    chargeData(IdCat){
        this.editMode = true;
        console.log(IdCat);
        let jsonArray = this.json1;
        for(let object in jsonArray){
          this.IdCat = IdCat;
          if(jsonArray[object].IdCat == this.IdCat){
            this.newCategoryName = jsonArray[object].CatName;
            console.log(this.newCategoryName);
            break;
          }
        }
    },
    // This method adds a new row to the database
    chargeNew(){
      try{
       axios.post("http://localhost:3000/categories", {CatName : this.newCategoryName}).then(response =>{
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
        axios.put("http://localhost:3000/categories", {IdCat : this.IdCat, CatName : this.CatName});
      }
      catch{
        
        console.log("Error en el put");
      }

      this.cleanData();
    },

    // This method deletes the row with the specified ID from the database
      execDelete(){
          try{
            axios.delete('http://localhost:3000/categories', { IdCat: this.IdCat});
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

    deleteARow(IdCat){
      if (confirm('¿Estas seguro de que quieres borrar este registro de la base de datos?')) {
        this.execDelete(IdCat);
      }
    }
    }
  }


</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

#newCat{
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

#submit{
    background-color: #a51129;
    text-shadow: 0 0 10px #000000;
    color: white;
    padding: 15px;
    width:35%;
    border-radius: 2px;
    border: 0px;
    transition: 0.3s;
    
}

#submit:hover{
    transition: 0.3s;
    background-color: #3c3c3b;
    text-shadow: 0 0 10px #000000;
}

#catName{
    height: 35px;
    width: 40%;
    text-align: center;
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
        float: right;
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
