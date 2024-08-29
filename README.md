# vue-mall-filter-component
A VUE mall advanced query component

### 大概效果就是这样
是之前做的组件，现在发现api没有返回值了，所以个别请求不到数据，但是基本就是这个样子，点击某项调用api重新获取数据并渲染。
#### FilterList的效果
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dc1fba1c653141c8aca0f40fa2c31d58~tplv-k3u1fbpfcp-watermark.image)

#### FilterListSall的效果
主要就是solt插槽的一个区别其他是一样的
页面引入是这样的，把需要slot的代码嵌入到组件里面，父子传参的方法就不写了。

```
<FilterListSall :options="options" @sendValueToParent="getValueFromChild">
        <el-row class="filterItem clearfix" type="flex">
          <el-col :span="2">
            <span class="lastOne">其他：</span>
          </el-col>
          <el-col :span="20">
            <div class="lastTwo">
              <el-form :inline="true" size="mini" v-model="searchForm">
                <el-select v-model="searchForm.value1" placeholder="车型" style="width:100px">
                  <el-option
                    v-for="(item,index) in Cartype"
                    :key="index"
                    :label="item.label"
                    :value="item.value"
                  ></el-option>
                </el-select>&ensp;
                <el-select v-model="searchForm.value2" placeholder="车龄">
                  <el-option
                    v-for="(item,index) in Carold"
                    :key="index"
                    :label="item.label"
                    :value="item.value"
                  ></el-option>
                </el-select>&ensp;
                <el-select v-model="searchForm.value3" placeholder="里程">
                  <el-option
                    v-for="(item,index) in Carmileage"
                    :key="index"
                    :label="item.label"
                    :value="item.value"
                  ></el-option>
                </el-select>&ensp;
                <el-select v-model="searchForm.value4" placeholder="排量">
                  <el-option
                    v-for="(item,index) in Caroutput"
                    :key="index"
                    :label="item.label"
                    :value="item.value"
                  ></el-option>
                </el-select>&ensp;
                <el-select v-model="searchForm.value5" placeholder="排放">
                  <el-option
                    v-for="(item,index) in Carstandard"
                    :key="index"
                    :label="item.label"
                    :value="item.value"
                  ></el-option>
                </el-select>&ensp;
                <el-select v-model="searchForm.value6" placeholder="颜色">
                  <el-option
                    v-for="item in Carcolor"
                    :key="item.colorId"
                    :label="item.colorName"
                    :value="item.colorName"
                  ></el-option>
                </el-select>&ensp;
                <el-select v-model="searchForm.value8" placeholder="座位数">
                  <el-option
                    v-for="(item,index) in Carnum"
                    :key="index"
                    :label="item.label"
                    :value="item.value"
                  ></el-option>
                </el-select>&ensp;
                <el-select v-model="searchForm.value9" placeholder="国别">
                  <el-option
                    v-for="(item,index) in Carcountry"
                    :key="index"
                    :label="item.label"
                    :value="item.value"
                  ></el-option>
                </el-select>&ensp;
                <el-select v-model="searchForm.value10" placeholder="燃料">
                  <el-option
                    v-for="(item,index) in Carfuel"
                    :key="index"
                    :label="item.label"
                    :value="item.value"
                  ></el-option>
                </el-select>
              </el-form>
            </div>
          </el-col>
          <el-col :span="2">
            <a href="#">
              <span class="buttonT" @click="clickTrue()">确定</span>
            </a>
          </el-col>
        </el-row>
      </FilterListSall>
      
   ```   
      
![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cb9329a36b864b62bd1222c8d36d530a~tplv-k3u1fbpfcp-watermark.image)
