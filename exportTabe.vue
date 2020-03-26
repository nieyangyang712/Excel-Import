<template>
    <div>
        <el-row :gutter="24" style="margin-bottom:20px;">
            <el-col :span="4" class="text-let">
                <el-button type="primary" size="small">
                    <a href="https://blog.csdn.net/qq_41646249/article/details/105118786">查看详细操作步骤</a>
                </el-button>
            </el-col>
            <el-col :span="20" class="text-right">
                <!--给按钮绑定事件-->
                <el-button type="primary" @click="exportExcel">点击导出</el-button>
            </el-col>
        </el-row>
        <!-- 表格导出1 -->
        <!--给表格添加一个id，导出文件事件需要使用-->
        <el-row :gutter="24" style="margin-bottom:20px;">
            <el-table :data="historyData" border style="width: 100%" id="out-table">
                <el-table-column type="index" label="序号" width="60"></el-table-column>
                <el-table-column prop="date" label="日期" width="150"></el-table-column>
                <el-table-column prop="name" label="姓名" width="150"></el-table-column>
                <el-table-column prop="province" label="省份" width="150"></el-table-column>
                <el-table-column prop="city" label="市区" width="150"></el-table-column>
                <el-table-column prop="address" label="地址"></el-table-column>
                <el-table-column prop="zip" label="邮编" width="150"></el-table-column>
                <el-table-column label="操作" width="200">
                <template slot-scope="scope">
                    <el-button type="text" @click="handleClick(scope.row)" size="small">查看</el-button>
                    <el-button type="text" size="small">编辑</el-button>
                </template>
                </el-table-column>
            </el-table>
        </el-row>
    </div>
</template>

<script>
    // 引入导出Excel表格依赖
    import FileSaver from "file-saver";
    import XLSX from "xlsx";
    export default {
    data() {
        return {
            // 导出一、
            historyData: [
            {
                date: '2016-05-02',
                name: '王小虎',
                province: '上海',
                city: '普陀区',
                address: '上海市普陀区金沙江路 1518 弄',
                zip: 200333
            }, {
                date: '2016-05-04',
                name: '王小虎',
                province: '上海',
                city: '普陀区',
                address: '上海市普陀区金沙江路 1517 弄',
                zip: 200333
            }, {
                date: '2016-05-01',
                name: '王小虎',
                province: '上海',
                city: '普陀区',
                address: '上海市普陀区金沙江路 1519 弄',
                zip: 200333
            }, {
                date: '2016-05-03',
                name: '王小虎',
                province: '上海',
                city: '普陀区',
                address: '上海市普陀区金沙江路 1516 弄',
                zip: 200333
            }],
            excelData:[],
        };
    },
    mounted() {
    },
    methods: {
        //定义导出Excel表格事件
        exportExcel() {
            /* 从表生成工作簿对象 */
            // var wb = XLSX.utils.table_to_book(document.querySelector("#out-table"));
            // /* 获取二进制字符串作为输出 */
            // var wbout = XLSX.write(wb, {
            //     bookType: "xlsx",
            //     bookSST: true,
            //     type: "array"
            // });
            // try {
            //     FileSaver.saveAs(
            //         //Blob 对象表示一个不可变、原始数据的类文件对象。
            //         //Blob 表示的不一定是JavaScript原生格式的数据。
            //         //File 接口基于Blob，继承了 blob 的功能并将其扩展使其支持用户系统上的文件。
            //         //返回一个新创建的 Blob 对象，其内容由参数中给定的数组串联组成。
            //         new Blob([wbout], { type: "application/octet-stream" }),
            //         //设置导出文件名称
            //         "导出表格.xlsx"
            //     );
            //     console.log("wbout:", wbout);
            // } catch (e) {
            //     if (typeof console !== "undefined") 
            //     console.log(e, wbout);
            // }
            // return wbout;
            this.$confirm('确定下载列表文件?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => {
                this.excelData = this.historyData //你要导出的数据list。
                this.export2Excel();
            }).catch(() => {});
        },
        export2Excel() {
            var that = this;
            require.ensure([], () => {
                const { export_json_to_excel } = require('../../../excel/Export2Excel'); //这里必须使用绝对路径，使用@/+存放export2Excel的路径
                const tHeader = ['date','name','province','city','address','zip']; // 导出的表头名信息
                const filterVal = ['date','name','province','city','address','zip']; // 导出的表头字段名，需要导出表格字段名
                const list = that.excelData;
                const data = that.formatJson(filterVal, list);
                export_json_to_excel(tHeader, data, '下载数据excel');// 导出的表格名称，根据需要自己命名
            })
        },
        formatJson(filterVal, jsonData) {
          return jsonData.map(v => filterVal.map(j => v[j]))
        },
        //查看
        handleClick(row) {
            console.log(row);
        },
    }
};
</script>

<style>
</style>
