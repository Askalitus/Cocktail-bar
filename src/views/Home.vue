<template>
  <div class="body">
    <div v-if="activePopup" :class="{ success__buy: true, success__buy__active: animationPopup}">
        <p>Коктейль успешно добавлен в корзину!</p>
    </div>
    <input type="text" class="search" v-model="searchText" placeholder="Введите название коктейля..." >
    <p v-if="searchH.length" class="find__cocktails">Найдено коктейлей: {{ searchH.length }}</p>
    <p v-else class="find__cocktails">Коктейли, с данными критериями, не найдены!</p>
    <div class="container">
      <div class = "filt">
        <Filt :cocktails = 'cocktails' :ingridients_list="ingridients_list" :searchH = "searchH" @getIngridient="getSelectedIngridients" :removedIngridients = 'removedIngridients' :deg_list="deg_list" @getDeg="getSelectedDeg" :taste_list="taste_list" @getTaste="getSelectedTaste" :base_list="base_list" @getBase="getSelectedBase" @getPrice="getSelectedPrice"/>
      </div>
      <div class="wrapper">
        <CocktailList :cocktails_list = "cocktails_list" :cocktails="cocktails"  @buyCocktail="buyCocktail"/>
      </div>
    </div>
    <div class="center" v-if="searchH.length">
      <p @click="showMore" v-if="!this.pageEnd" class="footer">Показать еще...</p>
      <p v-if="pageEnd" class="footer">Вы просмотрели все коктейли!</p>
      <p @click="goBack" v-if="this.pageEnd && (this.pagination_items_per_page >= this.searchH.length || Math.ceil(this.searchH.length / this.pagination_items_per_page != 1))" class="footer">Вернуться к началу</p>
      <Paginate
        v-if="searchH.length"
        v-model="page"
        :page-range="3"
        :margin-pages="2"
        :page-count="pagesCount"
        :click-handler="changePage"
        :prev-text="'Назад'"
        :next-text="'Вперед'"
        :container-class="'pagination'"
        :page-class="'page_item'"
        :active-class="'active'"
        :first-last-text="'buttons'"
      />
    </div>
  </div>
</template>

<script>
import CocktailList from '../components/CocktailList.vue';
import Filt from '../components/Filt.vue';

  export default {
    name: 'app',
    components: { CocktailList, Filt },
    props: { basketCount: Number },
    data() {
      return {
        cocktails: [
          {id: 1, title: "Маргарита", deg: 'Крепкое', taste: ['Кислый', 'Цитрусовый'], base: 'Текила', ingridients: ["Серебряная текила", "Трипл сек Fruko Schulz", "Сахарный сироп", "Лаймовый сок", "Лайм", "Соль", "Лед в кубиках"], price: 300, pop: 5, image: "src/assets/img/1.jpg"},

          {id: 2, title: "Негрони", deg: 'Крепкое', taste: ['Горький'], base: 'Джин', ingridients: ["Лондонский сухой джин", "Красный вермут", "Красный биттер", "Апельсиновая цедра", "Лед в кубиках"], price: 270, pop: 4.3, image: "src/assets/img/2.jpg"},

          {id: 3, title: "Мохито", deg: 'Слабоалкогольное', taste: ['Кислый', 'Мятный'], base: 'Ром', ingridients: ["Белый ром", "Сахарный сироп", "Содовая", "Лайм", "Мята", "Дробленый лед"], price: 250, pop: 4.7, image: "src/assets/img/3.jpg"},

          {id: 4, title: "Дайкири", deg: 'Слабоалкогольное', taste: ['Кислый', 'Цитрусовый'], base: 'Ром', ingridients: ["Белый ром", "Сахарный сироп", "Лаймовый сок", "Лед в кубиках"], price: 250, pop: 3.5, image: "src/assets/img/4.jpg"},

          {id: 5, title: "Джин тоник", deg: 'Крепкое', taste: ['Горький'], base: 'Джин', ingridients: ["Лондонский сухой джин", "Тоник", "Лайм", "Лед в кубиках"], price: 200, pop: 4, image: "src/assets/img/5.jpg"},

          {id: 6, title: "Апероль Шприц", deg: 'Крепкое', taste: ['Сладкий'], base: 'Игристое', ingridients: ["Апероль", "Просекко", "Содовая", "Апельсин", "Лед в кубиках"], price: 320, pop: 3.2, image: "src/assets/img/6.jpg"},

          {id: 7, title: "Эгейский негрони", deg: 'Крепкое', taste: ['Сухой', 'Травяной'], base: 'Джин', ingridients: ["Лондонский сухой джин", "Белый вермут", "Италикус", "Лаймовые листья", "Ледяная сфера", "Лед в кубиках"], price: 400, pop: 4.3, image: "src/assets/img/7.jpg"},

          {id: 8, title: "Салерс мартини", deg: 'Слабоалкогольное', taste: ['Ягодный', 'Сладкий', 'Пикантный'], base: 'Джин', ingridients: ["Лондонский сухой джин", "Ликер голубики De Kuyper", "Лимонный сок", "Сыр салерс", "Пюре голубики", "Китайский перец молотый", "Лед в кубиках"], price: 420, pop: 4.7, image: "src/assets/img/8.jpg"},

          {id: 9, title: "Секретный пунш", deg: 'Слабоалкогольное', taste: ['Тропический', 'Мятный', 'Фруктовый'], base: 'Ром', ingridients: ["Белый ром", "Абрикосовый ликер Fruko Schulz", "Медовый сироп", "Содовая", "Маракуйя", "Ананасовые листья", "Мята" ,"Лед в кубиках"], price: 390, pop: 5, image: "src/assets/img/9.jpg"},

          {id: 10, title: "Ежевичный сбитень", deg: 'Безалкогольное', taste: ['Ягодный', 'Пряный', 'Сладкий'], base: 'Сок', ingridients: ["Клюквенный сок", "Вода без газа", "Ежевика", "Мед", "Гвоздика", "Тростниковый сахарный песок", "Корица в палочках", "Лимонная цедра"], price: 490, pop: 4.1, image: "src/assets/img/10.jpg"},

          {id: 11, title: "Керман", deg: 'Крепкое', taste: ['Соленый'], base: 'Текила', ingridients: ["Серебряная текила", "Фисташковый сироп", "Лаймовый сок", "Розовая соль", "Лед в кубиках"], price: 240, pop: 5, image: "src/assets/img/11.jpg"}
      ],
      ingridients_list: ["Серебряная текила", "Трипл сек Fruko Schulz", "Сахарный сироп", "Лаймовый сок", "Лайм", "Соль", "Лед в кубиках", "Лондонский сухой джин", "Красный вермут", "Красный биттер", "Апельсиновая цедра", "Белый ром", "Содовая", "Мята", "Дробленый лед", "Тоник", "Апероль", "Просекко", "Апельсин", "Белый вермут", "Италикус", "Лаймовые листья", "Ледяная сфера", "Ликер голубики De Kuyper", "Лимонный сок", "Сыр салерс", "Пюре голубики", "Китайский перец молотый", "Абрикосовый ликер Fruko Schulz", "Медовый сироп", "Маракуйя", "Ананасовые листья", "Клюквенный сок", "Вода без газа", "Ежевика", "Мед", "Гвоздика", "Тростниковый сахарный песок", "Корица в палочках", "Лимонная цедра", "Фисташковый сироп", "Розовая соль"],
      deg_list: ['Все', 'Слабоалкогольное', 'Крепкое', 'Безалкогольное'],
      base_list: ['Все', 'Текила', 'Джин', 'Ром', 'Сок', 'Игристое'],
      taste_list: ['Все', 'Кислый', 'Горький', 'Цитрусовый', 'Мятный', 'Сладкий', 'Сухой', 'Травяной', 'Ягодный', 'Пикантный', 'Тропический', 'Фруктовый', 'Пряный', 'Соленый'],
      selected_deg: '',
      selected_base: '',
      selected_taste: '',
      searchText: '',
      removedIngridients: [],
      selected_price: [0, 500],

      page: 1,
      pagination_items_per_page: 5,
      pagination_offset: 0,
      pageEnd: false,

      activePopup: false,
      animationPopup: false,
      }
    },
    mounted(){
      if(localStorage.deg){
        this.selected_deg = localStorage.deg
      }
      if(localStorage.taste){
        this.selected_taste = localStorage.taste
      }
      if(localStorage.base){
        this.selected_base = localStorage.base
      }
      if(localStorage.removedIngridients){
        this.removedIngridients = JSON.parse(localStorage.removedIngridients)
      }
    },
    computed: {
      searchH() {
        return this.cocktails.filter((cocktail) => cocktail.title.toLowerCase().includes(this.searchText.toLowerCase()) &&
        !cocktail.ingridients.some(e => this.removedIngridients.includes(e)) &&
        cocktail.deg.includes(this.selected_deg) &&
        cocktail.base.includes(this.selected_base) &&
        cocktail.taste.some(e => e.includes(this.selected_taste)) &&
        cocktail.price >= this.minPrice &&
        cocktail.price <= this.maxPrice)
      },
      minPrice(){
        return this.selected_price[0]
      },
      maxPrice(){
        return this.selected_price[1]
      },
      pagesCount() {
        return Math.ceil(this.searchH.length / this.pagination_items_per_page)
      },
      cocktails_list(){
        return this.searchH.filter((cocktail, index) => index >= this.pagination_offset && index < (this.pagination_offset + this.pagination_items_per_page))
      },
    },
    methods: {
      getSelectedIngridients(data) {
        if (!this.removedIngridients.includes(data.ingridient)){
          this.removedIngridients.push(data.ingridient)
          localStorage.removedIngridients = JSON.stringify(this.removedIngridients)
          if(this.pagination_items_per_page >= this.searchH.length || this.page == Math.ceil(this.searchH.length / this.pagination_items_per_page)){
            this.pageEnd = true
          }
          return this.removedIngridients
        }
      },
      getSelectedDeg(data){
        if (data.deg == 'Все'){
          localStorage.deg = ''
          return this.selected_deg = ''
        }
        localStorage.deg = data.deg
        this.selected_deg = data.deg
        if(this.pagination_items_per_page >= this.searchH.length || this.page == Math.ceil(this.searchH.length / this.pagination_items_per_page)){
          this.pageEnd = true
        }
        return this.selected_deg
      },
      getSelectedTaste(data){
        if (data.taste == 'Все'){
          localStorage.taste = ''
          return this.selected_taste = ''
        }
        localStorage.taste = data.taste
        this.selected_taste = data.taste
        if(this.pagination_items_per_page >= this.searchH.length || this.page == Math.ceil(this.searchH.length / this.pagination_items_per_page)){
          this.pageEnd = true
        }
        return this.selected_taste
      },
      getSelectedBase(data){
        if (data.base == 'Все'){
          localStorage.base = ''
          return this.selected_base = ''
        }
        localStorage.base = data.base
        this.selected_base = data.base
        if(this.pagination_items_per_page >= this.searchH.length || this.page == Math.ceil(this.searchH.length / this.pagination_items_per_page)){
          this.pageEnd = true
        }
        return this.selected_base
      },
      getSelectedPrice(data){
        return this.selected_price = data.value
      },  
      changePage(page_num) {
        this.pagination_items_per_page = 5
        this.page = page_num
        this.pagination_offset = (this.pagination_items_per_page * page_num) - this.pagination_items_per_page

        this.cocktails_list = this.searchH.filter((item, index) => index >= this.pagination_offset && index < (this.pagination_offset + this.pagination_items_per_page))
        window.scroll(0,0)
        if(this.page == Math.ceil(this.searchH.length / this.pagination_items_per_page)){
          this.pageEnd = true
        }else{
          this.pageEnd = false
        }
      },
      showMore(){
        this.pagination_items_per_page += 5
        if(this.pagination_items_per_page >= this.searchH.length || this.page == Math.ceil(this.searchH.length / this.pagination_items_per_page)){
          this.pageEnd = true
        }
      },
      goBack(){
        this.pageEnd = false
        this.pagination_items_per_page = 5
        this.page = 1
        this.pagination_offset = 0

        this.cocktails_list = this.searchH.filter((item, index) => index >= this.pagination_offset && index < (this.pagination_offset + this.pagination_items_per_page))
        window.scroll(0,0)
      },
      buyCocktail(){
        this.activePopup = true
        let timer = setTimeout(()=> {
            this.animationPopup = true
        }, 1)
        let hidePopup = setTimeout(()=>{
            this.animationPopup = false
        }, 2000)
        let closePopup = setTimeout(() => {
            this.activePopup = false
        }, 3000);
        this.$emit('plusCount')
      }
    }
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
  .body{
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  .wrapper{
        display: flex;
        flex-flow: row wrap;
        gap: 35px;
        max-width: 1400px;
        margin-left: 150px;
    }

    .search {
      color: rgba(99, 80, 197, .5);
      box-shadow: 5px 5px 7px rgba(212, 0, 255, 1);
      width: 100%;
      height: 50px;
      padding: 5px 5px 5px 10px;
      font-size: 30px;
      border-radius: 20px;
      border: none;
      transition: all 0.2s ease-in-out;
    }
    .search:hover{
      box-shadow: 5px 5px 10px rgba(212, 0, 255, 0.6);
    }
    .search::placeholder{
      color: rgba(99, 80, 197, .5);
    }
    body{
      padding: 10px 100px;
    }
    .container{
      display: flex;
      margin-top: 20px;
    }

    .filt{
      margin-right: 50px;
      position: fixed;
      left: 100px;

    }
    .find__cocktails{
      font-size: 25px;
      margin-top: 20px;
      color: white;
    }
    .pagination {
    display: flex;
    align-items: center;
    list-style: none;
    color: white;
  }

  .page_item {
    border-radius: 7px;
    color: black;
    float: left;
    padding: 8px 16px;
    cursor: pointer;
    margin: 0 5px;
  }

  .page_item:hover {
    background-color: rgba(212, 0, 255, 1);
    color: white;
    transition: all .5s;
  }

  .active {
    background-color: rgba(212, 0, 255, 1);
    border-radius: 7px;
    color: white;
  }

  .page-link:hover {
    cursor: pointer;
  }

  .center {
    gap: 10px;
    margin-top: 20px;
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  .footer{
    color: white;
  }
  .success__buy{
    z-index: 1;
    padding: 20px 100px;
    background: white;
    border-radius: 15px;
    border: rgb(0, 253, 0) 5px solid;
    font-size: 20px;
    position: fixed;
    top: -80px;
    transition: 0.2s;
    transition-delay: 0.3s;
  }
  .success__buy__active{
    transform: translate3d(0, 100px, 0);
    transition: 0.2s;
    transition-delay: 0.3s;
  }
</style>