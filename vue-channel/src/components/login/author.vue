<template>
  <div class="dashboard-container">
    <!--<iframe src="javascript:void(0);" ref="regIframe"  frameborder="0" class="app-login"  style="width: 100%;height: 302px;" scrolling=auto></iframe>-->
    <h2 style="text-align:left;">输入UID</h2>
    <el-col :span="24"  style="height: 45px;text-align:left;" >
      <el-form :inline="true" size="small">
        <el-form-item label="appid">
          <!--<el-input v-model="appid"></el-input>-->
          <template>
            <el-select v-model="appid" placeholder="appid">
              <el-option
                v-for="item in appids"
                :key="item.value"
                :label="item.label"
                :value="item.value">
              </el-option>
            </el-select>
          </template>
        </el-form-item>

        <el-form-item label="area">
          <!--<el-input v-model="area"></el-input>-->
          <template>
            <el-select v-model="area" placeholder="area">
              <el-option
                v-for="item in areas"
                :key="item.value"
                :label="item.label"
                :value="item.value">
              </el-option>
            </el-select>
          </template>
        </el-form-item>

        <el-form-item label="uid">
          <el-input v-model="uid"></el-input>
        </el-form-item>
        <el-form-item class="search">
          <el-button type="primary"  @click="getUserToken" style="border-radius: 4px"><span class="el-icon-search margin"></span>进入房间</el-button>
        </el-form-item>
      </el-form>
    </el-col>

  </div>
</template>

<script>
  import { APPID, authURL, redirectURL } from '@/global.js';
  import { getBeforeLoginUrl, removeBeforeLoginUrl } from '@/utils/auth'
  import { getStorage, setStorage } from '@/utils/BaseUtil'

  export default {
    name: 'author',
    data() {
        return {
          uid: '',
          appid: 1504984159,
          area: 'china/private/share',
          token: '',
          reportType: 0,
          appids: [{
              value: 1504984159,
              label: '1504984159'
            }, {
              value: 1350626568,
              label: '1350626568'
            }, {
              value: 1833618279,
              label: '1833618279'
            }],
          areas: [{
              value: 'china/private/share',
              label: 'china/private/share'
            }, {
              value: 'CN-TEST',
              label: 'shenzhen/test/2'
            }, {
              value: 'CN-TEST1',
              label: 'shenzhen/test/1'
            }, {
              value: 'CN-INNER',
              label: 'inner'
            }, {
              value: 'CN-INNER1',
              label: 'inner1'
            }, {
              value: 'cn',
              label: 'cn'
            }, {
              value: 'ap_southeast',
              label: 'ap_southeast'
            }
          ],
      }
    },
    created() {
    },
    mounted () {
      let uid = getStorage('uid');
      let token = getStorage('token');
      console.log("author uid=" + uid + ", token=" + token);
      if (uid && token) {
          this.reportType = 1;
          let redirect = getBeforeLoginUrl() || '/';
          this.$router.push({ path: redirect });
          removeBeforeLoginUrl();
      } else {
        this.reportType = 0;
        this.getUserToken(this.uid);
        /*let token = 'ADp4VYnqABhfObBXLeUAAAFtQoNAaAAAC7gACnthOumbhue-pH26R8M3HCw2oPLBqZQExg9PIVQTqQ';
        let uid = '6860100817333733';
        setStorage("uid", uid);
        setStorage("token", token);
        */
      }
    },
    beforeDestroy() {

    },
    methods: {
      getUserToken(uid) {
        const appid = this.appid || APPID;
        this.$axios.get(authURL + '/user/token?uid=' + this.uid+'&appid=' + appid)
          .then(res => {
            if (res.status === 200) {
              console.log("res.data: " + JSON.stringify(res.data));
              let body = res.data;
              if (body.uid && body.token) {
                setStorage("uid", body.uid.toString());
                setStorage("token", body.token);
                setStorage("area", this.area);
                setStorage("appid", appid);
                
                let redirect = getBeforeLoginUrl() || '/';
                this.$router.push({ path: redirect });
                removeBeforeLoginUrl();
              }
            }
          })
          .catch(error => {
            console.log("error: " + JSON.stringify(error));
          });
      },
    }
  }
</script>

<style lang='less'>
  .dashboard {
    &-container {
      margin: 30px;
      display: flex;
      justify-content: center;
      align-items: left;
      flex-direction: column;
      //height: 300px;
    }
    &-text {
      font-size: 30px;
      line-height: 40px;
    }
  }

  .el-row {
    margin-bottom: 20px;
    &:last-child {
      margin-bottom: 0;
    }
  }
 .row-bg {
    padding: 10px 0;
    background-color: #f9fafc;
  }
  .text-unit {
    //margin: 10px 25px;
    text-align: left;
    //white-space: pre;
  }
</style>
