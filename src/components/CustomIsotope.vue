<template>
    <div class="hello">
        <h1>{{ msg }}</h1>
        <h1>Vue isotope</h1>
        <div>
            <div v-for="contact in annotation" :key="contact.id" @click="selected=contact">
                {{contact.name}}
                <br>
            </div>
        </div>

    </div>
</template>

<script>
    import Isotope from 'isotope-layout'

    function addClass(node, classValue) {
        if (node.data) {
            const initValue = (!node.data.staticClass) ? "" : node.data.staticClass + " "
            node.data.staticClass = initValue + classValue
        }
    }
    function getItemVm(elmt) {
        return elmt.__underlying_element
    }
    export default {
        name: 'CustomIsotope',
        props: {
            msg: String,
            options: {
                type: Object,
                default: {
                    layoutMode: 'masonry',
                    masonry: {
                        gutter: 10
                    }
                },
            },
            itemSelector: {
                type: String,
                default: "item"
            },
            list: {
                type: Array,
                required: true
            }
        },
        data() {
            contacts: [
                { id: 0, firstName: 'Danelle', lastName: 'Cline', email: 'dcline@mbari.org' },
                { id: 1, firstName: 'Evan', lastName: 'Elliott', email: 'dd@mbari.org' },
                { id: 2, firstName: 'Jimmy', lastName: 'Elliott', email: 'jimmy.elliott.fishon@gmail.com' }
            ]
        },
        render(h) {
            const map = {}
            const prevChildren = this.prevChildren = this.children
            const rawChildren = this.$slots.default || []
            const children = this.children = []
            const removedIndex = this.removedIndex = []

            rawChildren.forEach(elt => addClass(elt, this.itemSelector))

            for (let i = 0; i < rawChildren.length; i++) {
                const c = rawChildren[i]
                if (c.tag) {
                    if (c.key != null && String(c.key).indexOf('__vlist') !== 0) {
                        children.push(c)
                        map[c.key] = c
                    } else {
                        const opts = c.componentOptions
                        const name = opts ? (opts.Ctor.options.name || opts.tag || '') : c.tag
                        console.log(`Warning template error: isotope children must be keyed: <${name}>`)
                    }
                }
            }

            const displayChildren = this.displayChildren = [...children]

            if (prevChildren) {
                for (let i = 0; i < prevChildren.length; i++) {
                    const c = prevChildren[i]
                    if (!map[c.key]) {
                        displayChildren.splice(i, 0, c)
                        removedIndex.push(i)
                    }
                }
            }

            return h('div', null, displayChildren)
        },
        mounted() {
            var _ = require("lodash")
            const options = _.merge({}, this.compiledOptions)
            var update = (object) => {
                _.forOwn(object, (value, key) => {
                    object[key] = (itemElement) => { const res = getItemVm(itemElement); return value.call(this, res.vm, res.index); };
                });
            };
            update(options.getSortData)
            update(options.getFilterData);
            this._isotopeOptions = options
            if (options.filter) {
                options.filter = this.buildFilterFunction(options.filter)
            }

            this.$nextTick(() => {
                this.link()
                this.listen()
                const iso = new Isotope(this.$el, options)

                iso._requestUpdate = () => {
                    if (iso._willUpdate)
                        return

                    iso._willUpdate = true
                    this.$nextTick(() => {
                        iso.arrange()
                        iso._willUpdate = false
                    });
                };
                this.iso = iso
            })
        },
        beforeDestroy() {
            var _ = require("lodash")
            _.forEach(this._listeners, (unlisten) => { unlisten(); })
            if (this._filterlistener) {
                this._filterlistener()
            }
            this.iso = null
        },
        beforeUpdate() {
            this._oldChidren = Array.prototype.slice.call(this.$el.children)
        },
        updated() {
            if (!this.iso) {
                return;
            }
            var _ = require("lodash")
            const newChildren = [...this.$el.children]
            const added = _.difference(newChildren, this._oldChidren)
            const removed = this.removedIndex.map(index => this.$el.children[index])

            this.cleanupNodes()
            this.link()

            if ((!removed.length) && (!added.length))
                return;

            this.listen()

            this.iso.remove(removed)
            this.iso.insert(added)
            this.iso._requestUpdate()
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
            },
            cleanupNodes() {
                this.removedIndex.reverse()
                this.removedIndex.forEach(index => this._vnode.children.splice(index, 1))
            },
            link() {
                const slots = this.$slots.default || []
                slots.forEach(
                    (slot, index) => {
                        const elmt = slot.elm
                        if (elmt)
                            elmt.__underlying_element = { vm: this.annotation[index], index }
                    })
            },
            listen() {
                var _ = require("lodash")
                this._listeners = _(this.compiledOptions.getSortData).map((sort) => {
                    return _.map(this.annotation, (collectionElement) => {
                        return this.$watch(() => { return sort(collectionElement); }, () => {
                            this.iso.updateSortData();
                            this.iso._requestUpdate();
                        });
                    });
                }).flatten().value();
            },
            sort(name) {
                let sort = name
                var _ = require("lodash")
                if (_.isString(name)) {
                    sort = { sortBy: name }
                }
                this.arrange(sort)
                this.$emit("sort", name)
            },
            buildFilterFunction (name) {
                var _ = require("lodash")
                const filter = this._isotopeOptions.getFilterData[name]
                this._filterlistener = this.$watch(() => { return _.map(this.annotation, (el, index) => this.options.getFilterData[name](el, index)); },
                    () => { this.iso._requestUpdate(); });
                return filter
            },
            filter(name) {
                const filter = this.buildFilterFunction(name)
                this.arrange({ filter })
                this.$emit("filter", name)
            },
            unfilter() {
                this.arrange({ filter: () => { return true; } })
                this.$emit("filter", null)
            },
            layout(name) {
                let layout = name
                var _ = require("lodash")
                if (_.isString(name)) {
                    layout = { layoutMode: name }
                }
                this.arrange(layout)
                this.$emit("layout", layout)
            },
            arrange(option) {
                this.iso.arrange(option)
                this.$emit("arrange", option)
            },
            shuffle() {
                this.iso.shuffle()
                this.$emit("shuffle")
                this.$emit("sort", null)
            },
            getFilteredItemElements() {
                return this.iso.getFilteredItemElements()
            },
            getElementItems() {
                return this.iso.getElementItems()
            },
        },
        computed: {
            compiledOptions() {
                var _ = require("lodash")
                const options = _.merge({}, this.options, { itemSelector: "." + this.itemSelector, isJQueryFiltering: false })

                _.forOwn(options.getSortData, (value, key) => {
                    if (_.isString(value))
                        options.getSortData[key] = (itemElement) => { return itemElement[value]; };
                });

                return options;
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
