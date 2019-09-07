<template>
  <div>
    <div class="search-wrap"
         @click="searchLocus">
      {{date}}
      <span v-if="beginTime">({{beginTime}} - {{endTime}})</span>
      <span class="more"></span>
    </div>
    <!-- 暂无相关轨迹点 S -->
    <div class="no-points-data no-data"
         v-if="noPoint ||noimei">没有相关轨迹信息</div>
    <!-- 暂无相关轨迹点 S -->
    <!-- <div v-if="noPoint||noimei"
         class="no-data">
      <img src="../../assets/imgs/icon_kong.png" />
      <div>暂无相关轨迹点</div>
    </div> -->
    <!-- 暂无相关轨迹点 E -->
    <div id="mapPanel">
      <div class="map-loading-center"
           v-show='loading'>
        <van-loading type="spinner"
                     color="#1989fa"
                     style="margin: 0 auto" />
      </div>
    </div>
    <div class="footer-div"
         v-if="!noPoint&&!noimei">
      <!--
      轨迹播放器
      --->
      <!-- 弹出框 -->
      <van-popup v-model="infoShow"
                 position="bottom"
                 :overlay="false"
                 class="pop">

        <van-grid clickable
                  :border="false">
          <van-grid-item icon="play-circle-o"
                         class=" ml30">
            <img @click="start"
                 src='../../assets/imgs/Position/icon_qianjin@2x.png'
                 class="bofanqi" /></van-grid-item>
          <van-grid-item icon="pause-circle-o">
            <img @click="pause"
                 src='../../assets/imgs/Position/icon_bofang@2x.png'
                 class="bofanqi2" /></van-grid-item>
          <van-grid-item icon="replay"
                         class=" mr30">
            <img @click="reset"
                 src='../../assets/imgs/Position/icon_houtui@2x.png'
                 class="bofanqi" /></van-grid-item>
        </van-grid>
        <div class="pop-wrap">
          <!--轨迹信息-->
          <van-row class="van-grid">
            <van-col span="18"
                     class="colleft e-info">
              <!-- <div>福建省泉州市丰泽区港湾街靠近东海泰禾广场A座380号 </div> -->
              <div>{{positions}} </div>
              <div> {{times | formatTime}}</div>
              <!-- <div>2019-12-12 09:12:23</div> {{times| formatTime }} -->
              <div>
                <span class="gap">
                  定位模式：
                  <!--WIFI -->
                  <span>{{typeName}}</span>
                  <!-- <span v-if="lEquipInfo.locationModeGPS == 1">GPS</span> -->
                  <!-- <span v-if="lEquipInfo.locationModeGPS == 1 && lEquipInfo.locationModeLBS == 1">、</span>
                  <span v-if="lEquipInfo.locationModeLBS == 1">LBS</span>
                  <span v-if="lEquipInfo.locationModeLBS == 1 && lEquipInfo.locationModeWIFI == 1">、</span>
                  <span v-if="lEquipInfo.locationModeWIFI == 1">WIFI</span> -->
                </span>
              </div>
              <!--  <div>{{options.options}}</div>
              <div><span>{{options.times}}</span> <span>定位模式{{options.type}}</span></div>-->
            </van-col>
            <van-col span="6"
                     class="imggrid">
              <img src='../../assets/imgs/icon_liebiao@2x.png'
                   @click='viewLocusPoint'
                   class="daohan" />
              <div>轨迹列表</div>
            </van-col>
          </van-row>
        </div>
      </van-popup>
    </div>
    <v-bar :active="active"
           style="position: fixed;buttom:0;left:0"></v-bar>
  </div>
</template>
<script>
import { Grid, GridItem } from 'vant'
import { loadingMap } from '@/utils/load'
import { mapState } from 'vuex'
import mine from '@/store/mine/index'
import vanBar from '@/view/componse/vartarbbar'
import { LOCQUERY } from '@/store/mine/types'
import { constants } from 'fs'
import { formatDate } from '@/utils/datetime'
export default {
  components: {
    'v-bar': vanBar
    // 'van-grid': Grid,
    // 'van-grid-item': GridItem
  },
  data () {
    return {
      map: null, // 地图实例
      walking: null, //
      points: [],
      centerPoint: null,
      car: null,
      label: null,
      infoShow: true,
      index: 0,
      active: 1,
      timer: null,
      times: '',
      typeName: '',
      positions: '',
      loading: false,
      playing: false,
      i: 0,
      imei: '',
      noimei: true,
      // dogImg: require('../../assets/imgs/Position/icon_chognwu@2x.png'),
      dogImg: require('../../assets/imgs/icon_chognwu2.png'),
      options: {
        optionList: []
      }
    }
  },
  filters: {
    formatTime (times) {
      console.log(times, times == null || times === '')
      if (times == null || times === '') {
        return '暂无更新时间'
      }
      let date = new Date(times)
      console.log(formatDate(date, 'yyyy-MM-dd hh:mm:ss'))
      return '更新于 ' + formatDate(date, 'yyyy-MM-dd hh:mm:ss')
    }
  },
  mounted () {
    let nowEquiment = JSON.parse(sessionStorage.getItem('nowEquiment'))
    if (nowEquiment && nowEquiment.imei) {
      // console.log(nowEquiment, nowEquiment.IMEI, nowEquiment && nowEquiment.IMEI)
      this.noimei = false
      this.imei = nowEquiment.imei
      this.$store.dispatch(LOCQUERY, {
        IMEI: this.imei,
        startTime: this.date + ' ' + this.beginTime,
        endTime: this.date + ' ' + this.endTime
      })
      // this.initMap()
      this.initRoute()
    } else {
      this.initMap()
      this.noimei = true
    }
  },
  beforeDestroy() {
    // 重置数据
    this.$store.commit("SETLOCUSPOINT");
    this.$store.commit("SETLOCUSDATE");
  },
  watch: {
    pointList: {
      handler: function () {
        if (this.pointList.length > 0) {
          this.pointList.map((item, index) => {
            this.points.push(new BMap.Point(item.lon, item.lat))
          })
          this.getEquInfo(0)
          this.alertInfo(0)
          this.run()
        } else {
          initMap ()
          console.log("no-pointList");
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
      date: state => mine.state.date,
      pointList2: state => mine.state.pointList2,
      lEquipInfo: statr => mine.state.lEquipInfo
    })
  },
  methods: {
    initRoute () {
      this.map ? '' : this.initMap() // 地图尚未实例化
      this.map.centerAndZoom(this.points[0], 16)
      this.map.enableScrollWheelZoom()
    },
    // 选择时间
    searchLocus () {
      this.$store.commit('SETLOCUSPOINT', { pointList: 0, noPoint: false })
      console.log(this.imei)
      this.$router.push('/mine/device/selectdate')
    },
    // 取随机数
    getRandomNumberByRange (start, end) {
      return Math.floor(Math.random() * (end - start) + start)
    },
    play () {
      var point = this.points[this.index]
      if (this.index > 0) {
        this.map.addOverlay(new BMap.Polyline([this.points[this.index - 1], point], { strokeColor: '#FF432F', strokeWeight: 2, strokeOpacity: 1 }))
      }
      this.label.setContent('经度: ' + point.lng + '<br>纬度: ' + point.lat)
      this.car.setPosition(point)
      this.index++
      if (this.index < this.points.length) {
        this.timer = setTimeout(this.play(this.index), 60000)
      } else {
        this.map.panTo(point)
      }
    },
    run () {
      this.walking = new BMap.WalkingRoute(this.map, { renderOptions: { map: this.map, autoViewport: true } })
      var sdatae = this.points.slice(0, -1)
      var arr = []
      var myIcon = new BMap.Icon(this.dogImg, new BMap.Size(53, 53), { // 小车图片
        // offset: new BMap.Size(0, -5),    //相当于CSS精灵
        imageOffset: new BMap.Size(0, 0) // 图片的偏移量。为了是图片底部中心对准坐标点。
      })
      var chartData = []
      chartData = Object.assign([], this.points)
      // 定义起点终点
      var myIcon2 = new BMap.Icon('http://wwmimgs.oss-cn-shenzhen.aliyuncs.com/gps/2019-07-29/icon_zhogndian.png', new BMap.Size(34, 40), { imageOffset: new BMap.Size(0, 0) })// 起点
      var myIcon1 = new BMap.Icon('http://wwmimgs.oss-cn-shenzhen.aliyuncs.com/gps/2019-07-29/icon_qidian.png', new BMap.Size(34, 40), { imageOffset: new BMap.Size(0, 0) })/// 终点
      var marker1 = new BMap.Marker(chartData[0], { icon: myIcon1 })
      var marker2 = new BMap.Marker(chartData[chartData.length - 1], { icon: myIcon2 })
      var _this = this
      setTimeout(function () {
        _this.map.setViewport(chartData)
        _this.map.addOverlay(marker1)
        _this.map.addOverlay(marker2)
      }, 3000)
      this.map.enableScrollWheelZoom() // 启用滚轮放大缩小，默认禁用
      this.map.enableContinuousZoom() // 启用地图惯性拖拽，默认禁用
      this.map.clearOverlays()
      // 使用折现
      var linePoints = chartData
      var polyline = new BMap.Polyline(linePoints, { strokeColor: '#FF432F', strokeWeight: 2, strokeOpacity: 1 })
      this.map.addOverlay(polyline)
      // 多点跑
      // 实例化一个驾车导航用来生成路线

      // var arr = chartData
      // console.log(arr)
      // var drv = new BMap.WalkingRoute(chartData[0], {
      //   onSearchComplete: function (res) {
      //     if (drv.getStatus() == BMAP_STATUS_SUCCESS) {
      //       var plan = res.getPlan(0)
      //       var arrPois = []
      //       for (var j = 0; j < plan.getNumRoutes(); j++) {
      //         var route = plan.getRoute(j)
      //         arrPois = arrPois.concat(route.getPath())
      //       }
      //       _this.map.addOverlay(new BMap.Polyline(arrPois, { strokeColor: '#FF432F' }))
      //       _this.map.setViewport(arrPois)
      //     }
      //   }
      // })

      // for (var i = 0; i < arr.length - 1; i++) {
      //   var start = arr[i]
      //   var end = arr[i + 1];
      //   (() => {
      //     console.log('ab')
      //     drv.search(start, end)
      //   })()
      // }

      this.bogMk = new BMap.Marker(this.points[0], { icon: myIcon })
      this.map.addOverlay(this.bogMk)
      this.alertInfo(0)
      console.log(this.lEquipInfo)
      this.typeName = this.lEquipInfo[0].typeName
      this.times = this.lEquipInfo[0].versionTimeStr
      console.log(22222, this.times)
      // this.play()
    },
    // 前进
    start () {
      var _this = this
      console.log('kaishi')
      _this.i -= 5
      _this.playing = true
      setTimeout(function () {
        _this.resetMkPoint(_this.i, true)
      }, 100)
    },
    resetMkPoint (i) {
      var _this = this
      var paths = _this.points.length
      _this.bogMk.setPosition(_this.points[i])
      this.typeName = this.lEquipInfo[i].typeName
      this.times = this.lEquipInfo[i].versionTimeStr
      if (i < paths) {
        _this.timerout = setTimeout(function () {
          if (!_this.playing) {
            _this.i = i
            clearTimeout(_this.timerout)
          } else {
            i++
            _this.getEquInfo(i)
            _this.resetMkPoint(i)
          }
          _this.alertInfo(i)
        }, 100)
      }
    },
    // 播放 暂停
    pause () {
      console.log('播放')
      this.playing = !this.playing
      var _this = this
      console.log(this.i)

      setTimeout(function () {
        _this.resetMkPoint(_this.i, true)
      }, 100)
    },
    // 快进
    reset () {
      var _this = this
      console.log('快进')
      _this.playing = true
      _this.i += 5
      setTimeout(function () {
        _this.resetMkPoint(_this.i, true)
      }, 100)
    },
    // 返回上一页
    goBack () {
      this.$router.push('/mine/devicemanage')
    },
    // 初始化地图
    initMap () {
      this.map = new BMap.Map('mapPanel')
      // 初始城市上海
      this.map.centerAndZoom(new BMap.Point(121.480539, 31.235929), 15)
      this.map.addEventListener('dragend', () => {
      })
    },
    // 获取设备当前消息
    getEquInfo (i) {
      // console.log(this.pointList[i])
      // this.lEquipInfo.typeName = this.pointList[i].g.loc.typeName
      // this.lEquipInfo.updateTime = this.pointList[i].g.versionTime
    },
    // 弹出信息
    alertInfo (i) {
      var geoc = new BMap.Geocoder()
      var _this = this;
      (() => {
        geoc.getLocation(this.points[i], function (rs) {
          console.log(rs)
          // if (rs) { } else {
          var addComp = rs.addressComponents
          // let sdd = addComp.province + ', ' + addComp.city + ', ' + addComp.district + ', ' + addComp.street + ', ' + addComp.streetNumber
          // _this.options.optionList.push(addComp.province + ', ' + addComp.city + ', ' + addComp.district + ', ' + addComp.street + ', ' + addComp.streetNumber)
          _this.options.optionList.push(rs.address)
          // _this.lEquipInfo.option = sdd
          console.log(rs.address, 333)
          // }
          _this.positions = rs.address
        })
      })()
    },
    // 轨迹列表
    viewLocusPoint () {
      let imei = this.imei
      this.$router.push({ path: `/mine/locuslist/${imei}` })
    }
  }
}
</script>

<style lang="less" scoped>
#mapPanel {
  height: 650px;
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
  span.more {
    width: 0;
    height: 0;
    border-width: 7px 7px 0;
    border-style: solid;
    border-color: #232b38 transparent transparent; /*灰 透明 透明 */
    position: relative;
    top: 12px;
    left: 6px;
  }
}

.map-loading-center {
  position: absolute;
  top: 100px;
  left: 180px;
}

.bofanqi {
  width: 19px;
}
.ml30 {
  margin-left: 30px !important;
}
.mr30 {
  margin-right: 30px !important;
}
.bofanqi2 {
  width: 35px;
}
.footer-div {
  width: 100%;
  height: 150px;
  position: absolute;
  left: 0;
  background: #fff;
  border-radius: 10px 10px 0 0;
  bottom: 49px;
  .van-grid {
    width: 100%;
    justify-content: space-around !important;
    font-size: 13pt;
    color: #232b38;
  }
}
.daohan {
  width: 53px;
}
.imggrid {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding-top: 0px !important;
  text-align: center;
  border-left: 1px solid #e6e7ed;
  div {
    font-size: 13px;
      color: #232b38;
  }
}
.colleft {
  padding: 5px 13px;
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
.van-popup--bottom {
  bottom: 43px;
}
.pop-wrap div.e-info {
  margin: 0;
  padding-right: 0;
}
</style>
