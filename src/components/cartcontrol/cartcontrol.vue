<template>
    <div class="cartcontrol">
        <transition name="out">
            <div class="cart-decrease" v-show="food.count>0" @click.stop.prevent="decreaseCart">
                <transition name="inner">
                    <span class="inner iconfont icon-jian"></span>
                </transition>
            </div>
        </transition>
        <div class="cart-count " v-show="food.count>0">{{food.count}}</div>
        <div class="cart-add iconfont icon-jia" @click.stop.prevent="addCart($event)"></div>
    </div>
</template>

<script type="text/ecmascript-6">
import Vue from 'vue'
export default {
    props: {
        food: {
            type: Object
        }
    },
    data() {
        return {
        }
    },
    methods: {
        addCart(event) {
            if (!event._constructed) {
                return
            }
            if (!this.food.count) {
                Vue.set(this.food, 'count', 1)
                this.food.count = 1
            } else {
                this.food.count++
            }
            this.$emit('cartadd', event.target)
        },
        decreaseCart() {
            if (!event._constructed) {
                return
            }
            if (this.food.count) {
                this.food.count--
            }
        }
    },
    components: {
    }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
.cartcontrol
    font-size 0
    .cart-decrease, .cart-add
        display inline-block
        padding 6px
        line-height 24px
        font-size 22px
        color rgb(0,160,220)   
    .cart-decrease
        display inline-block
        padding 6px
        transform rotate(180deg)
        &.out-enter-active, &.out-leave-active
            transition all 0.4s linear
        &.out-enter, &.out-leave-active
            opacity 0
            transform translate3d(24px,0,0)
        &.inner-enter-active, &.inner-leave-active
            opacity 0
        .inner
            line-height 24px
            font-size 22px
            color rgb(0,160,220)
    .cart-count
        display inline-block
        vertical-align top
        width 12px
        padding-top 6px
        line-height 24px
        text-align center
        font-size 10px
        color rgb(147,153,159)
    .cart-add
        display inline-block
</style>