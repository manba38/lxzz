<template>
  <view>
    <!-- 搜索组件 -->
    <view class="search-box">
      <my-search @click="gotoSearch"></my-search>
    </view>

    <!-- 轮播图的区域 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <swiper-item v-for="(item, i) in swiperList" :key="i">
        <navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id">
          <image :src="item.image_src"></image>
        </navigator>
      </swiper-item>
    </swiper>

    <!-- 分类导航区域 -->
    <view class="nav-list">
       <view class="nav-item" v-for="(item, i) in navList" :key="i" @click="navClickHandler(item)">
         <view class="nav-img-container">
           <image :src="item.image_src" class="nav-img"></image>
         </view>
         <text class="nav-text">{{ item.text }}</text> <!-- 在这里添加文字 -->
       </view>
     </view>

    <!-- 楼层区域 -->
    <!-- 楼层的容器 -->
    <view class="floor-list">
      <!-- 每一个楼层的 item 项 -->
      <view class="floor-item" v-for="(item, i) in floorList" :key="i">
        <!-- 楼层的标题 -->
        <image :src="item.floor_title.image_src" class="floor-title"></image>
        <!-- 楼层的图片区域 -->
        <view class="floor-img-box">
          <!-- 左侧大图片的盒子 -->
          <navigator class="left-img-box" :url="item.product_list[0].url">
            <image :src="item.product_list[0].image_src" :style="{width: item.product_list[0].image_width + 'rpx'}" mode="widthFix"></image>
          </navigator>
          <!-- 右侧 4 个小图片的盒子 -->
          <view class="right-img-box">
            <navigator class="right-img-item" v-for="(item2, i2) in item.product_list" :key="i2" v-if="i2 !== 0" :url="item2.url">
              <image :src="item2.image_src" :style="{width: item2.image_width + 'rpx'}" mode="widthFix"></image>
            </navigator>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
  import badgeMix from '@/mixins/tabbar-badge.js'
  
  export default {
    mixins: [badgeMix],
    data() {
      return {
        // 这是轮播图的数据列表
        swiperList: [],
        // 分类导航的数据列表
        navList: [],
        // 楼层的数据
        floorList: []
      };
    },
    onLoad() {
      // 调用方法，获取轮播图的数据
      this.getSwiperList()
      this.getNavList()
      this.getFloorList()
    },
    methods: {
  async getSwiperList() {
    try {
      // 使用 HTTP 协议发起 GET 请求
      const response = await uni.$http.get('http://localhost:3000/message');
      
      // 输出响应数据以进行调试
  
      // 检查响应数据是否包含预期的字段
      if (response.data && response.data) {
        // 直接赋值，不进行状态码验证
        this.swiperList = response.data;
      } else {
        // 如果响应数据格式不正确，显示错误信息
        uni.$showMsg('响应数据格式错误或缺失必要字段');
      }
    } catch (error) {
      // 捕获请求过程中的错误
      console.error('请求轮播图数据失败:', error);
      uni.$showMsg('请求数据失败，请检查网络或联系管理员');
    }
  },
     async getNavList() {
       try {
         const response = await uni.$http.get('http://localhost:3000/message2');
     
         // 打印整个返回的数据
     
         // 直接赋值返回的数据到navList
         if (response.data) {
           this.navList = response.data;
         } else {
           uni.showToast({
             title: '没有返回数据',
             icon: 'none'
           });
         }
       } catch (error) {
         // 打印错误信息
         console.error('请求导航数据失败:', error);
         uni.showToast({
           title: '请求数据失败',
           icon: 'none'
         });
       }
     },
      navClickHandler(item) {
        if (item.name === '分类') {
          uni.switchTab({
            url: '/pages/cate/cate'
          })
        }
      },
      // 获取首页楼层数据的方法
    async getFloorList() {
      try {
        const res = await uni.$http.get('http://localhost:3000/message3');
        console.log('Response:', res.data); // 打印整个返回的数据
    
        // 直接赋值返回的数据到floorList
        if (res.data) {
          // 对数据进行处理
          res.data.forEach(floor => {
            floor.product_list.forEach(prod => {
              // 确保 navigator_url 存在
              if (prod.navigator_url) {
                prod.url = '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[1];
              }
            });
          });
          this.floorList = res.data; // 赋值 message 部分
        } else {
          uni.showToast({
            title: '没有返回数据',
            icon: 'none'
          });
        }
      } catch (error) {
        // 打印错误信息
        console.error('请求楼层数据失败:', error);
        uni.showToast({
          title: '请求数据失败',
          icon: 'none'
        });
      }
    },
      gotoSearch() {
        uni.navigateTo({
          url: '/subpkg/search/search'
        })
      }
    }
  }
</script>

<style lang="scss">
  swiper {
    height: 330rpx;

    .swiper-item,
    image {
      width: 100%;
      height: 100%;
    }
  }
.nav-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: 10px;
  text-align: center; /* 确保文字居中 */
}
  .nav-list {
    display: flex;
	flex-wrap: wrap;
    justify-content: space-around;
    margin: 15px 0;

    .nav-img {
      width: 64rpx;
      height: 70rpx;
    }
  }

  .floor-title {
    width: 100%;
    height: 60rpx;
  }

  .right-img-box {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
  }

  .floor-img-box {
    display: flex;
    padding-left: 10rpx;
  }
  
  .search-box {
    position: sticky;
    top: 0;
    z-index: 999;
	
  }
</style>
