<template>
  <div class="dashboard-card">
    <el-card shadow="never" class="box-card" style="height: 100%">
      <div slot="header" class="clearfix">
        <span class="dashboard-title">{{ $t('home.dashboard.scenario.title') }}</span>
      </div>
      <div v-loading="loading" element-loading-background="#FFFFFF">
        <div
          v-show="loadError"
          style="
            width: 100%;
            height: 300px;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
          ">
          <img style="height: 100px; width: 100px" src="/assets/module/figma/icon_load_error.svg" />
          <span class="addition-info-title" style="color: #646a73">{{ $t('home.dashboard.public.load_error') }}</span>
        </div>
        <div v-show="!loadError">
          <div class="main-info">
            <el-row :gutter="16">
              <el-col :span="12">
                <main-info-card
                  :title="$t('home.dashboard.scenario.title')"
                  :count-data="scenarioData"
                  redirect-page-name="scenario"
                  redirect-data-type="scenario"
                  :link-permission="['PROJECT_API_SCENARIO:READ']"
                  @redirectPage="redirectPage"
                  :is-execute-info="false" />
              </el-col>
              <el-col :span="12">
                <main-info-card
                  :link-permission="['PROJECT_API_SCENARIO:READ']"
                  :title="$t('home.dashboard.public.executed_times_in_week')"
                  :count-data="scenarioData"
                  :is-execute-info="true" />
              </el-col>
            </el-row>
          </div>
          <div class="addition-info">
            <el-row :gutter="16" style="margin: 0">
              <!--接口覆盖率-->
              <el-col :span="8" style="padding-left: 0" v-loading="coveredLoading">
                <hover-card
                  :title="$t('home.dashboard.scenario.covered_rate')"
                  :main-info="scenarioData.apiCoveredRate"
                  :tool-tip="apiCoveredRateToolTip">
                  <!--未覆盖、已覆盖-->
                  <template v-slot:mouseOut>
                    <div style="margin: 16px 0px 0px 16px">
                      <el-row>
                        <el-col :span="12">
                          <span class="addition-info-title">
                            {{ $t('home.dashboard.public.covered') }}
                          </span>
                          <div class="common-amount">
                            <el-link
                              class="addition-info-num"
                              v-permission-disable="['PROJECT_API_DEFINITION:READ']"
                              @click="redirectPage('api', 'api', 'coveredScenario', null)">
                              {{ formatAmount(scenarioData.coveredCount) }}
                            </el-link>
                          </div>
                        </el-col>
                        <el-col :span="12">
                          <span class="addition-info-title">
                            {{ $t('home.dashboard.public.not_covered') }}
                          </span>
                          <div class="common-amount">
                            <el-link
                              class="addition-info-num"
                              v-permission-disable="['PROJECT_API_DEFINITION:READ']"
                              @click="redirectPage('api', 'api', 'notCoveredScenario', null)">
                              {{ formatAmount(scenarioData.notCoveredCount) }}
                            </el-link>
                          </div>
                        </el-col>
                      </el-row>
                    </div>
                  </template>
                </hover-card>
              </el-col>
              <!--用例执行率-->
              <el-col :span="8">
                <hover-card
                  :title="$t('home.dashboard.scenario.executed_rate')"
                  :main-info="scenarioData.executedRate"
                  :tool-tip="executeRateToolTip">
                  <!--已执行、未执行-->
                  <template v-slot:mouseOut>
                    <div style="margin: 16px 0px 0px 16px">
                      <el-row>
                        <el-col :span="12">
                          <span class="addition-info-title">
                            {{ $t('home.dashboard.public.executed') }}
                          </span>
                          <div class="common-amount">
                            <el-link
                              class="addition-info-num"
                              v-permission-disable="['PROJECT_API_SCENARIO:READ']"
                              @click="redirectPage('scenario', 'scenario', 'executedCount', null)">
                              {{ formatAmount(scenarioData.executedCount) }}
                            </el-link>
                          </div>
                        </el-col>
                        <el-col :span="12">
                          <span class="addition-info-title">
                            {{ $t('home.dashboard.public.not_executed') }}
                          </span>
                          <div class="common-amount">
                            <el-link
                              class="addition-info-num"
                              v-permission-disable="['PROJECT_API_SCENARIO:READ']"
                              @click="redirectPage('scenario', 'scenario', 'unExecuteCount', null)">
                              {{ formatAmount(scenarioData.notExecutedCount) }}
                            </el-link>
                          </div>
                        </el-col>
                      </el-row>
                    </div>
                  </template>
                </hover-card>
              </el-col>
              <!--用例通过率-->
              <el-col :span="8" style="padding-right: 0">
                <hover-card
                  :title="$t('home.dashboard.scenario.pass_rate')"
                  :main-info="scenarioData.passRate"
                  :tool-tip="passRateToolTip">
                  <!--已完成、进行中、未开始-->
                  <template v-slot:mouseOut>
                    <div style="margin: 16px 0px 0px 16px">
                      <el-row>
                        <el-col :span="12">
                          <span class="addition-info-title">
                            {{ $t('home.dashboard.public.pass') }}
                          </span>
                          <div class="common-amount">
                            <el-link
                              class="addition-info-num"
                              v-permission-disable="['PROJECT_API_SCENARIO:READ']"
                              @click="redirectPage('scenario', 'scenario', 'executionPassCount', null)">
                              {{ formatAmount(scenarioData.passCount) }}
                            </el-link>
                          </div>
                        </el-col>
                        <el-col :span="12">
                          <span class="addition-info-title">
                            {{ $t('home.dashboard.public.not_pass') }}
                          </span>
                          <div class="common-amount">
                            <el-link
                              class="addition-info-num"
                              v-permission-disable="['PROJECT_API_SCENARIO:READ']"
                              @click="redirectPage('scenario', 'scenario', 'executionFailedCount', null)">
                              {{ formatAmount(scenarioData.unPassCount) }}
                            </el-link>
                          </div>
                        </el-col>
                      </el-row>
                    </div>
                  </template>
                </hover-card>
              </el-col>
            </el-row>
          </div>
        </div>
      </div>
    </el-card>
  </div>
</template>

<script>
import hoverCard from '@/business/home/components/card/HoverCard';
import mainInfoCard from '@/business/home/components/card/MainInfoCard';
import {formatNumber, scenarioCountByProjectId, scenarioCoveredByProjectId} from '@/api/home';
import {getCurrentProjectID} from 'metersphere-frontend/src/utils/token';

export default {
  name: 'ScenarioDashboard',
  components: { hoverCard, mainInfoCard },
  data() {
    return {
      loading: false,
      coveredLoading: false,
      loadError: false,
      apiCoveredRateToolTip: this.$t('api_test.home_page.formula.interface_coverage'),
      executeRateToolTip: this.$t('api_test.home_page.formula.scenario_execute'),
      passRateToolTip: this.$t('api_test.home_page.formula.pass'),
      scenarioData: {
        total: 0,
        createdInWeek: 0,
        executedTimesInWeek: 0,
        executedTimes: 0,
        apiCoveredRate: '0%',
        executedRate: '0%',
        passRate: '0%',
        coveredCount: 0,
        notCoveredCount: 0,
        executedCount: 0,
        notExecutedCount: 0,
        passCount: 0,
        unPassCount: 0,
        fakeErrorCount: 0,
      },
    };
  },
  methods: {
    search(versionId) {
      this.loading = true;
      this.coveredLoading = true;
      this.loadError = false;
      let selectProjectId = getCurrentProjectID();
      scenarioCountByProjectId(selectProjectId, versionId)
        .then((response) => {
          this.loading = false;
          this.loadError = false;
          this.parserScenarioData(response.data, false);
        })
        .catch(() => {
          this.loading = false;
          this.loadError = true;
        });
      scenarioCoveredByProjectId(selectProjectId, versionId)
        .then((response) => {
          this.coveredLoading = false;
          this.loadError = false;
          this.parserScenarioData(response.data, true);
        })
        .catch(() => {
          this.coveredLoading = false;
          this.loadError = true;
        });
    },
    parserScenarioData(scenarioResponse, isCovered) {
      if (isCovered) {
        this.scenarioData.apiCoveredRate = scenarioResponse.apiCoveredRate;
        this.scenarioData.coveredCount = scenarioResponse.coveredCount;
        this.scenarioData.notCoveredCount = scenarioResponse.notCoveredCount;
      } else {
        this.scenarioData.total = scenarioResponse.total;
        this.scenarioData.createdInWeek = scenarioResponse.createdInWeek;
        this.scenarioData.executedTimesInWeek = scenarioResponse.executedTimesInWeek;
        this.scenarioData.executedTimes = scenarioResponse.executedTimes;
        this.scenarioData.executedRate = scenarioResponse.executedRate;
        this.scenarioData.passRate = scenarioResponse.passRate;
        this.scenarioData.executedCount = scenarioResponse.executedCount;
        this.scenarioData.notExecutedCount = scenarioResponse.notExecutedCount;
        this.scenarioData.passCount = scenarioResponse.passCount;
        this.scenarioData.unPassCount = scenarioResponse.unPassCount;
        this.scenarioData.fakeErrorCount = scenarioResponse.fakeErrorCount;
      }
    },
    formatAmount(number) {
      return formatNumber(number);
    },
    redirectPage(redirectPage, dataType, selectRange, selectParam) {
      if (selectRange === 'fakeError') {
        selectRange = 'fakeErrorCount';
      }
      this.$emit('redirectPage', redirectPage, dataType, selectRange, selectParam);
    },
  },
};
</script>

<style scoped></style>
