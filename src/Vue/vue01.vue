<!-- 2018/8/17 by liulinqiang -->
<template>
    <div>
        <div class="content-body">
            <!-- 顶部，包括标题，操作按钮-->
            <div class="bd-top">
                <div class="md clearfix">
                    <!-- 1、左边标题 -->
                    <div class="md-left">
                        <h5>
                            <a>设置</a>
                            <router-link to="/setting_call" class="el-icon-arrow-right">停催规则设置</router-link>
                            <span class="el-icon-arrow-right">{{pageTitle}}</span>
                        </h5>
                    </div>
                    <!-- 2、右边操作按钮 -->
                    <div class="md-right">
                    </div>
                </div>
            </div>
            <div class="bd-line"></div>
            <div class="bd-main">
                <div class="mg_lf_50">
                    <el-form class="condition-form input_1  padding_15 edit" label-position="right" :model="data" size="small" ref="conditionForm" label-width="140px">
                        <div v-if="this.id">
                            <div class="el-col col-10">
                                <el-form-item prop="scCode	" label="停催规则编号：">
                                    {{data.scCode	}}
                                </el-form-item>
                            </div>
                        </div>
                        <div>
                            <div class="el-col col-10">
                                <el-form-item prop="scName" label="停催规则名称：" :rules="[{required:true,message: '必填项',trigger:'change'}]">
                                    <el-input v-model="data.scName" v-if="!data.scCode"></el-input>
                                    <span v-if="data.scCode">{{data.scName}}</span>
                                </el-form-item>
                            </div>
                        </div>
                        <div>
                            <div class="el-col col-10">
                                <el-form-item prop="type" label="停催规则类型：" :rules="[{required:true,message: '必选项',trigger:'change'}]" >
                                    <el-radio-group v-model="data.type" size="medium" @change="getDropdownData" :disabled="this.id" >
                                        <el-radio v-for="item in type" :label="item.code"  :key="item.id" >{{item.name}}</el-radio>
                                    </el-radio-group>
                                </el-form-item>
                            </div>
                        </div>
                        <div>
                            <div class="el-col col-10">
                                <el-form-item label="子规则设置：">
                                    <!-- <el-input v-model="data.recallPeriods"></el-input> -->
                                    <el-card shadow="hover" v-if="data.ruleSet.length>0" v-for="(item,index1) in data.ruleSet" :key="item.id" style="width: 350px;margin-bottom: 20px">
                                        <i class="el-icon-close" @click="reduceAreaRule(index1)" v-if="index1 != 0"></i>
                                        <el-row v-for="(obj,index2) in item" :key="obj.id">
                                            <el-col :span="17">
                                                <el-select v-model="obj.code">
                                                    <el-option v-for="item in dropdownData" :key="item.id" :label="item.name" :value="item.code"></el-option>
                                                </el-select>
                                                <span class="el-form-item__error" v-if="(!obj.code)&&showError&&((index1 == 0 && index2 == 0)?true: obj.exit)">必选项</span>
                                            </el-col>
                                            <el-col :span="5" style="position:relative">
                                                <el-input v-model="obj.name" class="inner_width" type="number" ></el-input>
                                                <span class="el-form-item__error" style="padding-left:10px" v-if="(!obj.name || obj.name === 0) && ((index1==0 && index2 ==0)?true: showError) && obj.exit">必选项</span>
                                            </el-col>
                                            <el-col :span="2" class="colorDark right">
                                                <i class="el-icon-remove" @click="reduceSingleRule(index1,index2)" v-if="!(index1 == 0 && index2 == 0)"></i>
                                            </el-col>
                                        </el-row>
                                        <el-row>
                                            <el-button class="el-icon-circle-plus normal" @click="addSingleRule(index1)" ></el-button>
                                        </el-row>
                                    </el-card>
                                    <el-card class="center addRule" >
                                        <span @click="addAreaRule">添加子规则</span>
                                    </el-card>
                                </el-form-item>
                                <div class="dialog_footer">
                                    <el-button class="button-submit" @click="submit">确认</el-button>
                                </div>
                            </div>
                        </div>
                    </el-form>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    export default {
        data() {
            return {
                id: '',
                data: {
                    ruleSet:[
                        [{}]
                    ],
                },
                showError: false,
                dropdownData: {},
                type: []
            }
        },
        methods: {
            // 获取规则接口
            getList() {
                this.$axios.post("/api/robot/speech/queryStopRuleById", {
                    id: this.id
                }).then(res => {
                    if (res.data.code == 0) {
                        this.data = res.data.data;
                        this.data.type?this.data.type = this.data.type.toString():'';
                        this.getDropdownData(this.data.type)
                    } else {
                        this.$util.failCallback(res.data, this);
                    }
                }).catch(err => {
                    console.log(err)
                })
            },
            // 获取类型
            getType() {
                this.$axios.post("/api/setting/stop/stopTypes",{}).then(res => {
                    if (res.data.code == 0) {
                        this.type = res.data.data;
                    } else {
                        this.$util.failCallback(res.data, this);
                    }
                }).catch(err => {
                    console.log(err)
                })
            },
            // 提交
            submit() {
                var errLen = 0;
                this.showError = true;
                this.data.ruleSet.forEach( (item) => {
                    if(item.length ==0) {errLen = 1;}
                    item.forEach( (ele)=> {
                        ele.exit = true
                        if((ele.name == '' || ele.name == null) && (ele.name !== 0)){
                            errLen = 1;
                        }
                        if(ele.code == '' || ele.code == null) {
                            errLen = 1;
                        }
                    })
                })
                this.$refs.conditionForm.validate((valid) => {
                    if(errLen ) return false;
                    if (valid) {
                        var url;
                        if (this.id) {
                            url = "/api/setting/stop/update";
                        } else {
                            url = "/api/setting/stop/create";
                        }
                        this.$axios.post(url, this.data).then(res => {
                            if (res.data.code == 0) {
                                this.$message.success('提交成功');
                                this.$refs.conditionForm.resetFields();
                                this.data.ruleSet = [[{}]];
                                this.showError = false;
                                localStorage.setItem('refreshListStop',true)
                                this.data.scCode = '';
                                window.close();
                            } else {
                                this.$util.failCallback(res.data, this);
                            }
                            this.loading = false
                        }).catch(err => {
                            console.log(err)
                        })
                    }
                })
            },
            // 获取下拉条件
            getDropdownData(data) {
                let formData = new FormData();
                formData.append('type',data)
                this.$axios.post("/api/setting/stop/ruleConditions",formData).then(res => {
                    if (res.data.code == 0) {
                        this.dropdownData = res.data.data;
                    } else {
                        this.$util.failCallback(res.data, this);
                    }
                    this.loading = false
                }).catch(err => {
                    console.log(err)
                })
            },
            // 增加区域规则
            addAreaRule() {
                this.data.ruleSet.push([{exit:false}])
            },
            // 添加单个规则
            addSingleRule(index1) {
                this.data.ruleSet[index1].push({exit:false})
            },
            // 删除整个区域
            reduceAreaRule(index1) {
                if(this.data.ruleSet.length>0) this.data.ruleSet.splice(index1,1)
            },
            // 删除单个区域
            reduceSingleRule(index1,index2) {
                if(this.data.ruleSet[index1].length>0) this.data.ruleSet[index1].splice(index2,1);
                if(this.data.ruleSet[index1].length == 0) this.data.ruleSet.splice(index1,1);
            }
        },
        created() {
            this.id = this.$route.query.id;
            this.getType();
            if (this.id) {
                this.pageTitle = '修改停催规则';
                this.getList();
            } else {
                this.pageTitle = '创建停催规则'
            }

        }
    };

</script>
<style lang="scss" scoped>
    .mg_lf_50 {
        margin-left: 20px;
    }

    .unit {
        color: rgb(51, 51, 51);
        padding: 0 5px;
    }

    .condition-form .el-form-item__label {
        padding: 0 20px 0 0 !important;
    }


    .el-row+.el-row {
        margin-top: 10px;
    }
    .el-card {
        display: inline-block;
        vertical-align: top;
        margin-right: 20px;
        color: #666666;
        position: relative;
    }
    .el-icon-circle-plus {
        width:284px;
        height:32px;
        border-radius:4px;
        border:1px dashed rgba(204,204,204,1);
        font-size: 18px;
        line-height: 14px;
        color:rgba(123,165,247,1);
        margin-top: 5px;
        &:hover {
            background: #ffffff;
            border-style: solid
        }
    }
    .addRule {
        line-height: 120px;
        color: #409eff;
        cursor: pointer;
        text-decoration: underline
    }
    .el-icon-close {
        position: absolute;
        display: block;
        right:5px;
        top: 5px;
        transition: all;
        &:hover {
            transform:rotate(360deg);
            transition:transform 1.5s linear;
        }
    }
    .el-icon-close {
        color: #f86422
    }
    .dialog_footer {
        margin-left: 140px
    }


</style>
