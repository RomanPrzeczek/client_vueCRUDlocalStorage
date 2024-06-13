<template>
    <div class="card" style="margin: 2% 10% 2% 10%; padding: 2%; background-color: beige;">
        <div class="container">
            <div class="row" style="margin-bottom: 15px;">
                <button class="btn" type="button" style="background-color: bisque;"  @click="printLocalStorage">PRINT LOCAL STORAGE TO CONSOLE</button>
            </div>
            <div class="row" style="margin-bottom: 15px;">
                <button class="btn" type="button" style="background-color: darksalmon;" @click="clearLSusers">REMOVE LSusersList</button>
            </div>
        </div>
    </div>

</template>

<script>
    export default {
        name: 'LocalStorage',
        methods: {
            printLocalStorage(){
                console.log("ACTUAL LOCAL STORED ITEMS:")
                for (let i = 0; i < localStorage.length; i++) {
                    const key = localStorage.key(i);
                    const value = localStorage.getItem(key);
                    console.log('Key: ' + key + ', Value: ' + value);  
                }
            },
            clearLSusers(){
                localStorage.removeItem("LSusersList");
                this.printLocalStorage();
            }
        }
    }
</script>