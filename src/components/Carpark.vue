<script>
export default {
  name: "Carpark",
  data () {
    return {
      carpark: null,
      smallMax: null,
      smallMaxItem: null,
      smallMin: null,
      smallMinItem: null,
      mediumMax: null,
      mediumMaxItem: null,
      mediumMin: null,
      mediumMinItem: null,
      bigMax: null,
      bigMaxItem: null,
      bigMin: null,
      bigMinItem: null,
      largeMax: null,
      largeMaxItem: null,
      largeMin: null,
      largeMinItem: null,
    }
  },
  methods: {
    async getCarpark() {
      return await this.$http.get("https://api.data.gov.sg/v1/transport/carpark-availability")
    },
    getItems(dataset, value) {
      return dataset.filter((carpark) => { 
        return carpark.lots_available == value
      }).map((item) => {
        return item.carpark_number
      });
    },
    async fetchData() {
    let response = await this.getCarpark();
    let carparkData = response.data.items[0].carpark_data
    this.smallMax = 0
    this.smallMin = carparkData.length
    this.mediumMax = 0
    this.mediumMin = carparkData.length
    this.bigMax = 0
    this.bigMin = carparkData.length
    this.largeMax = 0
    this.largeMin = carparkData.length

    let mappedCarpark = carparkData.map((item) => {
      let totalLots = item.carpark_info.reduce((accumulator, object) => {
            return accumulator + parseInt(object.total_lots);
          }, 0)
      let lotsAvailable = item.carpark_info.reduce((accumulator, object) => {
            return accumulator + parseInt(object.lots_available);
          }, 0)
      if (totalLots < 100) {
        if (lotsAvailable < this.smallMin) {
          this.smallMin = lotsAvailable
        }
        if (lotsAvailable > this.smallMax) {
          this.smallMax = lotsAvailable
        }
      } else if (totalLots < 300) {
        if (lotsAvailable < this.mediumMin) {
          this.mediumMin = lotsAvailable
        }
        if (lotsAvailable > this.mediumMax) {
          this.mediumMax = lotsAvailable
        }
      } else if (totalLots < 400) {
        if (lotsAvailable < this.bigMin) {
          this.bigMin = lotsAvailable
        }
        if (lotsAvailable > this.bigMax) {
          this.bigMax = lotsAvailable
        }
      } else {
        if (lotsAvailable < this.largeMin) {
          this.largeMin = lotsAvailable
        }
        if (lotsAvailable > this.largeMax) {
          this.largeMax = lotsAvailable
        }
      }
      return {
        carpark_number: item.carpark_number,
        total_lots: totalLots,
        lots_available: lotsAvailable,
      };
    })

    let small = mappedCarpark.filter((carpark) => { 
      return carpark.total_lots < 100
    });

    let medium = mappedCarpark.filter((carpark) => {
      return carpark.total_lots >= 100 && carpark.total_lots < 300
    });

    let big = mappedCarpark.filter((carpark) => { 
      return carpark.total_lots >= 300 && carpark.total_lots < 400
    });

    let large = mappedCarpark.filter((carpark) => { 
      return carpark.total_lots > 400
    });

    this.smallMaxItem = this.getItems(small, this.smallMax)
    this.smallMinItem = this.getItems(small, this.smallMin)
    this.mediumMaxItem = this.getItems(medium, this.mediumMax)
    this.mediumMinItem = this.getItems(medium, this.mediumMin)
    this.bigMaxItem = this.getItems(big, this.bigMax)
    this.bigMinItem = this.getItems(big, this.bigMin)
    this.largeMaxItem = this.getItems(large, this.largeMax)
    this.largeMinItem = this.getItems(large, this.largeMin)
    }
  },
  mounted() {
    this.fetchData()
    setInterval(this.fetchData, 60 * 1000)
  }
}
</script>

<template>
<div>
  <b-jumbotron header="Carpark Summary" />
  <div class="mb-3">
    <b-card-group deck>
      <b-card title="SMALL">
        <b-card-text>
          HIGHEST ({{ smallMax }} lots available)
        </b-card-text>
        <b-card-text>
          CARPARK : {{ smallMaxItem }}
        </b-card-text>
        <b-card-text>
          LOWEST ({{ smallMin }} lots available)
        </b-card-text>
        <b-card-text>
          CARPARK : {{ smallMinItem }}
        </b-card-text>
      </b-card>

      <b-card title="MEDIUM">
        <b-card-text>
          HIGHEST ({{ mediumMax }} lots available)
        </b-card-text>
        <b-card-text>
          CARPARK : {{ mediumMaxItem }}
        </b-card-text>
        <b-card-text>
          LOWEST ({{ mediumMin }} lots available)
        </b-card-text>
        <b-card-text>
          CARPARK : {{ mediumMinItem }}
        </b-card-text>
      </b-card>
    </b-card-group>
  </div>
  <div>
    <b-card-group deck>
      <b-card title="BIG">
        <b-card-text>
          HIGHEST ({{ bigMax }} lots available)
        </b-card-text>
        <b-card-text>
          CARPARK : {{ bigMaxItem }}
        </b-card-text>
        <b-card-text>
          LOWEST ({{ bigMin }} lots available)
        </b-card-text>
        <b-card-text>
          CARPARK : {{ bigMinItem }}
        </b-card-text>
      </b-card>

      <b-card title="LARGE">
        <b-card-text>
          HIGHEST ({{ largeMax }} lots available)
        </b-card-text>
        <b-card-text>
          CARPARK : {{ largeMaxItem }}
        </b-card-text>
        <b-card-text>
          LOWEST ({{ largeMin }} lots available)
        </b-card-text>
        <b-card-text>
          CARPARK : {{ largeMinItem }}
        </b-card-text>
      </b-card>
    </b-card-group>
  </div>
</div>
</template>

<style scoped>

</style>
