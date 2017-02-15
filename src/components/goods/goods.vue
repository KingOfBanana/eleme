<template>
  <div class="goods">
  	<div class="menu-wrapper">
  	  <ul>
  	  	<li v-for="item in goods" class="menu-item">
  	  	  <span class="text">
  	  	    <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>{{item.name}}
  	  	  </span>
  	  	</li>
  	  </ul>
  	</div>
  	<div class="foods-wrapper">
       <ul>
         <li v-for="item in goods">
           <h1 class="title">{{item.name}}</h1>
           <ul>
             <li v-for="food in item.foods" class="food-item">
               <div class="icon">
                 <img width="57" height="57" :src="food.icon"/>
               </div>
               <div class="content">
                 <h2>{{food.name}}</h2>
                 <p class="description" v-show="food.description">{{food.description}}</p>
                 <div class="sell-info">
                   <span class="sellCount">月售{{food.sellCount}}份</span>
                   <span class="rating">好评率{{food.rating}}%</span>
                 </div>
                 <div class="price">
                   <span class="newPrice"><span class="unit">￥</span>{{food.price}}</span>
                   <span v-show="food.oldPrice" class="oldPrice">￥{{food.oldPrice}}</span>
                 </div>
               </div>
             </li>
           </ul>
         </li>
       </ul> 
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  const ERROR_OK = 0;

  export default {
    data () {
      return {
        goods: []
      };
    },
    created () {
      this.$http.get('/api/goods').then(response => {
          this.someData = response.body;
          if (this.someData.errno === ERROR_OK) {
            this.goods = this.someData.data;
          }
        });
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
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
      .menu-item
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
        &:last-child
          display: none
</style>
