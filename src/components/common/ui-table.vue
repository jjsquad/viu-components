<script>

    export default{

        props: {
            name: {
                type: String,
                required: true
            },
            apiUrl: String,
            styles: String,
            columns: {
                type: Array,
                default: function () {
                    return [];
                }
            },
            dataSource: {
                type: Array,
                default: function () {
                    return [];
                }
            },
            actions: {
                type: Array,
                default: function () {
                    return [];
                }
            },
            size: {
                type: String,
                default: '12'
            },
            sortOrder: {
                type: Object,
                default: function () {
                    return {
                        column: '',
                        direction: 0
                    };
                }
            },
            queryParams: {
                type: Object,
                default: function () {
                    return {
                        sort: 'sort',
                        order: 'order',
                        page: 'page',
                        perPage: 'per_page'
                    }
                }
            },
            nodataText: {
                type: String,
                default: 'Não há resultados.'
            }
        },

        computed: {
            sortDirection: function () {
                return this.sortOrder.direction == 0 ? 'asc' : 'desc'
            },
            tableClass: function () {
                let prefix = ' table-'
                let splited = this.styles ? this.styles.split(',') : null
                let styles = splited ? Array.join(splited, prefix) : null
                return `col-md-${this.size} ${styles}`
            }
        },

        data(){
            return{
                loading: false
            }
        },

        events: {
            "ui-table:fill": function (target, data) {
                if(target == this.name) {
                    this.dataSource = this.getObjectValue(data, 'data');
                }
            },
            'ui-table:refresh': function (target) {
                if(target == this.name) {
                    this.loadData();
                }
            }
        },

        methods: {
            dispatchEvent: function (event, data) {
                this.$dispatch(event, this.name, data);
            },
            callAction: function (name, data, e) {
                e.preventDefault();
                this.dispatchEvent("ui-table:action", {
                    'name': name,
                    'data': this.getObjectValue(data, '')
                });
            },
            onRowClicked: function (row) {
                this.dispatchEvent("ui-table:row.clicked", row);
            },
            orderBy: function (column) {
                if(this.sortOrder.column == column) {
                    this.sortOrder.direction = this.sortOrder.direction == 0 ? -1 : 0;
                }
                else {
                    this.sortOrder.direction = 0;
                }
                this.sortOrder.column = column;
                //se api-url nao for undefined
                if(this.apiUrl != undefined) {
                    this.loadData();
                }
            },
            loadData: function () {

                this.onLoadData(true);

                var params = [
                        this.queryParams.sort + "=" + this.sortOrder.column,
                        this.queryParams.order + "=" + this.sortDirection
                ];

                var url = this.apiUrl + "?" + params.join("&");

                var self = this;
                self.$http.get(url)
                        .then(function (response) {
                            self.dispatchEvent("ui-table:load.success", response.data);
                            self.dataSource = self.getObjectValue(response.data, 'data');
                        }, function (response) {
                            self.dispatchEvent("ui-table:load.error", response.data);
                        });

                this.onLoadData(false);
            },
            onLoadData: function (isloading) {
                this.loading = isloading;
                if(isloading) {
                    this.$dispatch("ui-table:loading");
                }
                else {
                    this.$dispatch("ui-table:loaded");
                }
            },
            getObjectValue: function(object, path, defaultValue) {
                defaultValue = (typeof defaultValue == 'undefined') ? null : defaultValue;
                var obj = object;
                if (path.trim() != '') {
                    var keys = path.split('.');
                    keys.forEach(function(key) {
                        if (typeof obj[key] != 'undefined' && obj[key] !== null) {
                            obj = obj[key];
                        } else {
                            obj = defaultValue;
                        }
                    })
                }
                return obj;
            }
        },

        ready: function () {

        },

        created: function () {
            if(this.apiUrl != undefined) {
                this.loadData();
            }
        }
    }
</script>

<template src="./templates/ui-table.template"></template>

<style src="./styles/ui-table.style" scoped></style>