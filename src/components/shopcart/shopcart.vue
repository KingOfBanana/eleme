<template>
  <div class="shopcart">
    <div class="content">
      <div class="content-left">
        <div class="logo-wrapper">
          <div class="logo" :class="{'logo-highlight':totalCount>0}">
            <i class="icon-shopping_cart"></i>
          </div>
          <div class="num" v-show="totalCount>0">{{totalCount}}</div>
        </div>
        <div class="price" :class="{'price-highlight':totalPrice>0}">¥{{totalPrice}}</div>
        <div class="desc">另需配送费¥{{deliveryPrice}}元</div>
      </div>
      <div class="content-right">
        <div class="totalprice" :class="payClass">
          {{payDesc}}
        </div>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  export default {
    props: {
      selectFoods: {
        type: Array,
        default () {
          return [
          ];
        }
      },
      deliveryPrice: {
        type: Number,
        default: 0
      },
      minPrice: {
        type: Number,
        default: 0
      }
    },
    computed: {
      payClass () {
        if (this.totalPrice >= this.minPrice) {
          return 'enough';
        } else {
          return 'not-enough';
        }
      },
      payDesc () {
        if (this.totalPrice >= this.minPrice) {
          return '去结算';
        } else if (this.totalPrice === 0) {
          return this.minPrice + '元起送';
        } else {
          let diff = this.minPrice - this.totalPrice;
          return '还差' + diff + '元起送';
        }
      },
      totalPrice () {
        let total = 0;
        this.selectFoods.forEach((food) => {
          total += food.price * food.count;
        });
        return total;
      },
      totalCount () {
        let count = 0;
        this.selectFoods.forEach((food) => {
          count += food.count;
        });
        return count;
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .shopcart
    position: fixed
    left: 0
    bottom: 0
    z-index: 50
    width: 100%
    height: 48px
    .content
      display: flex
      background: #141d27
      font-size: 0
      .content-left
        flex: 1
        .logo-wrapper
          display: inline-block
          position: relative
          top: -10px
          margin: 0px 12px
          padding: 6px
          width: 56px
          height: 56px
          box-sizing: border-box
          background: #141d27
          border-radius: 50%
          vertical-align: top
          text-align: center
          .logo
            width: 100%
            height: 100%
            background: #2b343c
            border-radius: 50%
            .icon-shopping_cart
              font-size: 24px
              line-height: 44px
              font-weight: 700
              color: #80858a
            &.logo-highlight            
              background: rgb(0, 160, 220)
              .icon-shopping_cart
                color: rgb(255, 255, 255)  
          .num
            position: absolute
            top: 0
            right: 0
            width: 24px
            height: 16px
            background: rgb(240, 20, 20)
            font-size: 9px
            font-weight: 700
            color: rgb(255, 255, 255)
            text-align: center
            border-radius: 16px
            box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.7)
        .price
          display: inline-block
          vertical-align: top
          margin-top: 12px
          padding-right: 12px
          line-height: 24px
          font-size: 16px
          font-weight: 700        
          color: rgba(255, 255, 255, 0.4)
          border-right 1px solid rgba(255,255,255,0.1)
          &.price-highlight
            color: rgb(255, 255, 255)         
        .desc
          display: inline-block
          vertical-align: top
          margin-top: 12px
          padding-left: 12px
          font-size: 10px
          color: rgb(255, 255, 255, 0.4)
          font-weight: 700
          line-height: 24px
      .content-right
        flex: 0 0 105px
        width: 105px
        .totalprice
          height: 48px
          line-height: 48px       
          font-size: 12px
          color: rgb(255, 255, 255, 0.4)
          font-weight: 700
          text-align: center
          background: #2b333b
          &.enough
            background: #00b43c
          &.not-enough
            background: #2b333b
</style>
