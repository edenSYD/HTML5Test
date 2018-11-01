<el-dialog title="创建批次" :visible.sync="dialogVisible" :center="true" width="676px" v-if="dialogVisible" class="createBatch" @close="closeDialog" :close-on-click-modal="false">
    <div class="commonForm">
        <el-form :model="sendForm" ref="sendForm" :rules="rules" label-width="80px" size="mini" label-position="right" style="margin:0 auto;width:340px;">
            <el-form-item label="批次名称" prop="taskName" :rules="[{required:true,message:'必填项',trigger:'blur'}]">
                <el-input v-model="sendForm.taskName"></el-input>
            </el-form-item>
            <el-form-item label="智催策略" prop="ruleId" :rules="[{required:true,message:'必填项',trigger:'change'}]">
                <!-- <el-input v-model="sendForm.ruleId"></el-input> -->
                <el-select class="el-select" v-model="sendForm.ruleId" placeholder="请选择" clearable filterable>
                    <el-option v-for="item in dropDownData" :key="item.id" :label="item.name" :value="item.id"></el-option>
                </el-select>
            </el-form-item>
            <el-form-item class="prepend" label="线路数量" prop="callLines">
                <el-input v-model="sendForm.callLines"> <template slot="append">共有{{callLines}}条线路可用</template></el-input>
            </el-form-item>
            <div style="display:flex">
                <el-form-item label="时间段" prop="startTime" :rules="[{required:true,message:'必填项',trigger:'blur'}]">
                    <el-time-select class="timepicker" placeholder="起始时间" v-model="sendForm.startTime" :picker-options="{ start: '08:30', step: '00:30', end: '22:00',maxTime:sendForm.endTime}">
                    </el-time-select>
                </el-form-item>
                <el-form-item label="" prop="endTime" :rules="[{required:true,message:'必填项',trigger:'blur'}]" style="margin-left:-75px">
                    <el-time-select class="timepicker" placeholder="结束时间" v-model="sendForm.endTime" :picker-options="{ start: '08:30', step: '00:30', end: '22:00', minTime: sendForm.startTime}">
                    </el-time-select>
                </el-form-item>
            </div>
            <!-- 添加产品类型 -->
            <el-form-item label="产品类型" prop="batchType" :rules="[{required:true,message:'必填项',trigger:'change'}]">
                <el-select class="el-select" v-model="sendForm.batchType" placeholder="请选择" clearable filterable>
                    <el-option v-for="item in batchList" :key="item.id" :label="item.name" :value="item.id"></el-option>
                </el-select>
            </el-form-item>
            <el-form-item label="案件上传" prop="file" style="position:relative;" :rules="[{required:true,message:'必填项',trigger:'change'}]">
                <el-input v-model="sendForm.filename" readonly="readonly"></el-input><i class="el-icon-remove" style="color:#ccc"  @click="deleteFile"></i>
                <a href="javascript:;" class="a-upload">
                    <input type="file" name="files" @change="uploadFile" id="logo_img" title=" "  clearable>上传附件
                </a>
                <a href="javascript:;" class="template" @click="exportOut" style="position:absolute;left:180px;top:30px;">模板</a>
            </el-form-item>
            <el-form-item>
                <span style="color: #f56c6c">{{error}}</span>
            </el-form-item>
        </el-form>
    </div>
    <span slot="footer" class="dialog-footer">
        <el-button @click="closeDialog('sendForm')" class="button-cancel">取 消</el-button>
        <el-button type="primary" @click="confirmSubmit('sendForm')" class="button-submit" :disabled="preventsame">确定</el-button>
      </span>
</el-dialog>
