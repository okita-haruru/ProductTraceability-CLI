<template>
  <div>
    <div class="crumbs">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item>
          <i class="el-icon-lx-calendar"></i> 全局查询
        </el-breadcrumb-item>
        <el-breadcrumb-item>投诉处理</el-breadcrumb-item>
      </el-breadcrumb>
    </div>
    <div class="container">
      生产单位ID：
        <el-input
          v-model="unitID"
          size="large"
          style="width: 80%; margin: auto"
          placeholder=""
        />
    </div>
    <div class="container">
      <el-button
        size="large"
        type="success"
        style="margin: auto"
        round
        @click="traceOn"
        auto-insert-space="true"
        >开始查询</el-button
      >
    </div>
    <div class="container">
        <el-table :data="traceData" stripe style="width: 100%">
        <el-table-column prop="time" label="时间" width="180" />
        <el-table-column prop="name" label="生产单位名称" width="360" />
        <el-table-column prop="result" label="结果" width="540" />
      </el-table>
      </div>

  </div>
</template>

<script>
import * as bc from '../blockchain';
import service from '../utils/request';

export default {
  name: 'trace',
  data() {
    return {
      userID: '',
      unitID: '',
      traceData: [{
        time: '', result: '', name: '',
      }],
      stateTxt: ['属实', '一般', '不属实'],

    };
  },
  methods:
  {
    traceOn() {
      this.traceData = [];
      bc.getGlobalHandleComplain(this.unitID).then((res) => {
        // console.log(res);
        for (let i = 0, len = res.length; i < len; i += 1) {
          console.log(res[i]);
          this.traceData[i] = { result: this.stateTxt[res[i].returnValues.result] };
          service.get('/unit', { params: { ID: this.unitID } }).then((record) => {
            console.log(record);
            this.traceData[i].name = `${record.name}(${res[i].returnValues.productionUnitID})`;
          });
          service.get('/time', { params: { timestamp: res[i].returnValues.timeStamp } }).then((record) => {
            this.traceData[i].time = record.time;
          });
        }
      });
    },
  },
};
</script>
