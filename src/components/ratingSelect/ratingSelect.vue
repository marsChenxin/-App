<template>
	<div class="ratingselect">
		<div class="rating-type">
		  <span class="rating-all" :class="{active: selectType===2}" @click="toggleActive(2)">
		  	{{desc.all}}
		  	<span class="count">{{ratings.length}}</span>
		  </span>
		  <span class="rating-positive" :class="{active: selectType===0}" @click="toggleActive(0)">
		  	{{desc.positive}}
		  	<span class="count">{{positives.length}}</span>
		  </span>
		  <span class="rating-negative" :class="{active: selectType===1}" @click="toggleActive(1)">
		  	{{desc.negative}}
		  	<span class="count">{{negatives.length}}</span>
		  </span>
		</div>
		<div class="switch" :class="{on:onlyContent}" @click="toggleContent()">
			<span class="icon-selected-circle"></span>
			<span class="text">只看有内容的评价</span>
		</div>
	</div>
</template>

<script type="text/ecmascript-6">
  const POSITIVE = 0;
  const NEGATIVE = 1;
  const ALL = 2;
	export default {
		props: {
			ratings: {
				type: Array,
				default () {
					return [];
				}
			},
			selectType: {
				type: Number,
				default: ALL
			},
			onlyContent: {
				type: Boolean,
				default: true
			},
			desc: {
				type: Object,
				default () {
					return {
					  all: '全部',
					  positive: '满意',
					  negative: '不满意'
					};
				}
			}
		},
		computed: {
			positives () {
				return this.ratings.filter((ratings) => {
					return ratings.rateType === POSITIVE;
				});
			},
			negatives () {
				return this.ratings.filter((ratings) => {
					return ratings.rateType === NEGATIVE;
				});
			}
		},
		methods: {
			toggleActive (number) {
				this.$emit('changeType', number);
			},
			toggleContent () {
				this.$emit('toggleContent');
			}
		}
	};
</script>

<style lang="stylus" rel="stylesheet/stylus">
	@import "../../common/stylus/mixin.styl"
	.rating-type
		position relative
		padding-bottom 58px
		font-weight 500
		border-1px(rgba(7, 17, 27, 0.1))
		.rating-all, .rating-positive, .rating-negative
			font-size 14px
			padding 10px 16px
			line-height 18px
			color rgb(77, 85, 93)
			border-radius 3px
			margin-right 8px
			.count
				margin-left 2px
				font-size 8px
		.rating-all
			position absolute
			top 0px
			left 0px
			background-color rgb(0, 160, 220)
			&.active
				color #fff
				margin-top -5px
		.rating-positive
			position absolute
			top 0px
			left 100px
			background-color rgba(0, 160, 220, 0.2)
			&.active
				background rgb(0, 160, 220)
				color #fff
				margin-top -5px
		.rating-negative
			position absolute
			top 0px
			left 200px
			background-color rgba(77, 85, 93, 0.2)
			&.active
				background-color rgb(77, 85, 93)
				color #fff
				margin-top -5px
	.switch
		padding 16px
		font-size 0
		&.on
			.icon-selected-circle, .text
				color #00c850
		.icon-selected-circle
			display inline-block
			font-size 26px
			color rgb(147, 153, 159)
			line-height 24px
			margin-right 4px
		.text
			display inline-block
			font-size 15px
			font-weight 400
			line-height 24px
			color rgb(147, 153, 159)
			vertical-align top
</style>