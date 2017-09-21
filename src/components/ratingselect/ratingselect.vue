<template>
    <div class="ratingselect">
        <div class="rating-type border-1px">
            <span @click="select(2, $event)" class="block positive" :class="{'active':DselectType===2}">{{Ddesc.all}}
                <span class="count">{{ratings.length}}</span>
            </span>
            <span @click="select(0, $event)" class="block positive" :class="{'active':DselectType===0}">{{Ddesc.positive}}
                <span class="count">{{positives.length}}</span>
            </span>
            <span @click="select(1, $event)" class="block negative" :class="{'active':DselectType===1}">{{Ddesc.negative}}
                <span class="count">{{negatives.length}}</span>
            </span>
        </div>
        <div @click="toggleShow" class="swith" :class="{'on':DonlyContent}">
            <span class="iconfont icon-gou"></span>
            <span class="text">只看有内容的评价</span>
        </div>
    </div>
</template>

<script type="text/ecmascript-6">
const POSITIVE = 0
const NEGATIVE = 1
const ALL = 2
export default {
    props: {
        ratings: {
            type: Array,
            default() {
                return []
            }
        },
        selectType: {
            type: Number,
            default: ALL
        },
        onlyContent: {
            type: Boolean,
            default: false
        },
        desc: {
            type: Object,
            default() {
                return {
                    all: '全部',
                    positive: '满意',
                    negative: '不满意'
                }
            }
        }
    },
    data() {
        return {
            Dratings: this.ratings,
            DselectType: this.selectType,
            DonlyContent: this.onlyContent,
            Ddesc: this.desc
        }
    },
    computed: {
        positives() {
            return this.ratings.filter((rating) => {
                return rating.rateType === POSITIVE
            })
        },
        negatives() {
            return this.ratings.filter((rating) => {
                return rating.rateType === NEGATIVE
            })
        }
    },
    methods: {
        select(type, event) {
            if (!event._constructed) {
                return
            }
            this.DselectType = type
            this.$emit('ratingRefresh', type)
        },
        toggleShow(event) {
            if (!event._constructed) {
                return
            }
            this.DonlyContent = !this.DonlyContent
            this.$emit('onlyContent', this.DonlyContent)
        }
    }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
.ratingselect
    .rating-type
        padding 18px 18px
        border-1px(rgba(7, 17, 27, 0.1))
        font-size 0
        .block
            display inline-block
            padding 8px 12px
            margin-right 8px
            line-height 16px
            border-radius 1px
            font-size 12px
            color rgb(77,85,93)
            &.active
                color #fff
            &.positive
                background rgba(0,160,220,0.2)
                &.active
                    background rgb(0,160,220)
            &.negative
                background rgba(77,85,93,0.2)
                &.active
                    background rgb(77,85,93)
            .count
                font-size 8px
                margin-left 2px
    .swith
        padding 12px 18px
        line-height 24px
        border-bottom 1px solid rgba(7,17,27,0.1)
        color rgb(147,153,159)
        &.on
            .icon-gou
                color #00c850
        .icon-gou
            display inline-block
            vertical-align top
            font-size 20px
        .text
            font-size 14px
    
</style>