<template>
    <div v-for="(cocktail, index) in cocktails_list" :key="cocktail.id">
        <div class="card">
            <img :src="cocktail.image" class="cocktail__img">
            <h1>{{cocktail.title}}</h1>
            <p class="desc">Состав:</p>
            <div v-for="ingridient in cocktail.ingridients" :key="ingridient">
                <p class="ingridient">{{ ingridient }}</p>
            </div>
            <p class="price">{{cocktail.price}} Руб.</p>
            <div class="rating">
                <p><b>{{ cocktail.pop }}</b></p>
                <img src="../assets/icons/star.png" class="star">
            </div>
            <button @click="buyCocktail(cocktail, index)">В корзину</button>
        </div>
    </div>
</template>

<script>

    export default {
        data(){
            return{
                pursharedCocktails: []
            }
        },
        mounted() {
            if(localStorage.pursharedCocktails){
                this.pursharedCocktails = JSON.parse(localStorage.pursharedCocktails)
            }
    },
        emits:['buyCocktail'],
    props: {
        cocktails_list: {
            id: Number,
            title: String,
            deg: String,
            base: String,
            taste: Array,
            ingridients: Array,
            price: Number,
            image: String
        },
        cocktails: {
            id: Number,
            title: String,
            deg: String,
            base: String,
            taste: Array,
            ingridients: Array,
            price: Number,
            image: String
        }
    },
    computed: {
        
    },
    methods: {
        buyCocktail(cocktail){
            if(this.pursharedCocktails.length){
                let isPurshared = false
                this.pursharedCocktails.map((cocktails) => {
                    if (cocktail.id == cocktails.id){
                        isPurshared = true
                        cocktails.count++
                    }
                })
                if(!isPurshared){
                    this.pursharedCocktails.push(cocktail)
                    this.pursharedCocktails[this.pursharedCocktails.length - 1].count = 1
                }
            }else{
                this.pursharedCocktails.push(cocktail)
                this.pursharedCocktails[this.pursharedCocktails.length - 1].count = 1
            }
            localStorage.pursharedCocktails = JSON.stringify(this.pursharedCocktails)
            this.$emit('buyCocktail')
        }
    },
}
</script>

<style scoped>


    .card{
        display: flex;
        flex-flow: column nowrap;
        padding: 20px;
        background-color: white;
        border-radius: 15px;
        width: 200px;
        transition: all ease-in-out 0.3s;
        position: relative;
        box-shadow: 5px 5px 7px rgba(212, 0, 255, 1);
    }

    .card:hover{
        transform: scale(1.03);
        box-shadow: 5px 5px 10px rgba(212, 0, 255, 0.6);
    }
    h1{
        font-size: 20px;
        font-weight: 700;
        margin: 20px 0;

    }
    .cocktail__img{
        width: 100px;
        height: 170px;
        margin: 10px auto;
    }

    .rating{
        display: flex;
        align-items: center;
        gap: 5px;
    }

    .star{
        width: 10px;
        height: 10px;
    }
    .desc{
        font-weight: 700;
    }
    .ingridient{
        margin-top: 2px;
    }
    .price{
        font-size: 22px;
        font-weight: 700;
        margin: 10px 0;
        color: rgba(48, 0, 86, 1);
    }
    button {
    border: none;
    padding: 5px;
    border-radius: 5px;
    margin: 10px 0;
    background-color: rgba(99, 80, 197);
    box-shadow: 3px 3px 5px rgb(72, 54, 158);
    color: white;
    font-weight: 600;
    }
    button:active{
        box-shadow: inset 0px 0px 0 2px rgba(212, 0, 255, 0.5);
    }

</style>