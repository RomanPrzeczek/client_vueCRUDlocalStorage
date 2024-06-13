<template>
    <div class="card" style="margin: 2% 10% 2% 10%; padding: 2%; background-color: lightblue;">
        <form id="formRegister" v-on:submit.prevent="saveUser">
            <div ><h1>REGISTRATION</h1></div>
            <label for="nameInput" style="margin-bottom: 10px;">Name</label>
            <input type="text" placeholder="Enter your name please." class="form-control" style="margin-bottom: 15px;" required v-model="nameInput" >
            <label for="pswrdInput" style="margin-bottom: 10px; ">Password</label>
            <input type="password" placeholder="Enter your password please." class="form-control" style="margin-bottom: 15px;" required v-model="pswrdInput">
        </form>
        <div class="container">
            <div class="row" style="margin-bottom: 15px;">
                <button class="btn btn-sm btn-info" type="button" @click="saveUser">REGISTER</button>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'RegisPage',
        data: () => {
            return {
                id:0,
                nameInput:null,
                pswrdInput:null,
                usersList:JSON.parse(localStorage.getItem('LSusersList'))??[]
            }
        },
        methods: {
            saveUser(){
                let errorSameName = false;
                let errorEmptyInputs = false;
                // check of inputs (non)emptiness
                if (this.nameInput==null || this.pswrdInput==null) {
                    alert("Name nor password  can not be empty. Please check and fill in.");
                    errorEmptyInputs=true;
                    this.clearInputs();
                }
                // check of same name existense
                this.usersList.forEach(element => {
                    if(element.name==this.nameInput){   
                        alert("Given name already exists. Please choose another one.");
                        errorSameName = true;
                        this.clearInputs();
                    }
                });

                if (!errorSameName && !errorEmptyInputs) {
                    this.newUser();
                    errorSameName=false;
                    errorEmptyInputs=false;
                    this.clearInputs();
                }
            },
            newUser(){
                this.usersList.length != 0 ? this.usersList.findLast((user) => this.id = user.id) : this.id = 0; 
                let newUser = {
                    id: this.id+1,
                    name: this.nameInput,
                    pswrd: this.pswrdInput,
                    userItemsList: []
                };
                this.usersList.push(newUser);
                localStorage.setItem('LSusersList', JSON.stringify(this.usersList)); // storing users list
                this.$emit('actualPage',`login`);
            },
            clearInputs() {
                this.nameInput=null;
                this.pswrdInput=null;
            },
            consolePrintInputs(){
                console.log(`nameInput: ${this.nameInput}`);
                console.log(`pswrdInput: ${this.pswrdInput}`);
                console.log(`usersList: ${JSON.stringify(this.usersList)}`);
                console.log(`New user: id:${this.id} name:${this.nameInput} pswrd:${this.pswrdInput}`);
            }
        }
    }
</script>