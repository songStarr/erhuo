<style scoped>
	.title{
		color: #333;
        font-weight: 700;
	}
</style>
<template>
    <Card :dis-hover="true" class="mtb20">
			<div><span class="title">{{notice.notice_title}}</span>  <span class="ml10 text-disabled">{{notice.notice_time | formatDate}}</span></div>
			<div v-html="notice.notice_content" class="pl8 notice-content"></div>
	</Card>
</template>
<script>
    import {userName} from '../../components';
   	export default {
		components: {
			userName
		},
		props: ['notice']
	}
</script>