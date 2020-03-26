<template>
  <div>
    <!--limit:最大上传数，on-exceed：超过最大上传数量时的操作  -->
    <el-upload
        class="upload-demo"
        action=""
        :on-change="handleChange"
        :on-remove="handleRemove"
        :on-exceed="handleExceed"
        :limit="limitUpload"
        accept="application/vnd.openxmlformats-officedocument.spreadsheetml.sheet,application/vnd.ms-excel"
        :auto-upload="false">
        <el-button size="small" type="primary">点击上传</el-button>
        <div slot="tip" class="el-upload__tip">只 能 上 传 xlsx / xls 文 件(excel表的标题必须是英文)</div>
    </el-upload>
    <!-- 数据展示 -->
      <el-main>
        <el-table :data="tabeData" border style="width: 100%">
            <el-table-column type="index" label="序号" width="60"></el-table-column>
            <el-table-column :formatter="dateFormat" prop="date" label="日期" width="150"></el-table-column>
            <el-table-column prop="name" label="姓名" width="150"></el-table-column>
            <el-table-column prop="province" label="省份" width="150"></el-table-column>
            <el-table-column prop="city" label="市区" width="150"></el-table-column>
            <el-table-column prop="address" label="地址"></el-table-column>
            <el-table-column prop="zip" label="邮编" width="150"></el-table-column>
            <el-table-column label="操作" width="200">

            </el-table-column>
        </el-table>
    </el-main>   
  </div>
</template>
<script>
import {nowTime} from '../../../filter/time.js';
import XLSX from 'xlsx'
import moment from 'moment'
  export default {
    data() {
      return {
        limitUpload:1,
        fileTemp:null,
        file:null,
        tabeData:[],
        dalen:0
      };
    },
    methods:{
        handleChange(file, fileList){
            this.fileTemp = file.raw;
            if(this.fileTemp){
                if((this.fileTemp.type == 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet') || (this.fileTemp.type == 'application/vnd.ms-excel')){
                    this.importfxx(this.fileTemp);
                } else {
                    this.$message({
                        type:'warning',
                        message:'附件格式错误，请删除后重新上传！'
                    })
                }
            } else {
                this.$message({
                    type:'warning',
                    message:'请上传附件！'
                })
            }
        },
        handleExceed(){
            this.$message({
                type:'warning',
                message:'超出最大上传文件数量的限制！'
            })
            return;
        },
        handleRemove(file,fileList){
            this.fileTemp = null
        },
        importfxx(obj) {
            let _this = this;
            let inputDOM = this.$refs.inputer;
            // 通过DOM取文件数据

            this.file = event.currentTarget.files[0];

            var rABS = false; //是否将文件读取为二进制字符串
            var f = this.file;

            var reader = new FileReader();
            //if (!FileReader.prototype.readAsBinaryString) {
            FileReader.prototype.readAsBinaryString = function(f) {
                var binary = "";
                var rABS = false; //是否将文件读取为二进制字符串
                var pt = this;
                var wb; //读取完成的数据
                var outdata;
                var reader = new FileReader();
                reader.onload = function(e) {
                    var bytes = new Uint8Array(reader.result);
                    var length = bytes.byteLength;
                    for (var i = 0; i < length; i++) {
                        binary += String.fromCharCode(bytes[i]);
                    }
                    var XLSX = require("xlsx");
                    if (rABS) {
                        wb = XLSX.read(btoa(fixdata(binary)), {
                        //手动转化
                        type: "base64"
                        });
                    } else {
                        wb = XLSX.read(binary, {
                        type: "binary",
                        cellDates: true, //读取excel，日期格式需要加上 cellDates: true  参数， el-table-column  需要显示的时候也要格式化
                        });
                    }
                    //导入成功的提示
                    _this.$message({
                        message: '请耐心等待导入成功',
                        type: 'success'
                    });
                    outdata = XLSX.utils.sheet_to_json(wb.Sheets[wb.SheetNames[0]]); //outdata就是你想要的东西
                    console.log('未处理的原始数据如下：',outdata);
                    //此处可对数据进行处理
                    let arr = [];
                    outdata.map(v => {
                        let obj = {}
                        obj.index = v['index']
                        obj.date = v['date']
                        obj.name = v['name']
                        obj.province = v['province']
                        obj.city = v['city']
                        obj.address = v['address']
                        obj.zip = v['zip']
                        arr.push(obj)
                    });
                    _this.tabeData= arr;
                    _this.dalen=arr.length;
                    return arr;
                };
                reader.readAsArrayBuffer(f);
            };
            if (rABS) {
                reader.readAsArrayBuffer(f);
            } else {
                reader.readAsBinaryString(f);
            }
        },
        dateFormat: function (row, column) {
            var date = row[column.property]
            if (date === undefined) {
                return ''
            }
            // 不管时间，都改为导入此时的时间
            var currentdate = null;
            date = nowTime(currentdate);
            return moment(date).format('YYYY-MM-DD')
        }
    }
  }
</script>
