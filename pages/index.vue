<template>
  <v-container>
    <div class="btn-wrap d-flex justify-center flex-column align-center mt-4">
      <v-btn-toggle mandatory v-model="selectedType">
        <v-btn width="120" v-for="type of types" :key="type">{{type.toUpperCase()}}</v-btn>
      </v-btn-toggle>
      <v-subheader class="text-h6 pl-0 pt-2"><b>Available fragrances</b></v-subheader>
    </div>
    <div>
      <v-row class="d-flex" v-for="(key, i) of Object.keys(data[types[selectedType]])" :key="i">
        <v-col cols="12">
          <div class="word">Word {{String.fromCharCode(key)}}</div>
        </v-col>
        <v-col cols="6" md="4" lg="3" v-for="(item, n) of data[types[selectedType]][key]" :key="n">
          <v-card class="pa-2">{{item.title}}</v-card>
        </v-col>
      </v-row>
    </div>
  </v-container>
</template>

<script>
  export default {
    async asyncData({$axios}) {
      let data = await $axios.$get('https://fset-backend.herokuapp.com/fragrances')
      const pattern = /[^\x20\x2D0-9A-Z\x5Fa-z\xC0-\xD6\xD8-\xF6\xF8-\xFF]/g
      
      data = data.sort((a, b) => a.title.toUpperCase().replace(pattern, "").trim().localeCompare(b.title.toUpperCase().replace(pattern, "").trim()))
      const finalData = {}

      finalData.man = []
      finalData.woman = []
      finalData.unisex = []

      for (const key of Object.keys(finalData)) {
        const subdata = data.filter(el => el.type === key)
        subdata.forEach(el => {
          const str = el.title.toUpperCase().replace(pattern, "").trim()
          const code = str.charCodeAt()
          const {type, title} = el

          if (!finalData[key][code]) {finalData[key][code] = []}
          finalData[key][code].push({
            title,
            type
          })

        })

      }

      return {
        data: finalData
      }
    },
    data() {
      return {
        selectedType: 0,
        types: ['man', 'woman', 'unisex']
      }
    }
  }
</script>

<style lang="scss" scoped>
.btn-wrap {
  width: 100%;
}
.word {
  position: relative;
  font-size: 16px;
  font-weight: bold;
  letter-spacing: .4px;
  padding:  0  0 5px 5px;

  &::after {
    content: '';
    width: 100%;
    height: 2px;
    background: #272727;
    border-radius: 5px;
    position: absolute;
    bottom: 0;
    left: 0;
  }
}
</style>