<template>
	<view>
		<view class="information_item" v-for="(item,index) in information_list" :key="index" @click="click_item(item)">
			<view style="display: flex;justify-content: space-between; align-items: center;">
				<view style="display: flex;justify-content: center;align-items: center;">
					<image :src="item.user.avatar" mode="" style="" style="width: 80rpx;height: 80rpx;border-radius: 50%;margin-right: 15rpx;"></image>
					<view style="line-height: 50rpx;">
						<view style="font-weight: bold;">{{ item.user.nickname }}</view>
						<view style="display: flex;align-items: center;">
							<span v-if="item.user.community_id == user_info.community_id" style="transform:scale(0.8);margin-left: -10rpx;"><u-tag type="success" text="同小区" size="mini" plain plainFill></u-tag></span>
							<span v-if="item.community_id" style="transform:scale(0.8);margin-left: -15rpx;"><u-tag icon="map" :text="item.community.name" size="mini" plain plainFill></u-tag></span>
						</view>
					</view>
				</view>
				<view style="display: flex;justify-content: center;align-items: center;">
					<u-icon name="map-fill" size="14"></u-icon>
					<span style="margin-left: 5rpx;font-size: 14px;">{{ item.distance }}km</span>
				</view>
			</view>
			<view style="font-size: 16px;font-weight: bolder;margin-top: 15rpx;">
				<span>{{ item.category.name }}</span>
				<span>：</span>
				<span>{{ item.title }}</span>
			</view>
			<view style="margin-top: 15rpx;" v-if="!$u.test.isEmpty(item.images_arr)">
				<u-album :urls="item.images_arr" :previewFullImage="false" :rowCount="4" :maxCount="4"></u-album>
			</view>
			<view style="display: flex;justify-content: space-between;font-size: 14px;margin-top: 15rpx;align-items: center;">
				<view style="display: flex;justify-content: center;align-items: center;">
					<u-icon name="eye-fill" size="14" color="error"></u-icon>
					<u-badge type="error" max="1000" :value="item.look_num" numberType="limit" :inverted="true" :customStyle="{marginLeft:'-5rpx'}"></u-badge>
					<view style="display: flex;align-items: center;margin-left: 10rpx;">
						<u-icon name="chat" size="16" color="primary"></u-icon>
						<u-badge type="primary" max="1000" :value="item.comment_count" numberType="limit" :inverted="true" :customStyle="{marginLeft:'-5rpx'}"></u-badge>
					</view>
				</view>
				<view style="color: #797979;">
					{{$u.timeFormat(item.createtime, 'yyyy-mm-dd hh:MM')}}
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		name:"information-list",
		data() {
			return {};
		},
		props:{
			information_list:{
			    type: Object|Array,
			    default: [],
			},
			user_info:{
			    type: Object|Array,
			    default: [],
			},
		},
		methods:{
			/**
			 * 点击单条触发事件
			 * @param {Object} item
			 */
			click_item(item){
				this.$emit('click_item',item)
			}
		}
	}
</script>

<style lang="scss">
	.information_item{
		background:#ffffff;
		margin:10rpx 30rpx;
		border-radius:15rpx;
		padding: 30rpx;
		min-height: 200rpx;
	}
</style>
