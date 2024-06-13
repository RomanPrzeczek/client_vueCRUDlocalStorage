<template>
    <div  id="blinkDiv" v-if="showUpdateInputs" class="card" style="margin: 2% 10% 2% 10%; padding: 2%; background-color: beige;">
        <form id="formUpdate" v-on:submit.prevent="updateItem">
            <div ><h1>UPDATING PRODUCT</h1></div>
            <label for="titleProductInput" style="margin-bottom: 10px;">Title of product</label>
            <input type="text" v-bind:placeholder="titleProductGot" class="form-control" style="margin-bottom: 15px;" required v-model="titleProductInput" >
            <label for="valueProductInput" style="margin-bottom: 10px; ">Value of product</label>
            <input type="text" v-bind:placeholder="valueProductGot" class="form-control" style="margin-bottom: 15px;" v-model="valueProductInput">
        </form>
        <div class="container">
            <div class="row" style="margin-bottom: 15px;">
                <button class="btn" type="button" style="background-color: blue; color: white;"  @click="updateItem">SUBMIT UPDATE</button>
            </div>
        </div>
    </div>
    <div class="card" style="margin: 2% 10% 2% 10%; padding: 2%; background-color: beige;">
        <div class="container">
          <div class="row" style="margin-bottom: 15px;">
                    <table class="table table-striped table bordered">
                        <thead>
                            <tr>
                                <th>Product id</th>
                                <th>Product title</th>
                                <th>Product value</th>
                                <th></th>
                                <th></th>
                                <th></th>
                            </tr>
                        </thead>
                        <tbody v-if="(this.triggerAdd)"> <!-- if true -->
                            <tr v-for="item in ((this.usersListFromAdd[(this.logedUser).id-1]).userItemsList)" :key="item.id">
                                <td> {{item.id}} </td>
                                <td> {{item.title}} </td>
                                <td> {{item.value}} </td>
                                <td>true</td>
                                <td>
                                    <button class="btn" type="button" style="background-color: YELLOW;"  @click="getDataForUpdate(item.id)">UPDATE ITEM</button>
                                </td>
                                <td>
                                    <button class="btn" type="button" style="background-color: RED; color: white;"  @click="deleteItem(item.id)">DELETE ITEM</button>
                                </td>
                            </tr>
                        </tbody>
                        <tbody v-else-if="!this.triggerAdd"> <!-- if false -->
                            <tr v-for="item in (this.printActualItemsList())" :key="item.id">
                                <td> {{item.id}} </td>
                                <td> {{item.title}} </td>
                                <td> {{item.value}} </td>
                                <td>false</td>
                                <td>
                                    <button class="btn" type="button" style="background-color: YELLOW;"  @click="getDataForUpdate(item.id)">UPDATE ITEM</button>
                                </td>
                                <td>    
                                    <button class="btn" type="button" style="background-color: RED; color: white;"  @click="deleteItem(item.id)">DELETE ITEM</button>
                                </td>
                            </tr>
                        </tbody>
                        <tbody v-else>  <!-- if undefined -->
                            <tr v-for="item in ((this.usersList[(this.logedUser).id-1]).userItemsList)" :key="item.id">
                                <td> {{item.id}} </td>
                                <td> {{item.title}} </td>
                                <td> {{item.value}} </td>
                                <td>else</td>
                                <td>
                                    <button class="btn" type="button" style="background-color: YELLOW;"  @click="getDataForUpdate(item.id)">UPDATE ITEM</button>
                                </td>
                                <td>    
                                    <button class="btn" type="button" style="background-color: RED; color: white;"  @click="deleteItem(item.id)">DELETE ITEM</button>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>    
        </div>
    </div>
</template>

<script>

export default {
    name: 'ItemsTable',
    props: ['logedUser','usersListFromAdd','triggerAdd'],    
    data: () => {
        return {
            usersList:JSON.parse(localStorage.getItem('LSusersList'))??console.log("LoginPage / data userslist not loaded."),
            actualItemsList:[],
            updatedItemsList:[],
            titleProductInput:'',
            titleProductGot:'Enter the title',
            valueProductInput:'',
            valueProductGot:'Enter the value',
            showUpdateInputs:false,
            idItem:'',
            inputFetch: 1,  // fetched EUR rate from bank API
        }
    },
    methods: {
        async fetchKurzy(){         // getting/fetching EUR rate from bank API
            await fetch('http://localhost:8000/cnb')
                .then((res) => res.text())
                .then((body) => {
                    this.inputFetch =  parseFloat(body);
                })
                .catch(err => console.log(`FE ShowUser fetchKurzy / err : ${err}`))
        },
        printActualItemsList(){
            this.usersList=JSON.parse(localStorage.getItem('LSusersList'));
            this.actualItemsList=((this.usersList)[(this.logedUser).id-1]).userItemsList;
            //console.log(`ITEMSTABLE / printActualItemsList / actualItemsList: ${JSON.stringify(this.actualItemsList)} .`); 
            return this.actualItemsList
        },
        deleteItem(delId){
            this.printActualItemsList();
            if(this.usersList){
                (this.usersList).forEach(user => {
                    if(user.id==(this.logedUser).id){
                        this.updatedItemsList=(user.userItemsList).filter((value)=>{return value.id != delId});
                        (user.userItemsList)=this.updateIndexes(delId,this.updatedItemsList);
                        localStorage.setItem('LSusersList', JSON.stringify(this.usersList));     
                    }
                });
                this.$emit('triggerAddOff','true')
            }
        },
        updateIndexes(_delId,_list){
            let frontList = _list.slice();
            let backList = _list.slice();
            frontList.splice(_delId-1);
            backList.splice(0,_delId-1);
            backList.forEach((item)=>{
                item.id=(item.id)-1
            }) 
            _list = frontList.concat(backList);
            return _list
        },
        triggerUpdatePanel(){
            if(this.showUpdateInputs)this.showUpdateInputs=false
            else this.showUpdateInputs=true
        },
        getDataForUpdate(updId){
            this.idItem=updId;  // gets updated item id
            if(this.usersList){   // condition of correct: userList download
                (this.usersList).forEach(user => {
                    if(user.id==(this.logedUser).id){
                        (user.userItemsList).forEach( item => {
                            if(item.id==this.idItem){
                                this.titleProductGot=item.title;  // gets updated item title
                                this.valueProductGot=item.value;  // gets updated item value
                            }
                        })
                    }
                })
            }
            this.triggerUpdatePanel();
        },
        updateItem(){
            this.fetchKurzy(); 
            this.printActualItemsList();
            if(this.usersList && this.inputFetch!=1 ){   // condition of correct: userList download, EURrate download
                (this.usersList).forEach(user => {
                    if(user.id==(this.logedUser).id){
                        (user.userItemsList).forEach( item => {
                            if(item.id==this.idItem){
                                item.title=this.titleProductInput==''?this.titleProductGot:this.titleProductInput;
                                item.value=this.valueProductInput!==''?(this.valueProductInput/this.inputFetch):this.valueProductGot;
                            }
                        })
                        localStorage.setItem('LSusersList', JSON.stringify(this.usersList));     
                    }
                })
            }
            this.triggerUpdatePanel();
            this.titleProductGot='';
            this.valueProductGot='';
            this.$emit('triggerAddOff','true');
        }
    },
    created() {this.fetchKurzy()}   // lifecycle Vue hook ensuring performing of rate fetching before template/DOM (items list) diplaying/executing
}

</script>

<style> 
      
/* Define a CSS animation named "blink" */ 
@keyframes blink { 

    0%, 
    100% { 
        background-color: beige; 
        /* First color   */ 
    } 

    50% { 
        background-color: rgb(199, 199, 160); 
        /* Second color  */ 
    } 
} 

/* Apply the "blink" animation */ 
#blinkDiv { 
    animation: blink 1s linear infinite; 
    /* The animation will run indefinitely */ 
} 
</style> 