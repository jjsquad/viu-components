<template>

    <div class="col-md-{{ size }}">
        <div class="form-group">
            <div v-if="inputBox">
                <label v-if="label" for="{{ name }}" class="input-control">{{ label }}</label>
                <div class="input-group ui-ms-select">
                    <input v-el="input" type="text" class="form-control" v-model="option">
                    <span class="input-group-btn">
                        <button v-on="click: addOption, keyup: addOption | key 13" class="btn btn-primary">
                            <i class="glyphicon glyphicon-plus"></i>
                        </button>
                        <button v-if="multiple" v-class="disabled: !selected.length" v-on="click: removeEntry" class="btn btn-danger">
                            <i class="glyphicon glyphicon-remove"></i>
                        </button>
                    </span>
                </div>
            </div>
            <select v-model="selected" options="options" class="form-control"
                    name="{{ name }}"
                    id="{{ name }}"
                    v-attr="multiple: multiple, size: visibleRows"
            >
                <option v-show="placeholder != '' && !multiple" value="">{{ placeholder }}</option>
            </select>
        </div>
    </div>
</template>

<style scoped>
    .ui-ms-select {
        margin-bottom: 4px;
    }
</style>

<script>

    export default{

        /**
         * Propriedades:
         * 
         * name: string
         * label: string
         * size: number
         * visibleRows: number
         * dataSource: json
         * dataSourceUrl: String
         * dataSourceValueKey: string
         * dataSoruceTextKey: string
         * items: string (to Array)
         * inputBox: boolean
         * multiple: boolean
         * placeholder: string
         * dataString: boolean
         * onSelected: function
         * onChanged: function
         */
        props: {
            name: {
                type: String,
                required: true
            },
            label: String,
            size: {
                type: Number,
                default: 2
            },
            visibleRows: Number,
            dataSource: {},
            dataSourceUrl: String,
            dataSourceValueKey: String,
            dataSourceTextKey: String,
            items: {},
            inputBox: {
                type: Boolean,
                default: false
            },
            multiple: {
                type: Boolean,
                default: false
            },
            placeholder: {
                type: String,
                default: ''
            },
            dataString: Boolean,
            onChange: Function,
            onSelected: Function          
        },

        data(){
            return{
                option: null,
                options: [],
                selected: []
            }
        },

        watch: {
            /**
             * @param value
             */
            dataSource: function (value) {
                this.processData(value);
            },

            dataSourceUrl: function (value) {
                this.processUrl(value);
            },

            /**
             * options
             * Quando o valor de options muda dispara o evento ui.multiselect.changed
             * @param value
             */
            options: function (value) {
                this.onChange ? this.onChange(this.name, JSON.stringify(value)) : null;
                this.$dispatch('ui.select:changed', this.name, JSON.stringify(value));
            },

            /**
             *
             * @param value
             */
            selected: function (value) {
                if(value != '') {
                    var obj = _.find(this.options, ['value', value[0]]);
                    var data = this.dataString ? JSON.stringify(obj) : obj;
                    this.onSelected ? this.onSelected(this.name, data) : null;
                    this.$dispatch('ui.select:changed', this.name , data);
                }
            }
        },

        methods: {

            addOption: function (e) {
                e.preventDefault();
                if(this.option != '' && this.option != null) {
                    try {
                        var obj = JSON.parse(this.option);
                        if(typeof obj == 'object') {
                            this.options.push(obj);
                        }
                    }
                    catch (e)
                    {
                        this.options.push({
                            "value": this.option,
                            "text": this.option
                        });
                    }
                }
                this.$set('option', null);
                this.$$.input.focus();
            },

            removeEntry: function () {

                var self = this;

                self.selected.map(function (option) {
                    self.options.$remove(_.find(self.options, ['value', option]));
                });
                self.$set('selected', []);
            },

            processUrl: function (url) {
                var self = this;

                self.$http.get(url, function (response) {
                    self.processData(response);
                }).error(function (response) {
                    self.$dispatch('ui-select:ds.error', self, response);
                });
            },
            processData: function (data) {
                var self = this;
                data.map(function (item) {
                    var keys = Object.keys(item);
                    self.options.push({
                        "value" : keys[0] ? item[keys[0]] : item,
                        "text" : keys[1] ? item[keys[1]] : item
                    });
                });
            }
        },

        ready: function () {

            var self = this;
            if(self.items) {
                self.items.split(',').map(function (item) {
                    self.options.push({
                        "value": item,
                        "text": item
                    });
                });
            }

            this.dataSourceUrl ? this.processUrl(this.dataSourceUrl) : null;
        }
    }

</script>