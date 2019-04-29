<script>
import { Line, mixins } from 'vue-chartjs'

export default {
    name: 'Grafico',
    extends: Line,
    mixins: [mixins.reactiveProp],

    data() {
        return {
            chartdata: {
                labels: this.dias,
                datasets: [{
                    label: 'Temperatura',
                    fill: false,
                    backgroundColor: "#ff0000",
                    borderColor: "#ff0000",
                    data: this.temperatura
                },
                {
                    label: 'Umidade',
                    fill: false,
                    backgroundColor: "#0000cc",
                    borderColor: "#0000cc",
                    data: this.umidade
                }]
            },
            options: {
				responsive: true,
				tooltips: {
					mode: 'index',
					intersect: false,
				},
				hover: {
					mode: 'nearest',
					intersect: true
				},
				scales: {
					xAxes: [{
						display: true,
						scaleLabel: {
							display: true,
							labelString: ''
						}
					}],
					yAxes: [{
						display: true,
						scaleLabel: {
                            display: true,
                            labelString: ''
					    }
				    }]
			    }
		    }
        }
    },
    mounted() {
        this.renderChart(this.chartData, this.options)
    }
}
</script>
