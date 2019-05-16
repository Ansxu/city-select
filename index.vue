<template>
  <div>
      <!--顶部输入框-->
      <div class="white">
          <div class="topinput">
              <input type="text" placeholder="请输入城市名称" :value="inputName" @input="bindKeyInput">
              <img src="/static/images/search.png" class="searchpic absolu">
              <img src="/static/images/cancle.png" class="canclepic absolu" style="z-index:40" @click="bindBlur">
          </div>
      </div>
      <!--右侧字母 左侧列表-->
      <div class="container-inner">
          <div class="searchLetter touchClass">
            <div class="thishotText" @click="hotCity">
              <div style="margin-top:0;">当前</div>
              <div style="margin-top:0;">热门</div>
            </div>
            <div v-for="(item, idx) in searchLetter" :key="idx" style="color:#ff6325;font-size:20rpx;" :data-letter="item" @click="clickLetter">
              {{ item }}   
            </div>
          </div>
          <div class="container">
              <scroll-view scroll-y="true" v-bind:style="{height: winHeight + 'px'}" :scroll-into-view="scrollTopId">
              <!--定位当前城市-->
              <div class="item mylocal" id="currentcity">定位当前城市</div>
              <div class="itemlocat mylocal">
                  <div class="name">{{cityName}}</div>
                  <!-- <div class="chose" @click="choseCIty">
                    <img src="/static/images/can.png" class="can">
                    <text>重新定位</text>
                  </div> -->
              </div>
              
                <div class="item">热门城市</div>
                <div class="flex-container cityhot">
                    <div class="name" v-for="item in hotCity" :key="item.id" :data-city="item.name"  @click="bindCity">{{item.name}}</div>
                </div>
                <!--搜索城市-->
                <div class="citylist" v-for="(item,sindex) in searchlist" :key="sindex">
                    <div class="item" :id="item.initial">{{ item.initial }}</div>
                    <div style="padding:20rpx 30rpx;border-bottom:1rpx solid #f4f4f4"  :data-city="item.city" @click="bindCity">
                        {{item.city}}
                    </div>  
                </div>
                <!--城市列表-->
                  <div class="citylist" v-for="(item, idx) in citylist" :key="idx">
                    <div class="item" :id="item.initial">{{ item.initial }}</div>
                    <div style="padding:20rpx 30rpx;border-bottom:1rpx solid #f4f4f4" v-for="(cityItem, index) in item.cityInfo" :key="index" :data-code="cityItem.code" :data-city="cityItem.city" @click="bindCity">
                        {{cityItem.city}}
                    </div>
                  </div>
              </scroll-view>
          </div>
      </div>
      
  </div>
</template>

<script>
import "../../css/common.css";
import "../../css/global.css";
import { mapState, mapMutations } from "vuex"; //vuex辅助函数
export default {
  props:{
    cityName:{
      type:String,
      default:''
    }
  },
  components: {
    
  },
  data () {
    return {
        scrollTopId:"",//置顶id
        inputName:"",
        winHeight:"",
        searchLetter:['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'J', 'K', 'L', 'M', 'N', 'P', 'Q', 'R', 'S', 'T', 'W', 'X', 'Y', 'Z'],
        citylist:[],//加载城市列表
        searchlist:[],//搜索城市的列表
        hotCity:[
          {id:1,name:"深圳市"},{id:2,name:"上海市"},{id:3,name:"杭州市"},{id:4,name:"广州市"},{id:5,name:"南京市"},{id:6,name:"武汉市"},{id:7,name:"成都市"},{id:8,name:"北京市"},
        ],
        allCity: [
          {'id': '35', 'provincecode': '150000', 'city': '阿拉善盟', 'code': '152900', 'initial': 'A', 'short': 'Alashanmeng'}, {'id': '38', 'provincecode': '210000', 'city': '鞍山市', 'code': '210300', 'initial': 'A', 'short': 'Anshan'}, {'id': '105', 'provincecode': '340000', 'city': '安庆市', 'code': '340800', 'initial': 'A', 'short': 'Anqing'}, {'id': '156', 'provincecode': '410000', 'city': '安阳市', 'code': '410500', 'initial': 'A', 'short': 'Anyang'}, {'id': '256', 'provincecode': '510000', 'city': '阿坝藏族羌族自治州', 'code': '513200', 'initial': 'A', 'short': 'Aba'}, {'id': '262', 'provincecode': '520000', 'city': '安顺市', 'code': '520400', 'initial': 'A', 'short': 'Anshun'}, {'id': '289', 'provincecode': '540000', 'city': '阿里地区', 'code': '542500', 'initial': 'A', 'short': 'Ali'}, {'id': '299', 'provincecode': '610000', 'city': '安康市', 'code': '610900', 'initial': 'A', 'short': 'Ankang'}, {'id': '335', 'provincecode': '650000', 'city': '阿克苏地区', 'code': '652900', 'initial': 'A', 'short': 'Akesu'}, {'id': '341', 'provincecode': '650000', 'city': '阿勒泰地区', 'code': '654300', 'initial': 'A', 'short': 'Aletai'}, {'id': '1', 'provincecode': '110000', 'city': '北京市', 'code': '110000', 'initial': 'B', 'short': 'Beijing'}, {'id': '7', 'provincecode': '130000', 'city': '保定市', 'code': '130600', 'initial': 'B', 'short': 'Baoding'}, {'id': '25', 'provincecode': '150000', 'city': '包头市', 'code': '150200', 'initial': 'B', 'short': 'Baotou'}, {'id': '31', 'provincecode': '150000', 'city': '巴彦淖尔市', 'code': '150800', 'initial': 'B', 'short': 'Bayannaoer'}, {'id': '40', 'provincecode': '210000', 'city': '本溪市', 'code': '210500', 'initial': 'B', 'short': 'Benxi'}, {'id': '55', 'provincecode': '220000', 'city': '白山市', 'code': '220600', 'initial': 'B', 'short': 'Baishan'}, {'id': '57', 'provincecode': '220000', 'city': '白城市', 'code': '220800', 'initial': 'B', 'short': 'Baicheng'}, {'id': '100', 'provincecode': '340000', 'city': '蚌埠市', 'code': '340300', 'initial': 'B', 'short': 'Bangbu'}, {'id': '150', 'provincecode': '370000', 'city': '滨州市', 'code': '371600', 'initial': 'B', 'short': 'Binzhou'}, {'id': '222', 'provincecode': '450000', 'city': '北海市', 'code': '450500', 'initial': 'B', 'short': 'Beihai'}, {'id': '227', 'provincecode': '450000', 'city': '百色市', 'code': '451000', 'initial': 'B', 'short': 'Baise'}, {'id': '254', 'provincecode': '510000', 'city': '巴中市', 'code': '511900', 'initial': 'B', 'short': 'Bazhong'}, {'id': '265', 'provincecode': '520000', 'city': '毕节地区', 'code': '522400', 'initial': 'B', 'short': 'Bijie'}, {'id': '271', 'provincecode': '530000', 'city': '保山市', 'code': '530500', 'initial': 'B', 'short': 'Baoshan'}, {'id': '293', 'provincecode': '610000', 'city': '宝鸡市', 'code': '610300', 'initial': 'B', 'short': 'Baoji'}, {'id': '304', 'provincecode': '620000', 'city': '白银市', 'code': '620400', 'initial': 'B', 'short': 'Baiyin'}, {'id': '333', 'provincecode': '650000', 'city': '博尔塔拉蒙古自治州', 'code': '652700', 'initial': 'B', 'short': 'Boertala'}, {'id': '334', 'provincecode': '650000', 'city': '巴音郭楞蒙古自治州', 'code': '652800', 'initial': 'B', 'short': 'Bayinguoleng'}, {'id': '', 'provincecode': '500000', 'city': '重庆市', 'code': '500000', 'initial': 'C', 'short': 'Chongqing'}, {'id': '9', 'provincecode': '130000', 'city': '承德市', 'code': '130800', 'initial': 'C', 'short': 'Chengde'}, {'id': '10', 'provincecode': '130000', 'city': '沧州市', 'code': '130900', 'initial': 'C', 'short': 'Cangzhou'}, {'id': '16', 'provincecode': '140000', 'city': '长治市', 'code': '140400', 'initial': 'C', 'short': 'Changzhi'}, {'id': '27', 'provincecode': '150000', 'city': '赤峰市', 'code': '150400', 'initial': 'C', 'short': 'Chifeng'}, {'id': '48', 'provincecode': '210000', 'city': '朝阳市', 'code': '211300', 'initial': 'C', 'short': 'Chaoyang'}, {'id': '50', 'provincecode': '220000', 'city': '长春市', 'code': '220100', 'initial': 'C', 'short': 'Changchun'}, {'id': '77', 'provincecode': '320000', 'city': '常州市', 'code': '320400', 'initial': 'C', 'short': 'Changzhou'}, {'id': '107', 'provincecode': '340000', 'city': '滁州市', 'code': '341100', 'initial': 'C', 'short': 'Chuzhou'}, {'id': '110', 'provincecode': '340000', 'city': '巢湖市', 'code': '341400', 'initial': 'C', 'short': 'Chaohu'}, {'id': '113', 'provincecode': '340000', 'city': '池州市', 'code': '341700', 'initial': 'C', 'short': 'Chizhou'}, {'id': '183', 'provincecode': '430000', 'city': '长沙市', 'code': '430100', 'initial': 'C', 'short': 'Changsha'}, {'id': '189', 'provincecode': '430000', 'city': '常德市', 'code': '430700', 'initial': 'C', 'short': 'Changde'}, {'id': '192', 'provincecode': '430000', 'city': '郴州市', 'code': '431000', 'initial': 'C', 'short': 'Chenzhou'}, {'id': '215', 'provincecode': '440000', 'city': '潮州市', 'code': '445100', 'initial': 'C', 'short': 'Chaozhou'}, {'id': '231', 'provincecode': '450000', 'city': '崇左市', 'code': '451400', 'initial': 'C', 'short': 'Chongzuo'}, {'id': '238', 'provincecode': '510000', 'city': '成都市', 'code': '510100', 'initial': 'C', 'short': 'Chengdu'}, {'id': '276', 'provincecode': '530000', 'city': '楚雄彝族自治州', 'code': '532300', 'initial': 'C', 'short': 'Chuxiong'}, {'id': '285', 'provincecode': '540000', 'city': '昌都地区', 'code': '542100', 'initial': 'C', 'short': 'Changdu'}, {'id': '332', 'provincecode': '650000', 'city': '昌吉回族自治州', 'code': '652300', 'initial': 'C', 'short': 'Changji'}, {'id': '14', 'provincecode': '140000', 'city': '大同市', 'code': '140200', 'initial': 'D', 'short': 'Datong'}, {'id': '37', 'provincecode': '210000', 'city': '大连市', 'code': '210200', 'initial': 'D', 'short': 'Dalian'}, {'id': '41', 'provincecode': '210000', 'city': '丹东市', 'code': '210600', 'initial': 'D', 'short': 'Dandong'}, {'id': '64', 'provincecode': '230000', 'city': '大庆市', 'code': '230600', 'initial': 'D', 'short': 'Daqing'}, {'id': '71', 'provincecode': '230000', 'city': '大兴安岭地区', 'code': '232700', 'initial': 'D', 'short': 'Daxinganling'}, {'id': '139', 'provincecode': '370000', 'city': '东营市', 'code': '370500', 'initial': 'D', 'short': 'Dongying'}, {'id': '148', 'provincecode': '370000', 'city': '德州市', 'code': '371400', 'initial': 'D', 'short': 'Dezhou'}, {'id': '213', 'provincecode': '440000', 'city': '东莞市', 'code': '441900', 'initial': 'D', 'short': 'Dongguan'}, {'id': '242', 'provincecode': '510000', 'city': '德阳市', 'code': '510600', 'initial': 'D', 'short': 'Deyang'}, {'id': '252', 'provincecode': '510000', 'city': '达州市', 'code': '511700', 'initial': 'D', 'short': 'Dazhou'}, {'id': '280', 'provincecode': '530000', 'city': '大理白族自治州', 'code': '532900', 'initial': 'D', 'short': 'Dali'}, {'id': '281', 'provincecode': '530000', 'city': '德宏傣族景颇族自治州', 'code': '533100', 'initial': 'D', 'short': 'Dehong'}, {'id': '283', 'provincecode': '530000', 'city': '迪庆藏族自治州', 'code': '533400', 'initial': 'D', 'short': 'Diqing'}, {'id': '311', 'provincecode': '620000', 'city': '定西市', 'code': '621100', 'initial': 'D', 'short': 'Dingxi'}, {'id': '29', 'provincecode': '150000', 'city': '鄂尔多斯市', 'code': '150600', 'initial': 'E', 'short': 'Eerduosi'}, {'id': '174', 'provincecode': '420000', 'city': '鄂州市', 'code': '420700', 'initial': 'E', 'short': 'Ezhou'}, {'id': '181', 'provincecode': '420000', 'city': '恩施土家族苗族自治州', 'code': '422800', 'initial': 'E', 'short': 'Enshi'}, {'id': '39', 'provincecode': '210000', 'city': '抚顺市', 'code': '210400', 'initial': 'F', 'short': 'Fushun'}, {'id': '44', 'provincecode': '210000', 'city': '阜新市', 'code': '210900', 'initial': 'F', 'short': 'Fuxin'}, {'id': '108', 'provincecode': '340000', 'city': '阜阳市', 'code': '341200', 'initial': 'F', 'short': 'Fuyang'}, {'id': '115', 'provincecode': '350000', 'city': '福州市', 'code': '350100', 'initial': 'F', 'short': 'Fuzhou'}, {'id': '133', 'provincecode': '360000', 'city': '抚州市', 'code': '361000', 'initial': 'F', 'short': 'Fuzhou'}, {'id': '202', 'provincecode': '440000', 'city': '佛山市', 'code': '440600', 'initial': 'F', 'short': 'Foshan'}, {'id': '223', 'provincecode': '450000', 'city': '防城港市', 'code': '450600', 'initial': 'F', 'short': 'Fangchenggang'}, {'id': '130', 'provincecode': '360000', 'city': '赣州市', 'code': '360700', 'initial': 'G', 'short': 'Ganzhou'}, {'id': '197', 'provincecode': '440000', 'city': '广州市', 'code': '440100', 'initial': 'G', 'short': 'Guangzhou'}, {'id': '220', 'provincecode': '450000', 'city': '桂林市', 'code': '450300', 'initial': 'G', 'short': 'Guilin'}, {'id': '225', 'provincecode': '450000', 'city': '贵港市', 'code': '450800', 'initial': 'G', 'short': 'Guigang'}, {'id': '244', 'provincecode': '510000', 'city': '广元市', 'code': '510800', 'initial': 'G', 'short': 'Guangyuan'}, {'id': '251', 'provincecode': '510000', 'city': '广安市', 'code': '511600', 'initial': 'G', 'short': 'Guangan'}, {'id': '257', 'provincecode': '510000', 'city': '甘孜藏族自治州', 'code': '513300', 'initial': 'G', 'short': 'Ganzi'}, {'id': '259', 'provincecode': '520000', 'city': '贵阳市', 'code': '520100', 'initial': 'G', 'short': 'Guiyang'}, {'id': '314', 'provincecode': '620000', 'city': '甘南藏族自治州', 'code': '623000', 'initial': 'G', 'short': 'Gannan'}, {'id': '320', 'provincecode': '630000', 'city': '果洛藏族自治州', 'code': '632600', 'initial': 'G', 'short': 'Guoluo'}, {'id': '326', 'provincecode': '640000', 'city': '固原市', 'code': '640400', 'initial': 'G', 'short': 'Guyuan'}, {'id': '5', 'provincecode': '130000', 'city': '邯郸市', 'code': '130400', 'initial': 'H', 'short': 'Handan'}, {'id': '12', 'provincecode': '130000', 'city': '衡水市', 'code': '131100', 'initial': 'H', 'short': 'Hengshui'}, {'id': '', 'provincecode': '370000', 'city': '菏泽市', 'code': '371700', 'initial': 'H', 'short': 'Heze'}, {'id': '24', 'provincecode': '150000', 'city': '呼和浩特市', 'code': '150100', 'initial': 'H', 'short': 'Huhehaote'}, {'id': '30', 'provincecode': '150000', 'city': '呼伦贝尔市', 'code': '150700', 'initial': 'H', 'short': 'Hulunbeier'}, {'id': '49', 'provincecode': '210000', 'city': '葫芦岛市', 'code': '211400', 'initial': 'H', 'short': 'Huludao'}, {'id': '59', 'provincecode': '230000', 'city': '哈尔滨市', 'code': '230100', 'initial': 'H', 'short': 'Haerbin'}, {'id': '62', 'provincecode': '230000', 'city': '鹤岗市', 'code': '230400', 'initial': 'H', 'short': 'Hegang'}, {'id': '69', 'provincecode': '230000', 'city': '黑河市', 'code': '231100', 'initial': 'H', 'short': 'Heihe'}, {'id': '81', 'provincecode': '320000', 'city': '淮安市', 'code': '320800', 'initial': 'H', 'short': 'Huaian'}, {'id': '87', 'provincecode': '330000', 'city': '杭州市', 'code': '330100', 'initial': 'H', 'short': 'Hangzhou'}, {'id': '91', 'provincecode': '330000', 'city': '湖州市', 'code': '330500', 'initial': 'H', 'short': 'Huzhou'}, {'id': '98', 'provincecode': '340000', 'city': '合肥市', 'code': '340100', 'initial': 'H', 'short': 'Hefei'}, {'id': '101', 'provincecode': '340000', 'city': '淮南市', 'code': '340400', 'initial': 'H', 'short': 'Huainan'}, {'id': '103', 'provincecode': '340000', 'city': '淮北市', 'code': '340600', 'initial': 'H', 'short': 'Huaibei'}, {'id': '106', 'provincecode': '340000', 'city': '黄山市', 'code': '341000', 'initial': 'H', 'short': 'Huangshan'}, {'id': '112', 'provincecode': '340000', 'city': '亳州市', 'code': '341600', 'initial': 'H', 'short': 'Bozhou'}, {'id': '157', 'provincecode': '410000', 'city': '鹤壁市', 'code': '410600', 'initial': 'H', 'short': 'Hebi'}, {'id': '170', 'provincecode': '420000', 'city': '黄石市', 'code': '420200', 'initial': 'H', 'short': 'Huangshi'}, {'id': '178', 'provincecode': '420000', 'city': '黄冈市', 'code': '421100', 'initial': 'H', 'short': 'Huanggang'}, {'id': '186', 'provincecode': '430000', 'city': '衡阳市', 'code': '430400', 'initial': 'H', 'short': 'Hengyang'}, {'id': '194', 'provincecode': '430000', 'city': '怀化市', 'code': '431200', 'initial': 'H', 'short': 'Huaihua'}, {'id': '207', 'provincecode': '440000', 'city': '惠州市', 'code': '441300', 'initial': 'H', 'short': 'Huizhou'}, {'id': '210', 'provincecode': '440000', 'city': '河源市', 'code': '441600', 'initial': 'H', 'short': 'Heyuan'}, {'id': '228', 'provincecode': '450000', 'city': '贺州市', 'code': '451100', 'initial': 'H', 'short': 'Hezhou'}, {'id': '229', 'provincecode': '450000', 'city': '河池市', 'code': '451200', 'initial': 'H', 'short': 'Hechi'}, {'id': '232', 'provincecode': '460000', 'city': '海口市', 'code': '460100', 'initial': 'H', 'short': 'Haikou'}, {'id': '277', 'provincecode': '530000', 'city': '红河哈尼族彝族自治州', 'code': '532500', 'initial': 'H', 'short': 'Honghe'}, {'id': '297', 'provincecode': '610000', 'city': '汉中市', 'code': '610700', 'initial': 'H', 'short': 'Hanzhong'}, {'id': '316', 'provincecode': '630000', 'city': '海东地区', 'code': '632100', 'initial': 'H', 'short': 'Haidong'}, {'id': '317', 'provincecode': '630000', 'city': '海北藏族自治州', 'code': '632200', 'initial': 'H', 'short': 'Haibei'}, {'id': '318', 'provincecode': '630000', 'city': '黄南藏族自治州', 'code': '632300', 'initial': 'H', 'short': 'Huangnan'}, {'id': '319', 'provincecode': '630000', 'city': '海南藏族自治州', 'code': '632500', 'initial': 'H', 'short': 'Hainan'}, {'id': '322', 'provincecode': '630000', 'city': '海西蒙古族藏族自治州', 'code': '632800', 'initial': 'H', 'short': 'Haixi'}, {'id': '331', 'provincecode': '650000', 'city': '哈密地区', 'code': '652200', 'initial': 'H', 'short': 'Hami'}, {'id': '338', 'provincecode': '650000', 'city': '和田地区', 'code': '653200', 'initial': 'H', 'short': 'Hetiandi'}, {'id': '17', 'provincecode': '140000', 'city': '晋城市', 'code': '140500', 'initial': 'J', 'short': 'Jincheng'}, {'id': '19', 'provincecode': '140000', 'city': '晋中市', 'code': '140700', 'initial': 'J', 'short': 'Jinzhong'}, {'id': '42', 'provincecode': '210000', 'city': '锦州市', 'code': '210700', 'initial': 'J', 'short': 'Jinzhou'}, {'id': '51', 'provincecode': '220000', 'city': '吉林市', 'code': '220200', 'initial': 'J', 'short': 'Jilin'}, {'id': '61', 'provincecode': '230000', 'city': '鸡西市', 'code': '230300', 'initial': 'J', 'short': 'Jixi'}, {'id': '66', 'provincecode': '230000', 'city': '佳木斯市', 'code': '230800', 'initial': 'J', 'short': 'Jiamusi'}, {'id': '90', 'provincecode': '330000', 'city': '嘉兴市', 'code': '330400', 'initial': 'J', 'short': 'Jiaxing'}, {'id': '93', 'provincecode': '330000', 'city': '金华市', 'code': '330700', 'initial': 'J', 'short': 'Jinhua'}, {'id': '125', 'provincecode': '360000', 'city': '景德镇市', 'code': '360200', 'initial': 'J', 'short': 'Jingdezhen'}, {'id': '127', 'provincecode': '360000', 'city': '九江市', 'code': '360400', 'initial': 'J', 'short': 'Jiujiang'}, {'id': '131', 'provincecode': '360000', 'city': '吉安市', 'code': '360800', 'initial': 'J', 'short': 'Jian'}, {'id': '135', 'provincecode': '370000', 'city': '济南市', 'code': '370100', 'initial': 'J', 'short': 'Jinan'}, {'id': '142', 'provincecode': '370000', 'city': '济宁市', 'code': '370800', 'initial': 'J', 'short': 'Jining'}, {'id': '159', 'provincecode': '410000', 'city': '焦作市', 'code': '410800', 'initial': 'J', 'short': 'Jiaozuo'}, {'id': '175', 'provincecode': '420000', 'city': '荆门市', 'code': '420800', 'initial': 'J', 'short': 'Jingmen'}, {'id': '177', 'provincecode': '420000', 'city': '荆州市', 'code': '421000', 'initial': 'J', 'short': 'Jingzhou'}, {'id': '203', 'provincecode': '440000', 'city': '江门市', 'code': '440700', 'initial': 'J', 'short': 'Jiangmen'}, {'id': '216', 'provincecode': '440000', 'city': '揭阳市', 'code': '445200', 'initial': 'J', 'short': 'Jieyang'}, {'id': '302', 'provincecode': '620000', 'city': '嘉峪关市', 'code': '620200', 'initial': 'J', 'short': 'Jiayuguan'}, {'id': '303', 'provincecode': '620000', 'city': '金昌市', 'code': '620300', 'initial': 'J', 'short': 'Jinchang'}, {'id': '309', 'provincecode': '620000', 'city': '酒泉市', 'code': '620900', 'initial': 'J', 'short': 'Jiuquan'}, {'id': '153', 'provincecode': '410000', 'city': '开封市', 'code': '410200', 'initial': 'K', 'short': 'Kaifeng'}, {'id': '268', 'provincecode': '530000', 'city': '昆明市', 'code': '530100', 'initial': 'K', 'short': 'Kunming'}, {'id': '329', 'provincecode': '650000', 'city': '克拉玛依市', 'code': '650200', 'initial': 'K', 'short': 'Kelamayi'}, {'id': '336', 'provincecode': '650000', 'city': '克孜勒苏柯尔克孜自治州', 'code': '653000', 'initial': 'K', 'short': 'Kezile'}, {'id': '337', 'provincecode': '650000', 'city': '喀什地区', 'code': '653100', 'initial': 'K', 'short': 'Kashidi'}, {'id': '11', 'provincecode': '130000', 'city': '廊坊市', 'code': '131000', 'initial': 'L', 'short': 'Langfang'}, {'id': '22', 'provincecode': '140000', 'city': '临汾市', 'code': '141000', 'initial': 'L', 'short': 'Linfen'}, {'id': '23', 'provincecode': '140000', 'city': '吕梁市', 'code': '141100', 'initial': 'L', 'short': 'Lvliang'}, {'id': '45', 'provincecode': '210000', 'city': '辽阳市', 'code': '211000', 'initial': 'L', 'short': 'Liaoyang'}, {'id': '53', 'provincecode': '220000', 'city': '辽源市', 'code': '220400', 'initial': 'L', 'short': 'Liaoyuan'}, {'id': '80', 'provincecode': '320000', 'city': '连云港市', 'code': '320700', 'initial': 'L', 'short': 'Lianyungang'}, {'id': '97', 'provincecode': '330000', 'city': '丽水市', 'code': '331100', 'initial': 'L', 'short': 'Lishui'}, {'id': '111', 'provincecode': '340000', 'city': '六安市', 'code': '341500', 'initial': 'L', 'short': 'Liuan'}, {'id': '122', 'provincecode': '350000', 'city': '龙岩市', 'code': '350800', 'initial': 'L', 'short': 'Longyan'}, {'id': '146', 'provincecode': '370000', 'city': '莱芜市', 'code': '371200', 'initial': 'L', 'short': 'Laiwu'}, {'id': '147', 'provincecode': '370000', 'city': '临沂市', 'code': '371300', 'initial': 'L', 'short': 'Linyi'}, {'id': '149', 'provincecode': '370000', 'city': '聊城市', 'code': '371500', 'initial': 'L', 'short': 'Liaocheng'}, {'id': '154', 'provincecode': '410000', 'city': '洛阳市', 'code': '410300', 'initial': 'L', 'short': 'Luoyang'}, {'id': '162', 'provincecode': '410000', 'city': '漯河市', 'code': '411100', 'initial': 'L', 'short': 'Luohe'}, {'id': '195', 'provincecode': '430000', 'city': '娄底市', 'code': '431300', 'initial': 'L', 'short': 'Loudi'}, {'id': '219', 'provincecode': '450000', 'city': '柳州市', 'code': '450200', 'initial': 'L', 'short': 'Liuzhou'}, {'id': '230', 'provincecode': '450000', 'city': '来宾市', 'code': '451300', 'initial': 'L', 'short': 'Laibin'}, {'id': '241', 'provincecode': '510000', 'city': '泸州市', 'code': '510500', 'initial': 'L', 'short': 'Luzhou'}, {'id': '247', 'provincecode': '510000', 'city': '乐山市', 'code': '511100', 'initial': 'L', 'short': 'Leshan'}, {'id': '258', 'provincecode': '510000', 'city': '凉山彝族自治州', 'code': '513400', 'initial': 'L', 'short': 'Liangshan'}, {'id': '260', 'provincecode': '520000', 'city': '六盘水市', 'code': '520200', 'initial': 'L', 'short': 'Liupanshui'}, {'id': '273', 'provincecode': '530000', 'city': '丽江市', 'code': '530700', 'initial': 'L', 'short': 'Lijiang'}, {'id': '275', 'provincecode': '530000', 'city': '临沧市', 'code': '530900', 'initial': 'L', 'short': 'Lincang'}, {'id': '284', 'provincecode': '540000', 'city': '拉萨市', 'code': '540100', 'initial': 'L', 'short': 'Lasa'}, {'id': '290', 'provincecode': '540000', 'city': '林芝地区', 'code': '542600', 'initial': 'L', 'short': 'Linzhi'}, {'id': '301', 'provincecode': '620000', 'city': '兰州市', 'code': '620100', 'initial': 'L', 'short': 'Lanzhou'}, {'id': '312', 'provincecode': '620000', 'city': '陇南市', 'code': '621200', 'initial': 'L', 'short': 'Longnan'}, {'id': '313', 'provincecode': '620000', 'city': '临夏回族自治州', 'code': '622900', 'initial': 'L', 'short': 'Linxia'}, {'id': '68', 'provincecode': '230000', 'city': '牡丹江市', 'code': '231000', 'initial': 'M', 'short': 'Mudanjiang'}, {'id': '102', 'provincecode': '340000', 'city': '马鞍山市', 'code': '340500', 'initial': 'M', 'short': 'Maanshan'}, {'id': '205', 'provincecode': '440000', 'city': '茂名市', 'code': '440900', 'initial': 'M', 'short': 'Maoming'}, {'id': '208', 'provincecode': '440000', 'city': '梅州市', 'code': '441400', 'initial': 'M', 'short': 'Meizhou'}, {'id': '243', 'provincecode': '510000', 'city': '绵阳市', 'code': '510700', 'initial': 'M', 'short': 'Mianyang'}, {'id': '249', 'provincecode': '510000', 'city': '眉山市', 'code': '511400', 'initial': 'M', 'short': 'Meishan'}, {'id': '74', 'provincecode': '320000', 'city': '南京市', 'code': '320100', 'initial': 'N', 'short': 'Nanjing'}, {'id': '79', 'provincecode': '320000', 'city': '南通市', 'code': '320600', 'initial': 'N', 'short': 'Nantong'}, {'id': '88', 'provincecode': '330000', 'city': '宁波市', 'code': '330200', 'initial': 'N', 'short': 'Ningbo'}, {'id': '121', 'provincecode': '350000', 'city': '南平市', 'code': '350700', 'initial': 'N', 'short': 'Nanping'}, {'id': '123', 'provincecode': '350000', 'city': '宁德市', 'code': '350900', 'initial': 'N', 'short': 'Ningde'}, {'id': '124', 'provincecode': '360000', 'city': '南昌市', 'code': '360100', 'initial': 'N', 'short': 'Nanchang'}, {'id': '164', 'provincecode': '410000', 'city': '南阳市', 'code': '411300', 'initial': 'N', 'short': 'Nanyang'}, {'id': '218', 'provincecode': '450000', 'city': '南宁市', 'code': '450100', 'initial': 'N', 'short': 'Nanning'}, {'id': '246', 'provincecode': '510000', 'city': '内江市', 'code': '511000', 'initial': 'N', 'short': 'Neijiang'}, {'id': '248', 'provincecode': '510000', 'city': '南充市', 'code': '511300', 'initial': 'N', 'short': 'Nanchong'}, {'id': '282', 'provincecode': '530000', 'city': '怒江傈僳族自治州', 'code': '533300', 'initial': 'N', 'short': 'Nujiang'}, {'id': '288', 'provincecode': '540000', 'city': '那曲地区', 'code': '542400', 'initial': 'N', 'short': 'Naqu'}, {'id': '46', 'provincecode': '210000', 'city': '盘锦市', 'code': '211100', 'initial': 'P', 'short': 'Panjin'}, {'id': '117', 'provincecode': '350000', 'city': '莆田市', 'code': '350300', 'initial': 'P', 'short': 'Putian'}, {'id': '126', 'provincecode': '360000', 'city': '萍乡市', 'code': '360300', 'initial': 'P', 'short': 'Pingxiang'}, {'id': '155', 'provincecode': '410000', 'city': '平顶山市', 'code': '410400', 'initial': 'P', 'short': 'Pingdingshan'}, {'id': '160', 'provincecode': '410000', 'city': '濮阳市', 'code': '410900', 'initial': 'P', 'short': 'Puyang'}, {'id': '240', 'provincecode': '510000', 'city': '攀枝花市', 'code': '510400', 'initial': 'P', 'short': 'Panzhihua'}, {'id': '308', 'provincecode': '620000', 'city': '平凉市', 'code': '620800', 'initial': 'P', 'short': 'Pingliang'}, {'id': '4', 'provincecode': '130000', 'city': '秦皇岛市', 'code': '130300', 'initial': 'Q', 'short': 'Qinhuangdao'}, {'id': '60', 'provincecode': '230000', 'city': '齐齐哈尔市', 'code': '230200', 'initial': 'Q', 'short': 'Qiqihaer'}, {'id': '67', 'provincecode': '230000', 'city': '七台河市', 'code': '230900', 'initial': 'Q', 'short': 'Qitaihe'}, {'id': '94', 'provincecode': '330000', 'city': '衢州市', 'code': '330800', 'initial': 'Q', 'short': 'Quzhou'}, {'id': '119', 'provincecode': '350000', 'city': '泉州市', 'code': '350500', 'initial': 'Q', 'short': 'Quanzhou'}, {'id': '136', 'provincecode': '370000', 'city': '青岛市', 'code': '370200', 'initial': 'Q', 'short': 'Qingdao'}, {'id': '212', 'provincecode': '440000', 'city': '清远市', 'code': '441800', 'initial': 'Q', 'short': 'Qingyuan'}, {'id': '224', 'provincecode': '450000', 'city': '钦州市', 'code': '450700', 'initial': 'Q', 'short': 'Qinzhou'}, {'id': '264', 'provincecode': '520000', 'city': '黔西南布依族苗族自治州', 'code': '522300', 'initial': 'Q', 'short': 'Qianxinan'}, {'id': '266', 'provincecode': '520000', 'city': '黔东南苗族侗族自治州', 'code': '522600', 'initial': 'Q', 'short': 'Qiandong'}, {'id': '267', 'provincecode': '520000', 'city': '黔南布依族苗族自治州', 'code': '522700', 'initial': 'Q', 'short': 'Qiannan'}, {'id': '269', 'provincecode': '530000', 'city': '曲靖市', 'code': '530300', 'initial': 'Q', 'short': 'Qujing'}, {'id': '310', 'provincecode': '620000', 'city': '庆阳市', 'code': '621000', 'initial': 'Q', 'short': 'Qingyang'}, {'id': '145', 'provincecode': '370000', 'city': '日照市', 'code': '371100', 'initial': 'R', 'short': 'Rizhao'}, {'id': '287', 'provincecode': '540000', 'city': '日喀则地区', 'code': '542300', 'initial': 'R', 'short': 'Rikaze'}, {'id': '2', 'provincecode': '130000', 'city': '石家庄市', 'code': '130100', 'initial': 'S', 'short': 'Shijiazhuang'}, {'id': '', 'provincecode': '310000', 'city': '上海市', 'code': '310000', 'initial': 'S', 'short': 'Shanghai'}, {'id': '18', 'provincecode': '140000', 'city': '朔州市', 'code': '140600', 'initial': 'S', 'short': 'Shuozhou'}, {'id': '36', 'provincecode': '210000', 'city': '沈阳市', 'code': '210100', 'initial': 'S', 'short': 'Shenyang'}, {'id': '', 'provincecode': '530000', 'city': '普洱市', 'code': '530800', 'initial': 'P', 'short': 'Puer'}, {'id': '52', 'provincecode': '220000', 'city': '四平市', 'code': '220300', 'initial': 'S', 'short': 'Siping'}, {'id': '56', 'provincecode': '220000', 'city': '松原市', 'code': '220700', 'initial': 'S', 'short': 'Songyuan'}, {'id': '63', 'provincecode': '230000', 'city': '双鸭山市', 'code': '230500', 'initial': 'S', 'short': 'Shuangyashan'}, {'id': '70', 'provincecode': '230000', 'city': '绥化市', 'code': '231200', 'initial': 'S', 'short': 'Suihua'}, {'id': '78', 'provincecode': '320000', 'city': '苏州市', 'code': '320500', 'initial': 'S', 'short': 'Suzhou'}, {'id': '86', 'provincecode': '320000', 'city': '宿迁市', 'code': '321300', 'initial': 'S', 'short': 'Suqian'}, {'id': '92', 'provincecode': '330000', 'city': '绍兴市', 'code': '330600', 'initial': 'S', 'short': 'Shaoxing'}, {'id': '109', 'provincecode': '340000', 'city': '宿州市', 'code': '341300', 'initial': 'S', 'short': 'Suzhou'}, {'id': '118', 'provincecode': '350000', 'city': '三明市', 'code': '350400', 'initial': 'S', 'short': 'Sanming'}, {'id': '134', 'provincecode': '360000', 'city': '上饶市', 'code': '361100', 'initial': 'S', 'short': 'Shangrao'}, {'id': '163', 'provincecode': '410000', 'city': '三门峡市', 'code': '411200', 'initial': 'S', 'short': 'Sanmenxia'}, {'id': '165', 'provincecode': '410000', 'city': '商丘市', 'code': '411400', 'initial': 'S', 'short': 'Shangqiu'}, {'id': '171', 'provincecode': '420000', 'city': '十堰市', 'code': '420300', 'initial': 'S', 'short': 'Shiyan'}, {'id': '180', 'provincecode': '420000', 'city': '随州市', 'code': '421300', 'initial': 'S', 'short': 'Suizhou'}, {'id': '187', 'provincecode': '430000', 'city': '邵阳市', 'code': '430500', 'initial': 'S', 'short': 'Shaoyang'}, {'id': '198', 'provincecode': '440000', 'city': '韶关市', 'code': '440200', 'initial': 'S', 'short': 'Shaoguan'}, {'id': '199', 'provincecode': '440000', 'city': '深圳市', 'code': '440300', 'initial': 'S', 'short': 'Shenzhen'}, {'id': '201', 'provincecode': '440000', 'city': '汕头市', 'code': '440500', 'initial': 'S', 'short': 'Shantou'}, {'id': '209', 'provincecode': '440000', 'city': '汕尾市', 'code': '441500', 'initial': 'S', 'short': 'Shanwei'}, {'id': '233', 'provincecode': '460000', 'city': '三亚市', 'code': '460200', 'initial': 'S', 'short': 'Sanya'}, {'id': '245', 'provincecode': '510000', 'city': '遂宁市', 'code': '510900', 'initial': 'S', 'short': 'Suining'}, {'id': '286', 'provincecode': '540000', 'city': '山南地区', 'code': '542200', 'initial': 'S', 'short': 'Shannan'}, {'id': '300', 'provincecode': '610000', 'city': '商洛市', 'code': '611000', 'initial': 'S', 'short': 'Shangluo'}, {'id': '324', 'provincecode': '640000', 'city': '石嘴山市', 'code': '640200', 'initial': 'S', 'short': 'Shizuishan'}, {'id': '3', 'provincecode': '130000', 'city': '唐山市', 'code': '130200', 'initial': 'T', 'short': 'Tangshan'}, {'id': '13', 'provincecode': '140000', 'city': '太原市', 'code': '140100', 'initial': 'T', 'short': 'Taiyuan'}, {'id': '28', 'provincecode': '150000', 'city': '通辽市', 'code': '150500', 'initial': 'T', 'short': 'Tongliao'}, {'id': '47', 'provincecode': '210000', 'city': '铁岭市', 'code': '211200', 'initial': 'T', 'short': 'Tieling'}, {'id': '54', 'provincecode': '220000', 'city': '通化市', 'code': '220500', 'initial': 'T', 'short': 'Tonghua'}, {'id': '85', 'provincecode': '320000', 'city': '泰州市', 'code': '321200', 'initial': 'T', 'short': 'Taizhou'}, {'id': '96', 'provincecode': '330000', 'city': '台州市', 'code': '331000', 'initial': 'T', 'short': 'Taizhou'}, {'id': '104', 'provincecode': '340000', 'city': '铜陵市', 'code': '340700', 'initial': 'T', 'short': 'Tongling'}, {'id': '143', 'provincecode': '370000', 'city': '泰安市', 'code': '370900', 'initial': 'T', 'short': 'Taian'}, {'id': '263', 'provincecode': '520000', 'city': '铜仁地区', 'code': '522200', 'initial': 'T', 'short': 'Tongren'}, {'id': '292', 'provincecode': '610000', 'city': '铜川市', 'code': '610200', 'initial': 'T', 'short': 'Tongchuan'}, {'id': '305', 'provincecode': '620000', 'city': '天水市', 'code': '620500', 'initial': 'T', 'short': 'Tianshui'}, {'id': '330', 'provincecode': '650000', 'city': '吐鲁番地区', 'code': '652100', 'initial': 'T', 'short': 'Tulufan'}, {'id': '340', 'provincecode': '650000', 'city': '塔城地区', 'code': '654200', 'initial': 'T', 'short': 'Tachengdi'}, {'id': '343', 'provincecode': '120000', 'city': '天津市', 'code': '120000', 'initial': 'T', 'short': 'Tianjin'}, {'id': '26', 'provincecode': '150000', 'city': '乌海市', 'code': '150300', 'initial': 'W', 'short': 'Wuhai'}, {'id': '32', 'provincecode': '150000', 'city': '乌兰察布市', 'code': '150900', 'initial': 'W', 'short': 'Wulanchabu'}, {'id': '75', 'provincecode': '320000', 'city': '无锡市', 'code': '320200', 'initial': 'W', 'short': 'Wuxi'}, {'id': '89', 'provincecode': '330000', 'city': '温州市', 'code': '330300', 'initial': 'W', 'short': 'Wenzhou'}, {'id': '99', 'provincecode': '340000', 'city': '芜湖市', 'code': '340200', 'initial': 'W', 'short': 'Wuhu'}, {'id': '141', 'provincecode': '370000', 'city': '潍坊市', 'code': '370700', 'initial': 'W', 'short': 'Weifang'}, {'id': '144', 'provincecode': '370000', 'city': '威海市', 'code': '371000', 'initial': 'W', 'short': 'Weihai'}, {'id': '169', 'provincecode': '420000', 'city': '武汉市', 'code': '420100', 'initial': 'W', 'short': 'Wuhan'}, {'id': '221', 'provincecode': '450000', 'city': '梧州市', 'code': '450400', 'initial': 'W', 'short': 'Wuzhou'}, {'id': '278', 'provincecode': '530000', 'city': '文山壮族苗族自治州', 'code': '532600', 'initial': 'W', 'short': 'Wenshan'}, {'id': '295', 'provincecode': '610000', 'city': '渭南市', 'code': '610500', 'initial': 'W', 'short': 'Weinan'}, {'id': '306', 'provincecode': '620000', 'city': '武威市', 'code': '620600', 'initial': 'W', 'short': 'Wuwei'}, {'id': '325', 'provincecode': '640000', 'city': '吴忠市', 'code': '640300', 'initial': 'W', 'short': 'Wuzhong'}, {'id': '328', 'provincecode': '650000', 'city': '乌鲁木齐市', 'code': '650100', 'initial': 'W', 'short': 'Wulumuqi'}, {'id': '6', 'provincecode': '130000', 'city': '邢台市', 'code': '130500', 'initial': 'X', 'short': 'Xingtai'}, {'id': '21', 'provincecode': '140000', 'city': '忻州市', 'code': '140900', 'initial': 'X', 'short': 'Xinzhou'}, {'id': '33', 'provincecode': '150000', 'city': '兴安盟', 'code': '152200', 'initial': 'X', 'short': 'Xinganmeng'}, {'id': '34', 'provincecode': '150000', 'city': '锡林郭勒盟', 'code': '152500', 'initial': 'X', 'short': 'Xilinguolemeng'}, {'id': '76', 'provincecode': '320000', 'city': '徐州市', 'code': '320300', 'initial': 'X', 'short': 'Xuzhou'}, {'id': '114', 'provincecode': '340000', 'city': '宣城市', 'code': '341800', 'initial': 'X', 'short': 'Xuancheng'}, {'id': '116', 'provincecode': '350000', 'city': '厦门市', 'code': '350200', 'initial': 'X', 'short': 'Xiamen'}, {'id': '128', 'provincecode': '360000', 'city': '新余市', 'code': '360500', 'initial': 'X', 'short': 'Xinyu'}, {'id': '158', 'provincecode': '410000', 'city': '新乡市', 'code': '410700', 'initial': 'X', 'short': 'Xinxiang'}, {'id': '161', 'provincecode': '410000', 'city': '许昌市', 'code': '411000', 'initial': 'X', 'short': 'Xuchang'}, {'id': '166', 'provincecode': '410000', 'city': '信阳市', 'code': '411500', 'initial': 'X', 'short': 'Xinyang'}, {'id': '173', 'provincecode': '420000', 'city': '襄樊市', 'code': '420600', 'initial': 'X', 'short': 'Xiangfan'}, {'id': '176', 'provincecode': '420000', 'city': '孝感市', 'code': '420900', 'initial': 'X', 'short': 'Xiaogan'}, {'id': '179', 'provincecode': '420000', 'city': '咸宁市', 'code': '421200', 'initial': 'X', 'short': 'Xianning'}, {'id': '185', 'provincecode': '430000', 'city': '湘潭市', 'code': '430300', 'initial': 'X', 'short': 'Xiangtan'}, {'id': '196', 'provincecode': '430000', 'city': '湘西土家族苗族自治州', 'code': '433100', 'initial': 'X', 'short': 'Xiangxi'}, {'id': '279', 'provincecode': '530000', 'city': '西双版纳傣族自治州', 'code': '532800', 'initial': 'X', 'short': 'Xishuangbanna'}, {'id': '291', 'provincecode': '610000', 'city': '西安市', 'code': '610100', 'initial': 'X', 'short': 'Xian'}, {'id': '294', 'provincecode': '610000', 'city': '咸阳市', 'code': '610400', 'initial': 'X', 'short': 'Xianyang'}, {'id': '315', 'provincecode': '630000', 'city': '西宁市', 'code': '630100', 'initial': 'X', 'short': 'Xining'}, {'id': '15', 'provincecode': '140000', 'city': '阳泉市', 'code': '140300', 'initial': 'Y', 'short': 'Yangquan'}, {'id': '20', 'provincecode': '140000', 'city': '运城市', 'code': '140800', 'initial': 'Y', 'short': 'Yuncheng'}, {'id': '43', 'provincecode': '210000', 'city': '营口市', 'code': '210800', 'initial': 'Y', 'short': 'Yingkou'}, {'id': '58', 'provincecode': '220000', 'city': '延边朝鲜族自治州', 'code': '222400', 'initial': 'Y', 'short': 'Yanbian'}, {'id': '65', 'provincecode': '230000', 'city': '伊春市', 'code': '230700', 'initial': 'Y', 'short': 'Yichun'}, {'id': '82', 'provincecode': '320000', 'city': '盐城市', 'code': '320900', 'initial': 'Y', 'short': 'Yancheng'}, {'id': '83', 'provincecode': '320000', 'city': '扬州市', 'code': '321000', 'initial': 'Y', 'short': 'Yangzhou'}, {'id': '129', 'provincecode': '360000', 'city': '鹰潭市', 'code': '360600', 'initial': 'Y', 'short': 'Yingtan'}, {'id': '132', 'provincecode': '360000', 'city': '宜春市', 'code': '360900', 'initial': 'Y', 'short': 'Yichun'}, {'id': '140', 'provincecode': '370000', 'city': '烟台市', 'code': '370600', 'initial': 'Y', 'short': 'Yantai'}, {'id': '172', 'provincecode': '420000', 'city': '宜昌市', 'code': '420500', 'initial': 'Y', 'short': 'Yichang'}, {'id': '188', 'provincecode': '430000', 'city': '岳阳市', 'code': '430600', 'initial': 'Y', 'short': 'Yueyang'}, {'id': '191', 'provincecode': '430000', 'city': '益阳市', 'code': '430900', 'initial': 'Y', 'short': 'Yiyang'}, {'id': '193', 'provincecode': '430000', 'city': '永州市', 'code': '431100', 'initial': 'Y', 'short': 'Yongzhou'}, {'id': '211', 'provincecode': '440000', 'city': '阳江市', 'code': '441700', 'initial': 'Y', 'short': 'Yangjiang'}, {'id': '217', 'provincecode': '440000', 'city': '云浮市', 'code': '445300', 'initial': 'Y', 'short': 'Yunfu'}, {'id': '226', 'provincecode': '450000', 'city': '玉林市', 'code': '450900', 'initial': 'Y', 'short': 'Yulin'}, {'id': '250', 'provincecode': '510000', 'city': '宜宾市', 'code': '511500', 'initial': 'Y', 'short': 'Yibin'}, {'id': '253', 'provincecode': '510000', 'city': '雅安市', 'code': '511800', 'initial': 'Y', 'short': 'Yaan'}, {'id': '270', 'provincecode': '530000', 'city': '玉溪市', 'code': '530400', 'initial': 'Y', 'short': 'Yuxi'}, {'id': '296', 'provincecode': '610000', 'city': '延安市', 'code': '610600', 'initial': 'Y', 'short': 'Yanan'}, {'id': '298', 'provincecode': '610000', 'city': '榆林市', 'code': '610800', 'initial': 'Y', 'short': 'Yulin'}, {'id': '321', 'provincecode': '630000', 'city': '玉树藏族自治州', 'code': '632700', 'initial': 'Y', 'short': 'Yushu'}, {'id': '323', 'provincecode': '640000', 'city': '银川市', 'code': '640100', 'initial': 'Y', 'short': 'Yinchuan'}, {'id': '339', 'provincecode': '650000', 'city': '伊犁哈萨克自治州', 'code': '654000', 'initial': 'Y', 'short': 'Yilihasake'}, {'id': '8', 'provincecode': '130000', 'city': '张家口市', 'code': '130700', 'initial': 'Z', 'short': 'Zhangjiakou'}, {'id': '84', 'provincecode': '320000', 'city': '镇江市', 'code': '321100', 'initial': 'Z', 'short': 'Zhenjiang'}, {'id': '95', 'provincecode': '330000', 'city': '舟山市', 'code': '330900', 'initial': 'Z', 'short': 'Zhoushan'}, {'id': '120', 'provincecode': '350000', 'city': '漳州市', 'code': '350600', 'initial': 'Z', 'short': 'Zhangzhou'}, {'id': '137', 'provincecode': '370000', 'city': '淄博市', 'code': '370300', 'initial': 'Z', 'short': 'Zibo'}, {'id': '138', 'provincecode': '370000', 'city': '枣庄市', 'code': '370400', 'initial': 'Z', 'short': 'Zaozhuang'}, {'id': '152', 'provincecode': '410000', 'city': '郑州市', 'code': '410100', 'initial': 'Z', 'short': 'Zhengzhou'}, {'id': '167', 'provincecode': '410000', 'city': '周口市', 'code': '411600', 'initial': 'Z', 'short': 'Zhoukou'}, {'id': '168', 'provincecode': '410000', 'city': '驻马店市', 'code': '411700', 'initial': 'Z', 'short': 'Zhumadian'}, {'id': '184', 'provincecode': '430000', 'city': '株洲市', 'code': '430200', 'initial': 'Z', 'short': 'Zhuzhou'}, {'id': '190', 'provincecode': '430000', 'city': '张家界市', 'code': '430800', 'initial': 'Z', 'short': 'Zhangjiajie'}, {'id': '200', 'provincecode': '440000', 'city': '珠海市', 'code': '440400', 'initial': 'Z', 'short': 'Zhuhai'}, {'id': '204', 'provincecode': '440000', 'city': '湛江市', 'code': '440800', 'initial': 'Z', 'short': 'Zhanjiang'}, {'id': '206', 'provincecode': '440000', 'city': '肇庆市', 'code': '441200', 'initial': 'Z', 'short': 'Zhaoqing'}, {'id': '214', 'provincecode': '440000', 'city': '中山市', 'code': '442000', 'initial': 'Z', 'short': 'Zhongshan'}, {'id': '239', 'provincecode': '510000', 'city': '自贡市', 'code': '510300', 'initial': 'Z', 'short': 'Zigong'}, {'id': '255', 'provincecode': '510000', 'city': '资阳市', 'code': '512000', 'initial': 'Z', 'short': 'Ziyang'}, {'id': '261', 'provincecode': '520000', 'city': '遵义市', 'code': '520300', 'initial': 'Z', 'short': 'Zunyi'}, {'id': '272', 'provincecode': '530000', 'city': '昭通市', 'code': '530600', 'initial': 'Z', 'short': 'Zhaotong'}, {'id': '307', 'provincecode': '620000', 'city': '张掖市', 'code': '620700', 'initial': 'Z', 'short': 'Zhangye'}, {'id': '327', 'provincecode': '640000', 'city': '中卫市', 'code': '640500', 'initial': 'Z', 'short': 'Zhongwei'}
          ]
    }
  },
  watch:{
      inputName(){
        if(this.inputName.length== 0){
          this.searchlist=[]
          this.cityList()
        }
      }
  },
  computed:{
    // ...mapState(["cityName","nowPlace","longitude","latitude"])
  },
  onLoad(){
    this.setBarTitle();
    this.cityList()
  },
  methods: {
     bindBlur(e) {
      this.inputName = '';
      this.searchlist=[]
      this.cityList()
    },
    bindKeyInput(e){  //输入搜索
      //console.log(e)
       this.inputName = e.mp.detail.value;
       // 空搜索框时 取消匹配显示
        if (this.inputName.length < 1) {
          this.searchlist=[]
          this.cityList()
        }
        this.scrollTopId='citylist' 
        this.citylist=[]
        this.auto() 
        
    },
    auto(){
      let inputSd = this.inputName.trim();//去掉空格
      let sd = inputSd.toLowerCase();  //转为小写
      let num = sd.length;
      let finalCityList = [];
      //拼音搜索
      let temp = this.allCity.filter(
        item => {
          let text = item.short.slice(0, num).toLowerCase(); //把拼音转为小写
          // eslint-disable-next-line
          return (text && text == sd);
        }
      );
      //中文搜索
      let tempChinese = this.allCity.filter(
        itemChinese => {
          let textChinese = itemChinese.city.slice(0,num);
          return (textChinese && textChinese == sd)
        }
      )
      if (temp[0]) {
        temp.map(
          item=>{
            let testObj = {}
            testObj.city = item.city
            testObj.code = item .code 
            finalCityList.push(testObj)
          }
        );
        
        this.searchlist = finalCityList
      }else if(tempChinese[0]){
        tempChinese.map(
          item => {
            let testObj = {}
            testObj.city=item.city
            testObj.code=item.code
            finalCityList.push(testObj)
          }
        );
        console.log(finalCityList,"城市集合")
        this.searchlist=finalCityList
      }
    },
    clickLetter(e){
        //console.log(e)
        const showLetter=e.currentTarget.dataset.letter
        this.scrollTopId = showLetter;
    },
    bindCity(e){ //点击城市
      //console.log(e)
      // this.update({ cityName: e.currentTarget.dataset.city });
      this.$emit('success',e.currentTarget.dataset.city)
      this.scrollTopId=""  //清空
      this.scrollTopId= 'currentcity'
      this.searchlist=[]
      this.cityList()
    },
    setBarTitle() {
      wx.setNavigationBarTitle({
        title: "选择城市"
      });
    },
    // 对城市信息进行分组
    cityList(){
      this.citylist=[]
      this.searchLetter.map(
      initial => {
        let tempObj = {};
          // let cityInfo = [];

       tempObj.initial = initial;
         tempObj.cityInfo = this.allCity.filter(
           city => city.initial === initial
           );

           this.citylist.push(tempObj);
        }
     );
     //console.log(this.citylist)
        //this.citylist=JSON.stringify(this.citylist)  不用转码
        //console.log(this.citylist);
    //   //return tempArr;
    
    
    },
    
   
  },

  created () {
    // let app = getApp()
    const sysInfo = wx.getSystemInfoSync();
    //console.log(sysInfo); 获取设备信息
    this.winHeight = sysInfo.windowHeight;
  }
}
</script>

<style lang="scss" scoped>
  @import "./style";
  @import "../../css/common.css";
  .searchpic{
    width:26rpx;
    height:26rpx;
    left:20rpx;
    margin-top:-13rpx;
}
.canclepic{
    width:29rpx;
    height:29rpx;
    right:20rpx;
    margin-top:-15rpx;
}
.topinput{
    background: #f1f1f1;
    border-radius: 30rpx;
    position: relative;
    padding:8rpx;
    margin:0 20rpx;
}
.topinput input{
    font-size:24rpx;
    padding-left:50rpx;
    color:#ccc;
}
.can{
    width:40rpx;
    height:32rpx;
    vertical-align: middle;
    margin-right:15rpx;
}
.item{
    font-size: 32rpx;
    font-weight: bold;
    padding:30rpx;
}
.itemlocat{
    padding:10rpx 30rpx;
    font-size:bold;
    justify-content: flex-start;
}
.itemlocat div{
    display: inline-block;
}
.name{
    width:136rpx; 
    height:55rpx;
    border-radius: 5rpx;
    line-height:55rpx;
    text-align:center;
    background:#f8f8f8;
}
.chose{
    margin-left:50rpx;
}
.cityhot{
    flex-wrap: wrap;
    padding:0rpx 30rpx 20rpx 30rpx;
}
.cityhot div{
    width:23%;
    margin-top:20rpx;
}
.cityindo{
    padding:0 30rpx;
}
.cityindo div{
    padding:30rpx 0;
    border-bottom:1rpx solid #eee;
}
// ************************************************
  .container-inner {
  display: flex;
  flex-direction: row-reverse;
}
.mylocal{
  width:100%;
}
.container {
  flex-grow: 1;
  display: flex;
  flex-direction: column;
  padding: 10rpx;
}

.searchLetter {
  flex-shrink: 0;
  width: 80rpx;
  text-align: center;
  display: flex;
  flex-direction: column;
  color: #666;

}

.searchLetter div {
  margin-top: 20rpx;
}

.touchClass {
  background-color: #fff;
  color: #fff;
  padding-top: 16rpx;
  padding-bottom: 16rpx;
}

.showSlectedLetter {
  background-color: rgba(0, 0, 0, 0.5);
  color: #fff;
  display: flex;
  justify-content: center;
  align-items: center;
  position: fixed;
  top: 50%;
  left: 50%;
  margin: -100rpx;
  width: 200rpx;
  height: 200rpx;
  border-radius: 20rpx;
  font-size: 52rpx;
  z-index: 1;
}
.thisCityName {
  display: inline-block;
  border: 1rpx solid #ff6325;
  border-radius: 8rpx;
  padding: 10rpx 0;
  font-size: 24rpx;
  color: #ff6325;
  text-align: center;
  min-width: 149.5rpx;
  margin: 16rpx 0;
}

.thishotText {
  color: #ff6325;
  font-size: 20rpx;
  margin: 0 !important;
}
input {
  background-color: #eee;
}

</style>
