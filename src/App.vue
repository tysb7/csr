<template>
  <div id="app">
    <div class="block">
      <span class="wrapper">
        <!-- <el-button type="success" class="creatCsr" @click="startHacking">Let's do it</el-button> -->
        <el-button type="primary" class="creatCsr" @click="dialogFormVisible = true">创建CSR</el-button>
        <el-input
          class="searchDomain"
          prefix-icon="el-icon-search"
          placeholder="请输入域名"
          v-model="input10"
          clearable
        ></el-input>
      </span>
    </div>
    <el-dialog title="创建CSR" :visible.sync="dialogFormVisible">
      <el-form
        :model="ruleForm"
        :rules="rules"
        ref="ruleForm"
        label-width="100px"
        class="demo-ruleForm"
      >
        
            <el-form-item label="网站域名" prop="domain">
              <el-input v-model="ruleForm.domain"></el-input>
            </el-form-item>
            <el-form-item label="公司/姓名" prop="name">
              <el-input v-model="ruleForm.name"></el-input>
            </el-form-item>
            <el-form-item label="部门/单位" prop="department">
              <el-input v-model="ruleForm.department"></el-input>
            </el-form-item>
            <el-form-item label="邮箱" prop="mail">
              <el-input v-model="ruleForm.mail"></el-input>
            </el-form-item>
            <el-form-item label="国家" prop="country">
              <el-select v-model="ruleForm.country" filterable placeholder="请选择">
                <el-option
                  v-for="item in optionsCountry"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="省份" prop="province">
              <el-input v-model="ruleForm.province"></el-input>
            </el-form-item>
            <el-form-item label="城市" prop="city">
              <el-input v-model="ruleForm.city"></el-input>
            </el-form-item>
         <el-collapse  >
          <el-collapse-item title="高级设置" name="1">
            <el-form-item label="备用域名" prop="domains">
              <el-input
                type="textarea"
                :rows="2"
                placeholder="请输入备用域名 使用“,”隔开"
                v-model="ruleForm.domains"
              ></el-input>
            </el-form-item>
            <el-form-item label="私钥密码" prop="password">
              <el-input placeholder="请输入6位以上密码" type="password" v-model="ruleForm.password"></el-input>
            </el-form-item>
            <el-form-item label="密钥算法">
              <el-cascader :options="secretKey" v-model="ruleForm.secretKey"></el-cascader>
            </el-form-item>
            <el-form-item label="哈希算法">
              <el-select v-model="ruleForm.hash" :placeholder="ruleForm.hash">
                <el-option
                  v-for="item in hash"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-collapse-item>
        </el-collapse>
        <el-form-item style="margin-top:20px;">
          <el-button type="primary" @click="submitForm('ruleForm')">立即创建</el-button>
          <el-button @click="resetForm('ruleForm')">重置</el-button>
          <el-button @click="dialogFormVisible = false">取 消</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
</template>
<script>
const forge = require("node-forge"); // 引用AES源码js

export default {
  data() {
    // 验证域名是否正确
    var checkDomain = (rule, value, callback) => {
      const domainReg = /^(?=^.{3,255}$)[a-zA-Z0-9][-a-zA-Z0-9]{0,62}(\.[a-zA-Z0-9][-a-zA-Z0-9]{0,62})+$/;
      if (!value) {
        return callback(new Error("域名地址不能为空"));
      }
      setTimeout(() => {
        if (domainReg.test(value)) {
          callback();
        } else {
          callback(new Error("域名地址格式不正确"));
        }
      }, 100);
    };
    // 验证邮箱是否正确
    var checkEmail = (rule, value, callback) => {
      const mailReg = /^([a-zA-Z0-9_-])+@([a-zA-Z0-9_-])+(.[a-zA-Z0-9_-])+/;
      if (!value) {
        return callback(new Error("邮箱不能为空"));
      }
      setTimeout(() => {
        if (mailReg.test(value)) {
          callback();
        } else {
          callback(new Error("请输入正确的邮箱格式"));
        }
      }, 100);
    };
    return {
      input10: "", //搜索
      dialogFormVisible: false, // 生成CSR页面开关
      // 国家代码二进制
      optionsCountry: [
        { label: "阿富汗", value: "AF" },
        { label: "奥兰群岛", value: "AX" },
        { label: "阿尔巴尼亚", value: "AL" },
        { label: "阿尔及利亚", value: "DZ" },
        { label: "美属萨摩亚", value: "AS" },
        { label: "安道尔", value: "AD" },
        { label: "安哥拉", value: "AO" },
        { label: "安圭拉", value: "AI" },
        { label: "南极洲", value: "AQ" },
        { label: "安提瓜和巴布达", value: "AG" },
        { label: "阿根廷", value: "AR" },
        { label: "亚美尼亚", value: "AM" },
        { label: "阿鲁巴", value: "AW" },
        { label: "澳大利亚", value: "AU" },
        { label: "奥地利", value: "AT" },
        { label: "阿塞拜疆", value: "AZ" },
        { label: "巴哈马", value: "BS" },
        { label: "巴林", value: "BH" },
        { label: "孟加拉国", value: "BD" },
        { label: "巴巴多斯", value: "BB" },
        { label: "白俄罗斯", value: "BY" },
        { label: "比利时", value: "BE" },
        { label: "伯利兹", value: "BZ" },
        { label: "贝宁", value: "BJ" },
        { label: "百慕大", value: "BM" },
        { label: "不丹", value: "BT" },
        { label: "玻利维亚", value: "BO" },
        { label: "波黑", value: "BA" },
        { label: "博茨瓦纳", value: "BW" },
        { label: "布维岛", value: "BV" },
        { label: "巴西", value: "BR" },
        { label: "英属印度洋领地", value: "IO" },
        { label: "文莱", value: "BN" },
        { label: "保加利亚", value: "BG" },
        { label: "布基纳法索", value: "BF" },
        { label: "布隆迪", value: "BI" },
        { label: "柬埔寨", value: "KH" },
        { label: "喀麦隆", value: "CM" },
        { label: "加拿大", value: "CA" },
        { label: "佛得角", value: "CV" },
        { label: "开曼群岛", value: "KY" },
        { label: "中非", value: "CF" },
        { label: "乍得", value: "TD" },
        { label: "智利", value: "CL" },
        { label: "中国", value: "CN" },
        { label: "圣诞岛", value: "CX" },
        { label: "科科斯（基林）群岛", value: "CC" },
        { label: "哥伦比亚", value: "CO" },
        { label: "科摩罗", value: "KM" },
        { label: "刚果（布）", value: "CG" },
        { label: "刚果（金）", value: "CD" },
        { label: "库克群岛", value: "CK" },
        { label: "哥斯达黎加", value: "CR" },
        { label: "科特迪瓦", value: "CI" },
        { label: "克罗地亚", value: "HR" },
        { label: "古巴", value: "CU" },
        { label: "塞浦路斯", value: "CY" },
        { label: "捷克", value: "CZ" },
        { label: "丹麦", value: "DK" },
        { label: "吉布提", value: "DJ" },
        { label: "多米尼克", value: "DM" },
        { label: "多米尼加", value: "DO" },
        { label: "厄瓜多尔", value: "EC" },
        { label: "埃及", value: "EG" },
        { label: "萨尔瓦多", value: "SV" },
        { label: "赤道几内亚", value: "GQ" },
        { label: "厄立特里亚", value: "ER" },
        { label: "爱沙尼亚", value: "EE" },
        { label: "埃塞俄比亚", value: "ET" },
        { label: "福克兰群岛（马尔维纳斯）", value: "FK" },
        { label: "法罗群岛", value: "FO" },
        { label: "斐济", value: "FJ" },
        { label: "芬兰", value: "FI" },
        { label: "法国", value: "FR" },
        { label: "法属圭亚那", value: "GF" },
        { label: "法属波利尼西亚", value: "PF" },
        { label: "法属南部领地", value: "TF" },
        { label: "加蓬", value: "GA" },
        { label: "冈比亚", value: "GM" },
        { label: "格鲁吉亚", value: "GE" },
        { label: "德国", value: "DE" },
        { label: "加纳", value: "GH" },
        { label: "直布罗陀", value: "GI" },
        { label: "希腊", value: "GR" },
        { label: "格陵兰", value: "GL" },
        { label: "格林纳达", value: "GD" },
        { label: "瓜德罗普", value: "GP" },
        { label: "关岛", value: "GU" },
        { label: "危地马拉", value: "GT" },
        { label: "格恩西岛", value: "GG" },
        { label: "几内亚", value: "GN" },
        { label: "几内亚比绍", value: "GW" },
        { label: "圭亚那", value: "GY" },
        { label: "海地", value: "HT" },
        { label: "赫德岛和麦克唐纳岛", value: "HM" },
        { label: "梵蒂冈", value: "VA" },
        { label: "洪都拉斯", value: "HN" },
        { label: "中国香港", value: "HK" },
        { label: "匈牙利", value: "HU" },
        { label: "冰岛", value: "IS" },
        { label: "印度", value: "IN" },
        { label: "印度尼西亚", value: "ID" },
        { label: "伊朗", value: "IR" },
        { label: "伊拉克", value: "IQ" },
        { label: "爱尔兰", value: "IE" },
        { label: "英国属地曼岛", value: "IM" },
        { label: "以色列", value: "IL" },
        { label: "意大利", value: "IT" },
        { label: "牙买加", value: "JM" },
        { label: "日本", value: "JP" },
        { label: "泽西岛", value: "JE" },
        { label: "约旦", value: "JO" },
        { label: "哈萨克斯坦", value: "KZ" },
        { label: "肯尼亚", value: "KE" },
        { label: "基里巴斯", value: "KI" },
        { label: "朝鲜", value: "KP" },
        { label: "韩国", value: "KR" },
        { label: "科威特", value: "KW" },
        { label: "吉尔吉斯斯坦", value: "KG" },
        { label: "老挝", value: "LA" },
        { label: "拉脱维亚", value: "LV" },
        { label: "黎巴嫩", value: "LB" },
        { label: "莱索托", value: "LS" },
        { label: "利比里亚", value: "LR" },
        { label: "利比亚", value: "LY" },
        { label: "列支敦士登", value: "LI" },
        { label: "立陶宛", value: "LT" },
        { label: "卢森堡", value: "LU" },
        { label: "中国澳门", value: "MO" },
        { label: "前南马其顿", value: "MK" },
        { label: "马达加斯加", value: "MG" },
        { label: "马拉维", value: "MW" },
        { label: "马来西亚", value: "MY" },
        { label: "马尔代夫", value: "MV" },
        { label: "马里", value: "ML" },
        { label: "马耳他", value: "MT" },
        { label: "马绍尔群岛", value: "MH" },
        { label: "马提尼克", value: "MQ" },
        { label: "毛利塔尼亚", value: "MR" },
        { label: "毛里求斯", value: "MU" },
        { label: "马约特", value: "YT" },
        { label: "墨西哥", value: "MX" },
        { label: "密克罗尼西亚联邦", value: "FM" },
        { label: "摩尔多瓦", value: "MD" },
        { label: "摩纳哥", value: "MC" },
        { label: "蒙古", value: "MN" },
        { label: "黑山", value: "ME" },
        { label: "蒙特塞拉特", value: "MS" },
        { label: "摩洛哥", value: "MA" },
        { label: "莫桑比克", value: "MZ" },
        { label: "缅甸", value: "MM" },
        { label: "纳米比亚", value: "NA" },
        { label: "瑙鲁", value: "NR" },
        { label: "尼泊尔", value: "NP" },
        { label: "荷兰", value: "NL" },
        { label: "荷属安的列斯", value: "AN" },
        { label: "新喀里多尼亚", value: "NC" },
        { label: "新西兰", value: "NZ" },
        { label: "尼加拉瓜", value: "NI" },
        { label: "尼日尔", value: "NE" },
        { label: "尼日利亚", value: "NG" },
        { label: "纽埃", value: "NU" },
        { label: "诺福克岛", value: "NF" },
        { label: "北马里亚纳", value: "MP" },
        { label: "挪威", value: "NO" },
        { label: "阿曼", value: "OM" },
        { label: "巴基斯坦", value: "PK" },
        { label: "帕劳", value: "PW" },
        { label: "巴勒斯坦", value: "PS" },
        { label: "巴拿马", value: "PA" },
        { label: "巴布亚新几内亚", value: "PG" },
        { label: "巴拉圭", value: "PY" },
        { label: "秘鲁", value: "PE" },
        { label: "菲律宾", value: "PH" },
        { label: "皮特凯恩", value: "PN" },
        { label: "波兰", value: "PL" },
        { label: "葡萄牙", value: "PT" },
        { label: "波多黎各", value: "PR" },
        { label: "卡塔尔", value: "QA" },
        { label: "留尼汪", value: "RE" },
        { label: "罗马尼亚", value: "RO" },
        { label: "俄罗斯联邦", value: "RU" },
        { label: "卢旺达", value: "RW" },
        { label: "圣赫勒拿", value: "SH" },
        { label: "圣基茨和尼维斯", value: "KN" },
        { label: "圣卢西亚", value: "LC" },
        { label: "圣皮埃尔和密克隆", value: "PM" },
        { label: "圣文森特和格林纳丁斯", value: "VC" },
        { label: "萨摩亚", value: "WS" },
        { label: "圣马力诺", value: "SM" },
        { label: "圣多美和普林西比", value: "ST" },
        { label: "沙特阿拉伯", value: "SA" },
        { label: "塞内加尔", value: "SN" },
        { label: "塞尔维亚", value: "RS" },
        { label: "塞舌尔", value: "SC" },
        { label: "塞拉利昂", value: "SL" },
        { label: "新加坡", value: "SG" },
        { label: "斯洛伐克", value: "SK" },
        { label: "斯洛文尼亚", value: "SI" },
        { label: "所罗门群岛", value: "SB" },
        { label: "索马里", value: "SO" },
        { label: "南非", value: "ZA" },
        { label: "南乔治亚岛和南桑德韦奇岛", value: "GS" },
        { label: "西班牙", value: "ES" },
        { label: "斯里兰卡", value: "LK" },
        { label: "苏丹", value: "SD" },
        { label: "苏里南", value: "SR" },
        { label: "斯瓦尔巴岛和扬马延岛", value: "SJ" },
        { label: "斯威士兰", value: "SZ" },
        { label: "瑞典", value: "SE" },
        { label: "瑞士", value: "CH" },
        { label: "叙利亚", value: "SY" },
        { label: "中国台湾", value: "TW" },
        { label: "塔吉克斯坦", value: "TJ" },
        { label: "坦桑尼亚", value: "TZ" },
        { label: "泰国", value: "TH" },
        { label: "东帝汶", value: "TL" },
        { label: "多哥", value: "TG" },
        { label: "托克劳", value: "TK" },
        { label: "汤加", value: "TO" },
        { label: "特立尼达和多巴哥", value: "TT" },
        { label: "突尼斯", value: "TN" },
        { label: "土耳其", value: "TR" },
        { label: "土库曼斯坦", value: "TM" },
        { label: "特克斯和凯科斯群岛", value: "TC" },
        { label: "图瓦卢", value: "TV" },
        { label: "乌干达", value: "UG" },
        { label: "乌克兰", value: "UA" },
        { label: "阿联酋", value: "AE" },
        { label: "英国", value: "GB" },
        { label: "美国", value: "US" },
        { label: "美国本土外小岛屿", value: "UM" },
        { label: "乌拉圭", value: "UY" },
        { label: "乌兹别克斯坦", value: "UZ" },
        { label: "瓦努阿图", value: "VU" },
        { label: "委内瑞拉", value: "VE" },
        { label: "越南", value: "VN" },
        { label: "英属维尔京群岛", value: "VG" },
        { label: "美属维尔京群岛", value: "VI" },
        { label: "瓦利斯和富图纳", value: "WF" },
        { label: "西撒哈拉", value: "EH" },
        { label: "也门", value: "YE" },
        { label: "赞比亚", value: "ZM" },
        { label: "津巴布韦", value: "ZW" }
      ],
      ruleForm: {
        domain: "",
        domains: "",
        password: "",
        name: "",
        department: "",
        mail: "",
        country: "",
        province: "",
        city: "",
        hash: "SHA256",
        secretKey: ["RSA", "2048"]
      },
      rules: {
        name: [
          { required: true, message: "请输入活动名称", trigger: "blur" },
          { min: 3, max: 5, message: "长度在 3 到 5 个字符", trigger: "blur" }
        ],
        mail: [
          { required: true, message: "请输入邮箱地址", trigger: "blur" },
          { validator: checkEmail, trigger: "blur" }
        ],
        domain: [
          { required: true, message: "请输入域名地址", trigger: "blur" },
          { validator: checkDomain, trigger: "blur" }
        ],
        province: [
          { required: true, message: "请输入省份名称", trigger: "blur" }
        ],
        city: [{ required: true, message: "请输入城市名称", trigger: "blur" }],
        country: [
          { required: true, message: "请选择国家名称", trigger: "blur" }
        ],
        type: [
          {
            type: "array",
            required: true,
            message: "请至少选择一个活动性质",
            trigger: "change"
          }
        ]
      },
      hash: [
        {
          value: "SHA256",
          label: "SHA256"
        },
        {
          value: "SHA384",
          label: "SHA384"
        },
        {
          value: "SHA512",
          label: "SHA512"
        },
        {
          value: "SHA1",
          label: "SHA1"
        }
      ],
      secretKey: [
        {
          value: "RSA",
          label: "RSA",
          children: [
            {
              value: "2048",
              label: "2048"
            },
            {
              value: "3072",
              label: "3072"
            },
            {
              value: "4096",
              label: "4096"
            }
          ]
        },
        {
          value: "ECDSA",
          label: "ECDSA",
          children: [
            {
              value: "P256",
              label: "P256"
            },
            {
              value: "P384",
              label: "P384"
            },
            {
              value: "P521",
              label: "P521"
            },
            {
              value: "P224",
              label: "P224"
            }
          ]
        }
      ]
    };
  },
  methods: {
    // 下载文件
    downloadFile(name, value) {
      function fakeClick(obj) {
        var ev = document.createEvent("MouseEvents");
        ev.initMouseEvent(
          "click",
          true,
          false,
          window,
          0,
          0,
          0,
          0,
          0,
          false,
          false,
          false,
          false,
          0,
          null
        );
        obj.dispatchEvent(ev);
      }

      function exportRaw(name, data) {
        var urlObject = window.URL || window.webkitURL || window;
        var export_blob = new Blob([data]);
        var save_link = document.createElementNS(
          "http://www.w3.org/1999/xhtml",
          "a"
        );
        save_link.href = urlObject.createObjectURL(export_blob);
        save_link.download = name;
        fakeClick(save_link);
      }

      exportRaw(name, value);
    },
    
    // 生成csr和key
    creatCsr() {
      var keys = forge.pki.rsa.generateKeyPair(2048);
      var pkey = forge.pki.privateKeyToPem(keys.privateKey);

      // create a certification request (CSR)
      var csr = forge.pki.createCertificationRequest();

      csr.publicKey = keys.publicKey;

      csr.setSubject([
        { name: "commonName", value: this.ruleForm.domain },
        { name: "countryName", value: this.ruleForm.country },
        {shortName: "ST",value: this.ruleForm.province},
        {name: "localityName",value: this.ruleForm.city},
        {name: "organizationName",value: this.ruleForm.name},
        {shortName: "OU",value: this.ruleForm.department}
      ]);
      // set (optional) attributes
      csr.setAttributes([
        {
          name: "challengePassword",
          value: "password"
        },
        {
          name: "unstructuredName",
          value: "My Company, Inc."
        },
        {
          name: "extensionRequest",
          extensions: [
            {
              name: "subjectAltName",
              altNames: [
                {
                  // 2 is DNS type
                  type: 2,
                  value: "test.domain.com"
                },
                {
                  type: 2,
                  value: "other.domain.com"
                },
                {
                  type: 2,
                  value: "www.domain.net"
                }
              ]
            }
          ]
        }
      ]);

      // sign certification request
      csr.sign(keys.privateKey);

      // verify certification request
      var verified = csr.verify();

      // convert certification request to PEM-format
      var pem = forge.pki.certificationRequestToPem(csr);

      // convert a Forge certification request from PEM-format
      var csr = forge.pki.certificationRequestFromPem(pem);

      // get an attribute
      csr.getAttribute({ name: "challengePassword" });

      // get extensions array
      csr.getAttribute({ name: "extensionRequest" }).extensions;

      // 转换域名
      var domain = csr.subject.attributes[0].value.split(".");
      var domains = domain[0];
      for (var i = 1; i < domain.length; i++) {
        domains = domains + "-" + domain[i];
      }
      // 下载csr
      this.downloadFile(domains + ".csr", pem);
      // 下载key
      this.downloadFile(domains + ".key", pkey);

      this.startHacking("CSR创建成功！");
    },
    // 发送通知
    startHacking(words) {
      console.log(words);
      this.$notify({
        title: "创建成功！",
        message: words,
        duration: 6000
      });
    },
    submitForm(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          console.log(this.ruleForm);
          this.dialogFormVisible = false;
          this.creatCsr();
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    }
  }
};
</script>

<style >
body {
  font-family: Helvetica, sans-serif;
}
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* text-align: center; */
  color: #2c3e50;
  margin-top: 60px;
  /* background: #f6f6f6; */
  width: 100%;
  height: 90vh;
}
body {
  margin: 10px !important;
}
.searchDomain {
  width: 300px;
  margin-left: 50px;
}

.creatCsr {
  margin-left: 50px;
}
</style>