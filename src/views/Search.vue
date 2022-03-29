<template>
  <div class="search">
    <el-button type="primary" @click="init">重置</el-button>
    <el-button type="primary" @click="search">查询</el-button>
    <el-table
      :data="data1"
      :default-sort="{ prop: 'data1', order: 'descending' }"
      style="width: 100%"
    >
      <el-table-column label="Code" width="180">
        <template #default="scope">
          <el-popover placement="right" :width="400" :offset="300" trigger="click">
            <template #reference>
              <div style="color:blue;cursor: pointer;" @click="select(scope.row.CODE,0)">{{ scope.row.code }}</div>
            </template>
            <div style="background:#fff;width:600px;">
              <img :src="url" />
            </div>
          </el-popover>
        </template>
      </el-table-column>
      <!-- <el-table-column prop="code" label="Code" width="180" /> -->
      <el-table-column prop="name" label="Name" width="180" />
      <el-table-column prop="jlr_shishi" label="净流入" sortable />
      <el-table-column label="K线" width="180">
        <template #default="scope">
          <el-popover placement="left" :width="400" :offset="300" trigger="click">
            <template #reference>
              <div style="color:blue;cursor: pointer;" @click="select(scope.row.CODE,1)">日线</div>
            </template>
            <div style="background:#fff;width:600px;">
              <img :src="url" />
            </div>
          </el-popover>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
import { onMounted, reactive, ref, toRefs } from "vue";
import axios from "@/utils/axios";
import data from "../assets/json/code";
// import axios from 'axios'
// import { ElMessage } from 'element-plus'
export default {
  name: "Search",
  setup() {
    const data1 = ref([]);
    const data2 = ref([]);
    const url = ref(null)
    onMounted(() => {
      init()
    });
    const init = () => {
      axios
        .get(
          "http://quotes.money.163.com/hs/service/diyrank.php?host=http%3A%2F%2Fquotes.money.163.com%2Fhs%2Fservice%2Fdiyrank.php&page=0&query=STYPE%3AEQA&fields=NO%2CSYMBOL%2CNAME%2CPRICE%2CPERCENT%2CUPDOWN%2CFIVE_MINUTE%2COPEN%2CYESTCLOSE%2CHIGH%2CLOW%2CVOLUME%2CTURNOVER%2CHS%2CLB%2CWB%2CZF%2CPE%2CMCAP%2CTCAP%2CMFSUM%2CMFRATIO.MFRATIO2%2CMFRATIO.MFRATIO10%2CSNAME%2CCODE%2CANNOUNMT%2CUVSNEWS&sort=PERCENT&order=desc&count=24&type=query"
        )
        .then((res) => {
          console.log(res.list);
          data2.value = res.list;
        });
          // "http://quotes.money.163.com/hs/service/marketradar_ajax.php?host=http%3A%2F%2Fquotes.money.163.com%2Fhs%2Fservice%2Fmarketradar_ajax.php&page=0&query=STYPE%3AEQA&types=&count=28&type=query&order=desc"
    }
    const search = () => {
      data1.value = [];
      // data.data.forEach((item) => {
      //   let code = item[0];
      //   let name = item[1];
      //   let url = `http://quotes.money.163.com/service/zjlx_chart.html?symbol=${code.substr(2)}`;
      //   axios.get(url).then((res) => {
      //     let info = {
      //       name: name,
      //       code: code,
      //       jlr_shishi: res.jlr_shishi,
      //     };
      //     data1.value.push(info);
      //   });
      // });

      data2.value.forEach((item) => {
        let code = item.SYMBOL;
        let CODE = item.CODE;
        let name = item.NAME;
        let url = `http://quotes.money.163.com/service/zjlx_chart.html?symbol=${code}`;
        axios.get(url).then((res) => {
          let info = {
            name: name,
            code: code,
            jlr_shishi: Number(res.jlr_shishi.replace(",","")),
            CODE: CODE
          };
          // console.log(info);
          data1.value.push(info);
        });
      });
    };

    const select = (CODE, line) => {
      let code = "";
      let fast = CODE.substr(0, 1);
      if(fast == 1){
        code = `sz${CODE.substr(1, 6)}`
      }else {
        code = `sh${CODE.substr(1, 6)}`
      }
      console.log(code)
      if(line){
        url.value = `http://image.sinajs.cn/newchart/daily/n/${code}.gif`
      }else{
        url.value = `http://image.sinajs.cn/newchart/min/n/${code}.gif`
      }
    }
    return {
      // ...toRefs(state),
      data1,
      url,
      search,
      init,
      select
    };
  },
};
</script>

<style>
.search {
  margin-bottom: 20px;
}
</style>
