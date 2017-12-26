<template>
  <div class='Myinfo'>
    <head-top-tag></head-top-tag>
    <div class='MyinfoMain'>
      <Row class='infoTitle'>
        <Col span='24' class='g-center'>客户资料信息</Col>
      </Row>
      <Form ref='form' :model='form' :rules='ruleValidate' label-position="top">
        <div class="infoBox">
          <Row class='basic'>
            <Col span='24'>客户基本信息</Col>
          </Row>
          <div class="infoInput">
            <FormItem label="公司名称" prop='companyName'>
              <Input v-model="form.companyName"></Input>
            </FormItem>
            <FormItem label="所属行业">
              <Input v-model="form.industry"></Input>
            </FormItem>
            <FormItem label="品牌名称">
              <Input v-model="form.brands"></Input>
            </FormItem>
            <FormItem label="法人代表">
              <Input v-model="form.legalPerson"></Input>
            </FormItem>
            <FormItem label="法人代表手机号码">
              <Input v-model="form.legalMobile"></Input>
            </FormItem>
            <FormItem label="品牌负责人">
              <Input v-model="form.brandsPerson"></Input>
            </FormItem>
            <FormItem label="品牌负责人手机号码">
              <Input v-model="form.brandsMobile"></Input>
            </FormItem>
            <FormItem label="联系地址">
              <Input v-model="form.address"></Input>
            </FormItem>
            <FormItem label="邮编">
              <Input v-model="form.postNum"></Input>
            </FormItem>
            <FormItem label="电子邮箱" prop='email'>
              <Input v-model="form.email"></Input>
            </FormItem>
          </div>
        </div>
        <div class="infoBox">
          <Row class='basic'>
            <Col span='24'>资料上传</Col>
          </Row>
          <Row class='fileUpload'>
            <Row style="margin-top: 20px">
              <Col span="9">
              <h3>企业营业执照副本</h3>
              </Col>
              <Col span="13" offset="1">
              <Upload :show-upload-list="false" :headers="upload.headers" :on-error="Licenseerror" :on-success="Licensesuccess" :action="upload.url">
                <Button type="ghost" class="myBtn">上传文件</Button>
              </Upload>
              </col>
            </Row>
            <Row v-if="licences">
              <enlargeimg @remove="childRemove" :data="licences"></enlargeimg>
            </Row>
            <Row style="margin-top: 20px">
              <Col span="9">
              <h3>企业法人身份证正面</h3>
              </Col>
              <Col span="13" offset="1">
              <Upload :show-upload-list="false" :headers="upload.headers" :on-error="Toperror" :on-success="Topsuccess" :action="upload.url">
                <Button type="ghost" class="myBtn">上传文件</Button>
              </Upload>
              </Col>

            </Row>
            <Row v-if="uface">
              <enlargeimg @remove="childRemove" :data="uface"></enlargeimg>
            </Row>
            <Row style="margin-top: 20px">
              <Col span="9">
              <h3>企业法人身份证背面</h3>
              </Col>
              <Col span="13" offset="1">
              <Upload :show-upload-list="false" :headers="upload.headers" :on-error="Bottomerror" :on-success="Bottomsuccess" :action="upload.url">
                <Button type="ghost" class="myBtn">上传文件</Button>
              </Upload>
              </Col>

            </Row>
            <Row v-if="dface">
              <enlargeimg @remove="childRemove" :data="dface"></enlargeimg>
            </Row>

            <Row style="margin-top: 20px">
              <Col span="9">
              <h3>企业所属行业特有许可证或企业荣誉：</h3>
              </Col>
              <Col span="13" offset="1">
              <Upload multiple :show-upload-list="false" :headers="upload.headers" :on-error="Imageerror" :on-success="Imagesuccess" :action="upload.url">
                <Button type="ghost" class="myBtn">上传文件</Button>
              </Upload>
              </col>
            </Row>
            <Row v-if="images">
              <enlargeimg @remove="childRemove" :data="images"></enlargeimg>
            </Row>
          </Row>
        </div>

        <div class="infoBox">
          <Row class='basic'>
            <Col span='24'>相关资料</Col>
          </Row>
          <Row class='fileUpload'>
            <Row>
              <Col span="4" offset="4">
              <h3>相关业务资料：</h3>
              </Col>
              <Col span="11" offset="2">
              <Upload multiple :show-upload-list="false" :on-error="Fileerror" :on-success="Filesuccess" :headers="upload.headers" :action="upload.fileUrl">
                <Input style="width: 200px" class="myInput"></Input>
                <Button type="ghost" class="myBtn">上传文件</Button>
              </Upload>
              </Col>
            </Row>
            <Row v-if="files">
              <Col offset="12" span="12" :key="index" v-for="(item,index) in files"> {{item.profileName}}
              <a @click="remove(item)">删除</a>
              <a :href="item.profileUrl" download="w3logo">下载</a>
              </Col>
            </Row>
          </Row>
        </div>
        <Row class="g-center submitButton">
          <Button @click="submit" type="primary">提交并结算</Button>
        </Row>
        <BackTop> </BackTop>
      </Form>
    </div>

    <footer-tag></footer-tag>
  </div>
</template>

<script>
import HeadTopTag from "components/HeadTop";
import FooterTag from "components/Footer";
import { getFormByOrder, modifyForm } from "api/product";
import { deleteFile } from "api/public";
import enlargeimg from "components/enlargeimg";
export default {
  data() {
    return {
      form: {},
      upload: {
        url: "http://47.93.2.82:9999/api/File/ImageUpload",
        fileUrl: "http://47.93.2.82:9999/api/File/FileUpload",
        // headers: { 'Content-Type': 'multipart/form-data' }
        headers: {}
      },
      ruleValidate: {
        companyName: [
          {
            required: true,
            message: "公司名称不能为空",
            trigger: "blur"
          }
        ],
        email: [
          {
            type: "email",
            message: "邮箱格式不正确",
            trigger: "blur"
          }
        ]
      }
    };
  },
  components: {
    FooterTag,
    HeadTopTag,
    enlargeimg
  },
  created() {
    this.Init();
  },
  activated() {
    this.Init();
  },
  computed: {
    licences() {
      if (this.form.formProfiles != null) {
        return this.form.formProfiles.filter(c => {
          return c.profileType === 3;
        });
      } else {
        return [];
      }
    },
    uface() {
      if (this.form.formProfiles != null) {
        return this.form.formProfiles.filter(c => {
          return c.profileType === 4;
        });
      } else {
        return [];
      }
    },
    dface() {
      if (this.form.formProfiles != null) {
        return this.form.formProfiles.filter(c => {
          return c.profileType === 5;
        });
      } else {
        return [];
      }
    },

    files() {
      if (this.form.formProfiles != null) {
        return this.form.formProfiles.filter(c => {
          return c.profileType === 1;
        });
      } else {
        return [];
      }
    },
    images() {
      if (this.form.formProfiles != null) {
        return this.form.formProfiles.filter(c => {
          return c.profileType === 2;
        });
      } else {
        return [];
      }
    }
  },
  methods: {
    remove(item) {
      this.form.formProfiles = this.form.formProfiles.filter(
        c => c.profileId !== item.profileId
      );
    },
    download(item) {
      window.open(item.profileUrl);
    },
    childRemove(guid) {
      deleteFile(guid);
      this.form.formProfiles = this.form.formProfiles.filter(
        c => c.profileId !== guid
      );
    },
    Init() {
      const order = this.$route.query.orderId;
      getFormByOrder({
        id: order
      })
        .then(r => {
          if (r.data.success) {
            this.form = r.data.result;
            this.form.orderId = this.$route.query.id;
            this.form.customerId = localStorage.getItem("Credit-Id");
          }
        })
        .catch(e => {
          this.$Message.error(e.response.data.error.message);
        });
    },
    submit() {
      this.form.orderId = this.$route.query.orderId;
      // 图片同步
      const licences = this.form.formProfiles.filter(c => c.profileType === 3);
      this.form.license = licences != null ? licences[0].profileId : null;
      const topface = this.form.formProfiles.filter(c => c.profileType === 4);
      this.form.topIdCard = topface != null ? topface[0].profileId : null;
      const bottomface = this.form.formProfiles.filter(
        c => c.profileType === 5
      );
      this.form.bottomIdCard =
        bottomface != null ? bottomface[0].profileId : null;
      this.form.formProfiles = this.form.formProfiles.filter(
        c => c.profileType === 1 || c.profileType === 2
      );
      modifyForm(this.form).then(r => {
        if (r.data.success) {
          this.$router.push({
            path: "/order"
          });
        }
      });
    },

    Licenseerror(error, file) {
      if (!file.success) {
        this.$Message.error(file.error.message);
      }
    },
    Licensesuccess(response, file) {
      if (response.success) {
        if (
          this.form.formProfiles == null ||
          this.form.formProfiles.length <= 0
        ) {
          this.form.formProfiles = [];
        }
        const model = {
          profileId: response.result[0].guid,
          profileUrl: response.result[0].viewUrl,
          profileName: response.result[0].name,
          profileType: 3
        };
        this.form.formProfiles.push(model);
      }
    },
    Toperror(error, file) {
      if (!file.success) {
        this.$Message.error(file.error.message);
      }
    },
    Topsuccess(response, file) {
      if (response.success) {
        if (
          this.form.formProfiles == null ||
          this.form.formProfiles.length <= 0
        ) {
          this.form.formProfiles = [];
        }
        const model = {
          profileId: response.result[0].guid,
          profileUrl: response.result[0].viewUrl,
          profileName: response.result[0].name,
          profileType: 4
        };
        this.form.formProfiles.push(model);
      }
    },
    Bottomerror(error, file) {
      if (!file.success) {
        this.$Message.error(file.error.message);
      }
    },
    Bottomsuccess(response, file) {
      if (response.success) {
        if (
          this.form.formProfiles == null ||
          this.form.formProfiles.length <= 0
        ) {
          this.form.formProfiles = [];
        }
        const model = {
          profileId: response.result[0].guid,
          profileUrl: response.result[0].viewUrl,
          profileName: response.result[0].name,
          profileType: 5
        };
        this.form.formProfiles.push(model);
      }
    },
    Fileerror(error, file) {
      if (!file.success) {
        this.$Message.error(file.error.message);
      }
    },
    Filesuccess(response, file) {
      if (response.success) {
        if (
          this.form.formProfiles == null ||
          this.form.formProfiles.length <= 0
        ) {
          this.form.formProfiles = [];
        }
        const model = {
          profileId: response.result[0].guid,
          profileUrl: response.result[0].viewUrl,
          profileName: response.result[0].name,
          profileType: 1
        };
        this.form.formProfiles.push(model);
      }
    },
    Imageerror(error, file) {
      if (!file.success) {
        this.$Message.error(file.error.message);
      }
    },
    Imagesuccess(response, file) {
      if (response.success) {
        if (
          this.form.formProfiles == null ||
          this.form.formProfiles.length <= 0
        ) {
          this.form.formProfiles = [];
        }
        const model = {
          profileId: response.result[0].guid,
          profileUrl: response.result[0].viewUrl,
          profileName: response.result[0].name,
          profileType: 2
        };
        this.form.formProfiles.push(model);
      }
    }
  }
};
</script>

<style rel='stylesheet/scss' lang='scss'>
div {
  font-family: "Microsoft Yahei";
}

.g-center {
  text-align: center;
}

.Myinfo {
  .MyinfoMain {
    // background: #f5f5f6;
    padding-bottom: 35px;
    overflow: auto;
    .ivu-form-item-required .ivu-form-item-label:before {
      display: none;
    }
    .infoTitle {
      padding-top: 80px;
      padding-bottom: 40px;
      font-size: 20px;
      color: #373d41;
    }
    .infoBox {
      width: 1000px;
      margin: 0 auto;
      background: #fff;
      padding: 58px 96px;
      border-bottom: 2px solid #ebebeb;
      .basic {
        font-size: 20px;
        color: #373d41;
        margin-bottom: 46px;
      }
      .infoInput {
        width: 490px;
        margin: 0 auto;
        label {
          color: #9b9ea0;
          font-size: 14px;
        }
        input {
          height: 40px;
          border-radius: 0;
        }
      }
      .c9 {
        color: #9b9ea0;
        font-size: 14px;
        padding-top: 35px;
        margin-bottom: 8px;
      }
    }
    .infoTextArea {
      textarea.ivu-input {
        padding: 12px;
      }
    }
    .submitButton {
      margin: 120px 0 62px;
      button {
        width: 200px;
        height: 40px;
        background: #679fec;
        padding: 0;
        border: 1px solid #679fec;
        border-radius: 0;
        font-size: 14px;
      }
    }
    .fileUpload {
      width: 645px;
      img {
        height: 80px;
        border: 1px solid #c4c5c7;
      }
      .fileBox {
        width: 160px;
        height: 80px;
        line-height: 98px;
      }
      .ivu-upload-drag {
        margin: 0 auto;
        width: 160px;
        height: 80px;
        line-height: 98px;
        border: 1px solid #c4c5c7;
        border-radius: 0;
      }
      .g9b9ea0 {
        color: #9b9ea0;
        font-size: 14px;
      }
      .g-marginLeft {
        margin: 0 143px 0 36px;
      }
    }
    .myInput input {
      border-radius: 0;
      border-right: none;
    }
    .myBtn {
      border-radius: 0;
      margin-left: -3px;
      background: #4e5873;
      color: #fff;
      padding: 8px 15px;
    }
  }
}

.demo-upload-list {
  display: inline-block;
  width: 60px;
  height: 60px;
  text-align: center;
  line-height: 60px;
  border: 1px solid transparent;
  border-radius: 4px;
  overflow: hidden;
  background: #fff;
  position: relative;
  box-shadow: 0 1px 1px rgba(0, 0, 0, 0.2);
  margin-right: 4px;
}

.demo-upload-list img {
  width: 100%;
  height: 100%;
}

.demo-upload-list-cover {
  display: none;
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  background: rgba(0, 0, 0, 0.6);
}

.demo-upload-list:hover .demo-upload-list-cover {
  display: block;
}

.demo-upload-list-cover i {
  color: #fff;
  font-size: 20px;
  cursor: pointer;
  margin: 0 2px;
}

.singleimg {
  margin-left: 40px;
  width: 160px;
  height: 80px;
  border: 1px solid #c4c5c7;
}
</style>
