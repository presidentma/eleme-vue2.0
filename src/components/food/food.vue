<template>
    <div>
        <transition name="switch">
            <div v-show="showFalg" class="food" ref="food">
                <div class="food-content">
                    <div class="image-header">
                        <img :src="food.image">
                        <div class="back" @click="hide">
                            <i class="iconfont icon-weibiaoti6-copy"></i>
                        </div>
                    </div>
                    <div class="content-wrapper">
                        <h1 class="title">{{food.name}}</h1>
                        <div class="contents">
                            <span class="sell-count">月售{{food.sellCount}}</span>
                            <span class="rating">好评率{{food.rating}}%</span>
                        </div>
                        <div class="price">
                            <span class="now">￥{{food.price}}</span>
                            <span v-show="food.oldPrice" class="old"></span>
                        </div>
                        <div class="cartcontrol-wrapper">
                            <cartcontrol :food="food" @cartadd="cartadd"></cartcontrol>
                        </div>
                        <transition name="buyFade">
                            <div class="buy" @click.stop.prevent="addFirst" v-show="!food.count || food.count===0">加入购物车</div>
                        </transition>
                    </div>
                    <split v-show="food.info"></split>
                    <div class="info" v-show="food.info">
                        <h1 class="title">商品信息</h1>
                        <p class="text">{{food.info}}</p>
                    </div>
                    <split></split>
                    <div class="rating">
                        <h1 class="title">商品评价</h1>
                        <ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc" :ratings="food.ratings" @ratingRefresh="ratingRefresh" @onlyContent="only"></ratingselect>
                        <div class="rating-wrapper">
                            <ul v-show="food.ratings && food.ratings.length">
                                <li v-show="needShow(rating.rateType, rating.text)" v-for="rating in food.ratings" class="rating-item border-1px">
                                    <div class="user">
                                        <span class="name">{{rating.username}}</span>
                                        <img class="avatar" width="12" height="12" :src="rating.avatar">
                                    </div>
                                    <div class="time">{{rating.rateTime | formatDate}}</div>
                                    <p class="text">
                                        <span class="iconfont" :class="{'icon-damuzhi':rating.rateType === 0,'icon-down':rating.rateType === 1}"></span>{{rating.text}}
                                    </p>
                                </li>
                            </ul>
                            <div class="no-rating" v-show="!food.ratings || !food.ratings.length">
                                暂无评价
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </transition>
    </div>
</template>

<script type="text/ecmascript-6">
import BScroll from 'better-scroll'
import cartcontrol from '../cartcontrol/cartcontrol.vue'
import Vue from 'vue'
import split from '../split/split.vue'
import ratingselect from '../ratingselect/ratingselect.vue'
import { formatDate } from '../../common/js/date'
const ALL = 2
export default {
    props: {
        food: {
            type: Object
        }
    },
    data() {
        return {
            showFalg: false,
            selectType: ALL,
            onlyContent: false,
            desc: {
                all: '全部',
                positive: '推荐',
                negative: '吐槽'
            }
        }
    },
    methods: {
        show() {
            this.showFalg = true
            this.selectType = ALL
            this.onlyContent = false
            this.$nextTick(() => {
                if (!this.scroll) {
                    this.scroll = new BScroll(this.$refs.food, {
                        click: true
                    })
                } else {
                    this.scroll.refresh()
                }
            })
        },
        hide() {
            this.showFalg = false
        },
        addFirst(event) {
            if (!event._constructed) {
                return
            }
            Vue.set(this.food, 'count', 1)
        },
        // cartadd为详情页添加购物车增加小球动画
        cartadd(target) {
            this.$emit('cartadd', target)
        },
        needShow(type, text) {
            if (this.onlyContent && !text) {
                return false
            }
            if (this.selectType === ALL) {
                return true
            } else {
                return type === this.selectType
            }
        },
        // type改变时刷新scroll
        ratingRefresh(type) {
            this.selectType = type
            this.$nextTick(() => {
                this.scroll.refresh()
            })
        },
        only(status) {
            this.onlyContent = status
            this.$nextTick(() => {
                this.scroll.refresh()
            })
        }
        // 子组件改变props的值会出现overwritten报错
    },
    filters: {
        formatDate(time) {
            let date = new Date(time)
            return formatDate(date, 'yyyy-MM-dd hh:mm')
        }
    },
    components: {
        cartcontrol,
        split,
        ratingselect
    }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
@import '../../common/stylus/mixin'
    .food
        position fixed
        left 0
        top 0
        bottom 48px
        z-index 30
        width 100%
        background #fff
        &.switch-enter-active, &.switch-leave-active
            transition all 0.21s linear
            transform translate3d(0,0,0)
        &.switch-enter, &.switch-leave-active
            transform translate3d(100%,0,0)
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
                    display block
                    top 10px
                    left 0
                    padding 10px
                    font-size 20px
                    color #fff
            .content-wrapper
                padding  18px
                .title
                    line-height 14px
                    margin-bottom 8px
                    font-size 14px
                    font-weight 700
                    color rgb(7,17,27)
                .contents
                    margin-bottom 18px
                    line-height 10px
                    font-size 0
                    height 10px
                    .sell-count, .rating
                        font-size 10px
                        color rgb(147,153,159)
                    .sell-count
                        margin-right 12px
                .price
                        font-weight 700
                        line-height 24px
                        .now
                            margin-right 18px
                            font-size 14px
                            color rgb(240,20,20)
                        .old
                            text-decoration line-through
                            font-size 10px
                            color rgb(147,153,159)
                .cartcontrol-wrapper
                    position absolute
                    right 12px
                    bottom 12px
                .buy
                    position absolute
                    right 18px
                    bottom 18px
                    z-index 10
                    height 24px
                    line-height 24px
                    padding 0 12px
                    box-sizing border-box
                    border-radius 12px
                    font-size 10px
                    color #fff
                    background rgb(0,160,220)
                    &.buyFade-enter-active, &.buyFade-leave-active
                        transition all 0.3s
                        opacity 1
                    &.buyFade-enter, &.buyFade-leave-active
                        opacity 0        
            .info
                padding 18px
                .title
                    line-height 14px
                    margin-bottom 6px
                    font-size 14px
                    color rgb(7,17,27)
                .text
                    line-height 24px
                    padding 0 8px
                    font-size 12px
                    color rgb(77,25,93)
            .rating
                padding-top 18px
                .title
                    line-height 14px
                    margin-left 18px
                    font-size 14px
                    color rgb(7,17,27)
            .rating-wrapper
                padding 0 18px
                .rating-item
                    position relative
                    padding 16px 0
                    border-1px(rgba(7,17,27,0.1))
                    .user
                        position absolute
                        right 0
                        top 16px
                        line-height 12px
                        font-size 0
                        .name
                            display inline-block
                            margin-right 6px
                            vertical-align top
                            font-size 10px
                            color rgb(147,153,159)
                        .avatar
                            border-radius 50%
                    .time
                        margin-bottom 6px
                        line-height 12px
                        font-size 10px
                        color rgb(147,153,159)
                    .text
                        line-height 16px
                        font-size 12px
                        color rgb(7,17,27)
                        .icon-damuzhi
                            color rgb(0, 160, 220)
                        .icon-down
                            color rgb(147, 153, 159)
                .no-rating
                    padding 16px 0
                    font-size 12px
                    color rgb(147,153,159)
</style>