<template>
  <div class="ratings" v-el:ratings>
    <div class="ratings-content">
      <div class="overview">
        <div class="overview-left">
          <h1 class="score">{{seller.score}}</h1>
          <div class="title">综合评分</div>
          <div class="rank">高于周边商家{{seller.rankRate}}%</div>
        </div>
        <div class="overview-right">
          <div class="score-wrapper">
            <span class="title">服务态度</span>
            <star :size="36" :score="seller.serviceScore"></star>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
          <div class="score-wrapper">
            <span class="title">商品评分</span>
            <star :size="36" :score="seller.foodScore"></star>
            <span class="score">{{seller.foodScore}}</span>
          </div>
          <div class="delivery-wrapper">
            <span class="title">送到时间</span>
            <span class="delivery">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
    </div>
    <split></split>
    <ratingselect :select-type="selectType" :desc="desc" :ratings=""></ratingselect>
    <div class="ratings-wrapper">
      <ul>
        <li v-for="rating in ratings">
          <div class="avatar">
            <img width="25" height="28" :src="rating.avatar">
          </div>
          <div class="content">
            <div class="name">{{rating.username}}</div>
            <div class="star-wrapper">
              <star :size="24" :score="rating.score"></star>
              <span class="delivery" v-show="rating.deliveryTime">
                {{rating.deliveryTime}}
              </span>
              <p class="text">{{rating.text}}</p>
              <div class="recommend" v-show="rating.recommend &&rating.recommend.length">
                <i class="icon-thumb_up"></i>
                <span class="item" v-for="item in rating.recommend" >{{item}}</span>
              </div>
              <div class="time">
                {{rating.rateTime | formatDate}}
              </div>
            </div>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import {formatDate} from 'common/js/date';
  import star from 'components/star/star';
  import ratingselect from 'components/ratingselect/ratingselect';
  import split from 'components/split/split';

  const ALL = 2;
  const ERR_OK = 0;

  export default {
      props: {
          seller: {
              type: Object
          }
      },
      data() {
          return {
            ratings: [],
            showFlag: false,
            selectType: ALL,
            onlyContent: true
          };
      },
      created () {
        this.$http.get('/api/ratings').then((response) => {
          response = response.body;
          if (response.errno === ERR_OK) {
            this.ratings = response.data;
            this.$nextTick(() => {
              this.scroll = new BScroll(this.$els.ratings, {
                click: true
              });
            });
          }
        });
      },
      filters: {
        formatDate(time) {
          let date = new Date(time);
          return formatDate(date, 'yyyy-MM-dd hh:mm');
        }
      },
      components: {
         star,
         ratingselect,
         split
      }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .ratings
    position :absolute
    top :174px
    bottom :0
    left :0
    width :100%
    overflow :hidden
    .overview
      display :flex
      padding :18px 0
      .overview-left
        flex :0 0 137px
        padding :6px 0
        width :137px
        border-right :1px solid rgba(7,17,27,0.1)
        text-align :center
        @media only screen and (max-width :320px)
          flex :0 0 120px
          width :120px
        line-height :28px
        font-size :24px
        color :rgb(255,153,0)
        .title
          margin-bottom :8px
          line-height :12px
          font-size :12px
          color :rgb(7, 17, 27)
        .rank
          line-height :10px
          font-size :12px
          color :rgb(147, 153, 159)
      .overview-right
        flex :1
        padding : 6px 0 6px 24px
        @media only screen and (max-width :320px)
          padding-left : 6px
        .score-wrapper
          margin-bottom :8px
          font-size :0
          .title
            display :inline-block
            line-height :18px
            vertical-align :top
            font-size :12px
            color :rgb(7,17,27)
          .star
            display :inline-block
            vertical-align :top
            margin :0 12px
          .score
            display :inline-block
            line-height :18px
            vertical-align :top
            font-size :12px
            color :rgb(255,153,0)

        .delivery-wrapper
          font-size :0
          .title
            line-height :18px
            font-size :12px
            color :rgb(7,17,27)
          .delivery
            margin-left :12px
            font-size :12px
            color :rgb(147, 153, 159)

</style>
