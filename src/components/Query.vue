<template>
</template>

<script>
    export default {
        name: 'Query',
        props: {
            msg: String
        }  ,
        data() {
            contacts: [
                { id: 0, firstName: 'Danelle', lastName: 'Cline', email: 'dcline@mbari.org' },
                { id: 1, firstName: 'Evan', lastName: 'Elliott', email: 'dd@mbari.org' },
                { id: 2, firstName: 'Jimmy', lastName: 'Elliott', email: 'jimmy.elliott.fishon@gmail.com' }
            ]
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
