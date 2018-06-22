<template>
    <div>
        <yd-mask v-model="show" @click.native="close" :opacity="maskerOpacity"></yd-mask>
        <div class="yd-actionsheet" :class="show ? 'yd-actionsheet-active' : ''">
            <a v-for="item, key in items" @click.stop="itemClick(item)" href="javascript:;" class="yd-actionsheet-item" :key="key">{{item.label}}</a>
            <a v-if="cancel" @click.stop="close" href="javascript:;" class="yd-actionsheet-action">{{cancel}}</a>
        </div>
    </div>
</template>

<script type="text/babel">
    import {isIOS, pageScroll} from '../../../utils/assist';
    import Mask from '../../mask/src/mask.vue';

    export default {
        name: 'yd-actionsheet',
        components: {
            'yd-mask': Mask
        },
        data() {
            return {
                //单向数据流https://vuejs.org/v2/guide/components.html#One-Way-Data-Flow
                //每次更新父组件时，子组件中的所有道具都将刷新为最新值。这意味着你应该不是试图突变子组件内的道具。如果你这样做，Vue会在控制台中发出警告。
                show: this.value
            }
        },
        props: {
            value: {
                type: Boolean,
                default: false
            },
            items: {
                type: Array,
                required: true
            },
            cancel: String,
            maskerOpacity: {
                validator(val) {
                    return /^([0]|[1-9]\d*)?(\.\d*)?$/.test(val);
                },
                default: .5
            }
        },
        watch: {
            value(val) {
                if (isIOS) {
                    val ? pageScroll.lock() : pageScroll.unlock();
                }
                this.show = val;
            }
        },
        methods: {
            itemClick(item) {
                if(item) {
                    typeof item.callback === 'function' && item.callback(item);
                    !item.stay && this.close();
                }
            },
            close() {
            //https://www.vuefe.cn/v2/guide/components.html#自定义事件
               this.$emit('input', false);
            }
        },
        destroyed() {
            this.close();
            pageScroll.unlock();
        }
    }
</script>

<style lang="less">
    @import "../../../styles/components/actionsheet.less";
</style>
