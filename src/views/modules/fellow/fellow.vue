<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="参数名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('fellow:fellow:save')" type="primary" @click="addOrUpdateHandle(0)">新增</el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column
        prop="id"
        header-align="center"
        align="center"
        label="id">
      </el-table-column>
      <el-table-column
        prop="username"
        header-align="center"
        align="center"
        label="用户名">
      </el-table-column>
      <el-table-column
        header-align="center"
        align="center"
        label="性别">
        <template slot-scope="scope">
          <div>{{getGender(scope.row.gender)}}</div>
        </template>
      </el-table-column>
      <el-table-column
        prop="mobile"
        header-align="center"
        align="center"
        label="手机号码">
      </el-table-column>
      <el-table-column
        prop="email"
        header-align="center"
        align="center"
        label="邮箱">
      </el-table-column>
      <el-table-column
        header-align="center"
        align="center"
        label="头像">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="viewPic(scope.row.header)">查看</el-button>
        </template>
      </el-table-column>
      <el-table-column
        prop="price"
        header-align="center"
        align="center"
        label="定价">
      </el-table-column>
      <el-table-column
        prop="age"
        header-align="center"
        align="center"
        label="年龄">
      </el-table-column>
      <el-table-column
        prop="province"
        header-align="center"
        align="center"
        label="省">
      </el-table-column>
      <el-table-column
        prop="city"
        header-align="center"
        align="center"
        label="市">
      </el-table-column>
      <el-table-column
        prop="region"
        header-align="center"
        align="center"
        label="区">
      </el-table-column>
      <el-table-column
        prop="detailAddress"
        header-align="center"
        align="center"
        label="详细地址">
      </el-table-column>
      <el-table-column
        prop="sign"
        header-align="center"
        align="center"
        label="个性签名">
      </el-table-column>
      <el-table-column
        header-align="center"
        align="center"
        label="简介">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="viewText(scope.row.description)">查看</el-button>
        </template>
      </el-table-column>
      <el-table-column
        prop="status"
        header-align="center"
        align="center"
        label="状态">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="pageSize"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
    <div v-show="showHead" @click="closePicOrText" class="showPhoto">
      <img class="img" :src="picSrc" alt="图片加载失败" />
    </div>
    <el-dialog
      :visible="showText"
      title="简介">
      <span>{{textSrc}}</span>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" @click="closePicOrText">关 闭</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
  import AddOrUpdate from './fellow-add-or-update'
  export default {
    data () {
      return {
        showText: false,
        showHead: false,
        picSrc: '',
        textSrc: '',
        dataForm: {
          key: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false
      }
    },
    components: {
      AddOrUpdate
    },
    activated () {
      this.getDataList()
    },
    methods: {
      getGender: g => {
        return g === 1 ? '男' : '女'
      },
      viewText (t) {
        this.showText = true
        this.textSrc = t
      },
      viewPic (src) {
        this.picSrc = src
        this.showHead = true
      },
      closePicOrText () {
        this.showText = false
        this.showHead = false
      },
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/fellow/fellow/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'key': this.dataForm.key
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataList = data.page.list
            this.totalPage = data.page.totalCount
          } else {
            this.dataList = []
            this.totalPage = 0
          }
          this.dataListLoading = false
        })
      },
      // 每页数
      sizeChangeHandle (val) {
        this.pageSize = val
        this.pageIndex = 1
        this.getDataList()
      },
      // 当前页
      currentChangeHandle (val) {
        this.pageIndex = val
        this.getDataList()
      },
      // 多选
      selectionChangeHandle (val) {
        this.dataListSelections = val
      },
      // 新增 / 修改
      addOrUpdateHandle (id) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
      },
      // 删除
      deleteHandle (id) {
        var ids = id ? [id] : this.dataListSelections.map(item => {
          return item.id
        })
        this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/fellow/fellow/delete'),
            method: 'post',
            data: this.$http.adornData(ids, false)
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.getDataList()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        })
      }
    }
  }
</script>

<style>
.showPhoto {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  z-index: 99999;
  display: flex;
  align-items: center;
  justify-content: center;
}

.showPhoto .img {
  display: block;
  margin: auto 0;
  max-width: 100%;
  text-align: center;
}
</style>
