<template>
    <div id="app">
        <h1>{{ msg }}</h1>
        <viu-text size="6"
                  name="text"
                  @changed="textChanged"
                  :text.sync="msg"
        >
        </viu-text>
        <viu-text size="6" name="Text2"></viu-text>
    </div>
</template>

<script>

    import ViuText from './components/viu-text.vue'

    export default {
        components: {
            ViuText
        },

        data () {
            return {
              // note: changing this line won't causes changes
              // with hot-reload because the reloaded component
              // preserves its current state and we are modifying
              // its initial state.
              msg: 'Hello Vue!'
            }
        },

        methods: {
            textChanged (data) {
                window.console.log(JSON.stringify(data));

            }
        }
    }
</script>

<style>
body {
  font-family: Helvetica, sans-serif;
}
</style>
