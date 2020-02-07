<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card class="box-card">
        <el-row>
          <el-button type="info" plain size="small">{{$t('question.newadd')}}</el-button>
          <el-button type="info" plain size="small">{{$t('question.manyadd')}}</el-button>
        </el-row>
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
            <el-col :span="6">难&nbsp;&nbsp;&nbsp;&nbsp;度：
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
            <el-col :span="6">标&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;签：
              <el-select  v-model="searchForm.tags" placeholder="请选择" class="wd">
                <el-option
                v-for="item in tagNameList"
                :key="item.value"
                :value="item.value"
                :label="item.label">
                </el-option>
              </el-select>
            </el-col>
          </el-row>
          <el-row :gutter="20">
            <el-col :span="6">城市：
              <el-select v-model="searchForm.province" placeholder="请选择" style="width:98px;" @change="searchForm.city=''">
                <el-option
                v-for="(item,k) in provinces()"
                :key="k"
                :value="item"
                :label="item">
                </el-option>
              </el-select>
              <el-select v-model="searchForm.city" placeholder="请选择" style="width:98px;">
                <el-option
                 v-for="(item,k) in citys(searchForm.province)"
                :key="k"
                :value="item"
                :label="item">
                </el-option>
              </el-select>
            </el-col>
            <el-col :span="6">关键字：
              <el-input v-model="searchForm.keyword" placeholder="请输入内容" class="wd">
              </el-input>
            </el-col>
            <el-col :span="6">题目备注：
              <el-input v-model="searchForm.remarks" placeholder="请输入内容" class="wd">                
              </el-input>
            </el-col>
            <el-col :span="6">企业简称：
              <el-input v-model="searchForm.shortName" placeholder="请输入内容" class="wd">               
              </el-input>
            </el-col>
          </el-row>
          <el-row :gutter="20">
            <el-col :span="6">方向：
              <el-select v-model="searchForm.direction" class="wd">
                <el-option
                v-for="item in directionList"
                :key="item"
                :value="item"
                :label="item">
                </el-option>
              </el-select>
            </el-col>
            <el-col :span="6">录入人：
              <el-select v-model="searchForm.creatorID" class="wd">
                <el-option 
                v-for="item in createList"
                :key="item.id"
                :value="item.id"
                :label="item.username">
                </el-option>
              </el-select>
            </el-col>
            <el-col :span="6">二级目录：
              <el-input v-model="searchForm.catalogID" placeholder="请输入内容" class="wd">
              </el-input>
            </el-col>
            <el-col :span="6">
              <el-button type="primary" size="small" class="btn">搜 索</el-button>
              <el-button size="small" class="btn">清 除</el-button>
            </el-col>
          </el-row>
          <el-table :data="questionsList" style="width:100%">
            <el-table-column label="序号" type="index" width="60"></el-table-column>
            <el-table-column label="试题编号" prop="number"></el-table-column>
            <el-table-column label="学科" prop="subject"></el-table-column>
            <el-table-column :formatter="questionTypeListFMT" label="题型" prop="questionType"></el-table-column>
            <el-table-column label="题干" prop="question"></el-table-column>
            <el-table-column label="录入时间" width="170">
              <span slot-scope="stData">{{stData.row.addDate | parseTimeByString}}</span>
            </el-table-column>
            <el-table-column :formatter="difficultyListFMT" label="难度" prop="difficulty" ></el-table-column>
            <el-table-column label="录入人" prop="creator"></el-table-column>
            <el-table-column label="操作" width="200">
              <template slot-scope="stData">
                <a href="#">预览</a>
                <a href="#">修改</a>
                <a href="#" @click.prevent="del(stData.row)">删除</a>
                <a href="#">加入精选</a>
              </template>
            </el-table-column>
          </el-table>
          <el-pagination
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="searchForm.page"
            :page-sizes="[3, 5, 7, 10]"
            :page-size="100"
            layout="total, sizes, prev, pager, next, jumper"
            :total="tot">
          </el-pagination>
      </el-card>
    </div>
  </div>
</template>

<script>
import {simple} from '@/api/hmmm/subjects.js'
import {simple as tagsimple} from '@/api/hmmm/tags.js'
import {simple as createsimple} from '@/api/base/users.js'
import {provinces, citys} from '@/api/hmmm/citys.js'
import {list, remove} from '@/api/hmmm/questions.js'
import {
  difficulty as difficultyList,
  questionType as questionTypeList,
  direction as directionList
} from '@/api/hmmm/constants.js'

export default {
  name: 'QuestionsList',
  data() {
    return {
     searchForm: {
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
       page: 1,
       pagesize: 5
     },
     subjectList: [],
     difficultyList,
     questionTypeList,
     tagNameList: [],
     directionList,
     createList: [],
     questionsList: [],
     tot: 0
    }
  },
  watch: {
    searchForm: {
      handler: function(newVal) {
        this.getquestionsList()
      },
      deep: true
    }
  },
  created() {
    this.getsubjectList()
    this.gettagNameList()
    this.getcreateList()
    this.getquestionsList()
  },
  methods: {
    provinces,
    citys,
     handleSizeChange(val) {
        // console.log(`每页 ${val} 条`)
        this.searchForm.pagesize = val
      },
      handleCurrentChange(val) {
        // console.log(`当前页: ${val}`)
        this.searchForm.page = val
      },
    del(question) {
      this.$confirm('确定删除该试题吗?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(async () => {
          let res = await remove(question)
          this.$message({
            type: 'success',
            message: '删除成功!'
          })
          this.getquestionsList()
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          })          
        })     
    },
    questionTypeListFMT(row, column, cellValue, index) {
    // row:代表当前每个数据项记录信息的，是一个对象，{id:xxx,number:xx,difficulty:xx,addDate:xx……}
    //     可以通过这个row访问到当前任何数据项目记录信息,调用形式：row.id  row.number
    // column:可以获取记录的列的新，一般不使用
    // cellValue:当前行当前列的数据项目记录信息，类似 row.questionType的内容
    // index: 代表各个记录的序号信息1/2/3/4/5……，一般不使用
    return this.questionTypeList[cellValue - 1].label
    },
    difficultyListFMT(row, column, cellValue, index) {
      return this.difficultyList[cellValue - 1].label
    },
    // 获取基础题库列表
    async getquestionsList() {
      let res = await list(this.searchForm) 
      this.questionsList = res.data.items   
      this.tot = res.data.counts
    },
    // 获取录入人
    async getcreateList() {
      let res = await createsimple()
      this.createList = res.data
    },
    // 获取标签简单列表
    async gettagNameList() {
      let res = await tagsimple()
      this.tagNameList = res.data   
    },
    // 获取学科简单列表
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
.btn{
  float: right;
  margin-right: 17px;
}
.el-table {
 margin-top: 25px;
}
.el-pagination{
  margin-top:15px;
}
</style>
