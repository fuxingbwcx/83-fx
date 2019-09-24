<template>
   <el-card>
       <bread-crumb slot='header'>
          <template slot='title'>发布文章</template>
       </bread-crumb>
              <!-- 表单 -->
       <el-form ref="publishForm" :model="formData" :rules="publishRules" style='margin-left:100px' label-width="100px">
            <el-form-item prop="title" label="标题">
                <el-input v-model="formData.title" style='width:400px'></el-input>
            </el-form-item>
            <el-form-item prop="content" label="内容">
               <quill-editor v-model="formData.content"  style='height:300px'></quill-editor>
            </el-form-item>
            <el-form-item prop="cover" label="封面"  style='margin-top:120px'>
                <el-radio-group v-model="formData.cover.type" @change="changeType">
                    <el-radio :label="1">单图</el-radio>
                    <el-radio :label="3">三图</el-radio>
                    <el-radio :label="0">无图</el-radio>
                    <el-radio :label="-1">自动</el-radio>
                </el-radio-group>
            </el-form-item>
            <el-form-item prop="channel_id" label="频道">
                <el-select v-model="formData.channel_id">
                    <el-option v-for="item in channels" :key="item.id" :value="item.id" :label="item.name"></el-option>
                </el-select>
            </el-form-item>
            <el-form-item>
                <el-button type='primary' @click="publish(false)">发布文章</el-button>
                <el-button @click="publish(true)">存入草稿</el-button>
            </el-form-item>

       </el-form>
   </el-card>
</template>

<script>
export default {
  data () {
    return {
      channels: [],
      formData: {
        title: '',
        content: '',
        channel_id: '',
        // 封面类型 -1:自动，0-无图，1-1张，3-3张
        cover: {
          type: 0,
          images: []
        }
      },
      publishRules: {
        title: [{ required: true, message: '标题不能为空' },
        //  min /max =》 如果当前数据类型是字符串 =》 min 、max代表最短长度 和最大长度
        // 如果当前数据类型是 数字 =》 那么 min.max代表最大值和最小值  min  5, max: 30
          { min: 5, max: 30, message: '标题长度要控制在5到30个字符之间' }],
        content: [{ required: true, message: '内容不能为空' }],
        channel_id: [{ required: true, message: '频道不能为空' }]
      }
    }
  },
  methods: {
    // 类型改变事件
    changeType () {
      // 获取最新的type
      if (this.formData.cover.type === 1) {
        this.formData.cover.images = ['']
      } else if (this.formData.cover.type === 3) {
        this.formData.cover.images = ['', '', '']
      } else {
        this.formData.cover.images = []
      }
    },
    //   获取频道数据
    getChannels () {
      this.$axios({
        url: '/channels'
      }).then(result => {
        this.channels = result.data.channels // 获取channels频道
      })
    },
    // 根据文章id获取文章详情
    getArticleById (articleId) {
      this.$axios({
        url: `/articles/${articleId}`
      }).then(result => {
        this.formData = result.data
      })
    },
    publish (draft) {
      this.$refs.publishForm.validate((isOK) => {
        if (isOK) {
          // 有 articleId就是编辑 没articleId就是新增
          // let articleId = this.$route.params.articleId
          let { articleId } = this.$route.params

          this.$axios({
            url: articleId ? `/articles${articleId}` : '/articles',
            method: articleId ? 'put' : 'post',
            data: this.formData,
            params: { draft } // :draft
          }).then(() => {
            // 发布成功返回内容列表
            this.$router.push('/home/articles')
          })
        }
      })
    }
  },
  created () {
    this.getChannels() // 获取频道
    // 有 articleId就是编辑 没articleId就是新增
    let { articleId } = this.$route.params

    // 如果articleId存在才执行后界面的逻辑
    articleId && this.getArticleById(articleId)
  }
}
</script>

<style>
</style>
