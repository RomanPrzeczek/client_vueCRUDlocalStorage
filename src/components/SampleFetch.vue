<template>
    <div class="card" style="margin: 2% 10% 2% 10%; padding: 2%; background-color: lightgray;">
        <div class="container">
            <div class="row">
                <button @click="fetchKurzy">Fetch Data</button>
            </div>
            <div class="row">
                <div class="col">
                    Fetched data: 
                </div>
                <div class="col" style="color: red;">
                    {{ outputFetch }}
                </div>
<!--                 <div class="col" style="color: red;">
                    {{ outputFetch.rate }}
                </div>
                <div class="col" style="color: red;">
                    {{ outputFetch.date }}
                </div>
 -->                
            </div>
            <div class="row">
                <button @click="printData">Print Data to console</button>
            </div>
        </div>

    </div>
</template>

<script>

export default {
    name: 'SampleFetch',
    data: () => {
        return {
            outputFetch: 'iniUtput',
/*             outputFetch: {
                rate:1,
                date:''
            },
*/
        }
    },
    methods: {
        async fetchKurzy(){
            console.log('fetchKurzy');
            await fetch('http://localhost:8000/cnb')
                .then((res) => res.text())
                .then((body) => {
                    this.outputFetch=parseFloat(body);
                    console.log("SampleFetch / fetchKurzy / outputFetch: "+this.outputFetch);
                })
                .catch(err => console.log(`SampleFetch / initialFetch / err : ${err}`))
        },
        printData (){
            console.log(`outputFetch: ${JSON.stringify(this.outputFetch)}`)
        },
    }
}
</script>