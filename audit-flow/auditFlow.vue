<template>
    <bh-step-flow class='wec-audit-flow'>
        <template v-if='nodeList.length > 0'>
            <bh-flow-node v-for="item in nodeList" :tag="item.status" :num="$index + 1" :title='item.title' :class-name="item.tagClass">
                <div class="bh-mv-8">
                    <span class="bh-text-caption bh-color-caption">审核时间</span>
                    <span class="bh-text-caption bh-color-default bh-mh-4">{{item.shsj}}</span>
                </div>
                <div class="bh-mb-8">
                    <span class="bh-text-caption bh-color-caption">审核人</span>
                    <span class="bh-text-caption bh-color-default    bh-mh-4">{{item.shr}}</span>
                </div>
                <div>
                    <span class="bh-text-caption bh-color-caption">审核意见</span>
                    <span class="bh-text-caption bh-color-default    bh-mh-4">{{item.shyj}}</span>
                </div>
            </bh-flow-node>
        </template>
        <div v-else>
            当前无审核进度
        </div>
    </bh-step-flow>
</template>
<script>
    /**
     * @module aduitPro
     * 合同审核进度条，需要其url,
     *
     *
     * @example
     * <flow-state :url="url" ></flow-state>
     *
     */
    import bhStepFlow from 'bh-vue/bh-step-flow/bhStepFlow.vue'
    import bhFlowNode from 'bh-vue/bh-flow-node/bhFlowNode.vue'
    import {postJson, handler} from 'bh-vue/utils/http'

    export default{
        data: () => {
            return {
                nodeList: []
            }
        },
        props: {
            url: String,
            wid:String, // 合同 id
            tmId: String // 条目 id
        },
        components: {bhStepFlow, bhFlowNode},
        computed: {},
        created () {
            postJson(this.url, {wid: this.wid, tmwid: this.tmId}, handler.DATAS).then((data) => {
                let rows = data.rows;

                rows.forEach((item) => {
                    if (item.shzt === 0) {
                        item.status = '未审核'
                        item.tagClass = 'warning'
                    } else if (item.shzt === 1) {
                        item.status = '通过'
                        item.tagClass = 'success'
                    } else if (item.shzt === 2) {
                        item.status = '不通过'
                        item.tagClass = 'danger'
                    } else if (item.shzt === 4) {
                        item.status = '退回'
                        item.tagClass = 'danger'
                    }
                });

                this.nodeList = rows;
            })
        }
    }
</script>

<style>
    .wec-audit-flow .scenes-cbrt-title {
        overflow: hidden;
        text-overflow: ellipsis;
        white-space: nowrap;
        width: calc(100% - 30px);
    }
</style>
