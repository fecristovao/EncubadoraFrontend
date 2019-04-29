<template>
  <div id="tudo">
    <div id="data">
      Data Inicio: <datepicker v-model="inicio"></datepicker>
      Data Fim: <datepicker v-model="fim"></datepicker>
    </div>
    <Grafico id="gr" :chartData="chartData3"></Grafico>
    <div id="grafico">
      <Grafico id="grafico1" :chartData="chartData"></Grafico>
      <Grafico :chartData="chartData2"></Grafico><br>
      
    </div>
    
  </div>
  
  
</template>

<script>
// @ is an alias to /src
import Grafico from '@/components/Grafico.vue'
import axios from 'axios'
import { setInterval } from 'timers';
import Datepicker from 'vuejs-datepicker';

export default {
  name: 'home',
  components: {
    Grafico, Datepicker
  },
  data() {
    return {
      loaded: false,
      horas: [],
      temperatura: [],
      umidade: [],
      inicio: new Date(),
      fim: new Date(),
      chartData: {},
      chartData2: {},
      chartData3: {}
    }
  },
  computed: {
    dataInicio() {
      return this.formatarData(this.inicio)
    },
    dataFim() {
      return this.formatarData(this.fim)
    }
  },
  methods: {
    formatarData(data) {
        var dia  = data.getDate().toString().padStart(2, '0')
        var mes  = (data.getMonth()+1).toString().padStart(2, '0') //+1 pois no getMonth Janeiro começa com zero.
        var ano  = data.getFullYear()
        return dia+"-"+mes+"-"+ano;
    },
    pegarDados() {
      console.log("pegando dados")
      this.loaded = false
      this.umidade = []
      axios.get('http://encubensis.herokuapp.com/data/'+this.dataInicio+'/'+this.dataFim).then(response => {
        // JSON responses are automatically parsed.
        console.log(response)
        this.temperatura = response.data.map(t => {return t.temperatura != null ? t.temperatura : 0})
        this.umidade = response.data.map(u => {return u.umidade != null ? u.umidade : 0})
        var k = this;
        this.horas = response.data.map(hora => {
          var data = new Date(hora.created_at)
          return (data.getHours() + ":" + data.getMinutes()).length < 5 ?  (data.getHours() + ":0" + data.getMinutes()) :  (data.getHours() + ":" + data.getMinutes())
          })
      }).catch(e => {
        this.errors.push(e)
      }).finally(() => {
        this.chartData = {
                labels: this.horas,
                datasets: [{
                    label: 'Temperatura',
                    fill: false,
                    backgroundColor: "rgb(255, 99, 132)",
                    borderColor: "rgb(255, 99, 132)",
                    data: this.temperatura
                }]
          }
        this.chartData2 = {
                labels: this.horas,
                datasets: [
                {
                    label: 'Umidade',
                    fill: false,
                    backgroundColor: "rgb(54, 162, 235)",
                    borderColor: "rgb(54, 162, 235)",
                    data: this.umidade
                }]
          }

        this.chartData3 = {
                labels: this.horas,
                datasets: [
                {
                    label: 'Umidade',
                    fill: false,
                    backgroundColor: "rgb(54, 162, 235)",
                    borderColor: "rgb(54, 162, 235)",
                    data: this.umidade
                },{
                    label: 'Temperatura',
                    fill: false,
                    backgroundColor: "rgb(255, 99, 132)",
                    borderColor: "rgb(255, 99, 132)",
                    data: this.temperatura
                }]
          }
      })
    }
  },
  mounted() {
    setInterval(() => {
      this.pegarDados()
    }, 5000);
  }
}
</script>

<style scoped>
  #tudo {
    width: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
    align-self: center;
    text-align: center;
    justify-items: center;
    justify-self: center;
    flex-wrap: wrap;
  }
  #grafico {
    display: flex;
    flex-direction: row;
    justify-items: center;
    align-items: center;
    flex-wrap: wrap;
  }
  #gr {
    width: 70%;
  }
  #data {
     border: 1px solid #ddd;
  padding: 0em 1em 1em;
  display: flex;
    flex-direction: row;
  margin-bottom: 2em;
  }

  #data p {
    padding: 0;
    margin:0;
  }
  
</style>
