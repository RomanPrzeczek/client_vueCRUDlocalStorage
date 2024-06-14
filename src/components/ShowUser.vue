<template>
    <div v-bind="printUsersList" class="card" style="margin: 2% 10% 2% 10%; padding: 2%; background-color: beige;">
        <h1>Loged user: <span style="font-size: 50px; color: red;">{{ logedUser.name }}</span></h1>
    </div>
    <div class="card" style="margin: 2% 10% 2% 10%; padding: 2%; background-color: beige;">
        <form id="formRegister" v-on:submit.prevent="addItem">
            <div ><h1>ADDING PRODUCT</h1></div>
            <label for="inputProductTitle" style="margin-bottom: 10px;">Title of product</label>
            <input type="text" placeholder="Enter product title please." class="form-control" style="margin-bottom: 15px;" required v-model="inputProductTitle" >
            <label for="inputProductValue" style="margin-bottom: 10px; ">Value of product</label>
            <input type="text" placeholder="Enter your input please." class="form-control" style="margin-bottom: 15px;" v-model="inputProductValue">
        </form>
        <div class="container">
            <div class="row" style="margin-bottom: 15px;">
                <button class="btn" type="button" style="background-color: blue; color: white;"  @click="addItem">ADD ITEM</button>
            </div>
        </div>
        <div class="container">
            <div class="row" style="margin-bottom: 15px;">
                <button class="btn" type="button" style="background-color: blueviolet; color: white;"  @click="logout">LOGOUT USER</button>
            </div>
        </div>
    </div>

    <!-- 
    props transfered from ShowUser to ItemsTable: logedUser,usersListFromAdd,triggerAdd 
    callback sent by ItemsTable, received by Showuser: triggerAddOff, interacting in method setTrggerAddOff
    -->
    <ItemsTable
        v-bind:loged-user=this.logedUser
        v-bind:usersListFromAdd=this.usersList
        v-bind:triggerAdd=this.triggerAdd
        @triggerAddOff="setTriggerAddOff"       
    />

    <div class="card" style="margin: 2% 10% 2% 10%; padding: 2%; background-color: beige;">
        <LocalStorage/>
    </div>

    <div class="card" style="margin: 2% 10% 2% 10%; padding: 2%; background-color: beige;">
        <SampleFetch/>
    </div>
</template>

<script>
import LocalStorage from './LocalStorage.vue';
import ItemsTable from './ItemsTable.vue';
import SampleFetch from './SampleFetch.vue';

export default {
    name: 'ShowUser',
    props: [
        'logedUser',
    ],    
    components: {
        LocalStorage,
        ItemsTable,
        SampleFetch
    },
    data: () => {
        return {
            inputProductValue: '',  // user input of product value (string)
            validatedProductValue: '',  // validated input of product value (required number) recalculated with fetched EUR rate
            triggerAdd: false, // used for itemsList rerendering initiation after new item adding, see ItemsTable tbody conditional rendering
            actualPage: '', // used for pages switching
            usersList:JSON.parse(localStorage.getItem('LSusersList'))??console.log("LoginPage / data userslist not loaded."),
            pid:0,
            inputProductTitle:'', // user input of product title (string)
            inputFetch: 1,  // fetched EUR rate from bank API
        }
    },
    methods: {
        validateValue(){        // validates inputProductValue meeting requirements
            if(this.inputProductValue=='' || this.inputProductTitle=='') {    // requirement of non-emptiness
                alert("Product value or title can not be empty.");
                this.inputProductValue='';
           }
            else if (isNaN(parseFloat(this.inputProductValue)/2)) { // requirement of number format (non-text)
                alert("Product value must be a number.");
                this.inputProductValue='';
            }
            else {  // processing of ok validated value by parsing to a number format
                this.validatedProductValue=parseFloat(this.inputProductValue);
                this.inputProductValue='';
            }
        },
        setTriggerAddOn(){
            this.triggerAdd=true;
        },
        setTriggerAddOff(){
            this.triggerAdd=false;
        }, 
        logout(){
            this.$emit('actualPage',`login`);
        },
        async fetchKurzy(){         // getting/fetching EUR rate from bank API
            await fetch('https://servervuecrudlocalstorage-production.up.railway.app/cnb')
                .then((res) => res.text())
                .then((body) => {
                    this.inputFetch =  parseFloat(body);
                })
                .catch(err => console.log(`FE ShowUser fetchKurzy / err : ${err}`))
        },
        addItem(){ // main method of adding new item
            console.log(`sUser aItem triggerAdd pre: ${this.triggerAdd}`);
            this.fetchKurzy(); 
            this.validateValue();
            this.usersList=JSON.parse(localStorage.getItem('LSusersList'))??console.log("ShowUser addItem err: userslist not loaded.");
            if(this.usersList && this.inputFetch!=1 && this.validatedProductValue!='' && this.inputProductTitle!=''){   // condition of correct: userList download, EURrate download, userInput validation
                (this.usersList).forEach(user => { 
                    if(user.id==(this.logedUser).id){ // finding of logedUser data in usersList
                        if((user.userItemsList).length!=0){ // finding/seting last item id
                            (user.userItemsList).findLast((product) => this.pid = product.id)
                        } else this.pid=0; // setting item id 0 if no items yet
                        let newProduct = {
                            id     : this.pid+1,
                            title  : this.inputProductTitle,
                            value: this.validatedProductValue/this.inputFetch, 
                        };
                        (user.userItemsList).push(newProduct);
                        localStorage.setItem('LSusersList', JSON.stringify(this.usersList));    
                    }
                })                
            this.inputProductTitle=null;
            this.usersList=JSON.parse(localStorage.getItem('LSusersList'))??console.log("LoginPage / data userslist not loaded.");
            this.setTriggerAddOn();
            console.log(`sUser aItem triggerAdd post: ${this.triggerAdd}`);
            }
        }   
    },
    created() {this.fetchKurzy()}   // lifecycle Vue hook ensuring performing of rate fetching before template/DOM (items list) diplaying/executing
}

</script>