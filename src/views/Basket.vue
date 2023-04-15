<template>
    <div class="container">
        <p v-if="!pursharedCocktails.length" class="null__basket">Корзина пуста!</p>
        <div class="wrapper__basket">
            <div class="card" v-for="(cocktail, index) in pursharedCocktails" :key="cocktail.id">
                <img :src="cocktail.image" class="card__img">
                <div class="card__text">
                    <p class="card__title">{{ cocktail.title }}</p>
                    <div class="ingridients__wrapper">
                        <p><b>Состав: </b></p>
                        <div v-for="ingridient in cocktail.ingridients" :key="ingridient">
                            <p class="ingridient">{{ ingridient }}</p>
                        </div>
                    </div>
                </div>
                <div class="buttons">
                    <button @click="deleteCocktail(index)">-</button>
                    <p>{{ cocktail.count }}</p>
                    <button @click="addCocktail(index)">+</button>
                </div>
            </div>
        </div>
        <div class="price">
            <p>Итого: {{ endPrice }} руб.</p>
        </div>
    </div>
</template>

<script>
export default {
    data(){
        return{
            pursharedCocktails: [],
            cocktails: []
        }
    },
    mounted(){
        if(localStorage.pursharedCocktails){
            this.pursharedCocktails = JSON.parse(localStorage.pursharedCocktails)
        }
        if(localStorage.cocktails){
            this.cocktails = JSON.parse(localStorage.cocktails)
        }
    },
    methods: {
        addCocktail(index){
            this.pursharedCocktails[index].count++
            localStorage.cocktails
            localStorage.pursharedCocktails = JSON.stringify(this.pursharedCocktails)
            this.$emit('addCocktail')
        },
        deleteCocktail(index){
            this.pursharedCocktails[index].count--
            if(this.pursharedCocktails[index].count == 0){
                this.pursharedCocktails.splice(index, 1)
            }
            localStorage.pursharedCocktails = JSON.stringify(this.pursharedCocktails)
            this.$emit('deleteCocktail')
        }
    },
    computed: {
        endPrice(){
            let accum = 0
            this.pursharedCocktails.forEach(el => {
                accum = accum + el.price * el.count
            })
            return accum
        }
    },
}
</script>

<style scoped>
    .container{
        display: flex;
        flex-direction: column;
        align-items: center;
    }
    .null__basket{
        color: white;
        font-size: 30px;
        font-weight: 700;
    }
    .card{
        padding: 20px;
        background: white;
        border-radius: 15px;
        display: flex;
        width: 700px;
        margin-bottom: 20px;
    }
    .card__img{
        width: 100px;
        height: 150px;
        margin-right: 50px;
    }
    .card__title{
        font-size: 20px;
        font-weight: 700;
        margin-bottom: 20px;
    }
    .ingridients__wrapper{
        display: flex;
        max-width: 300px;
        flex-flow: row wrap;
        gap: 10px;
        margin-right: 100px;
    }
    .buttons{
        display: flex;
        align-items: center;
        gap: 10px;
    }
    button{
        border: none;
        padding: 5px 10px;
        border-radius: 5px;
        margin: 10px 0;
        background-color: rgba(99, 80, 197);
        box-shadow: 3px 3px 5px rgb(72, 54, 158);
        color: white;
        font-weight: 600;
    }
    .price{
        padding: 20px 30px;
        background: rgba(212, 0, 255, 1);
        border-radius: 15px 15px 0 0;
        position:fixed;
        bottom: 0;
        right: 50px;
    }
    .price p{
        color: white;
        font-size: 20px;
    }
</style>