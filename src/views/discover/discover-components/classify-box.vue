<template>
	<Card :dis-hover="true" :padding="0">
		<Row type="flex" justify="space-between" class="code-row-bg">
	        <Col span="18">
	        	<Menu mode="horizontal" theme="light" :active-name="cid" @on-select="changeMeau">
					<MenuItem :name="0">
			        	全部
			        </MenuItem>
			        <MenuItem :name="item.gclassify_id" v-for="(item, index) in classify" :key="index">
			        	{{item.gclassify_name}}
			        </MenuItem>
			    </Menu>
	        </Col>
	        <Col span="6" class="pr20">
	        <Input v-model="search" icon="ios-search" style="margin-top: 14px;" @on-click="click_search(search)" @on-enter="click_search(search)"></Input>
	        </Col>
	    </Row> 
	</Card>
</template>
<script>
	export default{
		data () {
			return {
				search: this.$route.query.search,
				cid: 0
			}
		},
		watch: {
			classify (val) {
				if (val) {
					// 等获取到classify(导航渲染完成) 再赋值cid
					setTimeout(()=> {
						this.cid = Number(this.$route.query.cid) ? Number(this.$route.query.cid) : 0;
					}, 0)
				}
			}
		},
		computed: {
			classify () {
				return this.$store.state.app.classify;
			}
		},
		methods: {
			click_search () {
				this.$emit('changeMeau', this.cid, this.search, 1);
			},
			changeMeau (name) {
				this.cid = name;
				this.$emit('changeMeau', this.cid, this.search, 1);
			}
		}
	}
</script>
