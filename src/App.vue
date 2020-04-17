<template>
  <div>
    <h1>Vue isotope</h1>
    <HelloWorld msg="Sorting App"/>
    <split-pane :min-percent='20' :max-percent='50' :default-percent='60' split="vertical">
      <template slot="paneL">

        <div>
          <isotope ref="cpt" id="root_isotope" class="isoDefault" :options='getOptions()' :list="annotation" @filter="filterOption=arguments[0]"
                   @sort="sortOption=arguments[0]">
            <div v-for="c in annotation" :key="c.id" @click="selected=c">
              <!--<span>{{c.name}}</span>-->
              <img :src="c.url" />
            </div>
          </isotope>
        </div>

      </template>
      <template slot="paneR">
        <div id="filter">

          <h2>Unique- Taxa</h2>
          <!--<div v-for="taxa in uniqueTaxa" :key="taxa">-->
            <!--{{ taxa.concept }}-->
          <!--</div>-->
          <div id="brrr">
            <model-select :options="uniqueTaxa"
                          v-model="item"
                          placeholder="select item">
            </model-select>
          </div>
          <div id="change">
            <button @click="add">Add</button>
            <button @click="replace">Replace</button>
            <button @click="remove">Remove</button>
          </div>
          <div>
            <input type="text" v-model="filterText" placeholder="no filter">
            <button :class="[filterOption==='filterByText' ? 'is-checked' : '']" @click="$refs.cpt.filter('filterByText')">Filter</button>
          </div>
          <button :class="[filterOption==='isEven' ? 'is-checked' : '']" @click="$refs.cpt.filter('isEven')">Filter Even</button>
          <button :class="[filterOption==='isOdd' ? 'is-checked' : '']" @click="$refs.cpt.filter('isOdd')">Filter Odd</button>
          <button @click="$refs.cpt.unfilter()">Unfilter</button>
        </div>
        <div id="sort">
          <button :class="[sortOption==='name' ? 'is-checked' : '']" @click="$refs.cpt.sort('name')">Sort by name</button>
          <button :class="[sortOption==='id' ? 'is-checked' : '']" @click="$refs.cpt.sort('id')">Sort by id</button>
          <button @click="$refs.cpt.shuffle()">Shuffle</button>
          <div v-if="selected" class="item">
            <recteditor ref="editor" :uniqueTaxa="uniqueTaxa" :annotation="selected" :canvas-height="300" :canvas-width="300" ></recteditor>
          </div>
        </div>
      </template>
    </split-pane>

  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld'
import isotope from 'vueisotope'
import splitPane from 'vue-splitpane'
import recteditor from './components/RectEditor'
import { ModelSelect } from "vue-search-select";

var count = 15;
export default {
  name: 'App',
  components: {
    ModelSelect,
    HelloWorld,
    isotope,
    splitPane,
    recteditor
  },
  data() {
    return {
      annotation: [
        {id: 0, name: 'Danelle', height: 100, width: 50, x: 10, y: 35, ref: 'http://localhost:3000/static/fullframe/D0232_03HD_00-57-05.jpg', url: 'http://localhost:3000/static/LONG_WHITE/D0232_03HD_00-48-10_0.jpg'},
        {id: 1, name: 'AEvan', height: 100, width: 50, x: 10, y: 35, ref: 'http://localhost:3000/static/fullframe/D0232_03HD_00-30-25.jpg', url: 'http://localhost:3000/static/PENIAGONE_SP_A/D0232_03HD_00-45-00_0.jpg'},
        {id: 2, name: 'BEvan', height: 100, width: 50, x: 10, y: 35, ref: 'http://localhost:3000/static/fullframe/D0232_03HD_00-30-25.jpg', url: 'http://localhost:3000/static/PENIAGONE_SP_A/D0232_03HD_00-45-00_0.jpg'},
        {id: 3, name: 'CEvan', height: 100, width: 50, x: 10, y: 35, ref: 'http://localhost:3000/static/fullframe/D0232_03HD_00-30-25.jpg', url: 'http://localhost:3000/static/PENIAGONE_SP_A/D0232_03HD_00-45-00_0.jpg'},
        {id: 4, name: 'DEvan', height: 100, width: 50, x: 10, y: 35, ref: 'http://localhost:3000/static/fullframe/D0232_03HD_00-30-25.jpg', url: 'http://localhost:3000/static/PENIAGONE_SP_A/D0232_03HD_00-45-00_0.jpg'},
        {id: 5, name: 'Evan', height: 100, width: 50, x: 10, y: 35, ref: 'http://localhost:3000/static/fullframe/D0232_03HD_00-30-25.jpg', url: 'http://localhost:3000/static/PENIAGONE_SP_A/D0232_03HD_00-45-00_0.jpg'},
        {id: 6, name: 'Evan', height: 100, width: 50, x: 10, y: 35, ref: 'http://localhost:3000/static/fullframe/D0232_03HD_00-30-25.jpg', url: 'http://localhost:3000/static/PENIAGONE_SP_A/D0232_03HD_00-45-00_0.jpg'},
        {id: 7, name: 'Evan', height: 100, width: 50, x: 10, y: 35, ref: 'http://localhost:3000/static/fullframe/D0232_03HD_00-30-25.jpg', url: 'http://localhost:3000/static/PENIAGONE_SP_A/D0232_03HD_00-45-00_0.jpg'},
        {id: 8, name: 'Evan', height: 100, width: 50, x: 10, y: 35, ref: 'http://localhost:3000/static/fullframe/D0232_03HD_00-30-25.jpg', url: 'http://localhost:3000/static/PENIAGONE_SP_A/D0232_03HD_00-45-00_0.jpg'},
        {id: 9, name: 'Evan', height: 100, width: 50, x: 10, y: 35, ref: 'http://localhost:3000/static/fullframe/D0232_03HD_00-30-25.jpg', url: 'http://localhost:3000/static/PENIAGONE_SP_A/D0232_03HD_00-45-00_0.jpg'},
        {id: 10, name: 'Evan', height: 100, width: 50, x: 10, y: 35, ref: 'http://localhost:3000/static/fullframe/D0232_03HD_00-30-25.jpg', url: 'http://localhost:3000/static/PENIAGONE_SP_A/D0232_03HD_00-45-00_0.jpg'},
        {id: 11, name: 'Evan', height: 100, width: 50, x: 10, y: 35, ref: 'http://localhost:3000/static/fullframe/D0232_03HD_00-30-25.jpg', url: 'http://localhost:3000/static/PENIAGONE_SP_A/D0232_03HD_00-45-00_0.jpg'},
        {id: 12, name: 'Evan', height: 100, width: 50, x: 10, y: 35, ref: 'http://localhost:3000/static/fullframe/D0232_03HD_00-30-25.jpg', url: 'http://localhost:3000/static/PENIAGONE_SP_A/D0232_03HD_00-45-00_0.jpg'},
        {id: 13, name: 'Evan', height: 100, width: 50, x: 10, y: 35, ref: 'http://localhost:3000/static/fullframe/D0232_03HD_00-30-25.jpg', url: 'http://localhost:3000/static/PENIAGONE_SP_A/D0232_03HD_00-45-00_0.jpg'},
        {id: 14, name: 'AAAJimmy', height: 100, width: 50, x: 10, y: 35, ref: 'http://localhost:3000/static/fullframe/D0232_03HD_00-30-25.jpg', url: 'http://localhost:3000/static/BENTHOCODON/D0232_03HD_01-00-05_0.jpg'}
      ],
      selected: null,
      sortOption: null,
      filterOption: null,
      filterText: "",
      uniqueTaxa: [
              {text: 'Da5555555555555555555555555555555555nelle', value: '4'},
              {text: 'AAAJimmy', value: '3'},
              {text: 'Evan', value: '0'},
              {text: 'AEvan', value: '1'},
              {text: 'BEvan', value: '2'},
              {text: 'CEvan', value: '5'}],
      item: {
        value: '',
        text: ''
      }
    }
  },
  // mounted() {
  //   this.$refs.sort.editor.set(this.editor.mode,this.editor.options);
  // },
  methods: {
    getOptions: function () {
      var _this = this
      return {
        layoutMode: 'masonry',
        masonry: {
          gutter: 1
        },
        getSortData: {
          id: "id",
          lastName: "name",
          name: function(itemElem){
            return itemElem.name.toLowerCase();
          }
        },
        getFilterData:{
          "show all": function(){
            return true;
          },
          "id > 50": function(itemElem) {
            return itemElem.id > 50;
          },
          isEven: function(itemElem){
            return itemElem.id % 2 === 0;
          },
          isOdd: function(itemElem){
            return itemElem.id % 2 !== 0;
          },
          filterByText: function(itemElem){
            return itemElem.name.toLowerCase().includes(_this.filterText.toLowerCase());
          }
        }
      }
    },
    add: function () {
      this.list.push({name:'Juan', id:count++});
    },
    replace: function(){
      this.list=[{name:'AAAJimmy', id: 14}, {name:'AAAJimmyFoobar', id:count++, height: 100, width: 50, x: 10, y: 35,
        ref: 'http://localhost:3000/static/fullframe/D0232_03HD_00-30-25.jpg',
        url: 'http://localhost:3000/static/LONG_WHITE/D0232_03HD_00-48-10_0.jpg'}]
    },
    remove: function(){
      if (this.list.length)
        this.list.splice(0, 1)
    },
    sort: function (key) {
      this.isotopeSort(key);
      this.sortOption=key;
    },
    filter : function (key){
      if (this.filterOption==key)
        key=null;
      this.isotopeFilter(key);
      this.filterOption=key;
    },
    shuttle : function(){
      this.isotopeShuttle();
      this.sortOption=null;
    }
  }
}
</script>

<style lang="scss">
  @import "./app.scss";
</style>
