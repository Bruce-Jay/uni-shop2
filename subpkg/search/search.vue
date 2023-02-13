<template>
  <view>
    <view class="search-box">
      <uni-search-bar @input="input" :radius="100" placeholder="输入进行搜索" cancelButton="none" :focus="true">
      </uni-search-bar>
    </view>

    <!-- 搜索建议列表 -->
    <view class="sugg-list" v-if="searchResults.length !== 0">
      <view class="sugg-item" v-for="(item, i) in searchResults" :key="i" @click="gotoDetail(item)">
        <view class="goods-name">
          {{item.goods_name}}
        </view>
        <uni-icons type="arrowright" size="16"></uni-icons>
      </view>

    </view>

    <!-- 搜索历史 -->
    <view class="history-box" v-else>
      <!-- 标题区域 -->
      <view class="history-title">
        <text>搜索历史</text>
        <uni-icons type="trash" size="17" @click="clean"></uni-icons>
      </view>
      <!-- 列表区域 -->
      <view class="history-list">
        <uni-tag :text="item" v-for="(item, i) in histories" :key="i" @gotoGoodsList(item)></uni-tag>
      </view>

    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        // 延时器的 timerId
        timer: null,
        // 搜索关键词
        kw: '',
        // 搜索的结果列表
        searchResults: [],
        // 历史记录
        historyList: []
      };
    },
    onLoad() {
      this.historyList = JSON.parse(uni.getStorageSync('kw') || '[]')
    },
    methods: {
      // input 输入事件的处理函数
      input(e) {
        clearTimeout(this.timer)

        this.timer = setTimeout(() => {
          this.kw = e
          this.getSearchList()
        }, 500)
      },
      async getSearchList() {
        // 判断搜索关键词是否为空
        if (this.kw.length === 0) {
          this.searchResults = []
          return
        }

        const {
          data: res
        } = await uni.$http.get('/api/public/v1/goods/qsearch', {
          query: this.kw
        })
        if (res.meta.status !== 200) return uni.$showMsg()
        this.searchResults = res.message

        // 将 keyword 转存到搜索历史中
        this.saveSearchHistory()
      },
      gotoDetail(item) {
        // console.log(item.goods_id)
        uni.navigateTo({
          url: '/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id
        })
      },
      saveSearchHistory() {
        // this.historyList.push(this.kw)
        const set = new Set(this.historyList)
        set.delete(this.kw)
        set.add(this.kw)

        this.historyList = Array.from(set)

        // 对搜索的历史数据进行持久化的存储
        uni.setStorageSync('kw', JSON.stringify(this.historyList))
      },
      clean() {
        this.historyList = []
        uni.setStorageSync('kw', '[]')
      },
      gotoGoodsList(kw) {
        uni.navigateTo({
          url: '/subpkg/goods_list/goods_list?query=' + kw
        })
      }

    },
    computed: {
      // 用于搜索历史列表的反转
      histories() {
        return [...this.historyList].reverse()
      }
    }
  }
</script>

<style lang="scss">
  .search-box {
    position: sticky;
    top: 0;
    z-index: 999;
  }

  .sugg-list {
    padding: 0 5px;

    .sugg-item {
      display: flex;
      // align-items: center;
      justify-content: space-between;
      font-size: 12px;
      padding: 13px 0;
      border-bottom: 1rpx solid #ccc;

      .goods-name {
        // 文字不允许换行（单行文本）
        white-space: nowrap;
        // 溢出部分隐藏
        overflow: hidden;
        // 文本溢出后，使用 ... 代替
        text-overflow: ellipsis;
      }
    }
  }

  .history-box {
    padding: 0 5px;

    .history-title {
      display: flex;
      justify-content: space-between;
      height: 40px;
      align-items: center;
      font-size: 13px;
      border-bottom: 1px solid #efefef;
    }

    .history-list {
      display: flex;
      flex-wrap: wrap;

      .uni-tag {
        margin-top: 5px;
        margin-right: 5px;
      }
    }
  }
</style>
