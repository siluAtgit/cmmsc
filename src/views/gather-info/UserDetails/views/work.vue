<template>
  <div class="app-container">
    <div class="filter-container">

      <el-button
        class="filter-item"
        style="margin-left: 10px;"
        type="primary"
        icon="el-icon-plus"
        @click="handleCreate"
      >
        {{ $t('common.add') }}
      </el-button>
      <el-button
        v-waves
        :loading="downloadLoading"
        class="filter-item"
        type="primary"
        icon="el-icon-download"
        @click="handleDownload"
      >
        {{ $t('common.export') }}
      </el-button>
      <el-checkbox
        v-model="showUnitAddr"
        class="filter-item"
        style="margin-left:15px;"
      >
        {{ $t('customer.unitAddres') }}
      </el-checkbox>
      <el-checkbox
        v-model="showDetailAddr"
        class="filter-item"
        style="margin-left:15px;"
      >
        {{ $t('customer.detailedAddres') }}
      </el-checkbox>
      <el-checkbox
        v-model="showUnitContact"
        class="filter-item"
        style="margin-left:15px;"
      >
        {{ $t('customer.unitContact') }}
      </el-checkbox>
      <el-checkbox
        v-model="showUnitPhone"
        class="filter-item"
        style="margin-left:15px;"
      >
        {{ $t('customer.unitPhone') }}
      </el-checkbox>
    </div>
    <el-table
      v-loading="listLoading"
      :data="list"
      border
      fit
      highlight-current-row
      style="width: 100%;"
    >
      <el-table-column :label="$t('customer.unitName')">
        <template slot-scope="{row}">
          <span>{{ row.unitName }}</span>
        </template>
      </el-table-column>
      <el-table-column :label="$t('customer.unitNature')">
        <template slot-scope="{row}">
          <span>{{ row.unitNature }}</span>
        </template>
      </el-table-column>
      <el-table-column :label="$t('customer.industry')">
        <template slot-scope="{row}">
          <span>{{ row.industry}}</span>
        </template>
      </el-table-column>
      <el-table-column :label="$t('customer.post')">
        <template slot-scope="{row}">
          <span>{{ row.post }}</span>
        </template>
      </el-table-column>
      <el-table-column
        :label="$t('customer.unitAddres')"
        v-if="showUnitAddr"
      >
        <template slot-scope="{row}">
          <span>{{ row.unitAddres }}</span>
        </template>
      </el-table-column>
      <el-table-column
        :label="$t('customer.detailedAddres')"
        v-if="showDetailAddr"
      >
        <template slot-scope="{row}">
          <span>{{ row.detailedAddres }}</span>
        </template>
      </el-table-column>
      <el-table-column
        :label="$t('customer.unitContact')"
        v-if="showUnitContact"
      >
        <template slot-scope="{row}">
          <span>{{ row.unitContact }}</span>
        </template>
      </el-table-column>
      <el-table-column
        :label="$t('customer.unitPhone')"
        v-if="showUnitPhone"
      >
        <template slot-scope="{row}">
          <span>{{ row.unitPhone }}</span>
        </template>
      </el-table-column>
      <el-table-column
        :label="$t('customer.startTime')"
        min-width="100px"
      >
        <template slot-scope="{row}">
          <span>{{ row.startTime | parseTime('{y}-{m}-{d} {h}:{i}:{s}') }}</span>
        </template>
      </el-table-column>
      <el-table-column
        :label="$t('customer.endTime')"
        min-width="100px"
      >
        <template slot-scope="{row}">
          <span>{{ row.endTime | parseTime('{y}-{m}-{d} {h}:{i}:{s}') }}</span>
        </template>
      </el-table-column>
      <el-table-column
        :label="$t('common.operate')"
        min-width="100px"
        align="center"
        class-name="small-padding fixed-width"
      >
        <template slot-scope="{row,$index}">
          <el-button
            type="primary"
            size="mini"
            style="margin-right: 5px"
            @click="handleUpdate(row)"
          >
            {{ $t('common.edit') }}
          </el-button>
          <el-popover
            :ref="'popover-' + row.id"
            placement="top"
            width="160"
            :title="$t('common.confirm-delete')"
            trigger="click"
          >
            <div style="text-align: right; margin: 0">
              <el-button
                size="mini"
                @click="$refs[`popover-` + row.id].doClose()"
              >{{ $t('common.cancel') }}</el-button>
              <el-button
                type="primary"
                size="mini"
                @click="$refs[`popover-` + row.id].doClose();handleDelete(row,$index)"
              >{{ $t('common.confirm') }}</el-button>
            </div>
            <el-button
              slot="reference"
              type="danger"
              size="mini"
            >
              {{ $t('common.delete') }}
            </el-button>
          </el-popover>
        </template>
      </el-table-column>
    </el-table>
    <el-dialog
      :title="textMap[dialogStatus]"
      :visible.sync="dialogFormVisible"
    >
      <el-form
        ref="dataForm"
        :rules="rules"
        :model="temp"
        label-position="right"
        label-width="100px"
        style="width: 100%"
      >
        <el-form-item :label="$t('customer.unitName')">
          <el-input
            v-model="temp.unitName"
            :placeholder="$t('common.enter')"
            minlength="2"
            maxlength="50"
            clearable
          />
        </el-form-item>
        <el-form-item :label="$t('customer.unitNature')">
          <el-input
            v-model="temp.unitNature"
            :placeholder="$t('common.choose')"
            minlength="2"
            maxlength="50"
            clearable
          />
        </el-form-item>
        <el-form-item :label="$t('customer.industry')">
          <el-input
            v-model="temp.industry"
            :placeholder="$t('common.enter')"
            minlength="2"
            maxlength="50"
            clearable
          />
        </el-form-item>
        <el-form-item :label="$t('customer.post')">
          <el-input
            v-model="temp.post"
            :placeholder="$t('common.enter')"
            minlength="2"
            maxlength="50"
            clearable
          />
        </el-form-item>
        <el-form-item :label="$t('customer.unitAddres')">
          <el-input
            v-model="temp.unitAddres"
            :placeholder="$t('common.enter')"
            minlength="2"
            maxlength="50"
            clearable
          />
        </el-form-item>
        <el-form-item :label="$t('customer.detailedAddres')">
          <el-input
            v-model="temp.detailedAddres"
            :placeholder="$t('common.enter')"
            minlength="2"
            maxlength="150"
            clearable
          />
        </el-form-item>
        <el-form-item :label="$t('customer.unitContact')">
          <el-input
            v-model="temp.unitContact"
            :placeholder="$t('common.enter')"
            minlength="2"
            maxlength="50"
            clearable
          />
        </el-form-item>
        <el-form-item
          :label="$t('customer.unitPhone')"
          prop="unitPhone"
        >
          <el-input
            v-model="temp.unitPhone"
            :placeholder="$t('common.enter')"
            minlength="3"
            maxlength="64"
            clearable
          />
        </el-form-item>
        <el-form-item :label="$t('customer.startTime')">
          <el-date-picker
            v-model="temp.startTime"
            type="datetime"
            :placeholder="$t('common.enter')"
            style="width: 100%"
          />
        </el-form-item>
        <el-form-item :label="$t('customer.endTime')">
          <el-date-picker
            v-model="temp.endTime"
            type="datetime"
            :placeholder="$t('common.enter')"
            style="width: 100%"
          />
        </el-form-item>
      </el-form>
      <div
        slot="footer"
        class="dialog-footer"
      >
        <el-button @click="dialogFormVisible = false">
          {{ $t('common.cancel') }}
        </el-button>
        <el-button
          type="primary"
          @click="dialogStatus==='create'?createData():updateData()"
        >
          {{ $t('common.confirm') }}
        </el-button>
      </div>
    </el-dialog>
  </div>

</template>

<script>
import waves from '@/directive/waves' // waves directive
// eslint-disable-next-line no-unused-vars
import { parseTime } from '@/utils'
// eslint-disable-next-line no-unused-vars
import { getUserData, upWork, delWork, chaneWork } from '@/api/getUser-info/work'
import { validPhone } from '@/utils/regular/cell-phone'
export default {

  name: 'Work',
  directives: { waves },
  data () {
    return {
      list: [],
      temp: {
        id: undefined,
        unitPhone: ''
      },
      dialogFormVisible: false,
      dialogStatus: '',
      textMap: {
        update: this.$t('common.edit'),
        create: this.$t('common.add')
      },
      rules: {
        unitPhone: [
          { required: false, message: '请输入正确手机号码', trigger: 'blur', validator: validPhone }
        ]
      },
      listLoading: false,
      downloadLoading: false,
      showUnitAddr: false,
      showUnitContact: false,
      showDetailAddr: false,
      showUnitPhone: false
    }
  },

  computed: {
    NatureOptions () {
      return [{
        value: 'zhinan',
        label: '指南'
      }, {
        value: 'jinan',
        label: '济南'
      }
      ]
    }
  },
  methods: {
    resetTemp () {
      this.temp = {
        id: undefined
      }
    },
    handleCreate () {
      this.resetTemp()
      this.dialogStatus = 'create'
      this.dialogFormVisible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].clearValidate()
      })
    },
    // 新增工作经历
    createData (dataForm) {
      // 转换为时间戳
      if (this.temp.startTime !== undefined) {
        this.temp['startTime'] = Date.parse(this.temp.startTime)
      }
      if (this.temp.endTime !== undefined) {
        this.temp['endTime'] = Date.parse(this.temp.endTime)
      }
      this.temp['consumerId'] = this.$route.params.id
      const valArray = []
      for (const val in this.temp) {
        valArray.push(val)
      }
      if (valArray.length <= 2) {
        this.$message.warning(`不能上传空表单`)
      } else {
        this.$refs.dataForm.validate((valid) => {
          if (valid) {
            upWork(this.temp).then(data => {
              this.getUserId()
              this.dialogFormVisible = false
            }).catch(err => {
              console.log(err)
            })
          }
        })
      }
    },

    // 删除工作经历
    handleDelete (row) {
      delWork({
        workId: row.id
      }).then(data => {
        this.getUserId()
      }).catch(err => {
        console.log(err)
      })
    },
    // 弹窗
    handleUpdate (row) {
      this.temp = Object.assign({}, row) // copy obj
      this.dialogStatus = 'update'
      this.dialogFormVisible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].clearValidate()
      })
    },
    // 修改工作经历
    updateData (dataForm) {
      // 判断时间格式
      if (typeof this.temp.startTime !== 'number') {
        if (this.temp.startTime === null) {
          this.temp['startTime'] = null
        } else {
          this.temp['startTime'] = Date.parse(this.temp.startTime)
        }
      }

      if (typeof this.temp.endTime !== 'number') {
        if (this.temp.endTime === '在职') {
          delete this.temp['endTime']
        } else if (this.temp.endTime === null && this.temp.endTime === undefined) {
          this.temp['endTime'] = null
        } else {
          this.temp['endTime'] = Date.parse(this.temp.endTime)
        }
      }
      delete this.temp['updateTime']
      delete this.temp['isCurrent']
      delete this.temp['createTime']
      this.$refs.dataForm.validate((valid) => {
        if (valid) {
          chaneWork(
            this.temp
          ).then(data => {
            this.getUserId()
            this.dialogFormVisible = false
          }).catch(err => {
            console.log(err)
          })
        }
      })
    },
    // 获取工作经历信息
    getUserId () {
      getUserData({
        consumerId: this.$route.params.id
      }).then(data => {
        this.list = data.data
        if (this.list[0]['endTime'] === null) {
          this.list[0]['endTime'] = '在职'
        }
      }).catch(err => {
        console.log(err)
      })
    },
    handleDownload () {
      this.downloadLoading = true
      import('@/vendor/Export2Excel').then(excel => {
        const tHeader = [this.$t('customer.unitName'), this.$t('customer.unitNature'), this.$t('customer.industry'),
        this.$t('customer.post'), this.$t('customer.unitAddres'), this.$t('customer.detailedAddres'),
        this.$t('customer.unitContact'), this.$t('customer.unitPhone'), this.$t('customer.startTime'), this.$t('customer.endTime')]
        const filterVal = ['unitName', 'unitNature', 'industry', 'post', 'unitAddres', 'detailedAddres', 'unitContact', 'unitPhone', 'startTime', 'endTime']
        const data = this.formatJson(filterVal)
        excel.export_json_to_excel({
          header: tHeader,
          data,
          filename: this.$t('route.client_info')
        })
        this.downloadLoading = false
      })
    },
    formatJson (filterVal) {
      return this.list.map(v => filterVal.map(j => {
        if (j === 'installedDate') {
          return parseTime(v[j])
        } else if (j === 'enabled') {
          if (v[j] === 1) {
            return this.$t('common.able')
          } else {
            return this.$t('common.disable')
          }
        } else {
          return v[j]
        }
      }))
    }
  },
  mounted () {
    this.getUserId()
  }
}

</script>

<style lang='less' scoped>
</style>
