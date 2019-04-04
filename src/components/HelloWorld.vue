<template>
  <div class="form_wapper">
    <el-card class="box-card">
      <div
        slot="header"
        class="clearfix"
      >
        <span>动态校验</span>
      </div>
      <el-button
        @click="addNewWay"
        size="mini"
        class="new_btn"
        type="primary"
        plain
      > + 新增联系方式</el-button>
      <el-form
        :model="formData"
        ref="elForm"
        :inline="true"
        size="mini"
      >
        <template v-for="(k,index) in formData.lists">
          <el-form-item
            label="联系方式"
            :prop="'lists.' + index +'.concatWay'"
            :ref="index+'concatWay'"
            :rules=" { required: true, message: '请选择联系方式', trigger: 'blur' }"
          >
            <el-select
              v-model="k.concatWay"
              @change="changeWays(k.concatWay,index)"
            >
              <template v-for="ways in concatWays">
                <el-option
                  :key="ways.code"
                  :label="ways.name"
                  :value="ways.code"
                ></el-option>
              </template>
            </el-select>
          </el-form-item>
          <el-form-item
            :ref="index+'concatValue'"
            :prop="'lists.' + index +'.concatValue'"
            :rules="k.rules"
          >
            <el-input v-model="k.concatValue"></el-input>
          </el-form-item>
        </template>
      </el-form>
      <el-row
        type="flex"
        justify="center"
      >
        <el-button
          @click="save"
          size="mini"
        >提交</el-button>
      </el-row>

    </el-card>
  </div>
</template>

<script>
// 重复的校验规则:必填
const baseRule = [
  { required: true, message: '请填写联系方式', trigger: 'blur' }
]
export default {
  name: 'HelloWorld',
  data () {
    return {
      formData: {
        lists: [{
          concatWay: '',//联系方式：电话/手机
          concatValue: '',//具体的联系方式的值
          rules: baseRule//基础校验规则:必填
        }]

      },
      inputRules: {//设置好需要的校验规则
        telephone: { pattern: /^1[3-9]\d{9}$/, message: '手机号格式错误', trigger: 'blur' },
        phone: { pattern: /^\d{10,12}$/, message: '座机号格式错误', trigger: 'blur' },
        QQ: { pattern: /^[1-9][0-9]{4,14}$/, message: 'QQ格式错误', trigger: 'blur' },
        mail: { pattern: /^([a-zA-Z0-9]+[_|\_|\.]?)*[a-zA-Z0-9]+@([a-zA-Z0-9]+[_|\_|\.]?)*[a-zA-Z0-9]+\.[a-zA-Z]{2,3}$/, message: '邮箱格式错误', trigger: 'blur' }
      },
      dyRoutes: {},
      concatWays: [
        { name: '手机', code: 'telephone' },
        { name: '座机', code: 'phone' },
        { name: 'QQ', code: 'QQ' },
        { name: '邮箱', code: 'mail' },
      ]
    }
  },
  methods: {
    addNewWay () {//新增联系方式
      this.formData.lists.push({
        concatWay: '',
        concatValue: '',
        rules: baseRule
      })
    },

    changeWays (data, index) {
      // 有值的话将自己的校验手动清空
      if (data) {
        let getRefWays = index + 'concatWay'
        this.$refs[getRefWays][0].clearValidate()
      }
      // 将值清空
      this.formData.lists[index].concatValue = ''
      // 去除联系方式的格式校验
      let getRefs = index + 'concatValue'
      this.$refs[getRefs][0].clearValidate()
      // 给表单加上新的校验
      this.formData.lists[index].rules = [this.inputRules[data]].concat(baseRule)


    },
    save () {
      this.$refs.elForm.validate(async (valid) => {
        if (valid) {
          let methosData = await this.getJson()
          console.log(methosData)
          this.$message({
            message: '表单提交成功，查看提交值请打开F12',
            type: 'success'
          });

        } else {
          console.log('error submit!!');
          return false;
        }
      });
    },
    getJson () {//只取到要提交的值
      return new Promise((resolve, reject) => {
        let tempJson = JSON.parse(JSON.stringify(this.formData.lists));
        tempJson.map((item) => {
          delete item.rules
          return item
        })
        resolve(tempJson)
      })

    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.new_btn {
  margin-bottom: 20px;
}
</style>
