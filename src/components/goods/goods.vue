<template>
  <div class="goods">
  	<div class="menu-wrapper" ref="menuWrapper">
  	  <ul>
  	  	<li v-for="(item,index) in goods" class="menu-item" :class="{'menu-item-selected':menuCurrentIndex===index}" @click="selectMenu(index, $event)">
  	  	  <span class="text">
  	  	    <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>{{item.name}}
  	  	  </span>
  	  	</li>
  	  </ul>
  	</div>
  	<div class="foods-wrapper" ref="foodWrapper">
       <ul>
         <li v-for="item in goods" class="food-list-hook">
           <h1 class="title">{{item.name}}</h1>
           <ul>
             <li v-for="food in item.foods" class="food-item">
               <div class="icon">
                 <img width="57" height="57" :src="food.icon"/>
               </div>
               <div class="content">
                 <h2 class="name">{{food.name}}</h2>
                 <p class="description" v-show="food.description">{{food.description}}</p>
                 <div class="sell-info">
                   <span class="sellCount">月售{{food.sellCount}}份</span>
                   <span class="rating">好评率{{food.rating}}%</span>
                 </div>
                 <div class="price">
                   <span class="newPrice"><span class="unit">￥</span>{{food.price}}</span>
                   <span v-show="food.oldPrice" class="oldPrice">￥{{food.oldPrice}}</span>
                 </div>
                 <div class="cartcontrol-wrapper">
                   <cartcontrol :food="food"></cartcontrol>
                 </div>
               </div>
             </li>
           </ul>
         </li>
       </ul> 
    </div>
    <shopcart :selectFoods="selectFoods" :deliveryPrice="seller.deliveryPrice" :minPrice="seller.minPrice">
      
    </shopcart>
  </div>
</template>

<script>
  
  import BScroll from 'better-scroll';
  import shopcart from 'components/shopcart/shopcart';
  import cartcontrol from 'components/cartcontrol/cartcontrol';

  const ERROR_OK = 0;

  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        goods: [],
        listHeight: [],
        scrollY: 0
      };
    },
    computed: {
      menuCurrentIndex () {
        for (let i = 0, l = this.listHeight.length; i < l; i++) {
          let topHeight = this.listHeight[i];
          let bottomHeight = this.listHeight[i + 1];
          if (!bottomHeight || (this.scrollY >= topHeight && this.scrollY < bottomHeight)) {
            return i;
          }
        }
        return 0;
      },
      selectFoods () {
        let foods = [];
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              foods.push(food);
            }
          });
        });
        return foods;
      }
    },
    created () {
      this.$http.get('/api/goods').then(response => {
          this.someData = response.body;
          if (this.someData.errno === ERROR_OK) {
            this.goods = this.someData.data;
            this.$nextTick(() => {
              this._initScroll();
              this._calculateHeight();
            });
          }
        });
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
    },
    methods: {
      selectMenu (index, event) {
        if (event._constructed) {
          return;
        }
        console.log(index);
        let foodList = this.$refs.foodWrapper.querySelectorAll('.food-list-hook');
        let el = foodList[index];
        this.foodScroll.scrollToElement(el, 300);
      },
      _initScroll () {
        this.menuScroll = new BScroll(this.$refs.menuWrapper, {
          click: true
        });
        this.foodScroll = new BScroll(this.$refs.foodWrapper, {
          click: true,
          probeType: 3
        });

        this.foodScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y));
        });
      },
      _calculateHeight () {
        let foodList = this.$refs.foodWrapper.querySelectorAll('.food-list-hook');
        let height = 0;
        this.listHeight.push(height);
        for (let i = 0; i < foodList.length; i++) {
          let item = foodList[i];
          height += item.clientHeight;
          this.listHeight.push(height);
        }
      }
    },
    components: {
      shopcart,
      cartcontrol
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl'
  @import '../../common/stylus/icon.styl'
  
  .goods
    display: flex
    position: absolute
    top: 174px
    bottom: 46px
    width: 100%
    overflow: hidden
    .menu-wrapper
      flex: 0 0 80px
      width: 80px
      background: #f3f5f7
      .menu-item-selected
        background white
        font-weight 700
        margin-top -1px
      .menu-item,.menu-item-selected
        display: table
        height: 54px
        width: 56px
        padding: 0 12px
        line-height: 14px
        .text
          display: table-cell
          width: 56px
          vertical-align: middle
          border-bottom: 1px solid rgba(7,17,27,0.1)
          font-size: 12px
	        .icon
	          display: inline-block
	          width: 12px
	          height: 12px
	          margin-right: 2px
	          background-size: 12px 12px
	          background-repeat: no-repeat
	          &.decrease
	            bg-image('decrease_3')
	          &.discount
	            bg-image('discount_3')
	          &.guarantee
	            bg-image('guarantee_3')
	          &.invoice
	            bg-image('invoice_3')
	          &.special
	            bg-image('special_3')
    .foods-wrapper
      flex: 1
      .title
        padding-left: 14px
        height: 26px
        line-height: 26px
        border-left: 2px solid #d9dde1
        font-size: 12px
        color: rgb(147, 153, 159)
        background: #f3f5f7
      .food-item
        display: flex
        margin: 18px
        padding-bottom: 18px
        border-1px(rgba(7, 17, 27, 0.1))
        &:last-child
          border-none()
          padding-bottom: 0px
        .icon
          flex: 0 0 57px
          margin-right: 10px
        .content
          flex: 1
          .name
            margin: 2px 0px 8px 0px
            font-size: 14px
            color: rgb(7, 17, 27)
            line-height: 14px
            font-weight: 700
          .sell-info,.description
            font-size 10px
            color rgb(147,153,159)
            line-height 10px
            .sellCount
              margin-right 4px
          .description
            font-size 10px
            margin-bottom 8px
            line-height: 12px
          .price
            font-size 10px
            font-weight 700
            line-height 24px
            .newPrice
              font-size 14px
              color rgb(240,20,20)
              .unit
                font-size 10px
                font-weight normal
            .oldPrice
              text-decoration line-through
              color rgb(147,153,159)
              padding-left 4px
          .cartcontrol-wrapper
            position: absolute
            right: 0
            bottom: 12px

        
</style>
