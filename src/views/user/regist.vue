<template>
    <layout :title="'用户注册'" :router="'login'" :h1="'已有二货账号'" :btn_text="'立即登录'">
        <Form slot="form" ref="userForm" :model="userForm" :label-width="80" :rules="ruleInline" style="padding: 30px 0;">
            <FormItem label="手机 / 邮箱" prop="user_name">
                <Input v-model="userForm.user_name" placeholder="请输入您的手机号码或邮箱..."></Input>
            </FormItem>
            <FormItem label="验证码" prop="code">
                <div style="display:inline-block;width:154px;">
                    <Input v-model="userForm.code" placeholder="请输入6位验证码..."></Input>
                </div>
                <div style="display:inline-block;position:relative;">
                    <Button @click="getIdentifyCode" :disabled="canGetIdentifyCode">{{ gettingIdentifyCodeBtnContent }}</Button>
                </div>
            </FormItem>
            <FormItem label="密码" prop="user_psd">
                <Input type="password" v-model="userForm.user_psd" placeholder="请输入您的密码..."></Input>
            </FormItem>
            <FormItem label="学校" prop="user_sid">
                <Cascader :data="schoolData" v-model="userForm.user_sid"></Cascader>
            </FormItem>
            <FormItem label="性别" prop="user_sex">
                <RadioGroup v-model="userForm.user_sex">
                    <Radio label="male">男生</Radio>
                    <Radio label="female">女生</Radio>
                </RadioGroup>
            </FormItem>
            <FormItem>
                <Button type="primary" @click="handleSubmit('userForm')" long>注  册</Button>
            </FormItem>
        </Form>
        <div slot="right">
        </div>
    </layout>
</template>
<script>
import util from '../../libs/util.js';
import md5 from 'js-md5';
import layout from './user-components/layout.vue';
    export default {
        components: {
            layout
        },
        data () {
        	const validatePassCheck = (rule, value, callback) => {
                if (value.length < 6 || value.length > 10) {
                    callback(new Error('请输入6-10位密码'));
                } else if (!/^[a-z0-9_-]{6,10}$/.test(value)) {
                    callback(new Error('（只能包含数字字母_）'));
                } else {
                    callback();
                }
            };
            return {
                userForm: {
                	user_name: '',
                	user_sex: '',
                	user_psd: '',
                	code: '',
                	user_sid: [12, '448'],
                },
                timer: null,
                schoolData: [],
                gettingIdentifyCodeBtnContent: '获取验证码',
                identifyCodeRight: false, // 验证码是否正确
	            hasGetIdentifyCode: false, // 是否点了获取验证码
	            canGetIdentifyCode: false, // 是否可点获取验证码
                ruleInline: {
                	user_name: [
                        { required: true, message: '输入手机号码/邮箱', trigger: 'blur' }
                    ],
                    user_psd: [{ validator: validatePassCheck, trigger: 'blur'}],
                    code: [{ required: true, min: 6, max: 6, message: '请输入6位验证码', trigger: 'blur'}],
                    user_sex: [{ required: true, type: 'string', message: '请选择性别', trigger: 'change'}],
                    user_sid: [{ required: true, type: 'array', message: '请选择学校', trigger: 'change'}]
                }
            }
        },
        created () {
        	this.init();
        },
        methods: {
            handleSubmit(name) {
                this.$refs[name].validate((valid) => {
                    if (valid) {
                    	let data = util.cloneObj(this.userForm);
                    	data.user_sid = this.userForm.user_sid[1];
                    	data.user_psd = md5(this.userForm.user_psd);
                        this.$fetch.user.regist(data)
                        .then(res => {
                        	if (res.code === 200) {
                        		this.$Message.info(res.msg);
                                this.$router.push({
                                    name: 'login'
                                })
                        	} else {
                        		this.$Message.error(res.msg);
                        	}
                        })
                    } else {
                        this.$Message.error('请填写正确表单');
                    }
                })
            },
            getIdentifyCode () {
	            this.hasGetIdentifyCode = true;
                this.canGetIdentifyCode = true;
                let timeLast = 60;
                this.timer = setInterval(() => {
                    if (timeLast >= 0) {
                        this.gettingIdentifyCodeBtnContent = timeLast + '秒后重试';
                        timeLast -= 1;
                    } else {
                        this.canGetCode()
                    }
                }, 1000);
                // you can write ajax request here
                this.$fetch.user.getCode({
                	username: this.userForm.user_name,
                	is_exist: 0
                }).then(res => {
                	if (res.code === 200) {
                		this.$Message.info(res.msg);
                	} else {
	                	this.canGetCode()
                		this.$Message.error(res.msg);
                	}
                }).catch(res => {
                	this.canGetCode()
                })
	        },
	        canGetCode () {
	        	clearInterval(this.timer);
                this.gettingIdentifyCodeBtnContent = '获取验证码';
                this.canGetIdentifyCode = false;
                    },
            init () {
            	this.schoolData = util.getSchool();
            }
        }
    }
</script>
