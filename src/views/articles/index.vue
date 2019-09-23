<template>
  <el-card>
    <bread-crumb slot="header">
      <template slot="title"></template>
    </bread-crumb>
    <el-form style="margin-left:10px">
      <el-form-item label="文章状态：">
         <!-- v-model来源于 el-radio中的label属性 -->
        <el-radio-group v-model="formData.status" @click="changeV">
          <el-radio :label="5">全部</el-radio>
          <el-radio :label="0">草稿</el-radio>
          <el-radio :label="1">待审核</el-radio>
          <el-radio :label="2">审核通过</el-radio>
          <el-radio :label="3">审核失败</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="频道列表:">
        <el-select  @click="changeV" v-model="formData.channel_id">
          <el-option v-for="item in channels" :key = "item.id" :value = "item.id" :label = "item.name"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="时间选择:">
         <!-- value-format 指定绑定的值的格式 -->
        <el-date-picker @change="changeV" v-model="formData.date" value-format="yyyy-MM-dd" type="daterange" start-placeholder="开始日期" end-placeholder="结束日期"></el-date-picker>
      </el-form-item>
      </el-form>
      <div class="total">共找到{{page.total}}条符合条件的内容</div>
      <div class="article-item" v-for="(item,index) in list" :key="index">
        <!-- 左侧 -->
         <div class='left'>
            <img :src="item.cover.images.length>0?item.cover.images[0]:defaultImg" alt="">
            <div class='info'>
                <span class='title'>{{item.title}}</span>
                <el-tag :type="item.status | statusType" class='status'>{{item.status | statusText}}</el-tag>
                <span class='date'>{{item.pubdata}}</span>
            </div>
        </div>
        <!-- 右侧 -->
        <div class='right'>
            <span><i class="el-icon-edit"></i>修改</span>
            <span @click="delArticles(item.id)"><i class="el-icon-delete"></i>删除</span>
        </div>
      </div>
      <el-row type="flex" justify="center" style="margin:20px 0">
        <el-pagination
  background
  @current-change="changePage"
  :current-page="page.currentPage"
  :page-size="page.pageSize"
  layout="prev, pager, next"
  :total="page.total">
</el-pagination>
      </el-row>
  </el-card>
</template>

<script>
export default {
  data () {
    return {
      formData: {
        // 文章状态 0-草稿，1-待审核，2-审核通过，3-审核失败，4-已删除
        status: 5,
        // 频道id
        channel_id: null,
        date: []
      },
      // 定义一个频道数组
      channels: [],
      list: [],
      // 将图片地址转为base64
      defaultImg: require('../../assets/img/404.png'),
      page: {
        total: 0,
        pageSize: 10,
        currentPage: 1
      }
    }
  },
  methods: {
    delArticles (id) {
      this.$confirm('亲，确定要删除此文章吗').then(() => {
        // id超过了安全数字限制 被jsonbigint转成了bigNUmber类型 要想变成字符串 id.toString()
        this.$axios({
          url: `/articles/${id.toString()}`,
          method: 'delete'
        }).then(() => {
          // 带条件的查询
          this.queryArticles()
        })
      })
    },
    changeV () {
      this.page.currentPage = 1
      this.queryArticles()
    },
    changePage (newPage) {
      this.page.currentPage = newPage
      this.queryArticles()
    },
    queryArticles () {
      // 因为值改变时 formdata已经是最新的值 所以直接可以用formData的值请求
      let params = {
        status: this.formData.status === 5 ? null : this.formData.status, // 状态  如果为5时，就是全部，但是接口要求全部不传内容 null就相当于什么都没传
        channel_id: this.formData.channel_id, // 频道id
        begin_pubdate: this.formData.date.length ? this.formData.date[0] : null,
        end_pubdate: this.formData.date.length > 1 ? this.formData.date[1] : null, // 结束时间
        page: this.page.currentPage,
        per_page: this.page.pageSize
      }
      this.getArticles(params)
    },
    getArticles (params) {
      this.$axios({
        url: '/articles',
        params
      }).then(result => {
        this.list = result.data.results
        this.page.total = result.data.total_count
      })
    },
    getChannels () {
      this.$axios({
        url: '/channels'
      }).then(result => {
        this.channels = result.data.channels
      })
    }
  },
  created () {
    this.getArticles()
    this.getChannels()
  },
  filters: {
    statusText (value) {
      switch (value) {
        // 文章状态 0-草稿，1-待审核，2-审核通过，3-审核失败，4-已删除
        case 0:
          return '草稿'
        case 1:
          return '待审核'
        case 2:
          return '审核通过'
        case 3:
          return '审核失败'
        case 4:
          return '已删除'
      }
    },
    statusType (value) {
      switch (value) {
        // 文章状态 0-草稿，1-待审核，2-审核通过，3-审核失败，4-已删除
        case 0:
          return 'warning'
        case 1:
          return 'info'
        case 2:
          return 'success'
        case 3:
          return 'danger'
        case 4:
          return 'danger'
      }
    }
  }
}
</script>

<style lang="less" scoped>
.total {
    border-bottom: 1px dashed #ccc;
    height:50px;
    line-height: 50px;
}
.article-item {
    display: flex;
    justify-content: space-between;
    padding: 20px 10px;
    border-bottom: 1px solid  #f2f3f5;
    .left {
        display: flex;
        img {
            width: 180px;
            height:100px;
            border-radius: 4px;
        }
        .info {
            height: 100px;
            display: flex;
            flex-direction: column;
            margin-left:10px;
            justify-content: space-around;
            .date {
                color:#999;
                font-size:12px;
            }
            .title {
                font-size:14px;
            }
            .status {
                width: 80px;
                text-align: center
            }
        }
    }
    .right {
        font-size: 12px;
        span {
            margin-right:8px;
            // 鼠标进入显示小手
            cursor: pointer;
        }
    }
}
</style>
