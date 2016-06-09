<template src="./templates/viu-modal.template"></template>
<style src="./styles/viu-modal.style" scoped></style>

<script>

    export default{
        data(){
            return{
                isVisible: false
            }
        },
        computed:{
            isHeaderVisible: function () {
                return this.headerVisible.toLowerCase() == 'true'
            }
        },

        props: {
            name: {
                type: String,
                required: true
            },
            title: {
                type: String,
                default: 'ViuModal'
            },
            width: String,
            height: String,
            headerVisible: {
                type: String,
                default: 'true'
            },
            footerVisible: {
                type: String,
                default: 'false'
            }
        },

        methods: {
            open: function () {
                this.isVisible = true
            },

            close: function() {
                this.isVisible = false
            }
        },

        events: {
            "viu-modal:open" : function (obj) {
                if(obj.modal == this.name) {
                    this.open()
                }
            }
        }
    }
</script>