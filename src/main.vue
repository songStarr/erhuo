<style lang="less" scoped>
@import "./style/common.css";
@import "./style/font-face.css";
@import "./main.less";
</style>
<template>
    <div class="layout">
        <Layout>
            <Alert closable type="info" show-icon banner style="margin-bottom: 0">
                <Icon type="speakerphone" slot="icon"></Icon>
                公告： 请大家遵守网络道德标准, 维护社会良好风气。
            </Alert>
            <Header>
                <meau-header></meau-header>
            </Header>
            <router-view></router-view>
            <Footer class="layout-footer-center">2017-2018 &copy; Songstar</Footer>
            <BackTop></BackTop>
        </Layout>
    </div>
</template>
<script>
import {meauHeader} from './views/main-components';
import util from './libs/util.js';
    export default {
        components: {
            meauHeader
        },
        created () {
            this.init()
        },
        watch: {
            '$route' (to) {
                util.setCurrentPath(this, this.$route.name);
                localStorage.currentPageName = to.name;
            }
        },
        methods: {
            init () {
                util.setCurrentPath(this, this.$route.name);
                this.$store.commit('getUser');
                this.getClassify();
                this.isLogin();
            },
            getClassify () {
                this.$fetch.classify.get({
                    type: 'gclassify'
                }).then(res => {
                    if (res.code === 200) {
                        this.$store.commit('setClassify', res.data);
                    } else {
                        console.log(res)
                        this.$Message.error(res.msg);
                    }
                })
            },
            isLogin () {
                this.$fetch.user.isLogin()
                .then(res => {
                    if (res.code === 200) {
                        this.$store.commit('setIslogin', res.data);
                    } else {
                        this.$Message.error(res.msg);
                    }
                })
            }
        }
    }
</script>