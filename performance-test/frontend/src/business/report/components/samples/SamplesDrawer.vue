<template>
  <div>
    <el-drawer
        :visible.sync="drawer"
        direction="rtl"
        custom-class="sample-drawer"
        :append-to-body="true"
        :size="820"
        :before-close="handleClose">
      <template v-slot:title>
        <span style="color: #1a1a1a; font-size: large;">
        {{ $t('performance_test.response_3_samples') }}
        </span>
      </template>
      <div style="margin: 0 10px 0 10px ">
        <el-collapse v-model="activeName" accordion>
          <el-collapse-item
              v-for="(sample, index) in sampleRecord" :key="index" :name="index">
            <template v-slot:title>
              <div style="font-size: 16px;color: #783887">
                <span> {{ sample.name }}</span>
              </div>
            </template>

            <request-result-tail :report-id="sample.id" :response="sample" ref="debugResult"/>
          </el-collapse-item>
        </el-collapse>
      </div>
    </el-drawer>
  </div>
</template>

<script>
import RequestResultTail from "@/business/report/components/samples/compnent/RequestResultTail";
import {datetimeFormat} from "fit2cloud-ui/src/filters/time";

export default {
  name: "ErrorSamplesTable",
  components: {RequestResultTail},
  data() {
    return {
      activeName: '1',
      sampleRecord: [],
      drawer: false,
    };
  },
  props: ['samples'],
  created() {
  },
  methods: {
    datetimeFormat,

    handleClose(done) {
      this.activeName = '1';
      this.sampleRecord = [];
      done();
    },
    openRecord(samples) {
      this.sampleRecord = [];

      this.$nextTick(() => {
        if (samples) {
          samples.forEach(sample => {
            this.sampleRecord.push(sample);
          });
        }
        this.drawer = true;
      })

    },
  },
}
</script>

<style scoped>
</style>
