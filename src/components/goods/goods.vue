<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
        <li v-for="(item,index) in goods" class="menu-item" :class="{current:currentIndex === index}" @click="selectMenu(index)" ref="menuList">
          <span class="text border-1px2">
             <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>{{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodsWrapper">
      <ul>
        <li v-for="item in goods" class="food-list" ref="foodList">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li v-for="(food,index) in item.foods" class="food-item">
              <div class="food-icon">
                <img width="57" height="57" :src="food.icon" alt="">
              </div>
              <div class="food-content" @click="selectsingleFood(food)">
                <p class="food-name">{{food.name}}</p>
                <p class="food-description">{{food.description}}</p>
                <div class="food-extra">
                  <span class="food-sellCount">月售{{food.sellCount}}份</span>
                  &nbsp;&nbsp;
                  <span class="food-rating" v-show="food.rating">好评率{{food.rating}}%</span>
                </div>
                <div class="food-price">
                  <p class="price">￥{{food.price}}</p>
                  <p v-show="food.oldPrice" class="oldPrice">￥{{food.oldPrice}}</p>
                </div>
              </div>
              <div class="cart-wrapper">
                  <cartControl :food="food"></cartControl>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <food :food="selectedFood" ref="foodDetail"></food>
    <shopcart :select-foods="selectFoods" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import shopcart from '../shopcart/shopcart.vue';
  import cartControl from '../cartControl/cartControl.vue';
  import food from '../food/food.vue';
  const ERR_OK = 0;
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
        scrollY: 0,
        selectedFood: {}
      };
    },
    created () {
      this.classMap = ['decrease', 'discount', 'special  ', 'invoice', 'guarante'];
      this.$http.get('api/goods').then(response => {
        response = response.body;
        if (response.errno === ERR_OK) {
            this.goods = response.data;
            this.$nextTick(function () {
              this._initScroll();
              this._calculateHeight();
            });
        }
      });
    },
    computed: {
      currentIndex () {
        for (let i = 0; i < this.listHeight.length; i++) {
          let height1 = this.listHeight[i];
          let height2 = this.listHeight[i + 1];
          if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
            this._followScroll(i);
            return i;
          }
        }
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
    methods: {
      _initScroll () {
        this.menuScroll = new BScroll(this.$refs.menuWrapper, {
          click: true
        });
        this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
          click: true,
          probeType: 3
        });
        this.foodsScroll.on('scroll', (pos) => {
          // 判断滑动方向，避免下拉时分类高亮错误（如第一分类商品数量为1时，下拉使得第二分类高亮）
          if (pos.y <= 0) {
            this.scrollY = Math.abs(Math.round(pos.y));
          }
        });
      },
      _calculateHeight () {
        let foodList = this.$refs.foodList;
        let height = 0;
        this.listHeight.push(height);
        for (let i = 0; i < foodList.length; i++) {
           let item = foodList[i];
           height += item.clientHeight;
           this.listHeight.push(height);
        }
      },
      selectMenu (index) {
        let foodList = this.$refs.foodList;
        let el = foodList[index];
        this.foodsScroll.scrollToElement(el, 300);
      },
      selectsingleFood (food) {
        this.selectedFood = food;
        this.$refs.foodDetail.show();
      },
      _followScroll (index) {
        let menuList = this.$refs.menuList;
        let el = menuList[index];
        this.menuScroll.scrollToElement(el, 300, 0, -100);
      }
    },
    components: {
      shopcart,
      cartControl,
      food
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  
  .goods
    display flex
    position absolute
    top 174px
    bottom 46px
    width 100%
    overflow hidden
    .menu-wrapper
      flex 0 0 80px
      width 80px
      font-weight 500
      background #f3f5f7
      .menu-item
        display table
        width 56px
        height 54px
        padding 0 12px
        line-height 14px
        &:last-child
          border-none()
        &.current
          position relative
          margin-top -1px
          background #fff
          z-index 10
          font-weight 700
        .text
          display table-cell
          width 56px
          vertical-align middle
          font-size 12px
          border-1px(rgba(7, 17, 27, 0.1))
          .icon
            display inline-block
            width 12px
            height 12px
            margin-right 2px
            vertical-align top
            background-size 12px 12px
            background-repeat no-repeat
            &.decrease
              bg-image("decrease_3")
            &.special
              bg-image("special_3")
            &.discount
              bg-image("discount_3")
            &.guarante
              bg-image("guarantee_3")
            &.invoice
              bg-image("invoice_3")
    .foods-wrapper
      flex 1
      .food-list
        .title
          padding-left 14px
          font-size 12px
          font-weight 500
          line-height 26px
          height 26px
          color rgb(147,153,159)
          border-left 1px solid #d9dde1
          background #f3f5f7
        .food-item
          display flex
          margin 18px
          padding-bottom 18px
          border-1px(rgba(7,17,27,0.1))
          &:last-child
            border-none()
            margin-bottom 0px
          .food-icon
            flex 0 0 57px
            margin-right 10px
          .food-content
            flex 1
            .food-name
              font-size 14px
              height 14px
              font-weight 500
              line-height 14px
              color rgb(7,17,27)
              margin 2px 0 8px 0
            .food-description,.food-extra
              font-size 10px
              font-weight 500
              line-height 10px
              color rgb(147,153,159)
              margin-bottom 8px
            .food-description
              line-height 12px
              .food-sellCount
                margin-right 12px
            .food-price
              .price
                display inline-block
                font-size 14px
                color red
                line-height 14px
                margin-right 12px
                font-weight 700
              .oldPrice
                display inline-block
                font-size 10px
                color rgb(147,153,159)
                line-height 10px
                font-weight 700
                vertical-align middle
                text-decoration line-through
          .cart-wrapper
            position absolute
            right -5px
            bottom 0px            
</style>