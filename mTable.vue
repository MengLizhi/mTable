<style scoped>
.m-table {
    position: relative;
    width: 100%;
    border-collapse: collapse;
    border-spacing: 0;
    
}
.m-table > thead > tr > th {
    border: 1px solid #c3c3c3;
    overflow: hidden;
}
.m-table > thead > tr > th > div {
    padding: 4px 16px;
    text-align: left;
    white-space: nowrap;
    text-overflow: ellipsis;
    overflow: hidden;
}
.m-table > tbody > tr > td {
    display: table-cell;
    border: 1px solid #c3c3c3;

}
.m-table > tbody > tr > td > div {
    padding: 4px 16px;
    text-align: left;
    white-space: nowrap;
    text-overflow: ellipsis;
    overflow: hidden;
}
.m-table-fixed {
    position: absolute;
    top: 0px;
    left: 0px;
    width: 100%;
    border-collapse: collapse;
    border-spacing: 0;
    
}
.m-flex {
    display: flex;
    flex-direction: row;
    align-items: center;
}

</style>
<template>
    <div>
        <table class="m-table-fixed">
            <thead>
                <tr>
                    <th>
                        <div style="min-width: 60px;" class="m-flex">test</div>
                    </th>
                    <!-- <th v-for="(item, index) in table.title" :key="item.key">
                        <div :style="{
                                minWidth:item.minWidth ? item.minWidth +'px' : '20px',
                                width: item.width + 'px'
                            }"
                            class="m-flex"
                            @mousemove="theadMove(item, index, $event)" 
                            @mouseleave="theadLeave"
                            @mousedown="theadDown"
                            @mouseup="theadUp">
                            {{item.name}}
                        </div>
                    </th> -->
                </tr>
            </thead>
            <tbody>
                <tr>
                    
                </tr>
                <!-- <tr v-for="(column, cIndex) in table.list" :key="`m-c-${cIndex}`">
                    <template v-for="(row, rIndex) in column" >
                        <td v-if="table.title[rIndex]" :key="`m-r-${rIndex}`" >
                            <div :style="{
                                    minWidth: table.title[rIndex].minWidth ? table.title[rIndex].minWidth + 'px' : '20px',
                                    width: table.title[rIndex].width + 'px'
                                }">
                                {{row}}
                            </div>
                        </td>
                    </template>
                </tr> -->
            </tbody>
        </table>
        <table ref="mTable" class="m-table">
            <thead>
                <tr>
                    <th v-for="(item, index) in table.title" :key="item.key">
                        <div :style="{
                                minWidth:item.minWidth ? item.minWidth +'px' : '20px',
                                width: item.width + 'px'
                            }"
                            class="m-flex"
                            @mousemove="theadMove(item, index, $event)" 
                            @mouseleave="theadLeave"
                            @mousedown="theadDown"
                            @mouseup="theadUp">
                            {{item.name}}
                        </div>
                    </th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(column, cIndex) in table.list" :key="`m-c-${cIndex}`">
                    <template v-for="(row, rIndex) in column" >
                        <td v-if="table.title[rIndex]" :key="`m-r-${rIndex}`" >
                            <div :style="{
                                    minWidth: table.title[rIndex].minWidth ? table.title[rIndex].minWidth + 'px' : '20px',
                                    width: table.title[rIndex].width + 'px'
                                }">
                                {{row}}
                            </div>
                        </td>
                    </template>
                    
                </tr>
            </tbody>
            
        </table>

    </div>
</template>
<script>
import { pagePath } from "../../router";
export default {
    name:'mTable',
    props:{
        title: Object,
        list: Array,
    },
    data() {
        return {
            table: {
                resizeFlag: false,
                initial: {
                    x: 0,
                    y: 0,
                    width: 0
                },
                title: {},
                list: []
            },
            test:{}
        }
    },
    watch: {
        'title': function (val) {
            console.log(val);
            let _title = JSON.parse(JSON.stringify(val))
            for (const key in _title) {
                if (_title.hasOwnProperty(key)) {
                    _title[key].width = 0
                    this.$set(this.table.title, key, _title[key])
                    for (const itemKey in _title[key]) {
                        this.$set(this.table.title[key], itemKey, _title[key][itemKey])
                    }
                }
            }
        },
        'list': function (val) {
            this.table.list = this.list
            this.$set(this.table, 'list', val)
        }
    },
    created(){
        let _title = JSON.parse(JSON.stringify(this.title))
        for (const key in _title) {
            if (_title.hasOwnProperty(key)) {
                _title[key].width = 0
                this.$set(this.table.title, key, _title[key])
                for (const itemKey in _title[key]) {
                    this.$set(this.table.title[key], itemKey, _title[key][itemKey])
                }
            }
        }
        this.$set(this.table, 'list', this.list)
    },
    methods:{
        // 表头鼠标移入
        theadMove(item, index, data){
            // 计算鼠标是否到达表格右边界            
            if(data.path[0].clientWidth - (data.layerX - data.path[0].clientLeft) < 10){
                data.path[0].style['cursor'] = 'col-resize'
            }

            if(this.table.resizeFlag){
                item.width = data.layerX  < item.minWidth ? item.minWidth : data.layerX - 20
            }
        },
        // 表头鼠标移出
        theadLeave(data){
            data.path[0].style['cursor'] = 'auto'
            this.table.resizeFlag = false
        },
        // 表头鼠标按下
        theadDown(data){
            this.$refs.mTable.style['user-select'] = 'none'
            this.table.resizeFlag = true
            this.table.initial.x = data.layerX
            this.table.initial.y = data.layerY
        },
        // 表头鼠标抬起
        theadUp(data){
            this.table.resizeFlag = false
            this.$refs.mTable.style['user-select'] = 'auto'
            data.path[0].style['cursor'] = 'auto'
        }
    }
}
</script>
