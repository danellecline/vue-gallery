<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <h1>Vue isotope</h1>
      <div>
        <CustomIsotope ref="cpt" id="root_isotope" class="isoDefault" :options='getOptions()' :list="annotation" @filter="filterOption=arguments[0]"
                       @sort="sortOption=arguments[0]">
          <div v-for="contact in annotation" :key="contact.id" @click="selected=contact">
            {{contact.name}}
            <br>
          </div>
        </CustomIsotope>
      </div>

  </div>
</template>

<script>
import CustomIsotope from "./CustomIsotope";

export default {
  name: 'Grid',
  components: {CustomIsotope},
  props: {
    msg: String
  },
  methods: {
    getOptions() {
      var _this = this
      return {
        layoutMode: 'masonry',
        masonry: {
          gutter: 10
        },
        getSortData: {
          id: "id",
          name: function (itemElem) {
            return itemElem.name.toLowerCase();
          }
        },
        getFilterData: {
          isEven: function (itemElem) {
            return itemElem.id % 2 === 0;
          },
          isOdd: function (itemElem) {
            return itemElem.id % 2 !== 0;
          },
          filterByText: function (itemElem) {
            return itemElem.name.toLowerCase().includes(_this.filterText.toLowerCase());
          }
        }
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
