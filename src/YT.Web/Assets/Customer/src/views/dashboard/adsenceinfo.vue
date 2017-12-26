<template>
  <div class='info'>
    <div class='infoMain'>
      <Row class='infosTitle'>
        <Col span='24' class='g-center'>{{info.title}}</Col>
      </Row>
      <Row>
        <Col span='24' class='g-time'>{{info.creationTime| formatDate}}</Col>
      </Row>
      <Row class='infosContent'>
        <Col span='24' >
        <div style="text-indent:25px" v-html="info.content"></div>
        </Col>
      </Row>
      <BackTop> </BackTop>
    </div>
  </div>
</template>

<script>
import { info } from "api/public";
import { parseTime } from "utils/index";
export default {
  data() {
    return {
      info: {}
    };
  },
  components: {},
  // 过滤器
  filters: {
    formatDate(time) {
      return parseTime(time);
    }
  },
  activated() {
    this.Init();
  },
  created() {
    this.Init();
  },
  methods: {
    Init() {
      const id = this.$route.params.id;
      info({
        id: id
      })
        .then(r => {
          if (r.data.success) {
            this.info = r.data.result;
          }
        })
        .catch(e => {
          this.$Message.error(e.response.data.error.message);
        });
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

.g-time {
  text-align: center;
  font-family: "Microsoft Yahei";
  font-size: 10px;
  color: #b8b8b8;
}

.info {
  .infoMain {
    // background: #f5f5f6;
    padding-bottom: 35px;
    overflow: auto;
    width: 700px;
    margin: 0 auto;
    .ivu-form-item-required .ivu-form-item-label:before {
      display: none;
    }
    .infosTitle {
      padding: 40px 0 3px;
      font-size: 20px;
      color: #373d41;
      border-bottom: 2px solid #ddd;
      margin-bottom: 5px;
    }
    .infosContent {
      padding-top: 20px;
      font-size: 14px;
      color: #0e0e0e;
    }
  }
}
</style>
