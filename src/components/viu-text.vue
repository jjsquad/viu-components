<script>

    export default {

        props : {
            label: String,
            size: String,
            name: {
                required: true,
                type: String
            },
            inline: String,
            errorMessage: String,
            text: String,
            mask: String,
            placeholder: String,
            maxlength: String,
            disabled: String
        },

        computed: {
            colsize () {
                return 'col-md-' + this.size
            }
        },

        watch: {
            text (value) {
                this.$dispatch('changed', {
                    name: this.name,
                    text: value
                })
            }
        },

        data() {
            return {

            }
        },

        methods: {
            capitalize: function (string) {
                return string.charAt(0).toUpperCase() + string.slice(1);
            }
        },

        ready () {
            if(!this.label) {
                this.placeholder = this.capitalize(this.name);
            }
        }
    }

</script>

<style scoped></style>

<template src="./templates/viu-text.template"></template>
