<template>
    <div id="app">
        <div>
            <ion-button
                    style="font-weight: bolder;"
                    type="submit"
                    @click="setView('todo')"
            >ToDo List</ion-button>
            <ion-button
                    style="font-weight: bolder;"
                    type="submit"
                    @click="setView('weather')"
            >Weather</ion-button>
        </div>
        <Weather v-if="view === 'weather'"></Weather>
        <ToDoList v-if="view === 'todo'"></ToDoList>
    </div>
</template>

<script>
    import Weather from "./components/Weather.vue";
    import ToDoList from "./components/ToDoList.vue";

    export default {
        name: "app",
        data() {
            return {
                view: "todo"
            };
        },
        components: {
            Weather,
            ToDoList
        },
        methods: {
            setView(view) {
                this.view = view;
            }
        }
    };
</script>

<style lang="scss">
    @import url('https://use.fontawesome.com/releases/v5.7.2/css/all.css');

    #app {
        font-family: "Avenir", Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
    }
</style>
