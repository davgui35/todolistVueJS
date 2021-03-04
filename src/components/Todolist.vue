<template>
    <div class="container">
        <div class="item">
            <div class="icon" @click="addItem">
                <i class="fas fa-plus"></i>
            </div>
            <form @submit.prevent="addItem">
                <input type="text" placeholder="Ajoute ta tâche..." v-model="entry">
            </form>
            <div class="favorite-icon" :class="{'red-bg': favorite}">
                <i v-if="!favorite" class="far fa-star" @click="favorite = !favorite" ></i>
                <i v-else class="fas fa-star" @click="favorite = !favorite"></i>
            </div>
        </div>
        <div class="todo">
            <div class="item" :class="{'show':item.show}" v-for="(item, index) in todos" :key="index">
                <div class="check-item">
                    <i v-if="!item.complete" class="fas fa-square" @click="completeItem(item)"></i>
                    <i v-else class="fas fa-check-square"></i>
                </div>
                <div class="title">
                    {{ item.title}}
                </div>
                <div class="favorite-icon" :class="{'red-bg': item.favorite}">
                    <i v-if="!item.favorite" class="far fa-star" @click="setFavorite(item)" ></i>
                    <i v-else class="fas fa-star" @click="item.favorite = !item.favorite"></i>
                </div>
            </div>
        </div>
        <div class="show-completed" v-if=" completedTodos.length > 0">
            <div class="button" @click="showCompletedList = !showCompletedList">
                <span v-if="!showCompletedList">Voir</span><span v-else>Cacher</span> la liste des tâches
            </div>
        </div>
        <div class="todo complete-list" v-if="showCompletedList">
            <div class="item" :class="{'show':item.show}" v-for="(item, index) in completedTodos" :key="index">
                <div class="check-item">
                    <i v-if="!item.complete" class="fas fa-square"></i>
                    <i v-else class="fas fa-check-square" @click="unCompleteItem(item)"></i>
                </div>
                <div class="title">
                    {{ item.title}}
                </div>
                <div class="favorite-icon" :class="{'red-bg': item.favorite}">
                    <i v-if="!item.favorite" class="far fa-star"></i>
                    <i v-else class="fas fa-star"></i>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data: () =>{
        return {
            entry: '',
            favorite: false,
            todos:[],
            completedTodos:[],
            showCompletedList:false
        }
    },
    methods: {
        addItem(){
            if(this.entry !== ''){
                let date = new Date;
                let newEntry = {
                    id: date.getTime(),
                    title: this.entry,
                    favorite: this.favorite,
                    complete: false,
                    show: false
                }
                if(newEntry.favorite){
                    this.todos.splice(0, 0, newEntry);
                }else{
                    this.todos.push(newEntry);
                }

                setTimeout(() => {
                    this.todos.find(element => element.id === newEntry.id).show = true;
                }, 10);
            }

            this.entry = '';
            this.favorite = false;
        }, 

        setFavorite(item) {
            item.favorite = !item.favorite;
            item.show = false;
            setTimeout(() =>{
                let index = this.todos.findIndex(element => element.id === item.id);
                this.todos.splice(index,1);
                this.todos.splice(0, 0, item);
                item.show = true;
            }, 500);
        }, 
        completeItem(item){
            item.complete = !item.complete;
            item.show =false;

            setTimeout(() =>{
                this.completedTodos.push(item);
                let index = this.todos.findIndex(element => element.id === item.id);
                this.todos.splice(index,1);
                item.show = true;
            }, 500);
        },
        unCompleteItem(item){
            item.complete = !item.complete;
            item.show =false;
            setTimeout(() => {
                if(item.favorite){
                    this.todos.splice(0.0, item);
                }else{
                    this.todos.push(item);
                }

                let index = this.completedTodos.findIndex(element => element.id === item.id);
                this.completedTodos.splice(index,1);
                item.show = true;
            }, 500);
        }

    }
}
</script>

<style lang="scss">
    .container {
        padding: 10px;
        width: calc(100% - 20%);
        min-height: calc(100% - 20px);
        margin: 0 auto;

        @mixin favorite-icon($color)
        {
            grid-column-start: 3;
            grid-column-end: 3;
            display: flex;
            justify-content: center;
            align-items: center;
            color: $color;
            font-size: 2rem;
            cursor: pointer;
        }

        .item {
            display: grid;
            grid-template-columns: 70px auto 70px;
            grid-template-rows: 60px;
            width: 100%;
            height: 70px;
            background:#F1F1F1;
            border-radius: 10px;
            margin: 5px;

            .icon{
                grid-column-start: 1;
                grid-column-end: 1;
                display: flex;
                justify-content: center;
                align-items: center;
                color: #333;
                font-size: 2rem;
                cursor: pointer;
            }

            input{
                grid-column-start: 2;
                grid-column-end: 2;
                border: none;
                outline: none;
                width: 100%;
                height: 70px;
                text-align: center;
                background: none;
                font-size: 1.6rem;

                &::placeholder{
                    color: rgb(212, 209, 209);
                }

                &::focus{
                    outline: none;
                }
            }

            .favorite-icon{
                @include favorite-icon(#fff);
            }
        }
        .todo{
            padding-top: 20px;
            .item{
            display: grid;
            grid-template-columns: 70px auto 70px;
            grid-template-rows: 60px;
            width: 100%;
            height: 70px;
            background:#F1F1F1;
            border-radius: 10px;
            margin: 5px;
            overflow: hidden;
            opacity: 0;
            transition: all .4s  linear;
            }

            .check-item{
                grid-column-start: 1;
                grid-column-end:1 ;
                display: flex;
                justify-content: center;
                align-items: center;
                font-size: 1.6rem;
            }

            .title{
                grid-column-start: 2;
                grid-column-end:2 ;
                display: flex;
                justify-content: center;
                align-items: center;
                font-size: 1.6rem;
            }

            .favorite-icon{
                @include favorite-icon(#333);
            }
        .show{
            opacity: 1;
        }
    }

    .complete-list{
        .item{
            position: relative;
            background: #F1F1F1;

            &::before{
                content: '';
                position: absolute;
                top: 30px;
                width: calc(100% - 120px);
                margin:0 60px;
                border-bottom: 2px solid rgb(122, 122, 122);
            }
        }
    }

    .show-completed {
        display: flex;
        justify-content: center;
        align-items: center;
        padding-top: 20px;

        .button {
            color: #fff;
            padding: 10px;
            font-size: 1.3rem;
            text-transform: uppercase;
            background: rgb(114, 112, 112);
            border-radius: 5px;
            overflow: hidden;
            cursor: pointer;
        }
    }
        .red-bg{
            background: crimson;
            color: #F1F1F1!important;
            height: 115%;
            border-top-right-radius: 15px;
            border-bottom-right-radius: 15px;
        }
    }
</style>