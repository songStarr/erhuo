<template>
	<div>
		<div v-title>二货 - 动态</div>
		<layout :info="user_info">
			<div slot="leftInfo" style="font-size:14px;" v-if="user_info">
					<p>{{user_info.user_sex}}</p>
		        	<p>{{user_info.user_sid}}</p>
		        	<span class="text-success btn">关注 ({{user_info.ship.fans_num}})</span>  &nbsp; <span class="text-success btn">粉丝 ({{user_info.ship.followers_num}})</span>
			</div>
			<div slot="leftMeau" style="text-align:center;" v-else>发表动态,请先登录</div>
			<div slot="rightMeau" style="padding:30px; height: 100%;" v-infinite-scroll="loadMore" :infinite-scroll-disabled="busy" :infinite-scroll-distance="10">
				<input-box @submit="submit" :content="content" class="mb20" :rows="4" :placeholder="'有什么想和大家分享的？'"></input-box>
				<Menu mode="horizontal" :active-name="type" @on-select="changeMeau" style="margin-bottom:20px;">
			        <MenuItem :name="0">
			            全部
			        </MenuItem>
			        <MenuItem :name="1">
			            文字
			        </MenuItem>
			        <MenuItem :name="2">
			            二货
			        </MenuItem>
			        <MenuItem :name="3">
			            关注
			        </MenuItem>
			    </Menu>
				<transition-group name="slide-fade">
				    <dynamic-item :dynamic="item" v-for="(item, index) in data" :key="index" :index="index" @share="share" @openModal="openModal"></dynamic-item>
				</transition-group>
				<div class="mt30">
					<div v-if="noMore">
		    			<Spin>
			                <div>已加载完成</div>
			            </Spin>
		    		</div>
		    		<div v-else>
		    			<Spin>
			                <Icon type="load-c" size=18 class="demo-spin-icon-load"></Icon>
			                <div>Loading</div>
			            </Spin>
		    		</div>
	    		</div>
			</div>
		</layout>
		<report-modal :type="3" :id="dynamic_id" ref="report"></report-modal>
		<del-modal :type="3" :id="dynamic_id" ref="del" @delDynamic="delDynamic"></del-modal>
		<share-modal :placeholder="shareContent" :type="1" :id="dynamic_id" ref="share" @shareDynamic="shareDynamic"></share-modal>
	</div>
</template>
<script>
    import layout from '../layout/layout.vue';
    import {inputBox, shareModal, delModal, reportModal} from '../components';
    import dynamicItem from './dynamic-components/dynamicItem.vue';
	export default{
		components: {
			layout,
			inputBox,
			dynamicItem,
			shareModal,
			delModal,
			reportModal
		},
		data () {
			return {
				content: '',
				data: [],
				page: 1,
				num: 5,
				type: 0,
				noMore: false,
				busy: false,
				shareContent:'',
				dynamic_id: null
			}
		},
		mounted () {
			this.getData();
		},
		methods: {
			getData () {
				console.log(this.page);
				this.busy = true;
				this.$fetch.dynamic.get({
					type: this.type,
					num: this.num,
					page: this.page
				}).then(res => {
					this.busy = false;
					if (res.code === 200) {
						if (res.data.length > 0) {
							if (this.data.length > 0 && this.data[0].dynamic_time < res.data[0].dynamic_time) {
								this.data.unshift(...res.data);
							} else {
								this.data.push(...res.data);
							}
						} else {
							this.noMore = true;
						}
					} else {
						this.$Message.error(res.msg);
					}
				})
			},
			loadMore () {
				if (!this.noMore) {
					this.page ++;
				    this.getData();
				}
			},
			submit (val) {
				this.content = val;
				this.$fetch.dynamic.add({
					content: this.content,
					type: 1
				}).then(res => {
					if (res.code === 200) {
						this.content = '';
						this.$Message.success(res.msg);
						this.data.unshift(res.data);
					} else {
						this.$Message.error(res.msg)
					}
				})
			},
			delDynamic (index) {
				this.data = [];
				this.page = 1;
				this.getData();
			},
			changeMeau (val) {
				this.type = val;
			},
			share (index) {
				let dynamic = this.data[index];
				this.dynamic_id = dynamic.dynamic_id;
				this.shareContent = `@${dynamic.user.name}: ${dynamic.dynamic_content}`;
				console.log(this.$refs.share)
				this.$refs.share.openModal();
			},
			shareDynamic () {
				this.page = 1;
				this.data = [];
				this.getData();
			},
			openModal (type, id) {
				this.dynamic_id = id;
				this.$refs[type].openModal();
			}
		},
		computed: {
			user_info () {
                if (this.$store.state.user.info) {
                    return this.$store.state.user.info;
                }
            }
		},
		watch: {
			type () {
				this.noMore = false;
				this.page = 1;
				this.data = [];
				this.getData();
			}
		}
	}
</script>