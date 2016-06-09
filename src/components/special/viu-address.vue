<template src="./templates/viu-address.template"></template>
<style src="./styles/viu-address.style"></style>
<script>

    export default{
        props: {
            name: {
                type: String,
                required: true
            },
            size: {
                type: String,
                default: '12'
            },
            cep: {
                type: String,
                default: ''
            },
            endereco: {
                type: String,
                default: ''
            },
            numero: {
                type: String,
                default: ''
            },
            complemento: {
                type: String,
                default: ''
            },
            bairro: {
                type: String,
                default: ''
            },
            cidade: {
                type: String,
                default: ''
            },
            estado: {
                type: String,
                default: ''
            },
            pais: {},
            labels: {
                type: String,
                default: 'true'
            },
            cepNotFoundText: {
                type: String,
                default: 'CEP não encontrado.'
            },
            cepInvalidText: {
                type: String,
                default: 'Formato ou tamanho do CEP inválido.'
            }
        },

        data(){
            return{
                cepNotFound: false,
                cepInvalid: false
            }
        },

        computed: {
            labelsVisible () {
                return this.labels.toLowerCase() == "true"
            },
            sizeClass () {
                return `col-md-${this.size}`
            }
        },

        events: {
            "viu-address:fill.cep" (cep) {
                this.$set('cep', cep);
                this.onChange();
                this.buscaCep();
            }
        },

        methods: {
            onChange () {
                this.$dispatch("viu-address:change")
            },

            buscaCep (ev) {

                let self = this

                self.reset()

                if(ev.type =='blur' && this.cep.length < 9 && this.cep != '') {
                    self.$set('cepInvalid', true)
                    return
                }

                if(this.cep.length < 9){
                    return;
                }

                let provider = this.providerUrl.replace("%cep%", this.cep);

                self.$http.get(provider).then(function (response) {
                    if (response.data.erro) {
                        self.$set('cepNotFound', true)
                        self.$els.endereco.focus()
                        self.$dispatch("viu-address:cep.not.found")
                        return
                    }
                    //adiciona os campos ao cliente
                    self.$set('complemento', response.complemento)
                    self.$set('endereco', response.logradouro)
                    self.$set('bairro', response.bairro)
                    self.$set('localidade', response.localidade)
                    self.$set('uf', response.uf)

                    self.$els.numero.focus()
                    self.$dispatch("viu-address:cep.found")
                })

                self.reset()
            },

            reset () {
                this.$set('cepNotFound', false)
                this.$set('cepInvalid', false)
            }
        }
    }
</script>