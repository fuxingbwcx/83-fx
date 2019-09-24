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
                <el-input v-model="formData.content" type="textarea" :rows="4"  placeholder="请输入内容"></el-input>
            </el-form-item>
            <el-form-item prop="cover" label="封面">
                <el-radio-group v-model="formData.cover.type">
                    <el-radio :label="1">单选</el-radio>
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
    //   获取频道数据
    getChannels () {
      this.$axios({
        url: '/channels'
      }).then(result => {
        this.channels = result.data.channels // 获取channels频道
      })
    },
    publish (draft) {
      this.$refs.publishForm.validate((isOK) => {
        if (isOK) {
          this.$axios({
            url: '/articles',
            method: 'post',
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
  }
}
</script>

<style>
</style>
