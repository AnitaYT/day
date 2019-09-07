<template>
  <div>
    <div class="search-wrap">
      <img src="../../../assets/imgs/goback.png" @click="goBack" class="back" />
      {{date}}
      <span v-if="beginTime">({{beginTime}} - {{endTime}})</span>
      <img class="more" @click="searchLocus" src="../../../assets/imgs/xiala@2x.png" />
    </div>
    <!-- 暂无相关轨迹点 S -->
    <div class="no-points-data" v-show="noPoint">没有相关轨迹信息</div>
    <!-- 暂无相关轨迹点 E -->
    <div id="mapPanel">
      <div class="map-loading-center">
        <van-loading type="spinner" color="#F9C718" style="margin: 0 auto" />
      </div>
    </div>
    <!-- 弹出框 -->
    <van-popup v-model="infoShow" position="bottom" :overlay="false" class="pop">
      <div class="l-s-wrap">
        <img @click="back" src="../../../assets/imgs/Position/icon_qianjin@2x.png" class="l-s-icon" />
        <img
          :src="playing ? require('../../../assets/imgs/Position/icon_zhanting@2x.png') : require('../../../assets/imgs/Position/icon_bofang@2x.png')"
          class="l-s-icon-center"
          @click="pauseOrPlay"
        />
        <img
          @click="goAdvance"
          src="../../../assets/imgs/Position/icon_houtui@2x.png"
          class="l-s-icon"
        />
      </div>
      <div class="pop-wrap">
        <div class="e-info">
          <div>{{site}} {{playing}}</div>
          <div>{{time| formatTime}}</div>
          <div>
            <span class="gap">定位模式：WIFI</span>
          </div>
        </div>
        <!-- 轨迹列表 -->
        <div class="e-img">
          <img src="../../../assets/imgs/Position/icon_liebiao.png" @click="viewLocusPoint" />
        </div>
      </div>
    </van-popup>
  </div>
</template>
<script>
import { formatDate } from "@/utils/datetime";
import { loadingMap } from "@/utils/load";
import { mapState } from "vuex";
import mine from "@/store/mine/index";
import { LOCQUERY } from "@/store/mine/types";
export default {
  data() {
    return {
      site: "",
      time: "",
      playing: false,
      infoShow: true, // 详情弹框
      map: null, // 地图实例
      points: [],
      index: 0,
      footmark: null,
      start: null,
      end: null,
      footImg: require("../../../assets/imgs/icon_chognwu2.png"),
      startImg: require("../../../assets/imgs/icon_qidian.png"),
      endImg: require("../../../assets/imgs/icon_zhogndian.png"),
      timer: null
    };
  },
  created() {
    let imei = this.$route.params.imei;
    this.site = this.$route.query.site;
    this.time = this.$route.query.time;
    if (imei) {
      this.$store.dispatch(LOCQUERY, {
        IMEI: imei,
        startTime: this.date + " " + this.beginTime,
        endTime: this.date + " " + this.endTime
      });
    }
  },
  watch: {
    pointList: {
      handler: function() {
        if (this.pointList.length > 0) {
          this.pointList.map((item, index) => {
            this.points.push(new BMap.Point(item.lon, item.lat));
          });
          this.initRoute();
        } else {
          // 初始化地图
          this.map = new BMap.Map("mapPanel");
          this.map.centerAndZoom(new BMap.Point(118.60036234, 24.90165328), 16);
          console.log("watch-pointList");
        }
      },
      deep: true
    }
  },
  computed: {
    ...mapState({
      noPoint: state => mine.state.noPoint,
      pointList: state => mine.state.pointList,
      beginTime: state => mine.state.beginTime,
      endTime: state => mine.state.endTime,
      date: state => mine.state.date
    })
  },
  filters: {
    formatTime(time) {
      if (time == null || time === "") {
        return "暂无更新时间";
      }
      let date = new Date(time);
      return "更新于 " + formatDate(date, "yyyy-MM-dd hh:mm:ss");
    }
  },
  methods: {
    addLine(arr) {
      this.map.clearOverlays();
      var linePoints = arr;
      let s = new BMap.Icon(this.startImg, new BMap.Size(34, 40));
      let e = new BMap.Icon(this.endImg, new BMap.Size(34, 40));
      // 添加起点、终点
      this.start = new BMap.Marker(
        new BMap.Point(linePoints[0].lng, linePoints[0].lat)
      );
      this.start.setIcon(s);
      this.map.addOverlay(this.start);
      this.end = new BMap.Marker(
        new BMap.Point(
          linePoints[linePoints.length - 1].lng,
          linePoints[linePoints.length - 1].lat
        )
      );
      this.end.setIcon(e);
      this.map.addOverlay(this.end);
      //显示 足迹图标
      let icon = new BMap.Icon(this.footImg, new BMap.Size(32, 32), {
        imageOffset: new BMap.Size(0, 0)
      });
      this.footmark = new BMap.Marker(
        new BMap.Point(linePoints[0].lng, linePoints[0].lat),
        { icon: icon }
      );
      this.map.addOverlay(this.footmark);
      // 添加折线
      this.polyline = new BMap.Polyline(linePoints, {
        strokeColor: "#FF432F",
        strokeWeight: 3,
        strokeOpacity: 1
      });
      this.map.addOverlay(this.polyline);
      // setTimeout(() => {
      this.map.setViewport(arr);
      // }, 1000);
    },
    // 后退 
    back() {
      console.log("back");
    },
    // 播放
    pauseOrPlay() {
      this.playing = !this.playing;
      if (this.playing) {
        console.log("播放", this.playing);
        this.play();
      } else {
        console.log("暂停", this.playing);
      }
    },
    // 前进
    goAdvance() {
      console.log("goAdvance");
    },
    // 轨迹列表
    viewLocusPoint() {
      let imei = this.$route.params.imei;
      this.$router.push({ path: `/mine/locuslist/${imei}` });
    },
    initRoute() {
      this.map ? "" : this.initMap(); // 地图尚未实例化
      this.map.centerAndZoom(this.points[0], 15);
      this.map.enableScrollWheelZoom();
      // 添加 轨迹路线
      this.addLine(this.points);
    },
    // 选择时间
    searchLocus() {
      this.$store.commit("SETLOCUSPOINT");
      this.$router.push(
        "/mine/device/selectdate?imei=" + this.$route.params.imei
      );
    },
    play() {
      var point = this.points[this.index];
      if (this.index > 0) {
        this.map.addOverlay(
          new BMap.Polyline([this.points[this.index - 1], point], {
            strokeColor: "transparent",
            strokeWeight: 1,
            strokeOpacity: 1
          })
        );
      }
      this.footmark.setPosition(point);
      this.index++;
      if (this.index < this.points.length) {
        this.timer = setTimeout(() => {
          this.play(this.index);
          this.map.panTo(this.points[this.index]);
        }, 500);
      } else {
        this.map.panTo(point);
      }
    },
    // 返回上一页
    goBack() {
      this.$store.commit("SETLOCUSPOINT");
      this.$store.commit("SETLOCUSDATE");
      this.$router.push("/mine/devicemanage");
    },
    // 初始化地图
    initMap() {
      this.map = new BMap.Map("mapPanel");
      // 初始城市上海
      this.map.centerAndZoom(new BMap.Point(121.480539, 31.235929), 15);
      this.map.addEventListener("dragend", () => {});
    }
  }
};
</script>

<style lang="less" scoped>
#mapPanel {
  height: 700px;
  width: 100%;
  background-color: rgba(76, 76, 76, 0.4);
}
.search-wrap {
  text-align: center;
  position: absolute;
  width: 94%;
  top: 20px;
  z-index: 100;
  left: calc((100% - 94%) / 2);
  background: #fff;
  color: #232b38;
  font-size: 17px;
  border-radius: 10px;
  padding: 17px 0;
  box-shadow: 0px 2px 10px 0px rgba(51, 51, 51, 0.2);
  .back {
    position: absolute;
    left: 10px;
    top: 15px;
  }
  .more {
    width: 12px;
    height: 8px;
    position: relative;
    top: -2px;
    left: 6px;
  }
}
.pop-wrap {
  width: 100%;
  background: #fff;
  padding: 0 12px;
  box-sizing: border-box;
  display: flex;
  border-radius: 10px 10px 0 0;
  overflow: hidden;
  align-items: center;
  div.e-info {
    // 数据信息
    margin: 20px 0;
    width: 72%;
    border-right: 1px solid #e6e7ed;
    padding-right: 18px;
    & div {
      font-size: 13px;
      color: #232b38;
      line-height: 22px;
      i {
        margin-left: 20px;
      }
      & > span.f {
        display: inline-block;
        min-width: 120px;
      }
      .gap {
        // margin-left: 10px;
      }
    }
  }
  div.e-img {
    img {
      width: 53px;
      height: 53px;
      margin-left: 40%;
    }
  }
}
.pop.van-popup {
  overflow: visible;
  overflow-y: visible;
}
.l-s-wrap {
  display: flex;
  justify-content: center;
  align-items: center;
  padding-top: 15px;
  img.l-s-icon {
    width: 19px;
    height: 25px;
  }
  img.l-s-icon-center {
    width: 36px;
    height: 36px;
    margin: 0 60px;
  }
}
</style>
