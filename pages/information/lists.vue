<template>
	<view style="background:#f3f4f6;position:absolute;top:0;left:0;right:0;bottom: 0;">
		<u-navbar :title="title" :autoBack="true" :placeholder="true"></u-navbar>
		<view style="margin-bottom: 13rpx;background-color: #ffffff;padding: 15rpx 30rpx;">
			<u-search :show-action="true" actionText="搜索" :animation="false" @custom="get_information(true)" @search="get_information(true)" v-model="information_form.key"></u-search>
		</view>
		<sl-filter :ref="'slFilter'" :isTransNav="true" :navHeight="0" :menuList="menuList"
		         @result="filter_confirm"></sl-filter>
		<view class="content">
			<view style="background:#ffffff;margin:30rpx;border-radius:15rpx;padding: 30rpx;"
				v-if="page_loding == 'nomore' && $u.test.isEmpty(information_list)">
				<u-empty mode="news" icon="http://cdn.uviewui.com/uview/empty/news.png" text=" "
					style="margin-bottom: 25rpx;">
				</u-empty>
			</view>
			<informationList ref="information_list" :information_list="information_list" :user_info="userInfo"
				@click_item="to_information"></informationList>
			<u-loadmore :status="page_loding" loading-text="数据加载中" loadingIcon="spinner"  v-if="!(page_loding == 'nomore' && $u.test.isEmpty(information_list))"/>
		</view>
	</view>
</template>

<script>
	import informationList from "@/components/information-list/information-list.vue"
	import slFilter from '@/components/sl-filter/sl-filter.vue'
	export default {
		components:{
			informationList,
			slFilter
		},
		data() {
			return {
				error_num:2,
				page_loding: "loading",
				title: '信息列表',
				information_form: {
					page:1,
					key:'',
					category_id:0,
					seach_type:0,
					seach_content:{
						distance:'',
						range:'',
						other_screening:'',
					}
				},
				information_list: [],
				userInfo: [],
				menuList: [
				    {
				        'title': '距离排序',
				        'detailTitle': '',
				        'isMutiple': false,
				        'key': 'distance',
				        'detailList': [
				            {
				                'title': '不限制',
				                'value': ''
				            },
				            {
				                'title': '从近到远',
				                'value': 1
				            },
				            {
				                'title': '从远到近',
				                'value': 2
				            }
				        ]
				    },
				    {
				        'title': '范围限制',
				        'detailTitle': '',
				        'key': 'range',
				        'isSort': true,
				        'isMutiple': false,
				        'defaultSelectedIndex': 0,
				        'detailList': [
				            {
				                'title': '不限制',
				                'value': ""
				            },
				            {
				                'title': '1公里内',
				                'value': 1
				            },
				            {
				                'title': '3公里内',
				                'value': 3
				            },
				            {
				                'title': '10公里内',
				                'value': 10
				            }
				        ]
				    },
				    {
				        'title': '更多筛选',
				        'detailTitle': '',
				        'key': 'other_screening',
				        'isMutiple': false,
				        'detailList': [
				            {
				                'title': '不限制',
				                'value': ''
				            },
				            {
				                'title': '仅看我的小区',
				                'value': 1
				            },
				            {
				                'title': '仅看当前小区',
				                'value': 2
				            }
				        ]
				    }
				]
			}
		},
		onLoad(e) {
			this.information_form.category_id = e.category_id
			this.get_information()
		},
		methods: {
			to_information(item) {
				uni.navigateTo({
					url: "../information/information?information_id=" + item.id
				})
			},
			/**
			 * 筛选确认回调
			 * @param {Object} e
			 */
			filter_confirm(e){
				this.information_form.seach_content.distance = e.distance
				this.information_form.seach_content.other_screening = e.other_screening
				this.information_form.seach_content.range = e.range
				this.information_list = []
				this.information_form.page = 1
				this.get_information()
			},
			/**
			 * 获取信息列表
			 */
			get_information(is_force=false) {
				if(is_force){
					this.information_list = []
					this.information_form.page = 1
				}
				this.page_loding = 'loading'
				return new Promise((suc, err) => {
					let information_form = uni.$u.deepClone(this.information_form) // 对象深度克隆
					this.$http.to_http('/api/information/get_list', information_form, 'POST', is_force?'加载中':false).then(res => {
						res = res.data
						if (res.code != 1) {
							return uni.showToast({
								title: "系统维护中",
								icon: "none"
							})
						}
						if(res.data.total>0){
							this.title = res.data.rows[0].category.name
						}
						if(res.data.rows.length>0){
							this.information_form.page++;
						}
						if(res.data.total > (res.data.rows.length + this.information_list.length)){
							this.page_loding = 'loadmore'
						}else{
							this.page_loding = 'nomore'
						}
						res.data.rows.forEach(el=>{
							this.information_list.push(el)
						})
						suc(true);
					}).catch(async err=>{
						if(err.statusCode = 401 && this.error_num>0){
							this.error_num--
							await this.$wechatLogin()
							await this.get_information()
						}
						suc(true);
					})
				})
			},
		}
	}
</script>

<style lang="scss">
	.content {
		background:#f3f4f6;
		padding-bottom: 10px;
	}
</style>
