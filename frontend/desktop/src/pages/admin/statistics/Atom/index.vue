/**
* Tencent is pleased to support the open source community by making 蓝鲸智云PaaS平台社区版 (BlueKing PaaS Community
* Edition) available.
* Copyright (C) 2017-2020 THL A29 Limited, a Tencent company. All rights reserved.
* Licensed under the MIT License (the "License"); you may not use this file except in compliance with the License.
* You may obtain a copy of the License at
* http://opensource.org/licenses/MIT
* Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on
* an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the
* specific language governing permissions and limitations under the License.
*/
<template>
    <div class="content-box">
        <div class="content-wrap">
            <div class="content-dimesion atom-statistics" v-bkloading="{ isLoading: isCitationLoading, opacity: 1 }">
                <div class="clearfix">
                    <div class="content-title">{{i18n.numberCitations}}</div>
                    <div class="content-date">
                        <div class="content-date-business">
                            <bk-select
                                v-model="businessSelected"
                                class="bk-select-inline"
                                :popover-width="260"
                                :searchable="true"
                                @selected="onAtomCiteData">
                                <bk-option
                                    v-for="(option, index) in businessList"
                                    :key="index"
                                    :id="option.cc_id"
                                    :name="option.cc_name">
                                </bk-option>
                            </bk-select>
                        </div>
                        <div class="content-date-picker" @click="onDatePickerClick">
                            <bk-date-picker
                                ref="datePickerRef"
                                v-model="businessTime"
                                class="bk-date-picker-common"
                                :placeholder="i18n.choice"
                                :type="'daterange'"
                                @change="onChangeBusinessTime">
                            </bk-date-picker>
                        </div>
                    </div>
                </div>
                <data-statistics :dimension-list="taskPlotData" :total-value="taskTotal"></data-statistics>
            </div>
        </div>
        <div class="content-process-detail">
            <bk-tab :type="'card'" :active="tabName" @tab-change="onChangeTabPanel">
                <bk-tab-panel name="processDetails" :label="i18n.processDetail">
                    <div class="content-wrap-detail">
                        <div class="content-wrap-from">
                            <div class="content-wrap-select">
                                <label class="content-detail-label">{{i18n.atom}}</label>
                                <bk-select
                                    v-model="selectedAtom"
                                    class="bk-select-inline"
                                    :popover-width="260"
                                    :searchable="true"
                                    :placeholder="i18n.choice"
                                    @clear="onClearAtom"
                                    @selected="onSelectedAtom">
                                    <bk-option
                                        v-for="(option, index) in componentsList"
                                        :key="index"
                                        :id="option.code"
                                        :name="option.name">
                                    </bk-option>
                                </bk-select>
                            </div>
                            <div class="content-wrap-select">
                                <label class="content-detail-label">{{i18n.taskStartTime}}</label>
                                <bk-date-picker
                                    v-model="tableTime"
                                    class="bk-date-picker-common"
                                    :placeholder="i18n.choice"
                                    :type="'daterange'"
                                    @change="onAtomTemplateData">
                                </bk-date-picker>
                            </div>
                            <div class="content-wrap-select">
                                <label class="content-detail-label">{{i18n.choiceBusiness}}</label>
                                <bk-select
                                    v-model="selectedCcId"
                                    class="bk-select-inline"
                                    :popover-width="260"
                                    :searchable="true"
                                    :placeholder="i18n.choice"
                                    @clear="onClearBizCcId"
                                    @selected="onSelectedBizCcId">
                                    <bk-option
                                        v-for="(option, index) in allBusinessList"
                                        :key="index"
                                        :id="option.cc_id"
                                        :name="option.cc_name">
                                    </bk-option>
                                </bk-select>
                            </div>
                            <div class="content-wrap-select">
                                <label class="content-detail-label">{{i18n.choiceCategory}}</label>
                                <bk-select
                                    v-model="selectedCategory"
                                    class="bk-select-inline"
                                    :popover-width="260"
                                    :searchable="true"
                                    :placeholder="i18n.choice"
                                    @clear="onClearCategory"
                                    @selected="onSelectedCategory">
                                    <bk-option
                                        v-for="(option, index) in categorys"
                                        :key="index"
                                        :id="option.value"
                                        :name="option.name">
                                    </bk-option>
                                </bk-select>
                            </div>
                        </div>
                        <data-table-pagination
                            :data="templateData"
                            :total="templateTotal"
                            :options="dataTableOptions"
                            :pagination="templatePagination"
                            :columns="templateColumns"
                            :loading="isTemplateLoading"
                            @handleSizeChange="onTemplateHandleSizeChange"
                            @handleIndexChange="onTemplateHandleIndexChange">
                        </data-table-pagination>
                    </div>
                </bk-tab-panel>
                <bk-tab-panel name="executionTime" :label="i18n.executionTime">
                    <div class="content-wrap-detail">
                        <div class="content-wrap-from">
                            <div class="content-wrap-select">
                                <label class="content-detail-label">{{i18n.taskStartTime}}</label>
                                <bk-date-picker
                                    class="bk-date-picker-common"
                                    v-model="tableTime"
                                    :placeholder="i18n.choice"
                                    :type="'daterange'"
                                    @change="onAtomExecuteData">
                                </bk-date-picker>
                            </div>
                            <div class="content-wrap-select">
                                <label class="content-detail-label">{{i18n.choiceBusiness}}</label>
                                <bk-select
                                    v-model="selectedCcId"
                                    class="bk-select-inline"
                                    :popover-width="260"
                                    :searchable="true"
                                    :placeholder="i18n.choice"
                                    @clear="onClearBizCcId"
                                    @selected="onSelectedBizCcId">
                                    <bk-option
                                        v-for="(option, index) in allBusinessList"
                                        :key="index"
                                        :id="option.cc_id"
                                        :name="option.cc_name">
                                    </bk-option>
                                </bk-select>
                            </div>
                            <div class="content-wrap-select">
                                <label class="content-detail-label">{{i18n.choiceCategory}}</label>
                                <bk-select
                                    v-model="selectedCategory"
                                    class="bk-select-inline"
                                    :popover-width="260"
                                    :searchable="true"
                                    :placeholder="i18n.choice"
                                    @clear="onClearCategory"
                                    @selected="onSelectedCategory">
                                    <bk-option
                                        v-for="(option, index) in categorys"
                                        :key="index"
                                        :id="option.value"
                                        :name="option.name">
                                    </bk-option>
                                </bk-select>
                            </div>
                        </div>
                        <data-table-pagination
                            :data="executeData"
                            :total="executeTotal"
                            :columns="executeColumns"
                            :pagination="executePagination"
                            :loading="isExecutionLoading"
                            @handleSizeChange="onExecuteHandleSizeChange"
                            @handleIndexChange="onExecuteHandleIndexChange">
                        </data-table-pagination>
                    </div>
                </bk-tab-panel>
                <bk-tab-panel name="taskDetails" :label="i18n.taskDetail">
                    <div class="content-wrap-detail">
                        <div class="content-wrap-from">
                            <div class="content-wrap-select">
                                <label class="content-detail-label">{{i18n.atom}}</label>
                                <bk-select
                                    v-model="selectedAtom"
                                    class="bk-select-inline"
                                    :popover-width="260"
                                    :searchable="true"
                                    :placeholder="i18n.choice"
                                    @clear="onClearAtom"
                                    @selected="onSelectedAtom">
                                    <bk-option
                                        v-for="(option, index) in componentsList"
                                        :key="index"
                                        :id="option.code"
                                        :name="option.name">
                                    </bk-option>
                                </bk-select>
                            </div>
                            <div class="content-wrap-select">
                                <label class="content-detail-label">{{i18n.taskStartTime}}</label>
                                <bk-date-picker
                                    class="bk-date-picker-common"
                                    v-model="tableTime"
                                    :placeholder="i18n.choice"
                                    :type="'daterange'"
                                    @change="onAtomInstanceData">
                                </bk-date-picker>
                            </div>
                            <div class="content-wrap-select">
                                <label class="content-detail-label">{{i18n.choiceBusiness}}</label>
                                <bk-select
                                    v-model="selectedCcId"
                                    class="bk-select-inline"
                                    :popover-width="260"
                                    :searchable="true"
                                    :placeholder="i18n.choice"
                                    @clear="onClearBizCcId"
                                    @selected="onSelectedBizCcId">
                                    <bk-option
                                        v-for="(option, index) in allBusinessList"
                                        :key="index"
                                        :id="option.cc_id"
                                        :name="option.cc_name">
                                    </bk-option>
                                </bk-select>
                            </div>
                            <div class="content-wrap-select">
                                <label class="content-detail-label">{{i18n.choiceCategory}}</label>
                                <bk-select
                                    v-model="selectedCategory"
                                    class="bk-select-inline"
                                    :popover-width="260"
                                    :searchable="true"
                                    :placeholder="i18n.choice"
                                    @clear="onClearCategory"
                                    @selected="onSelectedCategory">
                                    <bk-option
                                        v-for="(option, index) in categorys"
                                        :key="index"
                                        :id="option.value"
                                        :name="option.name">
                                    </bk-option>
                                </bk-select>
                            </div>
                        </div>
                        <data-table-pagination
                            :data="instanceData"
                            :total="instanceTotal"
                            :options="dataTableOptions"
                            :pagination="instancePagination"
                            :columns="instanceColumns"
                            :loading="isInstanceLoading"
                            @handleSizeChange="onInstanceHandleSizeChange"
                            @handleIndexChange="onInstanceHandleIndexChange">
                        </data-table-pagination>
                    </div>
                </bk-tab-panel>
            </bk-tab>
        </div>
    </div>
</template>
<script>
    import '@/utils/i18n.js'
    import tools from '@/utils/tools.js'
    import DataStatistics from '../dataStatistics/index.vue'
    import { mapActions, mapState } from 'vuex'
    import { AnalysisMixins } from '@/mixins/js/analysisMixins.js'
    import DataTablePagination from '@/components/common/dataTable/DataTablePagination.vue'
    import { errorHandler } from '@/utils/errorHandler.js'
    import moment from 'moment-timezone'

    const i18n = {
        numberCitations: gettext('引用次数'),
        processDetail: gettext('流程详情'),
        executionTime: gettext('执行耗时'),
        taskDetail: gettext('任务详情'),
        timeLimit: gettext('时间范围'),
        taskStartTime: gettext('任务开始时间'),
        choiceCategory: gettext('选择分类'),
        choiceBusiness: gettext('选择业务'),
        choice: gettext('请选择'),
        atom: gettext('标准插件'),
        choiceAllCategory: gettext('全部分类'),
        choiceAllBusiness: gettext('全部业务'),
        templateName: gettext('流程名称'),
        businessName: gettext('所属业务'),
        editTime: gettext('更新时间'),
        editor: gettext('更新人'),
        category: gettext('分类'),
        instanceName: gettext('任务名称'),
        createTime: gettext('创建时间'),
        creator: gettext('创建人'),
        atomTotal: gettext('标准插件数'),
        subprocessTotal: gettext('子流程数'),
        gatewaysTotal: gettext('网关数'),
        componentName: gettext('标准插件'),
        executeTimes: gettext('执行次数'),
        failedTimes: gettext('失败次数'),
        avgExecuteTime: gettext('平均执行耗时(秒)'),
        failedTimesPercent: gettext('失败率')
    }

    export default {
        name: 'StatisticsAtom',
        components: {
            DataStatistics,
            DataTablePagination
        },
        mixins: [AnalysisMixins],
        data () {
            return {
                i18n: i18n,
                bizCcId: undefined,
                category: undefined,
                choiceBusinessName: '',
                isDropdownShow: false,
                choiceDownShow: false,
                datePickerRefShow: false,
                isTemplateLoading: true,
                isCitationLoading: true,
                isExecutionLoading: true,
                isInstanceLoading: true,
                time: [0, 0],
                taskPlotData: [],
                nodeData: [],
                templateData: [],
                templateTotal: 0,
                templatePageIndex: 1,
                templateLimit: 15,
                templatePagination: {
                    limit: this.templateLimit,
                    pageIndex: this.templatePageIndex,
                    pageArray: this.dataTablePageArray
                },
                templateColumns: [
                    {
                        prop: 'templateName', // 识别id
                        label: i18n.templateName, // 表头显示名称
                        width: '285',
                        title: 'templateName',
                        formatter: (row, column, cellValue, index) => {
                            return `<a class="template-router" target="_blank" href="${this.site_url}template/edit/${row.businessId}/?template_id=${row.templateId}">${row.templateName}</a>`
                        }
                    },
                    {
                        prop: 'businessName', // 识别id
                        label: i18n.businessName, // 表头显示名称
                        align: 'center'// 对其格式，可选（right，left，center）
                    },
                    {
                        prop: 'editTime',
                        label: i18n.editTime,
                        align: 'center'
                    },
                    {
                        prop: 'editor',
                        label: i18n.editor,
                        align: 'center',
                        formatter: (row, column, cellValue) => {
                            return `<span>${row.editor || '--'}</span>`
                        }
                    },
                    {
                        prop: 'category',
                        label: i18n.category,
                        align: 'center'
                    }
                ],
                tabName: 'processDetails',
                nodePagination: {
                    // 分页操作
                    limit: this.nodeLimit,
                    pageIndex: this.nodePageIndex,
                    pageArray: this.dataTablePageArray // 公共js文件获取
                },
                nodeColumns: [
                    {
                        prop: 'instanceName',
                        label: i18n.instanceName,
                        width: '285',
                        title: 'instanceName',
                        formatter: (row, column, cellValue, index) => {
                            return `<a class="template-router" target="_blank" href="${this.site_url}taskflow/execute/${row.businessId}/?instance_id=${row.instanceId}">${row.instanceName}</a>`
                        }
                    },
                    {
                        prop: 'businessName', // 识别id
                        label: i18n.businessName, // 表头显示名称
                        align: 'center' // 对其格式，可选（right，left，center）
                    },
                    {
                        prop: 'createTime',
                        label: i18n.createTime,
                        align: 'center'
                    },
                    {
                        prop: 'creator',
                        label: i18n.creator,
                        align: 'center'
                    },
                    {
                        prop: 'category',
                        label: i18n.category,
                        align: 'center'
                    },
                    {
                        prop: 'atomTotal',
                        label: i18n.atomTotal,
                        align: 'center'
                    },
                    {
                        prop: 'subprocessTotal',
                        label: i18n.subprocessTotal,
                        align: 'center'
                    },
                    {
                        prop: 'gatewaysTotal',
                        label: i18n.gatewaysTotal,
                        align: 'center'
                    }
                ],
                atom: '',
                components: [],
                executeData: [],
                executeTotal: 0,
                executePageIndex: 1,
                executeLimit: 15,
                executePagination: {
                    limit: this.executeLimit,
                    pageIndex: this.executePageIndex,
                    pageArray: this.dataTablePageArray
                },
                executeColumns: [
                    {
                        prop: 'componentName',
                        label: i18n.componentName
                    },
                    {
                        prop: 'executeTimes',
                        label: i18n.executeTimes,
                        align: 'center'
                    },
                    {
                        prop: 'failedTimes',
                        label: i18n.failedTimes,
                        align: 'center'
                    },
                    {
                        prop: 'avgExecuteTime',
                        label: i18n.avgExecuteTime,
                        align: 'center'
                    },
                    {
                        prop: 'failedTimesPercent',
                        label: i18n.failedTimesPercent,
                        align: 'center'
                    }
                ],
                instanceData: [],
                instanceTotal: 0,
                taskTotal: 0,
                instancePageIndex: 1,
                instanceLimit: 15,
                instancePagination: {
                    limit: this.instanceLimit,
                    pageIndex: this.instancePageIndex,
                    pageArray: this.dataTablePageArray
                },
                instanceColumns: [
                    {
                        prop: 'instanceName',
                        label: i18n.instanceName,
                        formatter: (row, column, cellValue, index) => {
                            return `<a class="template-router" target="_blank" href="${this.site_url}taskflow/execute/${row.businessId}/?instance_id=${row.instanceId}">${row.instanceName}</a>`
                        }
                    },
                    {
                        prop: 'businessName',
                        label: i18n.businessName,
                        align: 'center'
                    },
                    {
                        prop: 'createTime',
                        label: i18n.createTime,
                        align: 'center'
                    },
                    {
                        prop: 'creator',
                        label: i18n.creator,
                        align: 'center'
                    },
                    {
                        prop: 'category',
                        label: i18n.category,
                        align: 'center'
                    }
                ],
                selectedCcId: '',
                selectedCategory: '',
                selectedAtom: '',
                choiceBusiness: undefined,
                tableTime: [],
                businessTime: [],
                endDateMax: '',
                businessSelected: 'all'
            }
        },
        computed: {
            ...mapState({
                allBusinessList: state => state.allBusinessList,
                categorys: state => state.categorys,
                site_url: state => state.site_url
            }),
            businessList () {
                if (this.allBusinessList.length === 0) {
                    this.getBizList(1)
                }
                const list = tools.deepClone(this.allBusinessList)
                list.unshift({ cc_id: 'all', cc_name: i18n.choiceAllBusiness })
                return list
            },
            componentsList () {
                // 选择器组件不支持拼接的字段，需要转换
                if (this.components) {
                    return this.components.map((item) => {
                        item.name = item.group_name + '-' + item.name
                        return item
                    })
                }
                return []
            }
        },
        created () {
            this.getDateTime()
            this.choiceBusinessName = this.i18n.choiceAllBusiness
            this.onChangeBusinessTime()
            this.onAtomTemplateData()
            this.getCategorys()
        },
        mounted () {
            if (this.components.length === 0) {
                this.getComponentList()
            }
        },
        methods: {
            ...mapActions('atomList/', [
                'queryAtomData',
                'loadSingleAtomList'
            ]),
            ...mapActions([
                'getBizList',
                'getCategorys'
            ]),
            onTemplateHandleSizeChange (limit) {
                this.templatePageIndex = 1
                this.templateLimit = limit
                this.onAtomTemplateData()
            },
            onTemplateHandleIndexChange (pageIndex) {
                this.templatePageIndex = pageIndex
                this.onAtomTemplateData()
            },
            onExecuteHandleSizeChange (limit) {
                this.executePageIndex = 1
                this.executeLimit = limit
                this.onAtomExecuteData()
            },
            onExecuteHandleIndexChange (pageIndex) {
                this.executePageIndex = pageIndex
                this.onAtomExecuteData()
            },
            onInstanceHandleSizeChange (limit) {
                this.instancePageIndex = 1
                this.instanceLimit = limit
                this.onAtomInstanceData()
            },
            onInstanceHandleIndexChange (pageIndex) {
                this.instancePageIndex = pageIndex
                this.onAtomInstanceData()
            },
            onAtomCiteData (business, name) {
                if (business) {
                    if (business === this.choiceBusiness) {
                        // 相同的内容不需要再次查询
                        return
                    }
                    this.choiceBusiness = business
                } else if (business === undefined) {
                    if (this.choiceBusiness === undefined) {
                        return
                    }
                    this.choiceBusiness = business
                }
                const time = this.getUTCTime([this.businessTime[0], this.businessTime[1]])
                const data = {
                    group_by: 'atom_cite',
                    conditions: JSON.stringify({
                        create_time: time[0],
                        finish_time: time[1],
                        biz_cc_id: this.choiceBusiness === 'all' ? '' : this.choiceBusiness
                    })
                }
                this.atomData(data)
            },
            onAtomTemplateData (value) {
                if (this.tabName !== 'processDetails' || this.atom === '') {
                    // 防止不同界面进行触发接口调用
                    // 防止标准插件数据未获取就发送数据
                    return
                }
                if (value) {
                    this.tableTime = value
                    this.resetPageIndex()
                }
                this.isTemplateLoading = true
                const time = this.getUTCTime([this.tableTime[0], this.tableTime[1]])
                const data = {
                    group_by: 'atom_template',
                    conditions: JSON.stringify({
                        create_time: time[0],
                        finish_time: time[1],
                        biz_cc_id: this.bizCcId,
                        category: this.category,
                        component_code: this.atom
                    }),
                    pageIndex: this.templatePageIndex,
                    limit: this.templateLimit
                }
                try {
                    this.atomTableData(data)
                } catch (e) {
                    errorHandler(e, this)
                }
            },
            onAtomExecuteData (value) {
                if (this.tabName !== 'executionTime') {
                    // 防止不同界面进行触发接口调用
                    return
                }
                if (value) {
                    this.tableTime = value
                    this.resetPageIndex()
                }
                this.isExecutionLoading = true
                const time = this.getUTCTime([this.tableTime[0], this.tableTime[1]])
                const data = {
                    group_by: 'atom_execute',
                    conditions: JSON.stringify({
                        create_time: time[0],
                        finish_time: time[1],
                        biz_cc_id: this.bizCcId,
                        category: this.category
                    }),
                    pageIndex: this.executePageIndex,
                    limit: this.executeLimit
                }
                try {
                    this.atomTableData(data)
                } catch (e) {
                    errorHandler(e, this)
                }
            },
            async atomTableData (data) {
                try {
                    const templateData = await this.queryAtomData(data)
                    switch (data.group_by) {
                        case 'atom_template':
                            this.templateData = templateData.data.groups
                            this.templateTotal = templateData.data.total
                            this.isTemplateLoading = false
                            break
                        case 'atom_execute':
                            this.executeData = templateData.data.groups
                            this.executeTotal = templateData.data.total
                            this.isExecutionLoading = false
                            break
                        case 'atom_instance':
                            this.instanceData = templateData.data.groups
                            this.instanceTotal = templateData.data.total
                            this.isInstanceLoading = false
                            break
                    }
                } catch (e) {
                    errorHandler(e, this)
                }
            },
            async atomData (data) {
                this.isCitationLoading = true
                try {
                    const templateData = await this.queryAtomData(data)
                    this.taskPlotData = templateData.data.groups
                    this.taskTotal = templateData.data.total
                } catch (e) {
                    errorHandler(e, this)
                } finally {
                    this.isCitationLoading = false
                }
            },
            onAtomInstanceData (value) {
                if (this.tabName !== 'taskDetails') {
                    // 防止不同界面进行触发接口调用
                    return
                }
                if (value) {
                    this.tableTime = value[1]
                    this.resetPageIndex()
                }
                this.isInstanceLoading = true
                const time = this.getUTCTime([this.tableTime[0], this.tableTime[1]])
                const data = {
                    group_by: 'atom_instance',
                    conditions: JSON.stringify({
                        create_time: time[0],
                        finish_time: time[1],
                        biz_cc_id: this.bizCcId,
                        category: this.category,
                        component_code: this.atom
                    }),
                    pageIndex: this.instancePageIndex,
                    limit: this.instanceLimit
                }
                try {
                    this.atomTableData(data)
                } catch (e) {
                    errorHandler(e, this)
                }
            },
            async getComponentList () {
                try {
                    this.components = await this.loadSingleAtomList()
                    this.atom = this.components[0].code
                    this.selectedAtom = this.atom
                    this.onAtomTemplateData(null)
                } catch (e) {
                    errorHandler(e, this)
                }
            },
            getDateTime () {
                const date = new Date()
                const endTime = moment(date).format('YYYY-MM-DD HH:mm:ss')
                this.tableTime[1] = endTime
                this.businessTime[1] = endTime
                date.setTime(date.getTime() - 3600 * 1000 * 24 * 30)
                const startTime = moment(date).format('YYYY-MM-DD HH:mm:ss')
                this.tableTime[0] = startTime
                this.businessTime[0] = startTime
            },
            onChangeTabPanel (name) {
                this.tabName = name
                switch (name) {
                    case 'processDetails' :
                        this.onAtomTemplateData()
                        break
                    case 'executionTime' :
                        this.onAtomExecuteData()
                        break
                    case 'taskDetails':
                        this.onAtomInstanceData()
                        break
                }
            },
            onShutTimeSelector () {
                this.choiceDownShow = this.$refs.datePickerRef.showDatePanel
            },
            onDatePickerClick () {
                this.choiceDownShow = this.$refs.datePickerRef.showDatePanel
            },
            onSelectedCategory (name, value) {
                if (this.category === name) {
                    return
                }
                this.category = name
                this.resetPageIndex()
                this.onChangeTabPanel(this.tabName)
            },
            onSelectedBizCcId (name, value) {
                if (this.bizCcId === name) {
                    return
                }
                this.bizCcId = name
                this.resetPageIndex()
                this.onChangeTabPanel(this.tabName)
            },
            onSelectedAtom (name, value) {
                if (this.atom === name) {
                    return
                }
                this.atom = name
                this.resetPageIndex()
                this.onChangeTabPanel(this.tabName)
            },
            onClearBizCcId () {
                this.selectedCcId = ''
                this.bizCcId = undefined
                this.resetPageIndex()
                this.onChangeTabPanel(this.tabName)
            },
            onClearCategory () {
                this.selectedCategory = ''
                this.category = undefined
                this.resetPageIndex()
                this.onChangeTabPanel(this.tabName)
            },
            onClearAtom () {
                this.selectedAtom = ''
                this.atom = undefined
                this.resetPageIndex()
                this.onChangeTabPanel(this.tabName)
            },
            onChangeBusinessTime (value) {
                if (value) {
                    this.businessTime = value
                }
                this.onAtomCiteData(null)
            },
            resetPageIndex () {
                switch (this.tabName) {
                    case 'processDetails':
                        this.templatePageIndex = 1
                        this.templatePagination.pageIndex = 1
                        break
                    case 'exectionTime':
                        this.executePageIndex = 1
                        this.executePagination.pageIndex = 1
                        break
                    case 'taskDetails':
                        this.instancePageIndex = 1
                        this.instancePagination.pageIndex = 1
                }
            }
        }
    }
</script>

<style lang="scss">
.content-box {
    .content-wrap {
        .content-dimesion.atom-statistics {
            width: 100%;
            .chart-statistics-tool .tool-name {
                width: 250px;
            }
        }
    }
}
.bk-select-inline,.bk-input-inline {
    display: inline-block;
    width: 260px;
    background-color: #ffffff;
}
.content-date-picker {
    vertical-align: top;
}
.content-business-picker {
    vertical-align: top;
}
</style>
