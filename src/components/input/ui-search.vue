<template>

    <div
         :class="searchClass">
        <form @submit.prevent="submit">
            <div class="input-group">
                <input type="text" name="search" v-model="searchTerm" class="form-control" />
                <div class="input-group-btn">
                    <button type="submit" class="btn btn-primary">
                        <i class="glyphicon glyphicon-search"></i>
                        Pesquisar
                    </button>
                    <a @click.prevent="refresh" class="btn btn-default">
                        <i class="glyphicon glyphicon-refresh"></i>
                    </a>
                </div>
            </div>
        </form>
    </div>

</template>

<style scoped>

</style>

<script>

    export default{
        props: {
            size: {
                type: String,
                default: '3'
            },
            name: {
                type: String,
                required: true
            },
            position: String,
            apiUrl: String
        },

        data(){
            return{
                searchTerm: ''
            }
        },

        computed:{
            searchClass: function () {
                return `col-md-${this.size} pull-${this.position.toLowerCase()}`
            }
        },

        methods: {
            submit: function (e) {
                this.search();
            },

            refresh: function (e) {
                this.$set('searchTerm', '');
                this.search();
            },

            search: function () {
                var self = this;
                var url = self.apiUrl + "?search=" + self.searchTerm;

                self.$dispatch("ui-search:searching", self.name);

                self.$http.get(url).then(function (response) {
                    self.onComplete(response);
                }, function (response) {
                    //error
                    self.onComplete(response);
                });
            },
            onComplete: function (response) {
                this.$dispatch('ui-search:completed', this.name, response);
            }
        }
    }
</script>