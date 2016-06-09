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

<template>
    <div class="{{ colsize }}">
        <div class="form-group"
             :class="{ 'form-inline': inline && inline.toLowerCase() == 'true'},
                 { 'has-error': errorMessage && errorMessage.length > 0 }"
        >
            <label v-if="label" for="{{ name }}" class="input-control">{{ label }}</label>
            <input type="text"
                   placeholder="{{ placeholder }}"
                   class="form-control"
                   name="{{ name }}"
                   v-model="text"
                   data-mask="{{ mask }}"
                   maxlength="{{ maxlengt }}"
                   :readonly="disabled && disabled.toLowerCase() == 'true'"
            />
            <span v-if="errorMessage && errorMessage.length > 0" class="text-danger">{{ errorMessage }}</span>
        </div>
    </div>
</template>