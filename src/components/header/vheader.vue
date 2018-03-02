<template>
  <div class="header">
	<div class="content-wrapper">
	  <div class="avatar">
	  	<img width="64" height="64" :src="seller.avatar" alt="">
	  </div>
	  <div class="content">
	  	<div class="title">
	  	  <span class="brand"></span> 
	  	  <span class="name">{{seller.name}}</span>
	  	</div>
	  	<div class="description">
	  	  {{seller.description}}/{{seller.deliveryTime}}分钟送达
	  	</div>
	  	<div class="supports">
	  	  <div v-if="seller.supports" class="support">
	  	  	<span class="icon" :class="classMap[seller.supports[0].type]"></span>
	  	  	<span class="text">{{seller.supports[0].description}}</span>
	  	  </div>
	  	</div>
	  </div>
	  <div v-if="seller.supports" class="support-count" @click="showDetail">
	  	<span class="count-bulletin" >{{seller.supports.length}}&nbsp;个</span>
	  </div>
	</div>
	<div class="bulletin-wrapper" @click="showDetail">
	  <span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
	</div>
	<div class="head-background">
	  <img :src="seller.avatar" width="100%" height="100%" alt="">
	</div>
    <transition name="fade">
      <div v-show="detailShow" class="detail">
	   <div class="detail-wrapper clearfix">
	  	<div class="detail-main">
	  	  <h1 class="name">{{seller.name}}</h1>
	  	  <div class="star-wrapper">
	  	  	<star :score="seller.score" :size="48"></star>
	  	  </div>
	  	  <div class="title">
	  	  	<div class="line"></div>
	  	  	<div class="text">优惠信息</div>
	  	  	<div class="line"></div>
	  	  </div>
	  	  <ul v-if="seller.supports" class="supports">
	  	  	<li class="supports-item" v-for="(item,index) in seller.supports">
	  	  		<span class="icon" :class="classMap[seller.supports[index].type]"></span>
	  	  		<span class="text">{{seller.supports[index].description}}</span>
	  	  	</li>
	  	  </ul>
	  	  <div class="title">
	  	  	<div class="line"></div>
	  	  	<div class="text">商家公告</div>
	  	  	<div class="line"></div>
	  	  </div>
	  	  <div class="bulletin">
	  	  	<p class="text">{{seller.bulletin}}</p>
	  	  </div>
	  	</div>	
	  </div>
	  <div class="detail-close" @click="closeDetail">
	  	<span class="icon-close"></span>
	  </div>
	 </div>	
    </transition>
  </div>
</template>

<script type="text/ecmascript-6">
  import star from '../stars/stars';
  export default {
    props: {
      seller: {
    	  type: Object
      }
    },
    data () {
    	return {
    		detailShow: false
    	};
    },
    methods: {
    	showDetail () {
    		this.detailShow = true;
    	},
    	closeDetail () {
    		this.detailShow = false;
    	}
    },
    created () {
      this.classMap = ['decrease', 'discount', 'special  ', 'invoice', 'guarante'];
    },
    components: {
      star
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
	@import "../../common/stylus/mixin"

	.header
	  position relative
	  color #fff
	  background rgba(7, 17, 27, 0.5)
	  overflow hidden
	  .content-wrapper
	  	position relative
	  	padding 24px 12px 18px 24px
	  	font-size 0
	  	.avatar
	  	  display inline-block
	  	  vertical-align top
	  	  img
	  	  	border-radius 2px
	  	.content
	  	  display inline-block
	  	  margin-left 16px
	  	  .title
	  	  	margin 2px 0 8px 0
	  	  	.brand
	  	  	  display inline-block
	  	  	  vertical-align top
	  	  	  width 30px
	  	  	  height 18px
	  	  	  bg-image('brand')
	  	  	  background-size 30px 18px
	  	  	  background-repeat no-repeat
	  	  	.name
	  	  	  display inline-block
	  	  	  margin-left 6px
	  	  	  font-size 16px 
	  	  	  line-height 18px
	  	  	  font-weight bold
	  	  .description
	  	  	margin-bottom 10px
	  	  	line-height 12px
	  	  	font-size 12px
	  	  	font-weight 500
	  	  .support
	  	  	.icon
	  	  	  display inline-block
	  	  	  vertical-align top
	  	  	  width 12px
	  	  	  height 12px
	  	  	  margin-right 4px
	  	  	  background-size 12px 12px
	  	  	  background-repeat no-repeat
	  	  	  &.decrease
	  	  	  	bg-image('decrease_1')
	  	  	  &.special
	  	  	  	bg-image('special_1')
	  	  	  &.discount
	  	  	  	bg-image('discount_1')
	  	  	  &.invoice
	  	  	  	bg-image('invoice_1')
	  	  	  &.guarantee
	  	  	  	bg-image('guarantee_1')
	  	  	.text
	  	  	  line-height 12px
	  	  	  font-size 10px
	  	  	  font-weight 500  
	  	.support-count
	  	  position absolute
	  	  right 12px
	  	  bottom 18px
	  	  padding 0 8px
	  	  border-radius 14px
	  	  height 24px
	  	  line-height 24px
	  	  background rgba(0, 0, 0, 0.2)
	  	  text-align center
	  	  .count-bulletin
	  	  	font-size 12px
	  	  	line-height 24px
	  	  	font-weight 500
	  	  .icon-more
	  	  	font-size 12px
	  	  	line-height 24px
	  	  	font-weight 700
	  .bulletin-wrapper
	  	position: relative
	  	height 28px
	  	line-height 28px
	  	padding 0 22px 0 12px
	  	white-space nowrap
	  	overflow hidden
	  	text-overflow ellipsis
	  	background-color rgba(7, 17, 27, 0.2)
	  	.bulletin-title
	  	  display inline-block
	  	  margin-top 8px
	  	  width 22px
	  	  height 12px
	  	  bg-image('bulletin')
	  	  background-size 22px 12px
	  	  background-repeat norepeat
	  	.bulletin-text
	  	  vertical-align top
	  	  margin 0 4px 
	  	  font-size 10px
	  	  font-weight 500
	  .head-background
	  	position absolute
	  	top 0
	  	left 0
	  	width 100%
	  	height 100%
	  	z-index -3
	  	filter blur(10px)
	  .detail
	  	position fixed
	  	top 0
	  	left 0
	  	width 100%
	  	height 100%
	  	z-index 100
	  	overflow auto
	  	background rgba(7, 17, 27, 0.8)
	  	transition all 0.7s
	  	backdrop-filter blur(5px)
	  	&.fade-enter-active, &.fade-leave-active
	  	 opacity 1
	  	&.fade-enter,&.fade-leave-to
	  	 opacity 0 
	  	.detail-wrapper
	  	  width 100%
	  	  min-height 100%
	  	  .detail-main
	  	  	margin-top 64px
	  	  	padding-bottom 64px
	  	  	.name
	  	  	  line-height 16px
	  	  	  font-size 16px
	  	  	  text-align center
	  	  	  font-weight 700
	  	  	.star-wrapper
	  	  	  margin-top 16px	
	  	  	  text-align center
	  	  	.title
	  	  	  display flex
	  	  	  width 80%
	  	  	  margin 28px auto 24px auto
	  	  	  .line
	  	  	  	flex 1
	  	  	  	position relative
	  	  	  	top -6px
	  	  	  	border-bottom 1px solid rgba(255, 255, 255, 0.2)
	  	  	  .text
	  	  	  	text-align center
	  	  	  	padding 0 12px
	  	  	  	font-weight 700
	  	  	  .bulletin-text
	  	  	  	width 100%
	  	  	  	font-size 12px
	  	  	.supports
	  	  	  width 80%
	  	  	  margin 0 auto
	  	  	  .supports-item
	  	  	    padding 0 12px
	  	  	    margin-bottom 12px
	  	  	    font-size 0
	  	  	    &:last-child
	  	  	    	margin-bottom 0px
	  	  	    .icon
	  	  	      display inline-block
	  	  	      width 16px
	  	  	      height 16px
	  	  	      vertical-align top
	  	  	      margin-right 6px
	  	  	      background-size 16px
	  	  	      background-repeat no-repeat
	  	  	      &.decrease
	  	  	        bg-image('decrease_2')
	  	  	      &.special
	  	  	        bg-image('special_2')
	  	  	      &.discount
	  	  	        bg-image('discount_2')
	  	  	      &.guarante
	  	  	        bg-image('guarantee_2')
	  	  	      &.invoice
	  	  	        bg-image('invoice_2')
	  	  	    .text
	  	  	     	line-height 16px
	  	  	     	font-size 12px
	  	  	     	font-weight 500
	  	  	.bulletin
	  	  	  width 80%
	  	  	  margin 0 auto
	  	  	  .text
	  	  	  	line-height 24px
	  	  	  	font-size 12px
	  	  	  	font-weight 500
	  	.detail-close
	  	  position relative
	  	  width 48px
	  	  height 48px
	  	  font-size 26px
	  	  margin -64px auto 0 auto
	  	  text-align center
	  	  clear both	
</style>