<!-- 广告栏添加页面 -->
<template>
  <a-modal
    :visible="visible"
    title="广告栏页面添加"
    ok-text="确认"
    cancel-text="取消"
    @ok="submitAdd"
    @cancel="cancelAdd">
    <a-form :form="form">
      <a-form-item label="标题" :labelCol="labelCol" :wrapperCol="wrapperCol">
        <a-input
          placeholder=" 请输入广告栏标题"
          v-decorator="['title', { rules: [{ required: true, message: '广告栏标题必须填写' }] }]"
        >
        </a-input>
      </a-form-item>
      <a-form-item label="排序" :labelCol="labelCol" :wrapperCol="wrapperCol">
        <a-input-number
          v-decorator="['sort',{initialValue:'100'}]"
          :max="9999999"
          :min="0"
          :default-value="100"
          placeholder="请输入广告栏排序"
          style="width:100%" />
      </a-form-item>
      <a-form-item label="位置" :labelCol="labelCol" :wrapperCol="wrapperCol">
        <a-select
          placeholder=" 请输入广告栏位置"
          v-decorator="['position', { rules: [{ required: true, message: '广告位置必须填写' }] }]"
        >
          <a-select-option
            v-for="item in position"
            :key="item.value"
            :value="item.value"
          >
            {{ item.desc }}
          </a-select-option>
        </a-select>
      </a-form-item>
      <a-form-item label="跳转类型" :labelCol="labelCol" :wrapperCol="wrapperCol">
        <a-select
          placeholder="请输入跳转类型"
          v-decorator="['routeId', { rules: [{ required: true, message: '跳转类型必须填写' }] }]"
        >
          <a-select-option
            v-for="item in route"
            :key="item.id"
            :value="item.id"
          >
            {{ item.description }}
            <span style="color: #bfc0c6">{{ item.route }}</span>
          </a-select-option>
        </a-select>
      </a-form-item>
      <a-form-item label="广告栏图片" :labelCol="labelCol" :wrapperCol="wrapperCol">
        <sf-simple-upload
          @input="updateImgUrl"
          :uploadType="0"
          v-decorator="['imgUrl', { rules: [{ required: true, message: '广告栏图片必须填写' }] }]"
        ></sf-simple-upload>
      </a-form-item>
      <a-form-item label="跳转目标" :labelCol="labelCol" :wrapperCol="wrapperCol">
        <a-input
          placeholder="跳转目标"
          v-decorator="['targetId']"
        >
        </a-input>
      </a-form-item>
    </a-form>
  </a-modal>
</template>
<script>
  import { BANNER_POSITION, BANNER_ROUTE_TYPE } from '@/views/banner/enum/BannerConstant'
  import { getRouteList } from '@/api/bannerRroute'
  import SfSimpleUpload from '@/components/Upload/SimpleUploadWrapper'
  import { addBanner } from '@/api/banner'
  import FooterToolBar from '@/components/FooterToolbar'
  export default {
    components: {
      SfSimpleUpload,
      FooterToolBar
    },
    created () {
      this.init()
    },
    data () {
      return {
        position: BANNER_POSITION,
        routeType: BANNER_ROUTE_TYPE,
        form: this.$form.createForm(this),
        labelCol: { lg: { span: 6 }, sm: { span: 6 } },
        wrapperCol: { lg: { span: 12 }, sm: { span: 12 } },
        search: {},
        route: []
      }
    },
    props: {
      visible: {
        type: Boolean,
        default: true
      }
    },
    methods: {
      init () {
        this.getRoute()
      },
      // 获取banner的route表
      getRoute () {
        getRouteList(this.search)
        .then(res => {
          console.log('获取route列表', res)
          this.route = res
        })
      },
      updateImgUrl (pic) {
        this.form.setFieldsValue({ imgUrl: pic })
      },
      submitAdd () {
        const {
          form: { validateFields }
        } = this
        validateFields((err, values) => {
          if (!err) {
            console.log('values:' + JSON.stringify(values))
            addBanner(values).then(res => {
              this.$message.success('添加成功')
              this.form.resetFields()
            })
            .catch(err => {
              this.$message.error(err)
            })
          }
        })
      },
      cancelAdd () {
        // todo
        this.$emit('cancel')
      }
    }
  }
</script>
