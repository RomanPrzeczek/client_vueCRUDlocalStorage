<template>
    <div class="card bg-secondary" style="margin: 2% 10% 2% 10%; padding: 2%;">
        <form id="formLogin">
            <div ><h1>LOGIN</h1></div>
            <label for="inputUserName" style="margin-bottom: 10px; color: rgb(153, 226, 245);">Name</label>
            <input type="text" placeholder="Enter your name please." class="form-control" style="margin-bottom: 15px;" v-model="inputUserName" required>
            <label for="inputUserPswrd" style="margin-bottom: 10px; color: rgb(153, 226, 245);">Password</label>
            <input type="password" placeholder="Enter your password please." class="form-control" style="margin-bottom: 15px;" v-model="inputUserPswrd" required>
        </form>
        <div class="container">
            <div class="row" style="margin-bottom: 15px;">
                <button class="btn btn-sm btn-primary" type="button" @click="login">LOGIN</button>
            </div>
        </div>
        <div class="container">
            <div class="row" style="margin-bottom: 15px;">
                <button class="btn" type="button" style="background-color: lightblue;"  @click="register">REGISTER</button>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name:'LoginPage',
        emits:['actualPage','logedUser'],
        data() {
            return {
                inputUserName:null, // user name input (string)
                inputUserPswrd:null, // user password input (password)
                usersList:JSON.parse(localStorage.getItem('LSusersList'))??console.log("LoginPage / data userslist not loaded."),
                logedUser:{ // inner variable of logedUser handed over through whole FE (after once successfully loged)
                    id: '',
                    name: '',
                    pswrd: '',
                    userItemsList: []
                }
            }
        },
        methods: {
            login(){         // main login function checking of login credentials, their validation and passing further in FE
                if (!this.checkInputEmptiness()){
                    if (this.usersList) { // name+password non-empty ?
                    (this.usersList).forEach(element => {
                        if(this.inputUserName==element.name){ // input name corresponds with stored ?
                            if(this.inputUserPswrd==element.pswrd){ // password realted name above corresponds with stored ?
                                (this.logedUser).id= element.id,    // sucesfull validation results in logedUser variable filling with user inputs credentials
                                (this.logedUser).name= element.name,
                                (this.logedUser).pswrd= element.pswrd,
                                (this.logedUser).userItemsList= element.userItemsList
                                } 
                            }
                        });
                    } else console.log("login() / usersList not loaded. usresList " + JSON.stringify(this.usersList)); // control of user list upload
                    // control of id+pswrd field uncorectness (after control of emptiness)
                    if ((this.logedUser).name!='') {
                        this.$emit('actualPage',`detailing`);
                        this.$emit('logedUser', this.logedUser);
                    }
                    else alert("Login credential are wrong or you are not yet registered.");
                }
                this.clearInputs();
            },
            checkInputEmptiness(){  	// supporting method for meeting requirements of non-emptiness of user inputs (name, password)
                let errorEmptyInputs = false;
                // check of inputs (non)emptiness
                if (this.inputUserName==null || this.inputUserPswrd==null) {
                    alert("Name nor password  can not be empty. Please check and fill in.");
                    errorEmptyInputs=true;
                    this.clearInputs();
                }
                return errorEmptyInputs;
            },
            clearInputs(){ // supporting method for clearing input fields in case of their error - emptiness or non existence (wrong inputs or not yet registered)
                this.inputUserName=null;
                this.inputUserPswrd=null;// clearing of form
            },
            register(){ // redirects from login to registration page after clicking button REGISTER
                this.$emit('actualPage','registration')
            }
        }
    }
</script>