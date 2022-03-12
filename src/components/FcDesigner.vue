<style>
    ._fc-designer {
        height: 100%;
        min-height: 500px;
        overflow: hidden;
        cursor: default;
        position: relative;
    }

    ._fc-designer > .el-main {
        position: absolute;
        top: 0;
        bottom: 0;
        left: 0;
        right: 0;
        padding: 0px;
    }

    ._fc-l, ._fc-m, ._fc-r {
        border-top: 1px solid #ECECEC;
        box-sizing: border-box;
    }

    ._fc-m-tools {
        height: 40px;
        align-items: center;
        display: flex;
        justify-content: flex-end;
        border: 1px solid #ECECEC;
        border-top: 0 none;
    }

    ._fc-m-tools button.el-button {
        padding: 5px 14px;
        display: flex;
        align-items: center;
    }

    ._fc-m-tools .fc-icon {
        font-size: 14px;
        margin-right: 2px;
    }

    ._fc-r .el-tabs__nav-wrap::after {
        height: 1px;
        background-color: #ECECEC;
    }

    ._fc-r ._fc-r-tabs {
        display: flex;
        padding: 0;
        border-bottom: 1px solid #ECECEC;
    }

    ._fc-r ._fc-r-tab {
        height: 40px;
        box-sizing: border-box;
        line-height: 40px;
        display: inline-block;
        list-style: none;
        font-size: 14px;
        font-weight: 600;
        color: #303133;
        position: relative;
        flex: 1;
        text-align: center;
    }

    ._fc-r ._fc-r-tab.active {
        color: #409EFF;
        border-bottom: 2px solid #409EFF;
    }

    .drag-box {
        min-height: 60px;
    }

    ._fc-m-drag {
        overflow: auto;
        padding: 2px;
        box-sizing: border-box;
    }

    ._fc-m-drag, .draggable-drag {
        background: #fff;
        height: 100%;
        position: relative;
    }

    ._fc-m-drag > form, ._fc-m-drag > form > .el-row {
        height: 100%;
    }
    ._fc-designer ul{
        padding-left: 10px;
        margin: 0;
        -webkit-margin-before: 0;
        margin-block-start: 0;
        -webkit-margin-after: .25em;
        margin-block-end: .25em;
        -webkit-padding-start: 10px;
        padding-inline-start: 10px;
    }

    ._fc-m .form-create .container-widget-item{
        background: #2E73FF;
        width: 100%;
        height: 2px;
        overflow: hidden;
        transition: all .3s ease;
    }
    ._fc-designer .container-widget-item{
        display: inline-block;
        height: 28px;
        line-height: 28px;
        width: 115px;
        float: left;
        margin: 5px 3px;
        cursor: move;
        white-space: nowrap;
        text-overflow: ellipsis;
        overflow: hidden;
        background: #f1f2f3;
    }
    ._fc-designer .el-collapse-item__header{
        padding-left: 10px;
        padding-right: 10px;
        font-weight: bold;
        border: 0px;
    }
    ._fc-designer .el-collapse{
        border-bottom: 0px;
    }
    ._fc-designer .el-collapse-item__wrap{
        border: 0px;
    }
    ._fc-designer .el-collapse:last-child{
        border-bottom: 1px solid #EBEEF5;
    }
    ._fc-designer .el-collapse:last-child .el-collapse-item:last-child{
        margin-bottom: 0px;
    }
    ._fc-designer .el-tabs__header{
        margin: 0px;
    }
    ._fc-designer .el-tabs__nav-scroll {
        padding-left: 10px;
    }
</style>

<template>
    <ElContainer class="_fc-designer" :style="'height:'+height_">
        <ElMain>
            <ElContainer style="height: 100%;">
                <el-aside class="_fc-l" width="270px">
                    <el-tabs value="widget">
                        <el-tab-pane label="组件库" name="widget">
                            <template v-for="(item, index) in menuList">
                                <el-collapse :value="[0,1,2,3]" accordion>
                                    <el-collapse-item :title="item.title" :name="index">
                                        <ul>
                                            <draggable :group="{name:'default', pull:'clone',put:false}" :sort="false"
                                                       :list="item.list">
                                                <li class="container-widget-item" v-for="(data, index) in item.list"
                                                    :key="index">
                                                    <span><i class="fc-icon" :class="data.icon || 'icon-input'"></i>{{ data.label }}</span>
                                                </li>
                                            </draggable>
                                        </ul>
                                    </el-collapse-item>
                                </el-collapse>
                            </template>
                        </el-tab-pane>
                        <el-tab-pane label="模板库" name="template">模板库</el-tab-pane>
                    </el-tabs>
                </el-aside>
                <ElContainer class="_fc-m">
                    <el-header class="_fc-m-tools" height="45">
                        <slot name="handle"></slot>
                        <el-button type="primary" icon="fc-icon icon-preview" plain round size="mini"
                                   @click="previewFc">预 览
                        </el-button>
                        <el-button type="danger" icon="fc-icon icon-delete" plain round size="mini"
                                   @click="clearDragRule">清 空
                        </el-button>
                        <el-button plain round size="mini" icon="fc-icon icon-import" @click="setJson"> 导入JSON</el-button>
                        <el-button plain round size="mini" icon="fc-icon icon-import" @click="setOption"> 导入Options</el-button>
                        <el-button plain round size="mini" type="primary" @click="showJson">生成JSON</el-button>
                        <el-button plain round size="mini" type="success" @click="showOption">生成Options</el-button>
                        <el-button plain round size="mini" type="danger" @click="showTemplate">生成组件</el-button>
                    </el-header>
                    <ElMain style="background: #F5F5F5;padding: 20px;">
                        <div class="_fc-m-drag">
                            <FormCreate :rule="dragForm.rule" :option="form.value"
                                        v-model="dragForm.api"></FormCreate>
                        </div>
                    </ElMain>
                </ElContainer>
                <ElAside class="_fc-r" width="320px">
                    <ElContainer style="height: 100%;">
                        <el-header height="40px" class="_fc-r-tabs">
                            <div class="_fc-r-tab" :class="{active: activeTab==='props'}" v-if="!!activeRule"
                                 @click="activeTab='props'">组件配置
                            </div>
                            <div class="_fc-r-tab" :class="{active: activeTab==='form' && !!activeRule}"
                                 @click="activeTab='form'">表单配置
                            </div>
                        </el-header>
                        <ElMain v-show="activeTab==='form'">
                            <FormCreate :rule="form.rule" :option="form.option"
                                        :value.sync="form.value.form"></FormCreate>
                        </ElMain>
                        <ElMain v-show="activeTab==='props'" style="padding: 0 20px;"
                                :key="activeRule ? activeRule._id: ''">
                            <div>
                                <ElDivider v-if="showBaseRule">基础配置</ElDivider>
                                <FormCreate v-show="showBaseRule" v-model="baseForm.api" :rule="baseForm.rule"
                                            :option="baseForm.options"
                                            @change="baseChange"></FormCreate>
                                <ElDivider>属性配置</ElDivider>
                                <FormCreate v-model="propsForm.api" :rule="propsForm.rule" :option="propsForm.options"
                                            @change="propChange" @removeField="propRemoveField"></FormCreate>
                                <ElDivider v-if="showBaseRule">验证规则</ElDivider>
                                <FormCreate v-show="showBaseRule" v-model="validateForm.api" :rule="validateForm.rule"
                                            :option="validateForm.options"
                                            @update:value="validateChange"></FormCreate>
                            </div>
                        </ElMain>
                    </ElContainer>
                </ElAside>
                <ElDialog :visible.sync="preview.state" width="800px" append-to-body>
                    <FormCreate :rule="preview.rule" :option="preview.option" v-if="preview.state"></FormCreate>
                </ElDialog>
                <el-dialog :title="title[type]" :visible.sync="state" class="_fc-t-dialog">
                    <div ref="editor" v-if="state"></div>
                    <span style="color: red;" v-if="err">输入内容格式有误!</span>
                    <span slot="footer" class="dialog-footer" v-if="type > 2">
                    <el-button @click="state = false" size="small">取 消</el-button>
                    <el-button type="primary" @click="onOk" size="small">确 定</el-button>
                  </span>
                </el-dialog>
            </ElContainer>
        </ElMain>
    </ElContainer>
</template>

<style>

</style>

<script>

import form from '../config/base/form';
import field from '../config/base/field';
import validate from '../config/base/validate';
import {deepCopy} from '@form-create/utils/lib/deepextend';
import is, {hasProperty} from '@form-create/utils/lib/type';
import {lower} from '@form-create/utils/lib/tocase';
import ruleList from '../config/rule';
import draggable from 'vuedraggable';
import formCreate from '@form-create/element-ui';
import createMenu from '../config/menu';
import {upper} from '../utils/index';

import jsonlint from 'jsonlint-mod';
import 'codemirror/lib/codemirror.css';
import 'codemirror/addon/lint/lint.css';
import CodeMirror from 'codemirror/lib/codemirror';
import 'codemirror/addon/lint/lint';
import 'codemirror/addon/lint/json-lint';
import 'codemirror/mode/javascript/javascript';
import 'codemirror/mode/vue/vue';
import 'codemirror/mode/xml/xml';
import 'codemirror/mode/css/css';
import 'codemirror/addon/mode/overlay';
import 'codemirror/addon/mode/simple';
import 'codemirror/addon/selection/selection-pointer';
import 'codemirror/mode/handlebars/handlebars';
import 'codemirror/mode/htmlmixed/htmlmixed';
import 'codemirror/mode/pug/pug';

const TITLE = ['生成规则', '表单规则', '生成组件', '设置生成规则', '设置表单规则'];

export default {
    name: 'FcDesigner',
    components: {
        draggable,
        FormCreate: formCreate.$form(),
    },
    props: ['menu', 'height', 'config'],
    computed: {
        height_() {
            const h = this.height;
            if (!h) return '100%';
            return is.Number(h) ? `${h}px` : h;
        }
    },
    provide: _ => ({
        fcx: {
            active: null
        }
    }),
    data() {
        const children = [];
        return {
            cacheProps: {},
            moveRule: null,
            addRule: null,
            added: null,
            activeTab: 'form',
            activeRule: null,
            children,
            menuList: this.menu || createMenu(),
            showBaseRule: false,
            visible: {
                preview: false
            },
            preview: {
                state: false,
                rule: [],
                option: {}
            },
            dragForm: {
                rule: this.makeDragRule(children),
                api: {},
            },
            form: {
                rule: form(),
                option: {
                    form: {
                        labelPosition: 'top',
                        size: 'mini'
                    },
                    submitBtn: false
                },
                value: {
                    form: {
                        inline: false,
                        hideRequiredAsterisk: false,
                        labelPosition: 'right',
                        size: 'mini',
                        labelWidth: '125px'
                    },
                    submitBtn: false
                }
            },
            baseForm: {
                rule: field(),
                api: {},
                options: {
                    form: {
                        labelPosition: 'top',
                        size: 'mini'
                    },
                    submitBtn: false,
                    mounted: (fapi) => {
                        fapi.activeRule = this.activeRule;
                        fapi.setValue(fapi.options.formData || {});
                    }
                }
            },
            validateForm: {
                rule: validate(),
                api: {},
                options: {
                    form: {
                        labelPosition: 'top',
                        size: 'mini'
                    },
                    submitBtn: false,
                    mounted: (fapi) => {
                        fapi.activeRule = this.activeRule;
                        fapi.setValue(fapi.options.formData || {});
                    }
                }
            },
            propsForm: {
                rule: [],
                api: {},
                options: {
                    form: {
                        labelPosition: 'top',
                        size: 'mini'
                    },
                    submitBtn: false,
                    mounted: (fapi) => {
                        fapi.activeRule = this.activeRule;
                        fapi.setValue(fapi.options.formData || {});
                    }
                }
            },
            state: false,
            value: null,
            title: TITLE,
            editor: null,
            err: false,
            type: -1,
        };
    },
    watch: {
        'preview.state': function (n) {
            if (!n) {
                this.$nextTick(() => {
                    this.preview.rule = this.preview.option = null;
                });
            }
        }
    },
    methods: {
        load() {
            let val;
            if(this.type === 2){
                val = this.value;
            }else if(this.type === 0){
                val = formCreate.toJson(this.value, 2);
            }else{
                val = JSON.stringify(this.value, null, 2);
            }
            this.$nextTick(() => {
                this.editor = CodeMirror(this.$refs.editor, {
                    lineNumbers: true,
                    mode: this.type === 2 ? {name: 'vue'} : 'application/json',
                    gutters: ['CodeMirror-lint-markers'],
                    lint: true,
                    line: true,
                    tabSize: 2,
                    lineWrapping: true,
                    value: val || ''
                });
                this.editor.on('blur', () => {
                    this.err = this.editor.state.lint.marked.length > 0;
                });
            });
        },
        addMenu(config) {
            if (!config.name || !config.list) return;
            let flag = true;
            this.menuList.forEach((v, i) => {
                if (v.name === config.name) {
                    this.$set(this.menuList, i, config);
                    flag = false;
                }
            });
            if (flag) {
                this.menuList.push(config);
            }
        },
        removeMenu(name) {
            [...this.menuList].forEach((v, i) => {
                if (v.name === name) {
                    this.menuList.splice(i, 1);
                }
            });
        },
        setMenuItem(name, list) {
            this.menuList.forEach(v => {
                if (v.name === name) {
                    v.list = list;
                }
            });
        },
        appendMenuItem(name, item) {
            this.menuList.forEach(v => {
                if (v.name === name) {
                    v.list.push(item);
                }
            });
        },
        removeMenuItem(item) {
            this.menuList.forEach(v => {
                let idx;
                if (is.String(item)) {
                    [...v.list].forEach((menu, idx) => {
                        if (menu.name === item) {
                            v.list.splice(idx, 1);
                        }
                    });
                } else {
                    if ((idx = v.list.indexOf(item)) > -1) {
                        v.list.splice(idx, 1);
                    }
                }
            });
        },
        addComponent(data) {
            if (Array.isArray(data)) {
                data.forEach(v => {
                    ruleList[v.name] = v;
                });
            } else {
                ruleList[data.name] = data;
            }
        },
        dragStart(children) {
            this.moveRule = children;
            this.added = false;
        },
        dragUnchoose(children, evt) {
            this.addRule = {
                children,
                oldIndex: evt.oldIndex
            };
        },
        getParent(rule) {
            let parent = rule.__fc__.parent.rule;
            const config = parent.config;
            if (config && config.config.inside) {
                rule = parent;
                parent = parent.__fc__.parent.rule;
            }
            return {root: parent, parent: rule};
        },
        makeDrag(group, tag, children, on, subRule) {
            return {
                type: 'DragBox',
                wrap: {
                    show: false
                },
                col: {
                    show: false
                },
                inject: true,
                props: {
                    rule: {
                        props: {
                            tag: 'el-col'
                        },
                        attrs: {
                            group: group === true ? 'default' : group,
                            ghostClass: 'ghost',
                            animation: 150,
                            handle: '._fc-drag-btn',
                            emptyInsertThreshold: 0,
                            direction: 'vertical',
                        }
                    },
                    subRule: subRule || {
                        props: {
                            name: 'fade',
                            tag: 'div'
                        },
                    },
                    tag,
                },
                children,
                on
            };
        },
        clearDragRule() {
            this.setRule([]);
        },
        makeDragRule(children) {
            return [this.makeDrag(true, 'draggable', children, {
                add: (inject, evt) => this.dragAdd(children, evt),
                end: (inject, evt) => this.dragEnd(children, evt),
                start: (inject, evt) => this.dragStart(children, evt),
                unchoose: (inject, evt) => this.dragUnchoose(children, evt),
            }, {
                props: {
                    name: 'fade',
                    tag: 'div'
                }
            })];
        },
        previewFc() {
            this.preview.state = true;
            this.preview.rule = this.getRule();
            this.preview.option = this.getOption();
        },
        getRule() {
            return this.parseRule(deepCopy(this.dragForm.api.rule[0].children));
        },
        getJson() {
            return formCreate.toJson(this.getRule());
        },
        getOption() {
            const option = deepCopy(this.form.value);
            delete option.submitBtn;
            return option;
        },
        setRule(rules) {
            const children = this.loadRule(is.String(rules) ? formCreate.parseJson(rules) : rules);
            this.children = children;
            this.clearActiveRule();
            this.dragForm.rule = this.makeDragRule(children);
        },
        clearActiveRule() {
            this.activeRule = null;
            this.activeTab = 'form';
        },
        setOption(option) {
            const _ = option;
            _.submitBtn = false;
            delete _.resetBtn;
            this.form.value = _;
        },
        loadRule(rules) {
            const loadRule = [];
            rules.forEach(rule => {
                if (is.String(rule)) {
                    return loadRule.push(rule);
                }
                const config = ruleList[rule._fc_drag_tag] || ruleList[rule.type];
                const _children = rule.children;
                rule.children = [];
                if (rule.control) {
                    rule._control = rule.control;
                    delete rule.control;
                }
                if (config) {
                    rule = this.makeRule(config, rule);
                    if (_children) {
                        let children = rule.children[0].children;

                        if (config.drag) {
                            children = children[0].children;
                        }
                        children.push(...this.loadRule(_children));
                    }
                } else if (_children) {
                    rule.children = this.loadRule(_children);
                }
                loadRule.push(rule);
            });
            return loadRule;
        },
        parseRule(children) {
            return [...children].reduce((initial, rule) => {
                if (is.String(rule)) {
                    initial.push(rule);
                    return initial;
                } else if (rule.type === 'DragBox') {
                    initial.push(...this.parseRule(rule.children));
                    return initial;
                } else if (rule.type === 'DragTool') {
                    rule = rule.children[0];
                    if (rule.type === 'DragBox') {
                        initial.push(...this.parseRule(rule.children));
                        return initial;
                    }
                }
                if (!rule) return initial;
                rule = {...rule};
                if (rule.children.length) {
                    rule.children = this.parseRule(rule.children);
                }

                delete rule._id;
                if (rule.config) {
                    delete rule.config.config;
                }
                if (rule.effect) {
                    delete rule.effect._fc;
                    delete rule.effect._fc_tool;
                }
                if (rule._control) {
                    rule.control = rule._control;
                    delete rule._control;
                }
                Object.keys(rule).filter(k => (Array.isArray(rule[k]) && rule[k].length === 0) || (is.Object(rule[k]) && Object.keys(rule[k]).length === 0)).forEach(k => {
                    delete rule[k];
                });
                initial.push(rule);
                return initial;
            }, []);
        },
        baseChange(field, value, _, fapi) {
            if (this.activeRule && fapi[this.activeRule._id] === this.activeRule) {
                this.$set(this.activeRule, field, value);
            }
            if(this.activeRule){
                //let parentRule = this.activeRule.__fc__.parent.rule;
                //parentRule.props.fieldKey=this.activeRule.field
            }
        },
        propRemoveField(field, _, fapi) {
            if (this.activeRule && fapi[this.activeRule._id] === this.activeRule) {
                this.dragForm.api.sync(this.activeRule);
                if (field.indexOf('formCreate') === 0) {
                    field = field.replace('formCreate', '');
                    if (!field) return;
                    field = lower(field);
                    if (field.indexOf('effect') === 0 && field.indexOf('>') > -1) {
                        this.$delete(this.activeRule.effect, field.split('>')[1]);
                    } else if (field.indexOf('props') === 0 && field.indexOf('>') > -1) {
                        this.$delete(this.activeRule.props, field.split('>')[1]);
                    } else if (field === 'child') {
                        this.$delete(this.activeRule.children, 0);
                    } else if (field) {
                        this.$set(this.activeRule, field, undefined);
                    }
                } else {
                    this.$delete(this.activeRule.props, field);
                }
            }
        },
        propChange(field, value, _, fapi) {
            if (this.activeRule && fapi[this.activeRule._id] === this.activeRule) {
                if (field.indexOf('formCreate') === 0) {
                    field = field.replace('formCreate', '');
                    if (!field) return;
                    field = lower(field);
                    if (field.indexOf('effect') === 0 && field.indexOf('>') > -1) {
                        this.$set(this.activeRule.effect, field.split('>')[1], value);
                    } else if (field.indexOf('props') === 0 && field.indexOf('>') > -1) {
                        this.$set(this.activeRule.props, field.split('>')[1], value);
                    } else if (field === 'child') {
                        this.$set(this.activeRule.children, 0, value);
                    } else {
                        this.$set(this.activeRule, field, value);
                    }
                } else {
                    this.$set(this.activeRule.props, field, value);
                }
            }
        },
        validateChange(formData) {
            if (!this.activeRule || this.validateForm.api[this.activeRule._id] !== this.activeRule) return;
            this.activeRule.validate = formData.validate || [];
            this.dragForm.api.refreshValidate();
            this.dragForm.api.nextTick(() => {
                this.dragForm.api.clearValidateState(this.activeRule.field);
            });
        },
        toolActive(rule) {
            if (this.activeRule) {
                delete this.propsForm.api[this.activeRule._id];
                delete this.baseForm.api[this.activeRule._id];
                delete this.validateForm.api[this.activeRule._id];
            }
            this.activeRule = rule;

            this.$nextTick(() => {
                this.activeTab = 'props';
                this.$nextTick(() => {
                    this.propsForm.api[this.activeRule._id] = this.activeRule;
                    this.baseForm.api[this.activeRule._id] = this.activeRule;
                    this.validateForm.api[this.activeRule._id] = this.activeRule;
                });
            });

            if (!this.cacheProps[rule._id]) {
                this.cacheProps[rule._id] = rule.config.config.props();
            }

            this.propsForm.rule = this.cacheProps[rule._id];

            const formData = {...rule.props, formCreateChild: rule.children[0]};
            Object.keys(rule).forEach(k => {
                if (['effect', 'config', 'payload', 'id', 'type'].indexOf(k) < 0)
                    formData['formCreate' + upper(k)] = rule[k];
            });
            ['props', 'effect'].forEach(name => {
                rule[name] && Object.keys(rule[name]).forEach(k => {
                    formData['formCreate' + upper(name) + '>' + k] = rule[name][k];
                });
            });
            this.propsForm.options.formData = formData;

            this.showBaseRule = hasProperty(rule, 'field') && rule.input !== false;

            if (this.showBaseRule) {
                this.baseForm.options.formData = {
                    field: rule.field,
                    title: rule.title || '',
                    info: rule.info,
                    _control: rule._control,
                };

                this.validateForm.options.formData = {validate: rule.validate ? [...rule.validate] : []};
            }
        },
        dragAdd(children, evt) {
            const newIndex = evt.newIndex;
            const menu = evt.item._underlying_vm_;
            if (!menu) {
                if (this.addRule) {
                    const rule = this.addRule.children.splice(this.addRule.oldIndex, 1);
                    children.splice(newIndex, 0, rule[0]);
                }
            } else {
                const rule = this.makeRule(ruleList[menu.name]);
                children.splice(newIndex, 0, rule);
            }
            this.added = true;
        },
        dragEnd(children, {newIndex, oldIndex}) {
            if (!this.added && !(this.moveRule === children && newIndex === oldIndex)) {
                const rule = this.moveRule.splice(oldIndex, 1);
                children.splice(newIndex, 0, rule[0]);
            }
            this.moveRule = null;
            this.addRule = null;
            this.added = false;
        },
        makeRule(config, _rule) {
            const rule = _rule || config.rule();
            rule.config = {config};
            if (!rule.effect) rule.effect = {};
            rule.effect._fc = true;
            rule._fc_drag_tag = config.name;

            let drag;

            if (config.drag) {
                const children = [];
                rule.children.push(drag = this.makeDrag(config.drag, rule.type, children, {
                    end: (inject, evt) => this.dragEnd(inject.self.children, evt),
                    add: (inject, evt) => this.dragAdd(inject.self.children, evt),
                    start: (inject, evt) => this.dragStart(inject.self.children, evt),
                    unchoose: (inject, evt) => this.dragUnchoose(inject.self.children, evt),
                }));
            }

            if (config.children && !_rule) {
                const child = this.makeRule(ruleList[config.children]);
                (drag || rule).children.push(child);
            }

            if (config.inside) {
                rule.children = [{
                    type: 'DragTool',
                    props: {
                        dragBtn: config.dragBtn !== false,
                        children: config.children,
                    },
                    effect: {
                        _fc_tool: true
                    },
                    inject: true,
                    on: {
                        delete: ({self}) => {
                            this.getParent(self).parent.__fc__.rm();
                            this.clearActiveRule();
                        },
                        add: ({self}) => {
                            const top = this.getParent(self);
                            top.root.children.splice(top.root.children.indexOf(top.parent) + 1, 0, this.makeRule(top.parent.config.config));
                        },
                        addChild: ({self}) => {
                            const top = this.getParent(self);
                            const config = top.parent.config.config;
                            const item = ruleList[config.children];
                            if (!item) return;
                            (!config.drag ? top.parent : top.parent.children[0]).children[0].children.push(this.makeRule(item));
                        },
                        copy: ({self}) => {
                            const top = this.getParent(self);
                            top.root.children.splice(top.root.children.indexOf(top.parent) + 1, 0, formCreate.copyRule(top.parent));
                        },
                        active: ({self}) => {
                            this.toolActive(this.getParent(self).parent);
                        }
                    },
                    children: rule.children
                }];
                return rule;
            } else {
                return {
                    type: 'DragTool',
                    props: {
                        dragBtn: config.dragBtn !== false,
                        children: config.children,
                    },
                    effect: {
                        _fc_tool: true
                    },
                    inject: true,
                    on: {
                        delete: ({self}) => {
                            self.__fc__.rm();
                            this.clearActiveRule();
                        },
                        add: ({self}) => {
                            const top = this.getParent(self);
                            top.root.children.splice(top.root.children.indexOf(top.parent) + 1, 0, this.makeRule(self.children[0].config.config));
                        },
                        addChild: ({self}) => {
                            const config = self.children[0].config.config;
                            const item = ruleList[config.children];
                            if (!item) return;
                            (!config.drag ? self : self.children[0]).children[0].children.push(this.makeRule(item));
                        },
                        copy: ({self}) => {
                            const top = this.getParent(self);
                            let copyRule = formCreate.copyRule(top.parent);
                            top.root.children.splice(top.root.children.indexOf(top.parent) + 1, 0, copyRule);
                            //copyRule.props.fieldKey=copyRule.children[0].field;
                            /*this.$nextTick(() => {
                                copyRule.props.fieldKey=copyRule.children[0].field;
                            });*/
                        },
                        active: ({self}) => {
                            this.toolActive(self.children[0]);
                        }
                    },
                    children: [rule]
                };
            }
        },
        showJson() {
            this.state = true;
            this.type = 0;
            this.value = this.getRule();
        },
        setJson() {
            this.state = true;
            this.type = 3;
            this.value = [];
        },
        setOption() {
            this.state = true;
            this.type = 4;
            this.value = {form: {}};
        },
        showOption() {
            this.state = true;
            this.type = 1;
            this.value = this.getOption();
        },
        showTemplate() {
            this.state = true;
            this.type = 2;
            this.value = this.makeTemplate();
        },
        onOk() {
            if (this.err) return;
            const json = this.editor.getValue();
            let val = JSON.parse(json);
            if (this.type === 3) {
                if (!Array.isArray(val)) {
                    this.err = true;
                    return;
                }
                this.setRule(formCreate.parseJson(json));
            } else {
                if (!is.Object(val) || !val.form) {
                    this.err = true;
                    return;
                }
                this.setOption(val);
            }
            this.state = false;
        },
        makeTemplate() {
            const rule = this.getRule();
            const opt = this.getOption();
            return `<template>
  <form-create
    v-model="fapi"
    :rule="rule"
    :option="option"
    @submit="onSubmit"
  ></form-create>
</template>

<script>
import formCreate from "@form-create/element-ui";

export default {
  data () {
    return {
        fapi: null,
        rule: formCreate.parseJson('${formCreate.toJson(rule).replaceAll('\\','\\\\')}'),
        option: formCreate.parseJson('${JSON.stringify(opt)}')
    }
  },
  methods: {
    onSubmit (formData) {
      //todo 提交表单
      console.log('formData',formData)
    }
  }
}
<\/script>`;
        }
    },
    watch: {
        state(n) {
            if (!n) {
                this.value = null;
                this.err = false;
            }
        },
        value() {
            this.load();
        }
    },
    created() {
        document.body.ondrop = e => {
            e.preventDefault();
            e.stopPropagation();
        };
    }
};
</script>
