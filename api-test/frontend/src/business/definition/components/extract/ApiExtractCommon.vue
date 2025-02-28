<template>
  <div>
    <el-row :gutter="10" type="flex" justify="space-between" align="middle">
      <el-col v-if="extractType === 'Regex'" :span="8">
        <el-select
          :disabled="isReadOnly"
          class="extract-item"
          v-model="common.useHeaders"
          :placeholder="$t('api_test.request.assertions.select_subject')"
          size="small">
          <el-option v-for="item in useHeadersOption" :key="item.value" :label="item.label" :value="item.value" />
        </el-select>
      </el-col>
      <el-col>
        <ms-api-variable-input
          :if-from-variable-advance="ifFromVariableAdvance"
          @savePreParams="savePreParams"
          :is-read-only="isReadOnly"
          v-model="common.variable"
          size="small"
          maxlength="60"
          @change="change"
          :show-copy-tip-with-multiple="common.multipleMatching"
          show-word-limit
          :placeholder="$t('api_test.variable_name')" />
      </el-col>
      <el-col>
        <el-input
          :disabled="isReadOnly"
          v-model="common.expression"
          size="small"
          show-word-limit
          :placeholder="expression"
          @click.native="savePreParams(common.variable)"
          style="width: 70%" />

        <el-autocomplete
          style="width: 25%; margin-left: 10px"
          size="small"
          placeholder="Template $i$ where i is capturing group number,stats at 1"
          v-model="common.template"
          :fetch-suggestions="querySearch" />
      </el-col>
      <el-col class="multiple_checkbox" v-if="edit">
        <el-checkbox v-model="common.multipleMatching" :disabled="isReadOnly">
          {{ $t('api_test.request.extract.multiple_matching') }}
        </el-checkbox>
      </el-col>
      <el-col class="extract-btn">
        <el-button
          :disabled="isReadOnly"
          type="danger"
          size="mini"
          icon="el-icon-delete"
          circle
          @click="remove"
          v-if="edit" />
        <el-button :disabled="isReadOnly" type="primary" size="small" @click="add" v-else>{{
          $t('commons.add')
        }}</el-button>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import { EXTRACT_TYPE, ExtractCommon } from '../../model/ApiTestModel';
import MsApiVariableInput from '../ApiVariableInput';

export default {
  name: 'MsApiExtractCommon',
  components: { MsApiVariableInput },
  props: {
    extractType: {
      type: String,
      validator: function (value) {
        return [EXTRACT_TYPE.XPATH, EXTRACT_TYPE.JSON_PATH, EXTRACT_TYPE.REGEX].indexOf(value) !== -1;
      },
    },
    common: {
      default: () => {
        return new ExtractCommon();
      },
    },
    edit: {
      type: Boolean,
      default: false,
    },
    index: Number,
    list: Array,
    callback: Function,
    isReadOnly: {
      type: Boolean,
      default: false,
    },
    ifFromVariableAdvance: {
      type: Boolean,
      default: false,
    },
  },

  data() {
    return {
      visible: false,
      useHeadersOption: [
        { label: 'Body', value: 'false' },
        { label: 'Request Headers', value: 'request_headers' },
        { label: 'Body (unescaped)', value: 'unescaped' },
        { label: 'Body as a Document', value: 'as_document' },
        { label: 'Response Headers', value: 'true' },
        { label: 'URL', value: 'URL' },
        { label: 'Response Code', value: 'code' },
        { label: 'Response Message', value: 'message' },
      ],
      restaurants: [
        { value: '$0$', id: '$0$' },
        { value: '$1$', id: '$1$' },
      ],
    };
  },
  created() {
    if (!this.common.template) {
      this.$set(this.common, 'template', '$1$');
    }
  },
  methods: {
    querySearch(queryString, cb) {
      cb(this.restaurants);
    },
    add() {
      this.common.type = this.extractType;
      this.list.push(new ExtractCommon(this.extractType, this.common));
      this.clear();
      this.callback();
    },
    change(variable) {
      this.common.value = '${' + variable + '}';
    },
    remove() {
      this.list.splice(this.index, 1);
    },
    clear() {
      this.common.variable = null;
      this.common.expression = null;
      this.common.value = null;
    },
    copy() {
      let input = document.createElement('input');
      document.body.appendChild(input);
      input.value = this.common.value;
      input.select();
      if (input.setSelectionRange) {
        input.setSelectionRange(0, input.value.length);
      }
      document.execCommand('copy');
      document.body.removeChild(input);
      this.visible = true;
      setTimeout(() => {
        this.visible = false;
      }, 1000);
    },
    savePreParams(data) {
      if (this.ifFromVariableAdvance) {
        this.$emit('savePreParams', data);
      }
    },
  },

  computed: {
    expression() {
      switch (this.extractType) {
        case EXTRACT_TYPE.REGEX:
          return this.$t('api_test.request.extract.regex_expression');
        case EXTRACT_TYPE.JSON_PATH:
          return this.$t('api_test.request.extract.json_path_expression');
        case EXTRACT_TYPE.XPATH:
          return this.$t('api_test.request.extract.xpath_expression');
        default:
          return '';
      }
    },
  },
};
</script>

<style scoped>
.variable {
  position: relative;
}

.variable-combine {
  color: #7f7f7f;
  max-width: 80px;
  line-height: 32px;
  position: absolute;
  top: 0;
  right: 25px;
  margin-right: -20px;
  display: flex;
  align-items: center;
}

.variable-combine .value {
  display: inline-block;
  max-width: 60px;
  margin-right: 10px;
  overflow-x: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.variable-combine .copy {
  font-size: 14px;
  cursor: pointer;
  color: #1e90ff;
}

.extract-btn {
  width: 60px;
}

.multiple_checkbox {
  text-align: center;
  width: 120px;
}
</style>
