<style scoped>
	.text{
	    padding: 0 7px;
	    border-radius: 13px;
	    color: #999;
	    background-color: #f4f4f4;
	    line-height: 24px;
	}
	.more{
		display: inline-block;
	    text-align: center;
	    font-size: 20px;
	    color: #999;
	}
</style>
<template>
	<my-card :info="comment.suser" :time="comment[`${key}_time`]">
		<div slot="more">
			<Dropdown trigger="click" class="more fr" @on-click="onClick">
		        <a href="javascript:void(0)">
		            <Icon type="ios-more"></Icon>
		        </a>
		        <DropdownMenu slot="list">
		            <DropdownItem v-if="status === '1'" name="0">设为未读</DropdownItem>
		            <DropdownItem v-if="status === '0'" name="1">设为已读</DropdownItem>
		            <DropdownItem name="3">删除消息</DropdownItem>
		        </DropdownMenu>
		    </Dropdown>
		</div>
		<div slot="content">
			<p class="ptb5">回复@{{comment.ruser.name}}: {{comment[`${key}_content`]}}</p>
			<p class="text" v-if="type==='goods'">
			    <goods-name :gid="comment.goods.id" :name="comment.goods.name"></goods-name>  评论中@了你
			</p>
			<p class="text" v-else>
			    <router-link :to="{name: 'dynamicOne', params: {did: comment.dmsg_gid}}" target="_blank"><span class="text-success">{{comment.father}}</span></router-link>  评论中@了你
			</p>
		</div>
	</my-card>
</template>
<script>
    import util from '../../../libs/util.js';
    import {goodsName, myCard} from '../../components';
	export default{
		components: {
			goodsName,
			myCard
		},
		props: ['comment', 'index', 'type'],
		methods: {
			onClick (status) {
				if (status === '3') {
					this.del_msg();
				} else {
					this.change_status(status);
				}
			},
			del_msg () {
				this.$fetch.msg.del_msg({
					type: 'goods',
				    id: this.comment[`${key}_id`]
				}).then(res => {
					if (res.code === 200) {
						// 未读页面删除 未读数-1
						if (this.status === '0') {
							this.$store.commit('changeMsgCount', -1);
						}
						this.$emit('splice_msg', this.index);
					} else {
						this.$Message.error(res.msg);
					}
				})
			},
			change_status (status) {
				this.$fetch.msg.change_status({
					type: this.type,
					lmsg_id: this.comment.lmsg_id,
					status
				}).then(res => {
					if (res.code === 200) {
						let count = status == 0 ? 1 : -1;
						this.$store.commit('changeMsgCount', count);
						this.$emit('splice_msg', this.index);
					} else {
						this.$Message.error(res.msg);
					}
				})
			}
		},
		computed: {
			key () {
				if (this.type === 'goods') {
					return 'lmsg';
				} else if (this.type === 'dynamic') {
					return 'dmsg';
				}
			},
			status () {
				return this.$route.query.status;
			}
		}
	}
</script>