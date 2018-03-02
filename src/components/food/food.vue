<template>
	<transition name="move">
		<div class="food" v-show="showFood" ref="foodWrapper">
			<div class="food-content">
				<div class="image-header">
					<img :src="food.image" style="position:absolute;top:0;left:0;width:100%;height:100%">
				</div>
				<div class="back" @click="hide">
					<i class="icon-back"></i>
				</div>
				<div class="food-info">
          <p class="food-name">{{food.name}}</p>
          <div class="food-extra">
            <span class="food-sellCount">月售{{food.sellCount}}份</span><span class="food-rating" v-show="food.rating">好评率{{food.rating}}%</span>
          </div>
          <div class="food-price">
            <span class="price">￥{{food.price}}</span>
            <span v-show="food.oldPrice" class="oldPrice">￥{{food.oldPrice}}</span>
          </div>
					<div class="cartWrapper">
            <transition name="fade">
					    <div class="addCart" v-show="!food.count || food.count===0" @click="addCart">加入购物车
					    </div>
					  </transition>
					  <transition name="fade">
					    <cartControl :food="food" v-show="food.count>0" class="cartcontrol"></cartControl>	
					  </transition>			
					</div>
        </div>
			  <divide v-show="food.info"></divide>
			  <div class="food-instruction" v-show="food.info">
			  	<p class="title">商品介绍</p>
			  	<p class="instruction">{{food.info}}</p>
			  </div>
				<divide></divide>
				<div class="ratingFood">
					<p class="title">商品评价</p>
					<ratingSelect :selectType="selectType" :onlyContent="onlyContent" :desc="desc" :ratings="food.ratings" @changeType="changeType" @toggleContent="toggleContent"></ratingSelect>
				</div>
		    <div class="ratingContent">
		    	<ul v-show="food.ratings && food.ratings.length">
		    		<li v-for="rating in food.ratings" class="rating-item" v-show="showNeed(rating.rateType,rating.text)">
		    			<div class="user">
		    				<p class="userName">{{rating.username}}</p>
		    				<img :src="rating.avatar" class="userAvatar" width="12" height="12">
		    			</div>
		    			<div class="ratingDate">{{rating.rateTime | formatDate(rating.rateTime)}}</div>
		    			<div class="ratingDetail">
		    				<span :class="{'icon-praise_fill':rating.rateType===0,'icon-praise_fill inverse':rating.rateType===1}">
		    				</span>
		    				<span class="noContent" v-show="rating.rateType===0 && rating.text===''">系统默认好评</span>
		    				<span class="noContent" v-show="rating.rateType===1 && rating.text===''">用户没给出评价</span>
		    				{{rating.text}}
		    		  </div>
		    		</li>
		    	</ul>
		    	<div class="no-ratings" v-show="!food.ratings || !food.ratings.length">
		    		<span class="no-rating-content">暂无评价</span>
		    	</div>
		    </div>
		  </div>
	  </div>
	</transition>
</template>

<script type="text/ecmascript-6">
import Vue from 'vue';
import divide from '../divide/divide.vue';
import BScroll from 'better-scroll';
import cartControl from '../cartControl/cartControl.vue';
import ratingSelect from '../ratingSelect/ratingSelect.vue';
import {formatDate} from '../../common/js/date.js';

  const ALL = 2;

	export default {
		props: {
			food: {
				type: Object
			}
		},
		data () {
			return {
				showFood: false,
				ratings: {
					type: Array,
					default () {
						return [];
					}
				},
			  onlyContent: true,
			  selectType: ALL,
			  desc: {
			  	all: '全部',
			  	positive: '推荐',
			  	negative: '吐槽'
			  }
			};
		},
		filters: {
				formatDate (time) {
					let date = new Date(time);
					return formatDate(date, 'yyyy-MM-dd  hh:mm');
			  }
		},
		methods: {
			show () {
				this.showFood = true;
				this.$nextTick(() => {
                   if (!this.scroll) {
                     this.scroll = new BScroll(this.$refs.foodWrapper, {
                       click: true
                     });
                   } else {
                     this.scroll.refresh();
                   }
                });
        this.selectType = ALL;
        this.onlyContent = true;
			},
			hide () {
				this.showFood = false;
			},
			addCart (event) {
				Vue.set(this.food, 'count', 1);
			},
			changeType (number) {
				this.selectType = number;
				this.$nextTick(() => {
					this.scroll.refresh();
				});
			},
			toggleContent () {
				this.onlyContent = !this.onlyContent;
				this.$nextTick(() => {
					this.scroll.refresh();
				});
			},
			showNeed (type, text) {
				if (this.onlyContent && !text) {
					return false;
				}
				if (this.selectType === ALL) {
					return true;
				} else {
					return this.selectType === type;
				}
			}
		},
		components: {
			divide,
			ratingSelect,
			cartControl
		}
};
</script>

<style lang="stylus" rel="stylesheet/stylus">
	@import "../../common/stylus/mixin.styl"
	.food
	  position fixed
	  top 0px
	  left 0px
	  bottom 56px
	  width 100%
	  background #fff
	  z-index 30
	  transform translate3d(0, 0, 0)
	  &.move-enter-active,&.move-leave-active
	    transition all 0.3s linear
	  &.move-enter,&.move-leave-active
	    transform translate3d(100%, 0, 0)
		  .food-content
		    .image-header
		    	position relative
		    	width 100%
		    	height 0
		    	padding-top 100%
		    	img
		    		position absolute
		    		top 0
		    		left 0
		    		width 100%
		    		height 100%
		    .back
		    	position absolute
		    	top 20px
		    	left 15px
		    	width 25px
		    	height 25px
		    	border-radius 50%
		    	background rgba(7, 17, 27, 0.6)
		    	.icon-back
		    		position absolute
		    		top -8px
		    		left -9px
		    		display block
		    		padding 10px
		    		font-size 20px
		    		font-weight 700
		    		color #fff
		    .food-info
		    	position relative
		    	padding 18px
		    	.food-name
		    		font-size 16px
		    		line-height 14px
		    		font-weight 700
		    		color rgb(7, 17, 27)
		    	.food-extra
		    		width 100%
		    		margin-top 8px
		    		margin-bottom 18px
		    		.food-sellCount,.food-rating
		    			font-size 12px
		    			font-weight 500
		    			color rgb(147, 153, 159)
		    		.food-sellCount
		    			margin-right 12px
		    	.food-price
		    		width 100%
		    		font-size 0
		    		.price
		    			display inline-block
		    			font-size 16px
		    			line-height 24px
		    			color rgb(240, 20, 20)
		    			font-weight 700
		    			margin-right 6px
		    		.oldPrice
		    			display inline-block
		    			font-size 12px
		    			line-height 24px
		    			color rgb(147, 153, 159)
		    			font-weight 500
		    			text-decoration line-through
		    	.addCart
		    			display inline-block
		    			position absolute
		    			right 18px
		    			bottom 18px
		    			width 78px
		    			height 28px
		    			line-height 28px
		    			font-size 12px
		    			font-weight 500
		    			box-sizing border-box
		    			color #fff
		    			background rgb(0, 160, 220)
		    			border-radius 14px
		    			text-align center
		    			&.fade-enter-active, &.fade-leave-active
		    				transition all 1s
		    			&.fade-enter, &.fade-leave-active
		    				opacity 0
		    	.cartcontrol
		    			position absolute
		    			right 12px
		    			bottom 12px
		    			opacity 1
		    			&.fade-enter-active, &.fade-leave-active
		    				transition all 0.5s
		    			&.fade-enter, &.fade-leave-active
		    				opacity 0
		  .food-instruction
		  	padding 18px
		  	.title
		  		font-size 16px
		  		line-height 24px
		  		font-weight 700
		  		color rgb(30, 40 ,50)
		  	.instruction
		  		font-size 14px
		  		color rgb(77, 85, 93)
		  		font-weight 400
		  		line-height 24px
		  		padding 0 14px
		  .ratingFood
		  	padding 18px 18px 6px 18px
		  	border-1px(rgba(7, 17, 27, 0.1))
		  	.title
		  		font-size 16px
		  		line-height 24px
		  		font-weight 700
		  		color rgb(30, 40, 50)
		  		margin-bottom 18px
		  .ratingContent
		  	padding 0px 18px 18px 18px
		  	font-size 0
		  	.rating-item
		  		position relative
					 border-1px(rgba(7, 17, 27, 0.1))
					 margin-top 16px
		  		.user
		  			position absolute
		  			top 0px
		  			right 0px
		  			.userName
		  				display inline-block
		  				font-size 12px
		  				color rgb(147, 153, 159)
		  				line-height 14px
		  				margin-right 8px
		  			.userAvatar
		  				border-radius 6px
		  				line-height 14px
		  				vertical-align top
		  		.ratingDate
		  			font-size 14px
		  			color rgb(147, 153, 159)
		  			line-height 14px
		  			margin-bottom 6px
		  		.ratingDetail
		  			display inline-block
		  			font-size 14px
		  			color rgb(7, 17, 27)
		  			line-height 16px
		  			vertical-align top
		  			padding-bottom 16px
		  			.icon-praise_fill
		  				display inline-block
		  				font-size 14px
		  				color rgb(0, 160, 220)
		  				line-height 24px
		  				&.inverse
		  					transform rotate(180deg)
</style>