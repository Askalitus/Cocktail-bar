<template>
  <div class="nav">
    <div class="logo">
      <img src="./assets/icons/cocktail.png" class="logo__img">
      <p class="logo__text">Глык</p>
    </div>
    <div class="links">
      <router-link to="/">Коктейли</router-link>
    <div class="basket">
      <router-link to="/basket">Корзина</router-link>
      <p>{{ basketCount }}</p>
    </div>
    </div>
  </div>
  <router-view @plusCount="plusCount" @addCocktail="plusCount" @deleteCocktail="deleteCocktail"/>
</template>

<script>
  export default{
    data(){
      return{
        basketCount: 0
      }
    },
    mounted(){
      if (localStorage.basketCount){
        this.basketCount = localStorage.basketCount
      }
    },
    methods: {
      plusCount(){
        this.basketCount++
        localStorage.basketCount = this.basketCount
      },
      deleteCocktail(){
        this.basketCount--
        localStorage.basketCount = this.basketCount
      }
    },
  }
</script>

<style>
  *{
    margin: 0;
    padding: 0;
    scroll-behavior: smooth;
    font-family: 'Golos Text', sans-serif;
  }
  body{
    background-color:rgb(60, 42, 150);
  }
  .nav{
    padding: 20px 0;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
  .logo{
    display: flex;
    align-items: center;
  }
  .logo__img{
    width: 50px;
    height: 50px;
  }
  .logo__text{
    color: white;
    font-size: 24px;
  }
  .links{
    display: flex;
    align-items: center;
    gap: 15px;
  }
  .nav a {
  color: white;
  text-decoration: none;
  }
  .nav a:hover{
    cursor: pointer;
    text-shadow: 1px 1px 5px rgba(212, 0, 255, 1);
  }
  .nav a.router-link-exact-active{
    text-decoration: underline;
  }
  .basket{
    display: flex;
    gap: 3px;
  }
  .basket p{
    background: rgba(212, 0, 255, 1);
    padding: 0 6px;
    border-radius: 100%;
    color: white;
  }
</style>