<template>
    <div>
        <el-popover ref="replace"
                    placement="top"
                    trigger="click"
                    v-model="show"
        >
            <p :class="s.title">单击行将直接更换</p>
            <DataTable :data="result"
                       :loading="loading"
                       :showOperate="false"
                       @rowClick="rowClick"
                       element-loading-text="拼命加载中...搜索三个平台...还要花时间去重哦~"
                       style="max-height: 300px; overflow: auto; width: 600px;"
            ></DataTable>
            <span slot="reference" :class="s.replace" @click="show = true">换</span>
        </el-popover>
    </div>
</template>
<script>

    export default {
        props: {
            info: {
                type: Object,
                default() {
                    return {}
                }
            }
        },
        data() {
            return {
                show: false,
                result: [],
                loading: false,
            }
        },
        watch: {
            show(val) {
                if (val) {
                    this.search()
                }
            }
        },
        methods: {
            async search() {
                console.log('search')
                this.loading = true
                let data = await this.$musicApi.searchSong(this.info.name)
                if (data.status) {
                    this.result = data.data.filter(item => item.id && !item.cp).map(item => {
                        return {
                            ...item,
                            commentId: item.id,
                            songId: item.id
                        }
                    })
                } else {
                    console.warn(data)
                    this.$message.warning(data.msg)
                }
                this.loading = false
            },
            rowClick(item) {
                this.$emit('replace', item)
            }
        }
    }
</script>
<style lang="scss" module="s">
    .replace {
        margin-right: -1px;
        line-height: 1;
        font-size: 14px;
        padding: 1px;
        cursor: pointer;
    }

    .title {
        padding-left: 10px;
        margin: 0;
        color: #303133;
        &::before {
            content: '*';
            font-size: 20px;
            color: #F56C6C;
            position: relative;
            top: 5px;
            margin-right: 2px;
        }
    }
</style>