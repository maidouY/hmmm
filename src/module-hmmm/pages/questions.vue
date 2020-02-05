<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card class="box-card">
          <el-row :gutter="20">
            <el-col :span="6">学科：
              <el-select v-model="searchForm.subjectID" placeholder="请选择" class="wd">
                <el-option
                v-for="item in subjectList"
                :key="item.value"
                :value="item.value"
                :label="item.label">
                </el-option>
              </el-select>
            </el-col>
            <el-col :span="6">难度：
              <el-select v-model="searchForm.difficulty" placeholder="请选择" class="wd">
                <el-option
                v-for="item in difficultyList"
                :key="item.value"
                :value="item.value"
                :label="item.label">
                </el-option>
              </el-select>
            </el-col>
            <el-col :span="6">试题类型：
              <el-select v-model="searchForm.questionType" placeholder="请选择" class="wd">
                <el-option
                v-for="item in questionTypeList"
                :key="item.value"
                :value="item.value"
                :label="item.label">
                </el-option>
              </el-select>
            </el-col>
            <el-col :span="6">
              444
            </el-col>
          </el-row>
      </el-card>
    </div>
  </div>
</template>

<script>
import {simple} from '@/api/hmmm/subjects.js'
import {
  difficulty as difficultyList,
  questionType as questionTypeList
} from '@/api/hmmm/constants.js'

export default {
  name: 'QuestionsList',
  data() {
    return {
     searchForm: {
       subjectID: '',
       difficulty: '',
       questionType: ''
     },
     subjectList: [],
     difficultyList,
     questionTypeList
    }
  },
  created() {
    this.getsubjectList()
  },
  methods: {
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
</style>
