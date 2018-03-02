<template>
<div>
  <div class="shopcart">
  	<div class="content">
  	  <div class="content-left">
  	  	<div class="logo-wrapper" @click="toggleList">
  	  	  <div class="logo" :class="{'highlight':totalCount>0}">
  	  	    <i class="icon-cart" :class="{'highlight':totalCount>0}"></i>
  	  	    <div class="num" v-show="totalCount>0">{{totalCount}}</div>
  	  	  </div>
  	    </div>
  	    <div class="price" :class="{'highlight':totalPrice>0}" v-show="totalPrice>0">￥{{totalPrice}}</div>
  	    <div class="delivery" v-show="totalPrice>0">
          另需配送费￥{{deliveryPrice}}元
        </div>
        <div class="choose-none" v-show="totalPrice==0">未选购商品
        </div>
  	  </div>
	  <div class="content-right">
  	    <div class="pay" :class="payClass">
          {{payDesc}}
        </div>
	  </div>
  	</div>
    <transition name="slideup">
      <div class="shoplist" v-show="showList">
        <div class="list-header">
          <h1 class="title">购物车</h1>
          <span class="empty" @click="emptyList">清空</span>
        </div>
        <div class="list-content" ref="listWrapper">
          <ul>
            <li class="food" v-for="food in selectFoods">
                <span class="name">{{food.name}}</span>
                <div class="price">￥{{food.price*food.count}}</div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food="food"></cartcontrol>
                </div>
            </li>
          </ul>
        </div>
      </div>
    </transition>
  </div>
  <transition name="fade">
    <div class="list-mask" v-show="!fold" @click="toggleList"></div>
  </transition>
</div>
</template>

<script type="text/ecmascript-6">
  import cartcontrol from '../cartControl/cartControl.vue';
  import BScroll from 'better-scroll';
  export default {
  	props: {
  		deliveryPrice: {
  			type: Number,
  			default: 0
  		},
  		minPrice: {
  			type: Number,
  			default: 0
  		},
  		selectFoods: {
  			type: Array,
  			default () {
  			  return [
  			  {price: 0, count: 0}
  			  ];
  			}
  		}
  	},
    data () {
      return {
        fold: true
      };
    },
  	computed: {
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
  		},
  		payDesc () {
  			if (this.totalPrice <= 0) {
  				return `20元起送`;
  			} else if ((this.totalPrice !== 0) && (this.totalPrice < this.minPrice)) {
  				let desc = this.minPrice - this.totalPrice;
  				return `还差￥${desc}元起送`;
  			} else if (this.totalPrice >= this.minPrice) {
  				return `去结算`;
  			}
  		},
  		payClass () {
  			if (this.totalPrice >= this.minPrice) {
  				return `enough`;
  			}
  		},
      showList () {
        if (!this.totalCount) {
          this.fold = true;
          return false;
        }
        let show = !this.fold;
        if (show) {
          this.$nextTick(() => {
            if (!this.scroll) {
              this.scroll = new BScroll(this.$refs.listWrapper, {
                click: true
              });
            } else {
              this.scroll.refresh();
            }
          });
        }
        return show;
      }
  	},
    methods: {
      toggleList () {
        if (!this.totalCount) {
          return;
        }
        this.fold = !this.fold;
      },
      emptyList () {
        this.selectFoods.forEach((food) => {
          food.count = 0;
        });
      }
    },
    components: {
      cartcontrol
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
    @import '../../common/stylus/mixin.styl'
    .shopcart
   	  position fixed
   	  left 0
   	  bottom 0
   	  z-index 50
   	  width 100%
   	  height 56px
   	  .content
   	    display flex
   	    font-size 0
        background rgb(43, 51, 59, 1) 
   	  	.content-left
   	  	  flex 1
   	  	  .logo-wrapper
   	  	  	display inline-block
   	  	  	position relative
   	  	  	top -10px
   	  	  	margin 0 12px
   	  	  	padding 6px
   	  	  	width 66px
   	  	  	height 66px
   	  	  	box-sizing border-box
   	  	  	border-radius 50%
   	  	  	background rgba(43, 51, 59, 1)
   	  	  	.logo
   	  	  	  width 100%
   	  	  	  height 100%
   	  	  	  border-radius 50%
   	  	  	  background  #4e5256
   	  	  	  text-align center
   	  	  	  &.highlight
   	  	  	  	background-color rgb(0, 160, 200)
   	  	  	  .icon-cart
   	  	  	  	font-size 24px
   	  	  	  	color #80858a
   	  	  	  	line-height 54px
   	  	  	  	&.highlight
   	  	  	  	  color #fff
   	  	  	.num
   	  	  	  position absolute
   	  	  	  top 0px
   	  	  	  right 5px
   	  	  	  padding 0 6px
   	  	  	  font-size 12px
   	  	  	  line-height 16px
   	  	  	  font-weight 700
   	  	  	  color rgb(255, 255, 255)
   	  	  	  background-color rgb(240, 20, 20)
   	  	  	  box-shadow 0 4px 8px 0 rgba(0, 0, 0,0.1)
   	  	  	  border-radius 16px
   	  	  .price
   	  	  	display inline-block
   	  	  	font-size 16px
   	  	  	line-height 23px
   	  	  	vertical-align top
   	  	  	font-weight 700
   	  	  	color #80858a
   	  	  	margin-top 14px
   	  	  	padding-right 12px
   	  	  	border-right 1px solid rgba(255, 255, 255, 0.1)
   	  	  	&.highlight
   	  	  	  color #fff
   	  	  .delivery,.choose-none
   	  	  	display inline-block
   	  	  	font-size 12px
   	  	  	font-weight 400
   	  	  	line-height 23px
   	  	  	vertical-align top
   	  	  	color rgba(255, 255, 255, 0.3)
   	  	  	margin-top 16px
   	  	  	padding-left 12px
        .content-right
          flex 0 0 105px
          width 105px
          .pay
            font-size 15px
            width 100%
            height 56px
            font-weight 700
            line-height 54px
            text-align center
            color #64696d
            background #4e5256
            &.enough
              background-color #00b43c
              color #fff
      .shoplist
        position absolute
        top 0
        left 0
        z-index -1
        width 100%
        transform: translate3d(0, -100%, 0)
        &.slideup-enter-active, &.slideup-leave-active
          transition: all 0.4s
        &.slideup-enter, &.slideup-leave-active
          transform: translate3d(0, 0, 0)
        .list-header
          height 48px
          line-height 48px
          background #f3f5f7
          padding 0 18px
          border-bottom: 1px solid rgba(7, 17, 27, 0.1)
          .title
            float left
            font-size 16px
            color rgb(7, 17, 27)
            font-weight 500
          .empty
            float right
            font-size 16px
            font-weight 500
            color rgb(0, 160, 200)
        .list-content
          padding 0 18px
          max-height 225px
          overflow hidden
          background #fff
          .food
            position relative
            padding 12px 0
            box-sizing border-box
            line-height 24px
            height 48px
            border-1px(rgba(7, 17, 27, 0.1))
            .name
              font-size 16px
              font-weight 500
              color rgb(7, 17, 27)
              line-height 24px
            .price
              position absolute
              right 90px
              bottom 12px
              font-size 16px
              font-weight 500
              line-height 24px
              color rgb(240, 20, 20)
            .cartcontrol-wrapper
              display inline-block
              position absolute
              right 0px
              bottom -7px
              font-size 16px
              line-height 24px
    .list-mask
      position fixed
      top 0px
      left 0px
      width 100%
      height 100%
      background rgba(7, 17, 27, 0.6)
      backdrop-filter blur(10px)
      z-index 40
      opacity 1
      &.fade-enter-active,&.fade-leave-active
        transition all 0.4s
      &.fade-enter,&.fade-leave-active
        opacity 0
        background: rgba(7, 17, 27, 0)
</style>