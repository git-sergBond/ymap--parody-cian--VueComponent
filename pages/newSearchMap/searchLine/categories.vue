<template>
<div class="container my-vars">
    
    <div class="dop-info">
        <p>Москва 24°С</p>
        <p>18:20</p>
        <div class="wrap-close" @click="closePanel">
        <img class="btn-close" src="/images/icons/крестик.PNG" ></div>
    </div>
    
    <div v-if="!child" class="container-row">
        <div class="item" v-for="(name, index) in filterData">
            <div v-for="item in model.child" 
                @click.prevent="clickOnItem(item)"
                v-if="(name == item.name)">
                    <img class="icons" :src='item.img'>
                    <p>{{item.name}}</p>
            </div>
            <!--!!((sortData.length-1 == index && otherItems ) && ())-->
            
        </div>
        <div @click="showMore" class="item">
            <img   src="/images/icons/троеточие.PNG">
            </div>
    </div>
    <div class="childs" v-if="!!child"> 
        <img class="icons" :src='child.img' @click="close">
        <div class="cats">
            <p v-for="item in child.child" @click.prevent="getServices(item)">{{ item.name }}</p>
        </div>
    </div>
</div>
</template>

<script>
import TOKENS from "../../../TOKENS.js";
export default {
    name: "categories",
    props: {
        model: Object,
    },
    data(){
        return {
            sortData: [
                'Питание',
                'Развлечения и отдых',
                'Авто и перевозки',
                'Красота',
                'Спорт',
                'Друг на час','Медицина',
                'Недвижимость',
                'Ремонт и строительство',
                'IT, интернет, телеком',
                'Деловые услуги',
                'Курьерские поручения',
                'Бытовые услуги',
                'Клининг',
                'Обучение',
                'Праздники, мероприятия',
                'Животные',
                'Реклама, полиграфия',
                'Сад, благоустройство',
                'Охрана, безопасность',
                'Патронажн, уход',
                
                'Благотворительность',
                'Ритуальные услуги'
            ],
            filterData: null,
            child: null,
            countDrawItems: 7,
            otherItems: true
        }
    },
    mounted(){
        this.showMore()
    },
    methods: {
        closePanel(){
            this.$emit('beforeState')
        },
        showMore(){
            let context = this;
            this.otherItems = !this.otherItems;
            if(!this.otherItems){
                let newMas = []
                this.sortData.forEach((el,ind)=>{
                    if(ind < context.countDrawItems)
                    newMas.push(el)
                })
                this.filterData = newMas
            }else{
                this.filterData = this.sortData
            }
            
        },
        clickOnItem(child){
            this.addCollectionStatistics(child);
            this.child = child
        },
        addCollectionStatistics(mainCategory){
            //сбор статистики 
            let getItems = localStorage.getItem(TOKENS.OFENUSED);
            if (getItems == null) {
                //если статистика собирается в первый раз
                let newStat = JSON.stringify([]);
                localStorage.setItem(TOKENS.OFENUSED, newStat);
                this.addCollectionStatistics(mainCategory);
            } else {
                //если не в первый раз, то добавляем данные
                let itemId = mainCategory.id;
                let oldData = JSON.parse(getItems);
                if(oldData[itemId] != undefined){
                    oldData[itemId]++;
                } else {
                    oldData[itemId] = 1;
                }
                let newStat = JSON.stringify(oldData);
                localStorage.setItem(TOKENS.OFENUSED, newStat);
            }
        },
        getServices(item){
            this.$emit('event_getServices',{
                typeQuery: 3,
                categoriesId: [item.id]
            })
        },
        close(){
            this.child = null;
        }
    }
}
</script>

<style scoped>
.my-vars{
    --c-margin: 5px;
    --c-proportion: 32px;
    --with-marg: 37px;
    --sizeV: 40px;
    --sizeH: var(--sizeV);
}
.container{
    display: flex;
    flex-direction: column;
    width: 400px;
    background-color: rgba(255, 255, 255, 0.7);
    margin-top: 10px;
    box-shadow: -7px 5px 30px -1px;/*-4px 7px 20px 0px;*/
}
.container-row{
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    justify-content: space-around;
}
.dop-info{
    display: flex;
    justify-content: space-around;
    position: relative;
}
.item{
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    width: 100px
}
.item:hover{
    background-color: rgba(200,200,200,0.5);
}
.item div {
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 100px
}
.item  p {
    text-align: center;
    font-size: 10px;
    padding: 0;
}
/**/
.childs{
    display: flex;
    flex-direction: row;
    height: 300px;
    flex-wrap: wrap;
}
.childs .icons {
    flex-basis: auto;
    width: 50px;
    margin: 10px;
}
.childs .cats{
    display: flex;
    flex-direction: column;
    flex-wrap: wrap;
    flex-basis: auto;
    /*width: 300px;*/
    height: 100%;
    justify-content: flex-start;
}
.childs .cats p {
    font-size: 9pt;
    width: 150px;
}
.childs .cats p:hover{
    background-color: rgba(200,200,200,0.5);
}
/**/
.wrap-close{
    position: absolute;
    right: 5px;
    top: 5px;
}
.btn-close{
    align-self: flex-end;
    width: 20px;
    height: 20px;
}
/*Иконки*/
.icons{
    height: var(--sizeH);
    width: var(--sizeV);
    margin: 0;
    padding: 0;
    z-index: 3;
}
</style>
