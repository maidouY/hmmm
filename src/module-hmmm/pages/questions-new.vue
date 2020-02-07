<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card class="box-card">
        <el-form ref="addFormRef" :model="addForm" label-width="80px">
          <el-form-item label="学科：">
            <el-select v-model="addForm.subjectID">
              <el-option
              v-for="item in subjectList"
              :key="item.value"
              :value="item.value"
              :label="item.label">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="目录：">
            <el-select v-model="addForm.catalogID">
              <el-option
              v-for="item in catalogList"
              :key="item.value"
              :value="item.value"
              :label="item.label">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="企业：">
            <el-select v-model="addForm.enterpriseID">
              <el-option
              v-for="item in enterpriseList"
              :key="item.id"
              :value="item.id"
              :label="item.shortName">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="城市：">
            <el-select v-model="addForm.province" @change="addForm.city=''">
              <el-option
              v-for="(item,k) in provinces()"
              :key="k"
              :value="item"
              :label="item">
              </el-option>
            </el-select>
            <el-select v-model="addForm.city">
              <el-option
              v-for="(item,k) in citys(addForm.province)"
              :key="k"
              :value="item"
              :label="item">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="方向：">
            <el-select v-model="addForm.direction">
              <el-option
              v-for="item in directionList"
              :key="item"
              :value="item"
              :label="item">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item  label="题型：">
            <el-radio-group v-model="addForm.questionType">
              <el-radio
              v-for="item in questionTypeList"
                :key="item.value"
                :value="item.value"
                :label="item.value+''">
                {{item.label}}             
              </el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item  label="难度：">
            <el-radio-group v-model="addForm.difficulty">
              <el-radio
              v-for="item in difficultyList"
                :key="item.value"
                :value="item.value"
                :label="item.value+''">
                {{item.label}}             
              </el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="题干：">
            <el-input type="textarea" v-model="addForm.question"></el-input>
          </el-form-item>
          <el-form-item label="选项：" v-if="anyShow">
            <template v-if="radioShow">
              <el-radio :label="0" v-model="singleSelect">
                A: <el-input type="text" class="xx" v-model="addForm.options[0]['title']"></el-input>
              </el-radio><br />
              <el-radio :label="1" v-model="singleSelect">
                B: <el-input type="text" class="xx" v-model="addForm.options[1]['title']"></el-input>
              </el-radio><br />
              <el-radio :label="2" v-model="singleSelect">
                C: <el-input type="text" class="xx" v-model="addForm.options[2]['title']"></el-input>
              </el-radio><br />
              <el-radio :label="3" v-model="singleSelect">
                D: <el-input type="text" v-model="addForm.options[3]['title']" ></el-input>
              </el-radio>
            </template>
            <template v-else>
              <el-checkbox v-model="addForm.options[0].isRight">
                A: <el-input type="text" class="xx" v-model="addForm.options[0]['title']"></el-input>
              </el-checkbox><br />
              <el-checkbox v-model="addForm.options[1].isRight">
                B: <el-input type="text" class="xx" v-model="addForm.options[1]['title']"></el-input>
              </el-checkbox><br />
              <el-checkbox v-model="addForm.options[2].isRight">
                C: <el-input type="text" class="xx" v-model="addForm.options[2]['title']"></el-input>
              </el-checkbox><br />
              <el-checkbox v-model="addForm.options[3].isRight">
                D: <el-input type="text" v-model="addForm.options[3]['title']" ></el-input>
              </el-checkbox>
            </template>
          </el-form-item>
          <el-form-item label="答案：">
            <el-input type="textarea" v-model="addForm.answer"></el-input>
          </el-form-item>
          <el-form-item label="备注：">
            <el-input type="textarea" v-model="addForm.remarks"></el-input>
          </el-form-item>
          <el-form-item label="标签：">
            <el-input type="text" v-model="addForm.tags"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="addquestion()">提交</el-button>
          </el-form-item>
        </el-form>
      </el-card>
    </div>
  </div>
</template>

<script>
import {simple} from '@/api/hmmm/subjects.js'
import {simple as catalogList} from '@/api/hmmm/directorys.js'
import {list as enterpriseList} from '@/api/hmmm/companys.js'
import {provinces, citys} from '@/api/hmmm/citys.js'
import {add} from '@/api/hmmm/questions.js'
import {
  direction as directionList,
  questionType as questionTypeList,
  difficulty as difficultyList
  } from '@/api/hmmm/constants.js'

export default {
  name: 'QuestionsNew',
  data() {
    return {
      addForm: {
        subjectID: '',
        catalogID: '',
        enterpriseID: '',
        city: '',
        province: '',
        direction: '',
        questionType: '1',
        difficulty: '2',
        question: '',       
        options: [
          {code: 'A', title: '', img: '', isRight: false},
          {code: 'B', title: '', img: '', isRight: false},
          {code: 'C', title: '', img: '', isRight: false},
          {code: 'D', title: '', img: '', isRight: false}
        ],
        videoURL: 'http://www.xxx.com', // 解析视频
        answer: '',
        remarks: '',
        tags: ''
      },
      subjectList: [],
      catalogList: [],
      enterpriseList: [],
      directionList,
      questionTypeList,
      difficultyList,
      singleSelect: '',
      radioShow: true, // 默认显示单选项目
      anyShow: true // 单选或多选默认显示一个
    }
  },
  watch: {
    'addForm.questionType': function(newV) {
      if (newV === '1') {
        this.anyShow = true
        this.radioShow = true
      } else if (newV === '2') {
        this.anyShow = true
        this.radioShow = false
      } else {
        this.anyShow = false
      }
    },
    singleSelect(newV) {
      for (var i = 0; i < 4; i++) {
        this.addForm.options[i].isRight = false
      }
      this.addForm.options[newV].isRight = true
    }
  },
  created() {
    this.getsubjectList()
    this.getcatalogList()
    this.getenterpriseList()
  },
  methods: {
    provinces,
    citys,
    // 添加试题
    async addquestion() {
      await add(this.addForm)
      this.$message.success('添加试题成功')
      this.$router.push('/questions/list')
    },
    // 获取企业列表
    async getenterpriseList() {
      let res = await enterpriseList()
      this.enterpriseList = res.data.items
    },
    // 获取简单目录列表
    async getcatalogList() {
      let res = await catalogList()
      this.catalogList = res.data
    },
    // 获取简单学科列表
    async getsubjectList() {
      let res = await simple()     
      this.subjectList = res.data
    }
  }
}
</script>

<style scoped>
.xx {
  margin-bottom: 5px;
}
</style>
