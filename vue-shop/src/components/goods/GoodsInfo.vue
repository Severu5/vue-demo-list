<template>
  <div class="goodslist">
    <transition @before-enter="beforeEnter" @enter="enter" @after-enter="afterEnter">
      <div class="ball" v-show="ballFlag" ref="ball"></div>
    </transition>

    <!-- 商品轮播区域 -->
    <div class="mui-card">
      <swipe :swipeList="msg" :isFull="false"></swipe>
    </div>
    <!-- 商品购买 -->

    <div class="mui-card">
      <div class="mui-card-header">{{ goodsinfo.title }}</div>
      <div class="mui-card-content">
        <div class="mui-card-content-inner">
          <p class="price">
            市场价：
            <del>￥{{ goodsinfo.market_price }}</del>&nbsp;&nbsp;销售价：
            <span class="now_price">￥{{ goodsinfo.sell_price }}</span>
          </p>
          <p>
            购买数量：
            <numbox :maxlength="goodsinfo.stock_quantity" @getcount="getSelectedCount"></numbox>
          </p>
          <mt-button type="primary" size="small">立即购买</mt-button>
          <mt-button type="danger" size="small" @click="addCart">加入购物车</mt-button>
        </div>
      </div>
    </div>

    <!-- 商品参数 -->

    <div class="mui-card">
      <div class="mui-card-header">商品参数</div>
      <div class="mui-card-content">
        <div class="mui-card-content-inner">
          <p>商品货号:&nbsp;{{ goodsinfo.goods_no }}</p>
          <p>库存情况:&nbsp;{{ goodsinfo.stock_quantity }}件</p>
          <p>上架时间:&nbsp;{{ goodsinfo.add_time | dateFormat }}</p>
        </div>
      </div>
      <div class="mui-card-footer">
        <mt-button type="primary" size="large" plain @click="goDesc(id)">图文介绍</mt-button>
        <mt-button type="danger" size="large" plain @click="goComment(id)">商品评论</mt-button>
      </div>
    </div>
  </div>
</template>
<script>
import swipe from "../subcomponents/swipe.vue";
import numbox from "../subcomponents/goodsInfoNumberbox.vue";
export default {
  data() {
    return {
      id: this.$route.params.id,
      msg: [],
      goodsinfo: {},
      ballFlag: false,
      selectedCount: 1
    };
  },
  created() {
    this.getSwipe();
    this.getGoodInfo();
  },
  methods: {
    getSwipe() {
      this.$http.get("api/getthumimages/" + this.id).then(result => {
        if (result.body.status === 0) {
          result.body.message.forEach(item => {
            item.img = item.src;
          });
          this.msg = result.body.message;
        }
      });
    },
    getGoodInfo() {
      this.$http.get("api/goods/getinfo/" + this.id).then(result => {
        if (result.body.status === 0) {
          this.goodsinfo = result.body.message[0];
        }
      });
    },
    goDesc(id) {
      this.$router.push({ name: "goodsdesc", params: { id } });
    },
    goComment(id) {
      this.$router.push({ name: "goodscomment", params: { id } });
    },
    addCart() {
      this.ballFlag = !this.ballFlag;
      var good = {id: this.id, count: this.selectedCount,price:this.goodsinfo.sell_price, selected:true};
      this.$store.commit('addToCart',good);
    },
    beforeEnter(el) {
      el.style.transform = "translate(0,0)";
    },
    enter(el) {
      el.offsetWidth;

        const ballPosition = this.$refs.ball.getBoundingClientRect();
        const badgePosition = document.getElementById('badge').getBoundingClientRect();

        const xDist = badgePosition.left - ballPosition.left;
        const yDist = badgePosition.top - ballPosition.top;
      el.style.transform = `translate(${xDist}px,${yDist}px)`;
      el.style.transition = "all 0.3s cubic-bezier(.34,-0.36,1,.69)";
    },
    afterEnter(el) {
      this.ballFlag = !this.ballFlag;
    },
    getSelectedCount(count){
        this.selectedCount=count;
    }
  },
  components: {
    swipe,
    numbox
  }
};
</script>
<style lang="scss" scoped>
.goodslist {
  background-color: #eee;
  overflow: hidden;
  .now_price {
    color: red;
    font-size: 16px;
    font-weight: bold;
  }
  .mui-card-footer {
    display: block;
    button {
      margin: 15px 0;
    }
  }
  .ball {
    width: 15px;
    height: 15px;
    border-radius: 50%;
    background-color: red;
    position: absolute;
    z-index: 1;
    top: 360px;
    left: 152px;
  }
}
</style>