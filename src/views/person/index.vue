<style lang="less" scoped>
</style>
<template>
	<layout :info="user_info">
		<div v-title>二货 - {{user_info.user_name}}</div>
		<div slot="leftInfo" style="font-size:14px;">
			<p>{{user_info.user_sex}}</p>
        	<p>{{user_info.user_sid}}</p>
        	<p>{{user_info.user_ltime}}</p>
        	<span class="text-success btn">关注 ({{user_info.ship.fans_num}})</span>  &nbsp; <span class="text-success btn">粉丝 ({{user_info.ship.followers_num}})</span>
		</div>
		<div slot="leftMeau">
			<span v-if="user_info.user_sign">{{user_info.user_sign}}</span>
			<span v-else>这个人很懒，啥都没有要说。</span>
		</div>
		<div slot="leftBottom">
			<Card :dis-hover="true" style="margin-top: 30px;">
				<fol-send :is_fans="user_info.is_fans" :uid="user_info.user_id" size="default" @updateFans="updateFans"></fol-send>
			</Card>
		</div>
		<my-meau slot="rightMeau" :meau="meau" @changeMeau="changeMeau" :active="active"></my-meau>
	</layout>
</template>
<script>
import util from '../../libs/util.js';
import layout from '../layout/layout.vue';
import {myMeau} from '../components';
import {folSend} from '../components';
	export default{
		components: {
			layout,
			myMeau,
			folSend
		},
		data () {
			return {
				user_info: {
					ship: {
						followers_num: 0
					}
				},
				meau: [{
					name: 'sell',
					icon: 'bag',
					title: '出售'
				}, {
					name: 'collection',
					icon: 'bag',
					title: '收藏'
				}, {
					name: 'fans',
					icon: 'happy-outline',
					title: '粉丝'
				}, {
					name: 'followers',
					icon: 'android-happy',
					title: '关注'
				}, {
					name: 'pdynamic',
					icon: 'ios-navigate',
					title: '动态'
				}],
				active: this.$route.name
			}
		},
		watch: {
			user_id () {
				this.get_user();
			},
			$route () {
				this.active = this.$route.name;
			}
		},
		created () {
			this.get_user();
		},
		methods: {
			get_user () {
				this.$fetch.user.get_one({
					uid: this.uid,
					user: this.user_id
				}).then(res => {
					if (res.code === 200) {
						this.user_info = util.formatUserData(res.data);
					} else {
						this.$Message.error(res.msg)
					}
				})
			},
            changeMeau (name) {
            	this.$router.push({
                    name
                });
            },
            updateFans (val) {
				this.user_info.is_fans = val;
			}
		},
		computed: {
			uid () {
                if (this.$store.state.user.info) {
                    return this.$store.state.user.info.user_id;
                }
            },
            user_id () {
            	return this.$route.params.uid;
            }
        }
	}
</script>
