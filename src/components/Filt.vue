<template>
    <div>
        <div class='filter__wrapper'>
            <p class="desc">Цена: </p>
            <vue-slider v-model="value" :enable-cross="false" :tooltip="always" :tooltip-placement="top" :min="min" :max="max" @change="filterPrice"/>
            <p class="desc">Отсортировать: </p>
            <div class="content">
                <button @click="filteredName()">По названию</button>
                <button @click="filteredMin()">По min цене</button>
                <button @click="filteredMax()">По max цене</button>
                <button @click="filteredPop()">По популярности</button>
                <button @click="filteredDefault()">Сброс</button>
            </div>
            <div class="filt_ing">
                <p class="filtm">Исключить ингридиент: </p>
                <select v-model="selected_ingridient" @change="removeIngridient">
                    <option v-for="ingridient in ingridients_list" :key="ingridient">
                        {{ ingridient }}
                    </option>
                </select>
                <div class="removed_ingridients">
                    <div v-for="ingridient in removedIngridients" :key="ingridient" class="removed_ingridient" @click="deleteRemove(ingridient)">
                        <p>{{ ingridient }}</p>
                        <img src="../assets/icons/close.png" alt="">
                    </div>
                </div>
                <p class="filtm">Выбрать крепкость: </p>
                <select v-model="selected_deg" @change="chooseDeg">
                    <option v-for="deg in deg_list" :key="deg">
                        {{ deg }}
                    </option>
                </select>
                <p class="filtm">Выбрать основу: </p>
                <select v-model="selected_base" @change="choseBase">
                    <option v-for="base in base_list" :key="base">
                        {{ base }}
                    </option>
                </select>
                <p class="filtm">Выбрать вкус: </p>
                <select v-model="selected_taste" @change="choseTaste">
                    <option v-for="taste in taste_list" :key="taste">
                        {{ taste }}
                    </option>
                </select>
            </div>
        </div>
    </div>
</template>

<script>
import VueSlider from 'vue-slider-component'
import 'vue-slider-component/theme/default.css'
export default {
    components: {
        VueSlider
    },
    data() {
        return {
            selected_ingridient: 'Нет',
            selected_deg: 'Все',
            selected_base: 'Все',
            selected_taste: 'Все',
            value: [0, 500],
            min: 0,
            max: 500
        }
    },
    emits: ['getIngridient', 'getDeg', 'getBase', 'getTaste'],
    props: {
        cocktails: {
            id: Number,
            title: String,
            deg: String,
            base: String,
            taste: String,
            ingridients: Array,
            price: Number,
            image: String
        },
        ingridients_list: Array,
        removedIngridients: Array,
        deg_list: Array,
        taste_list: Array,
        base_list: Array
    },
    mounted() {
        if(localStorage.selected_deg){
            this.selected_deg = localStorage.selected_deg
        }
        if(localStorage.selected_base){
            this.selected_base = localStorage.selected_base
        }
        if(localStorage.selected_taste){
            this.selected_taste = localStorage.selected_taste
        }
    },
    methods: {
        filteredName() {
            return this.cocktails.sort((a, b) => a.title.localeCompare(b.title))
        },
        filteredMin() {
            return this.cocktails.sort((a, b) => a.price - b.price)
        },
        filteredMax() {
            return this.cocktails.sort((a, b) => b.price - a.price)
        },
        filteredPop() {
            return this.cocktails.sort((a, b) => b.pop - a.pop)
        },
        filteredDefault() {
            return this.cocktails.sort((a, b) => a.id - b.id)
        },
        filterPrice() {
            this.$emit('getPrice', {
                value: this.value
            })
        },
        removeIngridient(){
            this.$emit('getIngridient', {
                ingridient: this.selected_ingridient
            })
        },
        chooseDeg(){
            localStorage.selected_deg = this.selected_deg
            this.$emit('getDeg', {
                deg: this.selected_deg
            })
        },
        choseBase(){
            localStorage.selected_base = this.selected_base
            this.$emit('getBase', {
                base: this.selected_base
            })
        },
        choseTaste(){
            localStorage.selected_taste = this.selected_taste
            this.$emit('getTaste', {
                taste: this.selected_taste
            })
        },
        deleteRemove(ingridient){
            let index = this.removedIngridients.indexOf(ingridient)
            this.removedIngridients.splice(index, 1)
            localStorage.removedIngridients = JSON.stringify(this.removedIngridients)
        }
    },
    computed: {
        
    }
}
</script>

<style scoped>
.filter__wrapper {
    width: 200px;
    background-color: white;
    border: 1px solid rgba(0, 0, 0, 0.4);
    border-radius: 10px;
    padding: 10px;
    box-shadow: 5px 5px 7px rgba(212, 0, 255, 1);

}

.content {
    display: flex;
    flex-direction: column;
    align-items: self-start;
}

button {
    border: none;
    padding: 5px;
    border-radius: 5px;
    margin: 10px 0;
    background-color: rgba(99, 80, 197);
    box-shadow: 3px 3px 5px rgba(212, 0, 255, 0.5);
    color: white;
    font-weight: 600;
}
button:hover{
    cursor: pointer;
    box-shadow: 3px 3px 5px rgba(212, 0, 255, 1);
}

.filtm {
    padding: 10px 0;
    font-weight: 700;
}

select {
    width: 170px;
}

.removed_ingridients{
    display: flex;
    flex-flow: row wrap;
    gap: 5px;
    margin-top: 10px;
}

.removed_ingridient{
    display: flex;
    justify-content: space-between;
    align-items: center;
    gap: 5px;
    padding: 3px 5px;
    background-color: darkgrey;
    font-size: 10px;
    border-radius: 5px;
}
img{
    width: 5px;
    height: 5px;
}
.desc{
    font-weight: 700;
}
select{
    border-radius: 5px;
}
</style>