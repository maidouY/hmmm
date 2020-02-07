<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card class="box-card">
        <el-row>
          <el-button type="info" plain size="small">新增试题</el-button>
          <el-button type="info" plain size="small">批量导入</el-button>
        </el-row>
        <el-row :gutter="20">
          <el-col :span="6">学&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;科：
            <el-select v-model="choiceForm.subjectID" placeholder="请选择" class="wd">
              <el-option
              v-for="item in subjectList"
              :key="item.value"
              :value="item.value"
              :label="item.label">
              </el-option>
            </el-select>
          </el-col>
          <el-col :span="6">状&nbsp;&nbsp;&nbsp;&nbsp;态：
            <el-select  v-model="choiceForm.chkState" placeholder="请选择" class="wd">
              <el-option>
              </el-option>
            </el-select>
          </el-col>
          <el-col :span="6">难&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;度：
            <el-select v-model="choiceForm.difficulty" placeholder="请选择" class="wd">
              <el-option
              v-for="item in difficultyList"
              :key="item.value"
              :value="item.value"
              :label="item.label">
              </el-option>
            </el-select>
          </el-col>
          <el-col :span="6">试题类型：
            <el-select v-model="choiceForm.questionType" placeholder="请选择" class="wd">
              <el-option
              v-for="item in questionTypeList"
              :key="item.value"
              :value="item.value"
              :label="item.label">
              </el-option>
            </el-select>
          </el-col>
        </el-row>
        <el-row :gutter="20">
          <el-col :span="6">标&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;签：
            <el-select  v-model="choiceForm.tags" placeholder="请选择" class="wd">
              <el-option
              v-for="item in tagNameList"
              :key="item.value"
              :value="item.value"
              :label="item.label">
              </el-option>
            </el-select>
          </el-col>         
          <el-col :span="6">录入人：
            <el-select v-model="choiceForm.creatorID" class="wd">
              <el-option 
              v-for="item in createList"
              :key="item.id"
              :value="item.id"
              :label="item.username">
              </el-option>
            </el-select>
          </el-col>
          <el-col :span="6">关&nbsp;&nbsp;键&nbsp;&nbsp;字：
            <el-input v-model="choiceForm.keyword" placeholder="请输入内容" class="wd">
            </el-input>
          </el-col>
          <el-col :span="6">题目备注：
            <el-input v-model="choiceForm.remarks" placeholder="请输入内容" class="wd">                
            </el-input>
          </el-col>           
        </el-row>
        <el-row :gutter="20">
          <el-col :span="6">二级目录：
            <el-input v-model="choiceForm.catalogID" placeholder="请输入内容" class="wd">
            </el-input>
          </el-col>
          <el-col :span="6">城&nbsp;&nbsp;&nbsp; 市：
            <el-select v-model="choiceForm.province" placeholder="请选择" style="width:98px;" @change="choiceForm.city=''">
              <el-option
              v-for="(item,k) in provinces()"
              :key="k"
              :value="item"
              :label="item">
              </el-option>
            </el-select>
            <el-select v-model="choiceForm.city" placeholder="请选择" style="width:98px;">
              <el-option
                v-for="(item,k) in citys(choiceForm.province)"
              :key="k"
              :value="item"
              :label="item">
              </el-option>
            </el-select>
          </el-col>
          <el-col :span="6">企业简称：
            <el-input v-model="choiceForm.shortName" placeholder="请输入内容" class="wd">               
            </el-input>
          </el-col>
          <el-col :span="6">方&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;向：
            <el-input v-model="choiceForm.direction" placeholder="请输入内容" class="wd">               
            </el-input>
          </el-col>           
        </el-row>
        <el-table :data="questionschoice" style="width:100%">
          <el-table-column label="序号" type="index" width="60"></el-table-column>
          <el-table-column label="试题编号" prop="number"></el-table-column>
          <el-table-column label="学科" prop="subject"></el-table-column>
          <el-table-column :formatter="questionTypeListFMT" label="题型" prop="questionType"></el-table-column>
          <el-table-column label="题干" prop="question"></el-table-column>
          <el-table-column label="录入时间" width="170">
            <span slot-scope="stData">{{stData.row.addDate | parseTimeByString}}</span>
          </el-table-column>
          <el-table-column label="录入人" prop="creator"></el-table-column>
          <el-table-column :formatter="difficultyListFMT" label="难度" prop="difficulty" ></el-table-column>
          <el-table-column label="使用次数" prop="number"></el-table-column>
          <el-table-column label="审核状态" prop="number"></el-table-column>
          <el-table-column label="审核意见" prop="number"></el-table-column>
          <el-table-column label="审核人" prop="number"></el-table-column>
          <el-table-column label="发布状态" prop="number"></el-table-column>
          <el-table-column label="操作" width="200">
              <a href="#">预览</a>
              <a href="#">修改</a>
              <a href="#">删除</a>
              <a href="#">加入精选</a>
          </el-table-column>
        </el-table>
      </el-card>
    </div>
  </div>
</template>

<script>
import {simple} from '@/api/hmmm/subjects.js'
import {simple as tagName} from '@/api/hmmm/tags.js'
import {simple as createsimple} from '@/api/base/users.js'
import {provinces, citys} from '@/api/hmmm/citys.js'
import {choice} from '@/api/hmmm/questions.js'
import {
  difficulty as difficultyList,
  questionType as questionTypeList
  } from '@/api/hmmm/constants.js'
export default {
  name: 'QuestionsChoice',
  data() {
    return {
      choiceForm: {
        subjectID: '',
        difficulty: '',
        questionType: '',
        tags: '',
        province: '',
        city: '',
        keyword: '',
        remarks: '',
        shortName: '',
        direction: '',
        creatorID: '',
        catalogID: '',
        chkState: ''
      },
      subjectList: [],
      difficultyList,
      questionTypeList,
      tagNameList: [],
      createList: []
    }
  },
  created() {
    this.getsubjectList()
    this.gettagNameList()
    this.getcreateList()
  },
  methods: {
    provinces,
    citys,
    // 获取录入人
    async getcreateList() {
      let res = await createsimple()
      this.createList = res.data
    },
    async gettagNameList() {
      let res = await tagName()
      this.tagNameList = res.data
    },
    async getsubjectList() {
      let res = await simple()
      this.subjectList = res.data
    }
  }
}
</script>

<style scoped>
.wd{
  width: 200px;
}
.el-row{
  margin-bottom: 10px;
}
.el-button{
  margin-right: 10px;
}
.el-table {
 margin-top: 25px;
}
</style>
