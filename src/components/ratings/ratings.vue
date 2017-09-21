<template>
    <div class="ratings" ref="ratings">
        <div class="ratings-content">
            <div class="overview">
                <div class="overview-left">
                    <h1 class="score">{{seller.score}}</h1>
                    <div class="title">综合评分</div>
                    <div class="rank">高于周边商家{{seller.rankRate}}%</div>
                </div>
                <div class="overview-right">
                    <div class="score-wrapper">
                        <span class="title">服务态度</span>
                        <star :size="36" :score="seller.serviceScore"></star>
                        <span class="score">{{seller.serviceScore}}</span>
                    </div>
                    <div class="score-wrapper">
                        <span class="title">商品评分</span>
                        <star :size="36" :score="seller.foodScore"></star>
                        <span class="score">{{seller.foodScore}}</span>
                    </div>
                    <div class="delivery-wrapper">
                        <span class="title">送达时间</span>
                        <span class="time">{{seller.deliveryTime}}分钟</span>
                    </div>
                </div>
            </div>
            <split></split>
            <ratingselect :select-type="selectType" :only-content="onlyContent" :ratings="ratings" @ratingRefresh="ratingRefresh" @onlyContent="only"></ratingselect>
            <div class="rating-wrapper">
                <ul v-show="ratings && ratings.length">
                    <li v-show="needShow(rating.rateType, rating.text)" v-for="rating in ratings" class="rating-item">
                        <div class="avatar">
                            <img width="28" height="28" :src="rating.avatar">
                        </div>
                        <div class="content">
                            <h1 class="name">{{rating.username}}</h1>
                            <div class="star-wrapper">
                                <star :size="24" :score="rating.score"></star>
                                <span v-show="rating.deliveryTime" class="delivery">
                                    {{rating.deliveryTime}}分钟送达
                                </span>
                            </div>
                            <p class="text">{{rating.text}}</p>
                            <div class="recommend" v-show="rating.recommend &&rating.recommend.length">
                                <i class="iconfont icon-damuzhi"></i>
                                <span class="item" v-for="item in rating.recommend">{{item}}</span>
                            </div>
                            <div class="time">
                                {{rating.rateTime | formatDate}}
                            </div>
                        </div>
                    </li>
                </ul>
                <div class="no-rating" v-show="!ratings || !ratings.length">
                    暂无评价
                </div>
            </div>
        </div>
    </div>
</template>

<script type="text/ecmascript-6">
import star from '../star/star'
import split from '../split/split.vue'
import ratingselect from '../ratingselect/ratingselect.vue'
import data from '../../common/json/data.json'
import { formatDate } from '../../common/js/date'
import BScroll from 'better-scroll'
const ALL = 2
export default {
    props: {
        seller: {
            type: Object
        }
    },
    data() {
        return {
            ratings: [],
            showFalg: false,
            selectType: ALL,
            onlyContent: false
        }
    },
    mounted() {
        this.ratings = data.ratings
        this.$nextTick(() => {
            if (!this.scroll) {
                this.scroll = new BScroll(this.$refs.ratings, {
                    click: true
                })
            } else {
                this.scroll.refresh()
            }
        })
    },
    filters: {
        formatDate(time) {
            let date = new Date(time)
            return formatDate(date, 'yyyy-MM-dd hh:mm')
        }
    },
    methods: {
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
        }
    },
    components: {
        star,
        split,
        ratingselect
    }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
@import '../../common/stylus/mixin'
.ratings
    position absolute
    top 174px
    bottom 0
    width 100%
    left 0
    overflow hidden
    .overview
        display flex
        padding 18px 0
        .overview-left
            flex 0 0 137px
            margin-top 5px
            padding 6px 0
            width 137px
            border-right 1px solid rgba(7,17,27,0.1)
            text-align center
            @media only screen and (max-width 320px)
                flex 0 0 110px
                width 110px
            .score
                margin-bottom 6px
                line-height 28px
                font-size 24px
                color rgb(255,153,0)
            .title
                margin-bottom 6px
                line-height 12px
                font-size 12px
                color rgb(7,17,27)
            .rank
                padding 6px 0
                line-height 10px
                font-size 10px
                color rgb(147,153,159)
        .overview-right
            flex 1
            padding 6px 0 6px 24px
            @media only screen and (max-width 320px)
                padding-left 6px
            .score-wrapper
                margin-bottom 8px
                line-height 18px
                font-size 0
                .title
                    display inline-block
                    vertical-align top
                    font-size 12px
                    color rgb(7,17,27)
                .star
                    display inline-block
                    vertical-align top
                    margin 0 12px
                .score
                    display inline-block
                    vertical-align top
                    font-size 12px
                    color rgb(255,153,0)
            .delivery-wrapper
                font-size 0
                .title
                    font-size 12px
                    color rgb(7,17,27)
                .time
                    margin-left 12px
                    font-size 12px
                    color rgb(147,153,159)
    .rating-wrapper
        padding 0 18px
        .rating-item
            display flex
            padding 18px 0
            border-1px(rgba(7,17,27,0.1))
            .avatar
                flex 0 0 28px
                width 28px
                margin-right 12px
                img
                    border-radius 50%
            .content
                position relative
                flex 1
                .name
                    margin-bottom 4px
                    line-height 12px
                    font-weight 700
                    font-size 10px
                    color rgb(7,17,27)
                .star-wrapper
                    margin-bottom 6px
                    font-size 0
                    .star
                        display inline-block
                        vertical-align top
                        margin-right 16px
                    .delivery
                        display inline-block
                        vertical-align top
                        font-size 10px
                        line-height 12px
                        color rgb(147,153,159)
                .text
                    margin-bottom 18px
                    line-height 18px
                    color rgb(7,17,27)
                    font-size 12px
                .recommend
                    line-height 16px
                    font-size 0
                    .iconfont, .item
                        display inline-block
                        margin 0 8px 4px 0
                        font-size 9px
                    .iconfont
                        color rgb(0,160,220)
                    .item
                        padding 0 6px
                        border 1px solid rgba(7,17,27,0.1)
                        border-radius 1px
                        color rgb(147,153,159)
                        background #fff
                .time
                    position absolute
                    top 0
                    right 0
                    line-height 12px
                    font-size 10px
                    color rgb(147,153,159)
        .no-rating
            padding 16px 0
            font-size 12px
            color rgb(147,153,159)

</style>