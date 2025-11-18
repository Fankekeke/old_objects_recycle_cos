<template>
  <div class="single-page">
    <a-row v-if="newsList.length > 0" class="news-banner">
      <a-col :span="22">
        <div class="news-content">
          <h3>新闻公告</h3>
          <p>{{ newsContent }}</p>
        </div>
      </a-col>
      <a-col :span="2">
        <p type="primary" class="next-button" @click="newsNext">
          下一条
        </p>
      </a-col>
    </a-row>
    <a-row :gutter="25" style="margin-bottom: 15px">
      <a-col :span="16">
        <div class="scenic-carousel-wrapper">
          <a-carousel autoplay>
            <div class="carousel-slide">
              <img src="http://127.0.0.1:9527/imagesWeb/SA1648175183138.jpg" alt="景区风光" class="carousel-image" />
              <div class="slide-overlay">
                <div class="slide-content">
                  <h2 class="slide-title">欢迎来到美丽景区</h2>
                  <p class="slide-description">探索自然与文化的完美融合，享受难忘的旅行体验</p>
                  <a-button type="primary" class="explore-btn" @click="exploreScenic">立即探索</a-button>
                </div>
              </div>
            </div>
            <div class="carousel-slide">
              <img src="http://127.0.0.1:9527/imagesWeb/SA1648524716556.jpg" alt="特色景点" class="carousel-image" />
              <div class="slide-overlay">
                <div class="slide-content">
                  <h2 class="slide-title">特色景点推荐</h2>
                  <p class="slide-description">发现隐藏的自然宝藏，感受独特的风景魅力</p>
                  <a-button type="primary" class="explore-btn" @click="viewAttractions">查看详情</a-button>
                </div>
              </div>
            </div>
            <div class="carousel-slide">
              <img src="http://127.0.0.1:9527/imagesWeb/SA1648524713924.jpg" alt="最佳季节" class="carousel-image" />
              <div class="slide-overlay">
                <div class="slide-content">
                  <h2 class="slide-title">最佳旅游季节</h2>
                  <p class="slide-description">四季皆有不同的美景，为您提供最佳游览建议</p>
                  <a-button type="primary" class="explore-btn" @click="viewSeasons">查看攻略</a-button>
                </div>
              </div>
            </div>
          </a-carousel>
        </div>
      </a-col>
      <a-col :span="24">
        <a-card :bordered="false" style="margin-left: -14px;margin-right: -14px;padding: 12px;">
          <post/>
        </a-card>
      </a-col>
    </a-row>
  </div>
</template>
<script>
import HeadInfo from '@/views/common/HeadInfo'
import {mapState} from 'vuex'
import moment from 'moment'
import Post from "./Post.vue";
moment.locale('zh-cn')

export default {
  name: 'HomePage',
  components: {Post, HeadInfo},
  data () {
    return {
      newsPage: 0,
      newsContent: '',
      newsList: [],
      avatar: '',
      userInfo: null,
      welcomeMessage: '',
      userEdit: {
        visiable: false
      }
    }
  },
  computed: {
    ...mapState({
      multipage: state => state.setting.multipage,
      user: state => state.account.user
    })
  },
  methods: {
    getNewList () {
      this.$get(`/cos/bulletin-info/list`).then((r) => {
        this.newsList = r.data.data
        if (this.newsList.length !== 0) {
          this.newsContent = `《${this.newsList[0].title}》 ${this.newsList[0].content}`
        }
      })
    },
    newsNext () {
      if (this.newsPage + 1 === this.newsList.length) {
        this.newsPage = 0
      } else {
        this.newsPage += 1
      }
      this.newsContent = `《${this.newsList[this.newsPage].title}》 ${this.newsList[this.newsPage].content}`
    },
    edit () {
      this.$refs.userEdit.setFormValues(this.userInfo)
      this.userEdit.visiable = true
    },
    handleUserEditClose () {
      this.userEdit.visiable = false
      this.getUserInfo()
    },
    handleUserEditSuccess () {
      this.userEdit.visiable = false
      this.$message.success('修改个人信息成功')
    },
    getUserInfo () {
      this.$get(`/cos/user-info/${this.user.userId}`).then((r) => {
        this.userInfo = r.data.data
        this.avatar = r.data.data.avatar
      })
    },
    welcome () {
      const date = new Date()
      const hour = date.getHours()
      let time = hour < 6 ? '早上好' : (hour <= 11 ? '上午好' : (hour <= 13 ? '中午好' : (hour <= 18 ? '下午好' : '晚上好')))
      return `${time}`
    }
  },
  mounted () {
    this.getNewList()
  }
}
</script>
<style lang="less">
.head-info-card {
  height: 360px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
  background: rgba(255, 255, 255, 0.85);
  backdrop-filter: blur(10px);
  border-radius: 16px;
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.08);
  border: 1px solid rgba(255, 255, 255, 0.3);
  padding: 30px 20px;

  .edit-profile-btn {
    border-radius: 20px;
    padding: 0 24px;
    height: 26px;
    background: linear-gradient(45deg, #4A90E2, #63B3ED);
    border: none;
    color: white;
    font-weight: 500;
    box-shadow: 0 4px 12px rgba(74, 144, 226, 0.3);
    transition: all 0.3s ease;
    font-size: 14px;
    margin-top: 15px;

    &:hover {
      transform: translateY(-2px);
      box-shadow: 0 6px 16px rgba(74, 144, 226, 0.4);
      background: linear-gradient(45deg, #3A7BC8, #4A90E2);
    }

    &:active {
      transform: translateY(0);
    }
  }

  .head-info-avatar {
    margin-bottom: 20px;

    ::v-deep .ant-avatar {
      width: 100px;
      height: 100px;
      border-radius: 50%;
      box-shadow: 0 6px 16px rgba(0, 0, 0, 0.15);
      border: 3px solid rgba(255, 255, 255, 0.5);
      background: #f0f2f5;

      img {
        width: 100%;
        height: 100%;
        object-fit: cover;
      }
    }
  }

  .head-info-count {
    flex: 1;
    display: flex;
    flex-direction: column;
    justify-content: center;

    .head-info-welcome {
      font-size: 22px;
      font-weight: 600;
      color: #333;
      margin-bottom: 12px;
    }

    .head-info-desc {
      color: #666;
      font-size: 16px;
      margin-bottom: 8px;

      p {
        margin: 0;
      }
    }

    .head-info-time {
      color: #999;
      font-size: 14px;
      margin-top: 10px;
    }
  }

  ::v-deep .ant-btn {
    border-radius: 20px;
    padding: 0 24px;
    height: 36px;
    background: linear-gradient(45deg, #4A90E2, #63B3ED);
    border: none;
    color: white;
    font-weight: 500;
    box-shadow: 0 4px 12px rgba(74, 144, 226, 0.3);
    transition: all 0.3s ease;

    &:hover {
      transform: translateY(-2px);
      box-shadow: 0 6px 16px rgba(74, 144, 226, 0.4);
    }
  }
}
.news-banner {
  width: 100%;
  margin: 0 auto 15px;
  padding: 0;
  background: rgba(255, 255, 255, 0.85);
  backdrop-filter: blur(10px);
  border-radius: 12px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
  border: 1px solid rgba(255, 255, 255, 0.3);
  overflow: hidden;

  .news-content {
    padding: 16px 24px;

    h3 {
      margin: 0 0 8px 0;
      font-size: 16px;
      font-weight: 600;
      color: #333;
    }

    p {
      margin: 0;
      font-size: 14px;
      color: #666;
      line-height: 1.5;
    }
  }

  .next-button {
    height: 100%;
    width: 100%;
    height: 48px;
    border-radius: 0;
    text-align: center;
    line-height: 48px;
    border: none;
    background: linear-gradient(90deg, #4A90E2, #63B3ED);
    color: white;
    font-weight: 500;
    letter-spacing: 0.5px;
    transition: all 0.3s ease;

    &:hover {
      background: linear-gradient(90deg, #3A7BC8, #4A90E2);
      box-shadow: 0 4px 12px rgba(74, 144, 226, 0.3);
    }
  }
}

</style>
<style lang="less" scoped>.scenic-carousel-wrapper {
  border-radius: 2px;
  overflow: hidden;
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.15);
  position: relative;
  height: 360px;

  // 轮播图基础样式
  ::v-deep .ant-carousel .slick-slider {
    height: 100%;
  }

  ::v-deep .ant-carousel .slick-list {
    height: 100%;
  }

  ::v-deep .ant-carousel .slick-track {
    height: 100%;
  }

  ::v-deep .ant-carousel .slick-slide {
    height: 360px;
    position: relative;
  }
}

.carousel-slide {
  position: relative;
  height: 100%;

  .carousel-image {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }

  .slide-overlay {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: linear-gradient(to right, rgba(0, 0, 0, 0.7) 0%, rgba(0, 0, 0, 0.3) 50%, transparent 100%);

    .slide-content {
      position: absolute;
      top: 50%;
      left: 5%;
      transform: translateY(-50%);
      color: white;
      max-width: 60%;
      z-index: 2;

      .slide-title {
        color: white;
        font-size: 28px;
        font-weight: 700;
        margin-bottom: 12px;
        text-shadow: 0 2px 4px rgba(0, 0, 0, 0.5);
      }

      .slide-description {
        font-size: 16px;
        margin-bottom: 24px;
        text-shadow: 0 1px 2px rgba(0, 0, 0, 0.5);
        line-height: 1.5;
      }

      .explore-btn {
        border-radius: 24px;
        padding: 0 28px;
        height: 42px;
        font-weight: 500;
        background: rgba(255, 255, 255, 0.2);
        backdrop-filter: blur(10px);
        border: 1px solid rgba(255, 255, 255, 0.3);
        color: white;
        font-size: 15px;
        transition: all 0.3s ease;

        &:hover {
          background: rgba(255, 255, 255, 0.3);
          transform: translateY(-2px);
          box-shadow: 0 6px 16px rgba(0, 0, 0, 0.2);
        }
      }
    }
  }
}
</style>
