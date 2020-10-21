
<template>
  <div class="FilterList">
    <div class="filter-more">
      <transition name="selectbox">
        <div>
          <el-row v-for="(item,index) in filterBox" :key="index" class="filterItem clearfix" type="flex">
            <el-col :span="2" class="filterBrand">{{item.name}}</el-col>
            <el-col :span="20" class="brandList itemBrand">
              <!-- 首字母，只有当是品牌的时候会出现 -->
              <div class="first-letter" v-if="item.key === 'brand' ">
                <el-row>
                    <el-col :span="24" >
                      <div class="spaceFilter" v-for="(v,i) in firstLetter" :key="i" @click="clickLetter('brand',v)" >
                        <a href="#" class="text-filter" >
                          <span :class="{isActive:v.active}">{{v.text}}</span>
                        </a>
                      </div>
                    </el-col>
                </el-row>
              </div>
              <div class="item-content clearfix" :class="{showAll: item.showAllFlag}">
                <div class="spaceFilter" v-for="(v,i) in item.items" :key="i" @click="clickRange(item, v)">
                  <a href="#" class="text-filter" >
                    <!-- <span :class="{ isActive:v.active}">{{v.text}}{{v.num !== undefined ?`(${v.num})` : ''}}</span> -->
                    <span :class="{ isActive:v.text=== selectObj[item.key].text}">{{v.text}}{{v.num !== undefined ?`(${v.num})` : ''}}</span>
                  </a>
                </div>
              </div>

            </el-col>
            <el-col :span="2" class="filterAll" v-if="item.key=== 'area' || item.key === 'brand' || item.key === 'series'">
              <el-button type="text" v-if="!item.showAllFlag" @click="switchShowAll(item)" >全部<i class="el-icon-arrow-down"></i></el-button>
              <el-button type="text" v-else @click="switchShowAll(item)">收起<i class="el-icon-arrow-up"></i></el-button>
            </el-col>
          </el-row>
          <slot></slot>
        </div>
      </transition>
    </div>
  </div>
</template>

<script>
import { filter } from 'minimatch';
export default {
  'name':'FilterListSall',
  data() {
    return {
      MinPrice:'',
      MaxPrice:'',
      isActive:true,
      selectBox: [],
      QueryHotSeriesList:null,
      showAllFlag:true,
      filterBox: [
        {
          name: '所在地:',
          key: 'area',
          showAllFlag: false,
          items: [
            { value: '1', text: '不限', active: true },
          ]
        },
        {
          name: '品牌:',
          key: 'brand',
          showAllFlag: false,
          items: []
        },
        {
          name: '车系:',
          key: 'series',
          showAllFlag: false,
          items: [
            { value: '1', text: '不限', active: true },
          ]
        },
        {
          name: '价格:',
          key: 'price',
          items: [
            { value: '1', text: '不限', active: true },
            { value: '2', text: '3万以下', active: false },
            { value: '3', text: '3-5万', active: false },
            { value: '4', text: '5-8万', active: false },
            { value: '5', text: '8-10万', active: false },
            { value: '6', text: '10-15万', active: false },
            { value: '7', text: '15-20万', active: false },
            { value: '8', text: '20-30万', active: false },
            { value: '9', text: '30-50万', active: false },
          ]
        },
      ],
      selectObj: { // 选中的所有类下的某一个元素
        hot: { value: '1', text: '不限', active: true },
        area: { value: '1', text: '不限', active: true },
        brand: { value: '1', text: '不限', active: true },
        series: { value: '1', text: '不限', active: true },
        power: { value: '1', text: '不限', active: true },
        year: { value: '1', text: '不限', active: true },
        price: { value: '1', text: '不限', active: true },
        basePrice: { value: '1', text: '不限', active: true },
        mode: { value: '1', text: '不限', active: true }
      },
      firstLetter: [
        { value: '1', text: '不限', active: true },
      ],
      selectLetter: '' // 选中的首字母
    }
  },
  'props': {
    options:{
      type: Object,
      required: true
    }
  },
  mounted() {
    this.MallCount();                //根据所在地汇总
    this.QueryHotSeries({});            //查询热门车系
    // this.ProductQueryHotSeries({});     //查询品牌下热门车系
    this.QueryAllBrand({});             //查询所有品牌（按字母分类）
    var priceActive = localStorage.getItem("priceActive")
    var brandIdActive = localStorage.getItem("brandIdActive")
    var filterConditionPrice = localStorage.getItem('filterConditionPrice');
    if  (filterConditionPrice) {
      filterConditionPrice = JSON.parse(filterConditionPrice);
      this.selectObj[filterConditionPrice.type] = filterConditionPrice.data;
      localStorage.removeItem("filterConditionPrice")
    };
    var filterConditionBrand = localStorage.getItem('filterConditionBrand');
    if  (filterConditionBrand) {
      filterConditionBrand = JSON.parse(filterConditionBrand);
      this.selectObj.brand.text = filterConditionBrand.data.text;
      this.selectObj.brand.brandId = filterConditionBrand.data.brandId;
      localStorage.removeItem("filterConditionBrand")
    };
  },
  updated(){
  },
  watch: { // 监听内部变量的变化

    selectObj: {
      handler(val) {
        // console.log('%cselectObj变了','color:green', val)
        this.SearchSend();
      },
      deep: true
    },
  },
  methods: {
    SearchSend() {
      
        var brandId =  this.selectObj.brand ? this.selectObj.brand.brandId : '';
        var seriesId = this.selectObj.series ? this.selectObj.series.seriesId : '';
        var price = this.selectObj.price ? this.selectObj.price.text : this.MinPrice+'-'+this.MaxPrice+'万' ? this.MinPrice+'-'+this.MaxPrice+'万' : '';
        var area =  this.selectObj.area ? this.selectObj.area.text : '';
        if(price == '-万'){
          price = '';
        }else if(this.selectObj.price){
          this.MinPrice = ''
          this.MaxPrice = ''
        }
        this.$emit('sendValueToParent',brandId,seriesId,price,area,true);
    },
    //查询所有品牌（按字母分类）
    QueryAllBrand() {
      this.$api.home.queryAllBrand().then(res => {
          if (res.data && res.data.length) {
            const list = res.data.map(el =>{
              const ret = el;
              ret.active = false;
              ret.text = el.initial;
              return ret;
            })
            this.firstLetter.push(...list || [])
            const allList = []
            for (let i = 0; i < this.firstLetter.length; i++) {
                let brandList = this.firstLetter[i].brandList;
                if (brandList && brandList.length) {
                  brandList = brandList.map(el =>{
                    return {
                      ...el,
                      active: false,
                      text: el.name,
                    }
                  })
                  allList.push(...brandList)
                }
            }
            // this.filterBox[1].items = allList;
            this.filterBox[1].items = [{ value: '1', text: '不限', active: true },...allList]
          }
      })
    },
    //查询品牌下的热门车系
    // ProductQueryHotSeries() {
    //   const params = {
    //     brandId : 7
    //   }
    //   const id = 7;
    //   this.$api.home.productQueryHotSeries(id, params).then(res => {

    //   })
    // },
    //查询热门车系
    QueryHotSeries() {
      this.$api.home.queryHotSeries().then(res => {
          if (res.data && res.data.length) {
            const list = res.data.map(el =>{
              const ret = el;
              ret.active = false;
              ret.text = el.name;
              return ret;
            })
            this.filterBox[2].items.push(...list || [])
            this.QueryHotSeriesList = list
          }
      })
    },
    // 根据所在地汇总
    MallCount() {
      this.$api.home.MallCount().then(res => {
          if (res.data && res.data.length) {
            const list = res.data.map(el =>{
              const ret = el;
              ret.active = false;
              ret.text = el.name;
              return ret;
            })
            this.filterBox[0].items.push(...list || [])
          }
      })
    },
    //查询品牌下的所有车系
    QueryAllSeries() {
      this.firstLetter.forEach(el => {
        el.active = false;
      })
      const params = {
        brandId: this.selectObj.brand.brandId ? this.selectObj.brand.brandId : 0,
      }
      this.$api.home.queryAllSeries(params.brandId, params).then(res => {
          if (res.data && res.data.length) {
            const list = res.data.map(el =>{
              const ret = el;
              ret.active = false;
              ret.text = el.name;
              return ret;
            })
            this.filterBox[2].items = [{ value: '1', text: '不限', active: true },...list]
          }
          if(params.brandId == 0){
            this.filterBox[2].items = [{ value: '1', text: '不限', active: true },...this.QueryHotSeriesList];
            this.selectObj.series={ value: '1', text: '不限', active: true };
          }
      })
    },
    //根据首字母
    clickLetter(type, v) {
      this.firstLetter.forEach(el => {
        el.active = false;
      })
      v.active = !v.active;
      if (v.active) {
        this.selectLetter = v;
      } else {
        this.selectLetter = null;
      }
      if (v.brandList !== undefined) { // 点击其他字母
        const brandList = v.brandList.map(el => {
                            return {
                              ...el,
                              active: false,
                              text: el.name,
                            }
                        })
        // this.filterBox[1].items = brandList;
        this.filterBox[1].items = [{ value: '1', text: '不限', active: true },...brandList];
      } else { // 点击不限
        const allList = []
     if(this.selectObj.series !== ''){
         this.selectObj.series.active = false;
       }
        for (let i = 0; i < this.firstLetter.length; i++) {
            let brandList = this.firstLetter[i].brandList;
            if (brandList && brandList.length) {
              brandList = brandList.map(el =>{
                return {
                  ...el,
                  active: false,
                  text: el.name,
                }
              })
              allList.push(...brandList)
            }
        }
        this.filterBox[1].items = [{ value: '1', text: '不限', active: true },...allList];
        this.filterBox[2].items = [{ value: '1', text: '不限', active: true },...this.QueryHotSeriesList];
        this.selectObj.brand={ value: '1', text: '不限', active: true };
        this.selectObj.series={ value: '1', text: '不限', active: true };
      }
    },
    switchShowAll(item){
      item.showAllFlag = !item.showAllFlag;
    },
    clickRange(item, v) {
      // 先把当前类的所有元素都清除选中状态
      (item.items || []).forEach(el => {
        el.active = false;
      })
      // 切换当前点击的选中状态
      v.active = !v.active;
      if (v.active) { // 如果是选中当前状态，就把选中的这一项存到selectObj对象中。
        const key = item.key;
        this.selectObj[key] = v;
      } else { // 如果是取消选中状态，就从selectObj中把选中的给删掉。
        this.selectObj[key] = null;
      }
      if(item.key == "brand"){
         this.QueryAllSeries({});            //查询品牌下所有车系
      }
    
      
    },
  },

}
</script>

<style scoped lang="less">
.clearfix::after {
  content: "";
  display: block;
  height: 0;
  clear: both;
}

.FilterList {
  height: 100%;
}
.filter-more {
  width: 100%;
  border: 1px solid rgba(239,239,239,1);
  border-bottom: none;
}
// .el-button--text{
//   // color: #4A4A4A;
// }
.filterItem {
  // height: 60px;
  color: #8a8a8a;
  text-align: left;
  border-bottom: 1px solid rgba(239,239,239,1);
  overflow: hidden;
}
.filterItem a {
  height: 30px;
  display: inline-block;
  line-height: 30px;
  text-align: center;
  color:#4A4A4A;
  // padding: 0px 10px;
}
.filterBrand{
  padding-left: 15px;
  background: #FAFAFA;
  // height: 60px;
  line-height: 60px;
  color: #000;
  // border-bottom: 1px solid rgba(239,239,239,1);
}
.brandList{
  line-height: 60px;
  // overflow:hidden;
  max-height: 200px;
  overflow-y: auto;
  text-overflow:ellipsis;
  display:-webkit-box;
  -webkit-box-orient:vertical;
  -webkit-line-clamp:1;
  padding-left: 15px;
}
.brandList .first-letter{
  height: 32px;
  line-height: 46px;
}
.filterAll{
  // height: 60px;
  line-height: 60px;
  text-align: center;
  font-size: 14px;
}
.filterAll2{
  display: flex;
  justify-content: flex-start;
  line-height: 60px;
  align-items: center;
  margin-left: -160px;
}
.filterAll2 input{
    text-indent: 10px;
    width: 60px;
}
.filterAll2 a .buttonT{
  width: 58px;
  height: 28px;
  margin-left: 10px;
  line-height: 28px;
  display: block;
  background-color: #4685E8;
  color: #ffffff;
  text-align: center;
}
.filterInput{
  width: 60px;
  height: 26px;
  border: 1px solid rgba(239,239,239,1);
  line-height: 60px;
}
.clickNow{
  background: #4685E8;
  color: #fff !important;
}
.spaceFilter{
  margin:0px 4px;
  float: left;
}
.item-content {
  height: 60px;
  overflow: hidden;
}
.showAll {
  height: auto;
}
.item-content .spaceFilter{
  display: inline-block;
}
.text-filter {
  display: inline-block;
}
.text-filter span {
  display: inline-block;
  text-align: center;
  padding: 0px 8px;
}
.text-filter span:hover {
  color: #ffffff;
  animation: myfirst 1s;
  -moz-animation: myfirst 1s; /* Firefox */
  -webkit-animation: myfirst 1s; /* Safari and Chrome */
  -o-animation: myfirst 1s; /* Opera */
  animation-fill-mode: forwards;
}
.isActive {
  background-color: #4685E8;
  color: #ffffff;
}
.unit1{
  position: absolute;
  right: 170px;
}
@keyframes myfirst {
  from {
    background: #ffffff;
  }
  to {
    background: #4685E8;
  }
}
</style>
