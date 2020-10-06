<template>
    <div class="container">
        <input v-model="input" @keydown="pressEnter" :placeholder="inputText"><button @click="addListItem">Add</button>
        <ul class="list" v-if="items.length">
            <li class="item" v-for="item in items" :class="{done: item.isDone}" :key="item.id">
                <span>{{ item.text }}</span>
                <div>
                    <button @click="doneItem(item)">Done</button>
                    <button @click="deleteItem(item.id)">Delete</button>
                </div>
            </li>
        </ul>
        <div class="result">{{ doingNum }} / {{ items.length }}</div>
    </div>
</template>

<script>
import { onMounted, reactive, ref, computed } from 'vue';
export default {
    name: 'Sample',
    props: {
        inputText: String
    },
    setup() {
        let input = ref('');
        let items = reactive([]);
        let id = ref(0);
        const addListItem = () => {
            if (input.value !== '') {
                id.value++;
                const item = {
                    id: id.value,
                    text: input.value,
                    isDone: false
                }
                items.push(item)
                localStorage.setItem('taskList', JSON.stringify(items));
                localStorage.setItem('lastID', JSON.stringify(item.id));
                input.value = '';
            }
        }
        const pressEnter = e => {
            if (e.keyCode === 13) {
                addListItem();
            }
        }
        const setListItem = () => {
            const tasks = JSON.parse(localStorage.getItem('taskList'));
            if (tasks != null && tasks.length > 0) {
                id.value = JSON.parse(localStorage.getItem('lastID'));
                tasks.forEach(task => {
                    items.push(task);
                });
            }
        }
        const deleteItem = id => {
            if (confirm('Run delete OK?')) {
                const tasks = items.filter(item => item.id !== id);
                items.length = 0;
                tasks.forEach(task => {
                    items.push(task);
                });
                localStorage.setItem('taskList', JSON.stringify(items));
            }
        }
        const doneItem = item => {
            if (!item.isDone) {
                item.isDone = true;
            } else {
                item.isDone = false;
            }
            localStorage.setItem('taskList', JSON.stringify(items));
        }
        const doingNum = computed(() => {
            return items.filter(item => item.isDone === false).length;
        });
        onMounted(setListItem);
        return {
            input,
            items,
            addListItem,
            pressEnter,
            deleteItem,
            doneItem,
            doingNum
        }
    }
    // data() {
    //     return {
    //         input: '',
    //         items: [],
    //         id: 0,
    //     }
    // },
    // computed: {
    //     doingNum: function() {
    //         return this.items.filter(item => item.isDone === false).length;
    //     }
    // },
    // methods: {
    //     addListItem() {
    //         this.id++;
    //         const item = {
    //             id: this.id,
    //             text: this.input,
    //             isDone: false,
    //         }
    //         this.items.push(item);
    //         localStorage.setItem('taskList', JSON.stringify(this.items));
    //         localStorage.setItem('lastID', JSON.stringify(item.id));
    //         this.input = '';
    //     },
    //     pressEnter(e) {
    //         if (e.key === 'Enter') {
    //             this.addListItem();
    //         }
    //     },
    //     doneItem(item) {
    //         if (!item.isDone) {
    //             item.isDone = true;
    //         } else {
    //             item.isDone = false;
    //         }
    //         localStorage.setItem('taskList', JSON.stringify(this.items));
    //     },
    //     deleteItem(id) {
    //         if (confirm('Run delete OK?')) {
    //             const tasks = this.items.filter(item => item.id !== id);
    //             this.items.length = 0;
    //             tasks.forEach(task => {
    //                 this.items.push(task);
    //             });
    //             localStorage.setItem('taskList', JSON.stringify(this.items));
    //         }
    //     }
    // },
    // mounted: function() {
    //         const tasks = JSON.parse(localStorage.getItem('taskList'));
    //         if (tasks != null && tasks.length > 0) {
    //             this.id = JSON.parse(localStorage.getItem('lastID'));
    //             tasks.forEach(task => {
    //                 this.items.push(task);
    //             });
    //         }
    //     }
}
</script>

<style scoped>
    .container {
        max-width: 600px;
        margin: auto;
    }
    .list {
        padding-left: 0;
    }
    .item {
        text-align: left;
        display: flex;
        justify-content: space-between;
        padding: 10px 0;
    }
    .done > span {
        text-decoration: line-through;
    }
    .result {
        text-align: right;
    }
</style>