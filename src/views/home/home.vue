<style scoped>
.content{
	height: 100%;
}
.demo-spin-icon-load{
        animation: ani-demo-spin 1s linear infinite;
    }
</style>
<template>
	<Content class="content" style="padding: 0 50px"  v-infinite-scroll="loadMore" :infinite-scroll-disabled="busy" :infinite-scroll-distance="10">
		<div v-title>二货 - 首页</div>
	    <carousel-box></carousel-box>
	    <search-box :style="{margin: '20px 0'}"></search-box>
	    <Row :gutter="15">
	    	<Col span="5">
	    	    <side-block title="人气用户" order="user_pop" class="mb20"></side-block>
	    	    <side-block title="最近登录" order="user_ltime" class="mb20"></side-block>
	    	    <side-block title="最近注册" order="user_rtime" class="mb20"></side-block>
	        </Col>
	    	<Col span="19">
		    		<goods-box :data="data" ></goods-box>
		    		<div v-if="noMore">
		    			<Spin size="large">
			                <div>没有更多了</div>
			            </Spin>
		    		</div>
		    		<div v-else>
		    			<Spin size="large">
			                <Icon type="load-c" size=18 class="demo-spin-icon-load"></Icon>
			                <div>Loading</div>
			            </Spin>
		    		</div>
	    	</Col>
	    </Row>

	</Content>
	
</template>
<script>
import {carouselBox, sideBlock, goodsBox} from './home-components';
import {searchBox} from '../main-components';
    export default {
    	components: {
    		carouselBox,
            searchBox,
            sideBlock,
            goodsBox
    	},
    	data () {
    		return {
    			data: [],
    			page: 1,
    			busy: false,
    			noMore: false
    		}
    	},
    	mounted () {
    		this.getData();
    	},
        methods: {
        	getData () {
				this.busy = true;
				this.$fetch.goods.get({
					num: 12,
					page: this.page
				}).then(res => {
					this.busy = false;
					if (res.code === 200) {
						if (res.data.length > 0) {
							if (this.data.length > 0 && this.data[0].goods_time < res.data[0].goods_time) {
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
			}
        }
    };
</script>