<template>
  <div class="container screen-container">
    <div class="border bg-shadow" v-corner="'absolute'"></div>
    <div class="left">
      <div class="logo">
        <img src="./assets/img/screenLogo.png" alt="">
      </div>
      <div class="header">科技云平台</div>
      <div class="left-content">
        <div class="left-content-top">
          <div class="data-wrap bg-shadow" v-corner>
            <ul>
              <li>
                <span class="label">全国三恒总面积</span>
                <span class="data">
                  <strong v-for="(item,idx) in allAreaList" :key="idx">{{item}}</strong>
                  <i class="unit"> 平方</i>
                </span>
              </li>
              <li>
                <span class="label">三恒在线面积</span>
                <span class="data">
                  <strong v-for="(item,idx) in onlineAreaList" :key="idx">{{item}}</strong>
                  <i class="unit"> 平方</i>
                </span>
              </li>
              <li>
                <span class="label">异常报警</span>
                <span class="data">
                  <strong v-for="(item,idx) in alarmCountList" :key="idx">{{item}}</strong>
                  <i class="unit"> 项</i>
                </span>
              </li>
            </ul>
            <div class="legend-wrap">
              <p class="count">今日新增上线项目数量：<span>{{areaInfo.todayCount==null?'--':areaInfo.todayCount}}</span></p>
              <div class="legend">
                <span
                  ><i :style="'backgroundColor:' + colorList[0]"></i
                  >在线</span
                >
                <span
                  ><i :style="'backgroundColor:' + colorList[1]"></i
                  >离线</span
                >
                <span
                  ><i :style="'backgroundColor:' + colorList[2]"></i
                  >异常报警</span
                >
              </div>
            </div>
          </div>
          <div class="map-wrap bg-shadow" v-corner>
            <!-- <div v-show="chooseName != '全国'" class="back" @click="goBack">
              返回
            </div> -->
            <div class="select-area">
              <el-select
                v-model="chooseName"
                @change="provinceChange"
                filterable
                class="select-province"
                popper-class="select-province-popper"
                placeholder="请选择"
                size="mini"
              >
                <el-option label="全国" value="全国"></el-option>
                <el-option
                  v-for="item in mapData"
                  :label="item.name"
                  :value="item.name"
                  :key="item.name"
                ></el-option>
              </el-select>
            </div>
            <div id="map"></div>
            <!-- 耗电量统计-->
            <div class="power-consumption bg-shadow">
              <p class="title">耗电量统计</p>
              <ul>
                <li id="villa"></li>
                <li id="apartment"></li>
              </ul>
            </div>
            <!-- 系统运行状况-->
            <div class="system-runStatus">
              <ul>
                <li>
                  <span class="data">{{projectCountInfo.allProjectCount==null?'--':projectCountInfo.allProjectCount}}</span>
                  <span class="label">总项目数量</span>
                </li>
                <li>
                  <span class="data">{{projectCountInfo.allOnlineProjectCount==null?'--':projectCountInfo.allOnlineProjectCount}}</span>
                  <span class="label">在线项目数量</span>
                </li>
                <li>
                  <span class="data">{{projectCountInfo.allOffLineProjectCount==null?'--':projectCountInfo.allOffLineProjectCount}}</span>
                  <span class="label">离线项目数量</span>
                </li>
              </ul>
            </div>
          </div>
        </div>
        <div class="left-content-bottom bg-shadow" v-corner>
          <ul>
            <li class="chart">
              <p class="title">系统运行压力监测点状图</p>
              <div id="pressure-monitor"></div>
            </li>
            <li class="chart">
              <p class="title">室内舒适度统计</p>
              <div id="indoor-comfort"></div>
            </li>
            <li class="table">
              <p class="title">异常报警信息栏</p>
              <div id="alarm">
                <ul class="item">
                  <li v-for="(item, index) in alarmList" :key="index" 
                    :title="item.projectName+'：'+item.cause+' '+ item.time+(item.status==0?'未恢复':'已恢复')"
                  >
                    <span :class="item.status===0?'not-recovered':'restored'">{{ item.status === 0 ? '未恢复' : '已恢复' }}</span>
                    <span>{{ item.projectName + '：' }}</span>
                    <span>{{ item.cause }}</span>
                    <span style="margin-left:10px">{{ item.time }}</span>
                    
                  </li>
                </ul>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </div>
    <div class="right">
      <div class="header">{{ curDate }}</div>
      <div class="project-category">
        <!-- <p class="title">系统运行状况</p> -->
        <ul>
          <li class="bg-shadow" v-corner>
            <div class="category">
              <span v-for="(item,idx) in '商用项目'" :key="idx">{{item}}</span>
            </div>
            <div class="data-wrap">
              <span class="label">昨日每平方用电费用</span>
              <span class="data">
                <strong v-for="(item,idx) in '0.29'" :key="idx">{{item}}</strong>
                <i>元</i>
              </span>
            </div>
            <div class="data-wrap">
              <span class="label">电费单价</span>
              <span class="data">
                <strong v-for="(item,idx) in '1.1'" :key="idx">{{item}}</strong>
                <i>元/kw/h</i>
              </span>
            </div>
          </li>
          <li class="bg-shadow" v-corner>
            <div class="category">
              <span v-for="(item,idx) in '住宅项目'" :key="idx">{{item}}</span>
            </div>
            <div class="data-wrap">
              <span class="label">昨日每平方用电费用</span> 
              <span class="data">
                <strong v-for="(item,idx) in '0.17'" :key="idx">{{item}}</strong>
                <i>元</i>
              </span>
            </div>
            <div class="data-wrap">
              <span class="label">电费单价</span>
              <span class="data">
                <strong v-for="(item,idx) in '0.58'" :key="idx">{{item}}</strong>
                <i>元/kw/h</i>
              </span>
            </div>
          </li>
        </ul>
        <p></p>
        <p>   </p>
      </div>
      <div class="air-index bg-shadow" v-corner>
        <p class="title">在线项目室内环境空气指标概况</p>
        <ul>
          <li id="temperature"></li>
          <li id="humidity"></li>
          <li id="PM"></li>
          <li id="CO2"></li>
          <li id="TVOC"></li>
          <li class="air-quality">
            <p class="title">室外空气质量 <span style="float:right;padding-right:10px">{{airQualityData.city}}</span></p>
            <div class="bubble">
              <p class="tem data-wrap">
                <span class="label">温度</span>
                <span class="data">{{airQualityData.temp==null ? '--':airQualityData.temp}} <i class="unit">℃</i></span>
              </p>
              <p class="hum data-wrap">
                <span class="label">湿度</span>
                <span class="data">{{airQualityData.humidity==null?'--':airQualityData.humidity}} <i class="unit">%RH</i></span>
              </p>
              <p class="pm data-wrap">
                <span class="label">PM2.5</span>
                <span class="data">{{airQualityData.pm2_5==null?'--':airQualityData.pm2_5}} <i class="unit">μg/m3</i></span>
              </p>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import './assets/js/bubble'

import chinaJson from "@/assets/json/china.json";
import { 
  getNationalProjectView,
  getNationalDataView,
  getProvinceProjectRoomData,
  getProvinceProjectView,
  getPowerConsumptionData 
} from '@/api/screen'
import dayjs from 'dayjs'

const mapData = [
  {
    name: "安徽省",
    json: "ah",
    center: [117.226862, 31.849273],
  },
  {
    name: "澳门特别行政区",
    json: "am",
    center: [113.566988, 22.159307],
  },
  {
    name: "北京市",
    json: "bj",
    center: [116.41995, 40.189943],
  },
  {
    name: "重庆市",
    json: "cq",
    center: [107.883899, 30.067297],
  },
  {
    name: "福建省",
    json: "fj",
    center: [118.006365, 26.069889],
  },
  {
    name: "广东省",
    json: "gd",
    center: [113.429915, 23.334652],
  },
  {
    name: "甘肃省",
    json: "gs",
    center: [103.823557, 36.058039],
  },
  {
    name: "广西省",
    json: "gx",
    center: [108.7944, 23.833381],
  },
  {
    name: "贵州省",
    json: "gz",
    center: [106.880457, 26.826368],
  },
  {
    name: "海南省",
    json: "hainan",
    center: [109.754859, 19.189767],
  },
  {
    name: "河北省",
    json: "hb",
    center: [116.649983, 39.553253],
  },
  {
    name: "黑龙江省",
    json: "hlj",
    center: [127.693016, 48.04047],
  },
  {
    name: "河南省",
    json: "hn",
    center: [113.619748, 33.902617],
  },
  {
    name: "湖北省",
    json: "hubei",
    center: [112.271286, 30.987521],
  },
  {
    name: "湖南省",
    json: "hunan",
    center: [111.711649, 27.629221],
  },
  {
    name: "吉林省",
    json: "jl",
    center: [126.171249, 43.70394],
  },
  {
    name: "江苏省",
    json: "js",
    center: [119.486395, 32.983908],
  },
  {
    name: "江西省",
    json: "jx",
    center: [115.732975, 27.636112],
  },
  {
    name: "辽宁省",
    json: "ln",
    center: [122.605251, 41.299975],
  },
  {
    name: "内蒙古自治区",
    json: "nmg",
    center: [114.077404, 44.331072],
  },
  {
    name: "宁夏回族自治区",
    json: "nx",
    center: [106.169867, 37.291331],
  },
  {
    name: "青海省",
    json: "qh",
    center: [96.043533, 35.726403],
  },
  {
    name: "四川省",
    json: "sc",
    center: [102.693453, 30.674545],
  },
  {
    name: "山东省",
    json: "sd",
    center: [118.187667, 36.376018],
  },
  {
    name: "上海市",
    json: "sh",
    center: [121.438734, 31.07256],
  },
  {
    name: "陕西省",
    json: "shanxi",
    center: [108.887304, 35.263625],
  },
  {
    name: "山西省",
    json: "sx",
    center: [112.304761, 37.618555],
  },
  {
    name: "天津市",
    json: "tj",
    center: [117.347019, 39.28803],
  },
  {
    name: "台湾省",
    json: "tw",
    center: [120.971485, 23.749452],
  },
  {
    name: "香港特别行政区",
    json: "xg",
    center: [114.134391, 22.37737],
  },
  {
    name: "新疆维吾尔自治区",
    json: "xj",
    center: [85.294711, 41.371801],
  },
  {
    name: "西藏自治区",
    json: "xz",
    center: [88.388277, 31.56375],
  },
  {
    name: "云南省",
    json: "yn",
    center: [101.485106, 25.008644],
  },
  {
    name: "浙江省",
    json: "zj",
    center: [120.109921, 29.181449],
  },
];

export default {
  name: "DataScreen",
  directives: {
    corner(el, binding, vnode) {
      const corner = Array.from(el.getElementsByClassName('corner-span'))
      if(corner.length) return ; //时间定时器导致指令一直执行
      el.style.position = binding.value || 'relative'
      const spanWidth = 10;
      const lineList = [
        {
          top: 0,
          left: 0,
          rotate: 0,
        },
        {
          top: spanWidth / 2,
          left: -spanWidth / 2,
          rotate: 90,
        },
        {
          top: 0,
          right: 0,
          rotate: 0,
        },
        {
          top: spanWidth / 2,
          right: -spanWidth / 2,
          rotate: 90,
        },
        {
          bottom: 0,
          left: 0,
          rotate: 0,
        },
        {
          bottom: spanWidth / 2,
          left: -spanWidth / 2,
          rotate: -90,
        },
        {
          bottom: 0,
          right: 0,
          rotate: 0,
        },
        {
          bottom: spanWidth / 2,
          right: -spanWidth / 2,
          rotate: 90,
        },
      ];
      lineList.forEach((item) => {
        const span = document.createElement("span");
        span.className = 'corner-span'
        span.style.position = "absolute";
        span.style.left = item.left + "px";
        span.style.right = item.right + "px";
        span.style.top = item.top + "px";
        span.style.bottom = item.bottom + "px";
        span.style.transform = `rotate(${item.rotate}deg)`;
        span.style.width = `${spanWidth}px`;
        span.style.height = "1px";
        span.style.background = "cyan";
        el.append(span);
      });
    },
  },
  computed:{
    allAreaList() {
      return this.areaInfo.allArea && (this.areaInfo.allArea+'').split('')
    },
    onlineAreaList() {
      return this.areaInfo.onlineArea && (this.areaInfo.onlineArea+'').split('')
    },
    alarmCountList() {
      return this.areaInfo.alarmCount && (this.areaInfo.alarmCount+'').split('')
    },
  },
  data() {
    return {
      curDate: dayjs(new Date()).format("YYYY年MM月DD日 hh:mm:ss"),
      fullscreenLoading:true,
      mapChart: null,
      domImg: null,
      domHoverImg: null,

      pressureChart: null, //管道压力
      indoorComfortChart: null, //室内舒适度

      villaChart: null, //别墅图
      apartmentChart: null, //公寓图
      temperatureChart: null,
      humidityChart: null,
      PMChart: null,
      CO2Chart: null,
      TVOCChart: null,

      mapData: mapData,
      colorList: [
        "rgba(147, 235, 248, 1)",
        "rgba(241, 109, 115, 0.8)",
        "rgba(255, 235, 59, 0.7)",
      ],
      chooseName: "全国", //选择省名称

      areaInfo:{},//左侧面积等信息
      alarmList:[],//底部报警信息
      systemPressureList:{},//底部系统压力监测
      roomComfortList:{},//底部室内舒适度统计
      airQualityData:{},//室外空气质量
      
      projectCountInfo:{},//右侧顶部项目数量
      projectList:[],//项目数量

      resizeTimer:null,//窗口大小定时器
      dateTimer:null,//日期定时器
    };
  },
  created() {
    this.getCurDate();
    this.getNationalProjectView()
    this.getNationalDataView()
    this.getProvinceProjectRoomData()
    this.getProvinceProjectView({jsonData:chinaJson})
    this.getPowerConsumptionData()
  },
  mounted() {
    this.domImg = document.getElementById("domImg");
    this.chartInit();
    const chartArr = [
      "mapChart",
      "pressureChart",
      "indoorComfortChart",
      "villaChart",
      "apartmentChart",
      "temperatureChart",
      "humidityChart",
      "PMChart",
      "CO2Chart",
      "TVOCChart",
    ];
    window.onresize = () => {
      clearTimeout(this.resizeTimer);
      this.resizeTimer = null
      this.resizeTimer = setTimeout(() => {
        chartArr.forEach((item) => {
          this[item].resize();
        });
      }, 500);
    };
    window.onload = () => {
      setInterval(() => {
        location.reload()
      }, 60000 * 8);
      /*3D标签云*/
      tagcloud({
        selector: ".bubble",  //元素选择器
        fontsize: 6,       //基本字体大小, 单位px
        radius: 40,         //滚动半径, 单位px 页面宽度和高度的五分之一
        mspeed: "slow",   //滚动最大速度, 取值: slow, normal(默认), fast
        ispeed: "slow",   //滚动初速度, 取值: slow, normal(默认), fast
        direction: 10,     //初始滚动方向, 取值角度(顺时针360): 0对应top, 90对应left, 135对应right-bottom(默认)...
        keep: false          //鼠标移出组件后是否继续随鼠标滚动, 取值: false, true(默认) 对应 减速至初速度滚动, 随鼠标滚动
      });
    }
  },
  destroyed() {
    clearInterval(this.dateTimer)
    this.dateTimer = null

    clearTimeout(this.resizeTimer);
    this.resizeTimer = null
  },
  methods: {
    getCurDate() {
      this.dateTimer = setInterval(() => {
        const curDate = new Date();
        this.curDate = dayjs(curDate).format(
          "YYYY年MM月DD日 HH:mm:ss"
        );
      }, 1000);
    },
    chartInit() {
      const chartArr = [
        {
          typeName: "setDotOption",
          id: "pressure-monitor",
          name: "pressureChart",
        },
        {
          typeName: "setDotOption",
          id: "indoor-comfort",
          name: "indoorComfortChart",
        },
        {
          typeName: "setLineOption",
          id: "villa",
          name: "villaChart",
        },
        {
          typeName: "setLineOption",
          id: "apartment",
          name: "apartmentChart",
        },
        {
          typeName: "setPieOption",
          id: "temperature",
          name: "temperatureChart",
          config: {
            type: "temHum",
          },
        },
        {
          typeName: "setPieOption",
          id: "humidity",
          name: "humidityChart",
          config: {
            type: "temHum",
          },
        },
        {
          typeName: "setPieOption",
          id: "PM",
          name: "PMChart",
          config: {
            type: "env",
          },
        },
        {
          typeName: "setPieOption",
          id: "CO2",
          name: "CO2Chart",
          config: {
            type: "env",
          },
        },
        {
          typeName: "setPieOption",
          id: "TVOC",
          name: "TVOCChart",
          config: {
            type: "env",
          },
        },
      ];

      this.mapChart = this.$echarts.init(document.getElementById("map"));
      this.mapChart.on("click", (e) => {
        this.mapClick(e);
      });
      this.setMapOption({ data: chinaJson });

      chartArr.forEach((item) => {
        this[item.name] = this.$echarts.init(document.getElementById(item.id));
        this[item.typeName](this[item.name], item.config);
      });
    },
    //环形图配置
    setPieOption(chart, config) {
      const CIRCLE = {
        type: "pie",
        center: ["30%", "50%"],
        startAngle: 90,
        selectedMode: "single",
        selectedOffset: 0,
        data: config.data || [],
        label: {
          show: false,
        },
        labelLine: {
          show: false,
        },
        emphasis: {
          scale: false,
        },
      };
      //颜色16进制换算rgba,添加透明度
      function hexToRgba(hex, opacity) {
        return (
          "rgba(" +
          parseInt("0x" + hex.slice(1, 3)) +
          "," +
          parseInt("0x" + hex.slice(3, 5)) +
          "," +
          parseInt("0x" + hex.slice(5, 7)) +
          "," +
          opacity +
          ")"
        );
      }
      const color = ['#503EFF', '#3E82FF', '#8BF39A', '#ffed5b',"#ff9e5b"];
      let color1 = [],
        color2 = [],
        color3 = [];
      // 设置每层圆环颜色和添加间隔的透明色
      color.forEach((item) => {
        color1.push(item);
        color2.push(hexToRgba(item, 0.7));
        color3.push(hexToRgba(item, 0.4));
      });
      const temHumOption = {
        series: [
          {
            radius: ["30%", "38%"],
            itemStyle: {
              color: (params) => {
                return color3[params.dataIndex];
              },
            },
            ...CIRCLE,
          },
          {
            radius: ["38%", "46%"],
            itemStyle: {
              color: (params) => {
                return color2[params.dataIndex];
              },
            },
            ...CIRCLE,
          },
          {
            radius: ["46%", "54%"],
            itemStyle: {
              color: (params) => {
                return color1[params.dataIndex];
              },
            },
            ...CIRCLE,
          },
        ],
      };
      const envOption = {
        series: [
          {
            type: "pie",
            roseType: 'radius',
            radius: ["40%", "50%"],
            center: ["30%", "50%"],
            avoidLabelOverlap: false,
            label: {
              show: false,
              position: "center",
            },
            labelLine: {
              show: false,
            },
            data: config.data || [],
          },
        ],
      };
      chart.setOption({
        title: {
          text: config.name || '--',
          right: 10,
          top: 10,
          textStyle: {
            color: "cyan",
            fontSize: 12,
          },
        },
        tooltip: {
          trigger:'item',
          backgroundColor:'rgba(7,45,106,0.8)',
          borderWidth:0,
          textStyle:{
            fontSize:12,
            color:"#5dbbfa",
          },
          formatter(params) {
            const {data} = params
            let str = `
              <span style="font-size:12px">${params.marker}${data.name} ${data.value}</span>
            `
            return str
          }
        },
        legend: {
          orient: "vertical",
          right: "2%",
          bottom: "2%",
          itemGap: 4,
          itemWidth: 8,
          itemHeight: 8,
          textStyle: {
            color: "#fff",
            fontSize: 12,
          },
          z:10
        },
        color: ['#0097FB', '#30ECA6',"#FDFA4E",'#FFC227',"#FF4848","#92E1FF"],
        series: config.type == "env" ? envOption.series : temHumOption.series,
      });
    },
    //折线图配置
    setLineOption(chart, { title,xData,yData }={}) {
      chart.setOption({
        title: {
          text: title || '--',
          // textVerticalAlign:'middle',
          top: 5,
          right: "8%",
          textStyle: {
            fontSize: 11,
            color: "#1a88b8",
          },
        },
        tooltip: {
          trigger: "axis",
          backgroundColor:'rgba(7,45,106,0.8)',
          borderWidth:0,
          textStyle:{
            fontSize:12,
            color:"#5dbbfa",
          },
          formatter:function(params) {
            let str = `<span style="color:#fff">${params[0].axisValue}</span></br>`
            for(let i = 0;i < params.length; i ++) {
              str += `${params[i].marker}${(params[i].data === null ? '--' : params[i].data)}kwh</br>`
            }
            return str;
          }
        },
        xAxis: {
          type: "category",
          data: ['1月','2月','3月','4月','5月','6月','7月','8月','9月','10月','11月','12月'],//xData || [],
          axisLabel: {
            color: "#1a88b8",
            fontSize: 10,
          },
          axisLine: {
            lineStyle: {
              color: "#105893",
            },
          },
          axisTick: {
            show: true,
            inside: true,
          },
        },
        yAxis: {
          type: "value",
          name: "kwh",
          nameLocation: "end",
          nameTextStyle: {
            fontSize: 10,
            verticalAlign: "middle",
            color: "#1b77ac",
          },
          axisLabel: {
            color: "#1a88b8",
            fontSize: 10,
          },
          axisLine: {
            show: true,
            lineStyle: {
              color: "#105893",
            },
          },
          axisTick: {
            show: true,
            inside: true,
          },
          splitLine: {
            lineStyle: {
              color: "#105893",
            },
          },
        },
        grid: {
          left: "17%",
          top: "15%",
          bottom: "15%",
          right: "8%",
        },
        series: [
          {
            data: yData || [],
            type: "line",
            lineStyle: {
              width: 0,
            },
            showSymbol: true,
            // symbol:'circle',
            itemStyle: {
              color: "#1cc6e6",
              borderColor: "#1cc6e6",
            },
            areaStyle: {
              opacity: 0.8,
              color: new this.$echarts.graphic.LinearGradient(0, 0, 0, 1, [
                {
                  offset: 0,
                  color: "#1cc6e6",
                },
                {
                  offset: 1,
                  color: "#24639f",
                },
              ]),
            },
            smooth: true,
          },
        ],
      });
    },
    //散点图配置
    setDotOption(chart,{data,type}={}) {
      const markLineOpt = {
        animation: false,
        lineStyle: {
          type: "solid",
          color: "#1c75aa",
        },
        tooltip: {
          // formatter: "test",
          show:false
        },
        data: [
          [
            {
              coord: ['1月', 0.3],
              symbol: "none",
            },
            {
              coord: ['12月', 0.3],
              symbol: "none",
            },
          ],
        ]
      };
      const pressureSeries = [
        {
          name: "正常",
          type: "scatter",
          xAxisIndex: 0,
          yAxisIndex: 0,
          itemStyle: {
            color:'rgba(92, 164, 199,0.6)',
            shadowBlur:10,
            borderWidth:2,
            borderColor: 'rgb(92, 164, 199)',
            shadowColor: 'rgb(92, 164, 199)',
          },
          data: data ? data.normal : [],
          markLine: markLineOpt,
        },
        {
          name: "压力过高",
          type: "scatter",
          xAxisIndex: 0,
          yAxisIndex: 0,
          itemStyle: {
            color:'rgba(190, 93, 104,0.7)',
            shadowBlur:10,
            borderWidth:2,
            borderColor: 'rgb(190, 93, 104)',
            shadowColor: 'rgb(190, 93, 104)',
          },
          data: data ? data.high : [],
          markLine: markLineOpt,
        },
        {
          name: "压力过低",
          type: "scatter",
          xAxisIndex: 0,
          yAxisIndex: 0,
          itemStyle: {
            color:'rgba(200, 169, 90,0.6)',
            shadowBlur:10,
            borderWidth:2,
            borderColor: 'rgb(200, 169, 90)',
            shadowColor: 'rgb(200, 169, 90)',
          },
          data: data ? data.low : [],
          markLine: markLineOpt,
        },
      ]
      const comfortSeries = [
        {
          name: "PMV(预计平均热感觉指标)",
          type: "scatter",
          xAxisIndex: 0,
          yAxisIndex: 0,
          itemStyle: {
            color:'rgba(15,179,243,0.7)',
            shadowBlur:10,
            borderWidth:2,
            borderColor: '#0FC5F3',
            shadowColor: '#0FC5F3',
          },
          data: data ? data.pmv : [],
        },
        {
          name: "PPD(预计不满意者百分数)",
          type: "scatter",
          xAxisIndex: 0,
          yAxisIndex: 0,
          itemStyle: {
            color:'rgba(23,216,161,0.7)',
            shadowBlur:10,
            borderWidth:2,
            borderColor: '#17D8A1',
            shadowColor: '#17D8A1',
          },
          data: data ? data.ppd : [],
        },
      ] 
      chart.setOption({
        title: {
          text: '',
          top: 5,
          right: "8%",
          textStyle: {
            fontSize: 11,
            color: "#1a88b8",
          },
        },
        legend:{
          textStyle:{
            color:'#fff',
            fontSize:11
          },
          itemWidth:8,
          itemHeight:8,
          // left:60,
          top:5,
        },
        grid: [{ left: "10%", top: "15%", bottom: "15%",right:'8%' }],
        tooltip: {
          trigger:'item',
          backgroundColor:'rgba(7,45,106,0.8)',
          borderWidth:0,
          textStyle:{
            fontSize:12,
            color:"#5dbbfa",
          },
          formatter({marker,seriesName,data}) {
            if(type == 'pressure'){
              return `<span>${marker}${data[0]} ${data[1]}Mpa</span>`
            }else {
              return `<span>${marker}${data[0]} ${data[1]}</span>`
            }
          }
        },
        xAxis: {
          // name:'月',
          // nameTextStyle: {
          //   fontSize: 10,
          //   verticalAlign: "middle",
          //   color: "#1b77ac",
          // },
          data:['1月','2月','3月','4月','5月','6月','7月','8月','9月','10月','11月','12月'],
          axisLabel: {
            color: "#1a88b8",
            fontSize: 10,
            align:'center'
          },
          axisLine: {
            lineStyle: {
              color: "#105893",
            },
          },
          axisTick: {
            show: true,
            inside: true,
          },
          splitLine: {
            show: false,
          },
          gridIndex: 0,
          min:0,
        },
        yAxis: {
          name: type=='pressure'?"管道压力/Mpa":"",
          nameLocation: "end",
          nameTextStyle: {
            fontSize: 10,
            verticalAlign: "middle",
            color: "#1b77ac",
          },
          axisLabel: {
            color: "#1a88b8",
            fontSize: 10,
          },
          axisTick: {
            show: true,
            inside: true,
          },
          axisLine: {
            show:true,
            lineStyle: {
              color: "#105893",
            },
          },
          splitLine: {
            show: false,
          },
        },
        series: type == 'pressure' ? pressureSeries : type == 'comfort' ? comfortSeries:[],
      });
    },
    setMapOption({ data, center }) {
      const that = this;
      this.$echarts.registerMap("map", data);
      this.mapChart.setOption({
        tooltip:{
          trigger:'item',
          backgroundColor:'rgba(7,45,106,0.8)',
          borderWidth:0,
          textStyle:{
            fontSize:12,
            color:"#5dbbfa",
          },
          formatter(params) {
            const {data} = params
            let str = `
              <span>${data.projectName}</span><br/>
              <span style="font-size:12px">${params.marker}${data.province}${data.city}${data.area}</span>
            `
            return str
          }
        },
        geo: {
          show: true,
          map: "map",
          silent: false, //不响应鼠标事件
          roam: true, //是否开启缩放
          top: that.chooseName == "全国" ? 150 : 40,
          center: center,
          zoom: that.chooseName == "全国" ? 1.5 : 1,
          label: {
            show: true,
            color: "#fff",
            center: "center",
          },
          itemStyle: {
            borderColor: "rgba(147, 235, 248, 1)",
            borderWidth: 1,
            areaColor: {
              type: "radial",
              x: 0.5,
              y: 0.5,
              r: 0.8,
              colorStops: [
                {
                  offset: 0,
                  color: "rgba(147, 235, 248, 0)", // 0% 处的颜色
                },
                {
                  offset: 1,
                  color: "rgba(147, 235, 248, .2)", // 100% 处的颜色
                },
              ],
              globalCoord: false, // 缺省为 false
            },
            shadowColor: "rgba(128, 217, 248, 1)",
            shadowOffsetX: -2,
            shadowOffsetY: 2,
            shadowBlur: 10,
          },
          emphasis: {
            focus: "self",
            itemStyle: {
              areaColor: "#389BB7",
              borderWidth: 0,
            },
            label: {
              show: true,
              color: "#fff",
            },
          },
          regions: [
            {
              name: "广东省",
            },
          ],
        },
        series: [
          {
            type: "effectScatter",
            coordinateSystem: "geo",
            zlevel:10,//分层渲染
            rippleEffect: {
              scale: 4,
              period: 15,
              brushType: "fill",
            },
            showEffectOn: "render",
            itemStyle: {
              shadowBlur: 10,
              shadowColor: "#333",
              color: function ({data}) { //1 掉线  2报警 0在线
                const idx = data.isOnline ? data.isAlarm ? 2 : 0 : 1
                return that.colorList[idx];
              },
            },
            label: {
              color: "#fff",
            },
            emphasis: {
              scale: true,
            },
            data: this.projectList,
          },
        ],
      });
    },
    //切换省份数据
    toggleProvince(chooseProvince) {
      if (chooseProvince) {
        this.chooseName = chooseProvince.name;
        import(`@/assets/json/${chooseProvince.json}.json`).then((res) => {
          this.setMapOption({
            data: res,
            center: chooseProvince.center,
          });
          this.getProvinceProjectView({jsonData:res,center:chooseProvince.center})
        });
        this.getProvinceProjectRoomData()
      }
    },
    //点击省份
    mapClick(ev) {
      let [chooseProvince] = mapData.filter((item) => item.name == ev.name);
      this.toggleProvince(chooseProvince);
    },
    goBack() {
      this.chooseName = "全国";
      this.setMapOption({ data: chinaJson });
      this.getProvinceProjectRoomData()
      this.getProvinceProjectView({jsonData:chinaJson})
    },
    //选择省份
    provinceChange(name) {
      if (name == "全国") {
        this.setMapOption({
          data: chinaJson,
        });
        this.getProvinceProjectRoomData()
        this.getProvinceProjectView({jsonData:chinaJson})
        return;
      }
      let [chooseProvince] = mapData.filter((item) => item.name == name);
      this.toggleProvince(chooseProvince);
    },
    //获取面积等信息
    getNationalProjectView() {
      getNationalProjectView().then(({data}) => {
        this.areaInfo = data
      }).catch(err => err)
    },
    //底部报警等信息
    getNationalDataView() {
      getNationalDataView().then(({data}) => {
        this.alarmList = data.alarmMessageList || {}

        const systemPressureList = data.systemPressureList || {}
        let systemPressureData =[]
        let pressureData = {
          high:[],
          normal:[],
          low:[]
        }
        for(let prop in systemPressureList) {
          systemPressureData.push([prop+'月',systemPressureList[prop].value,systemPressureList[prop].norm])
        }
        for(let prop in pressureData) {
          pressureData[prop] = systemPressureData.filter(item => {
            return item.includes(prop)
          })
        }
        this.setDotOption(this['pressureChart'],{data:pressureData,type:'pressure'})

        let result = {
          pmv:[],
          ppd:[]
        }
        const {pmv,ppd} = data.roomComfortList[0] || {}
        for(let prop in pmv) {
          result.pmv.push([prop+'月',pmv[prop]])
        }
        for(let prop in ppd) {
          result.ppd.push([prop+'月',ppd[prop]])
        }
        this.setDotOption(this['indoorComfortChart'],{data:result,type:'comfort'})
        
        
        // console.log(data.roomComfortList)
      }).catch(err => err)
    },
    //环境数据等信息
    getProvinceProjectRoomData(){
      const province = this.chooseName == '全国' ? null : this.chooseName
      getProvinceProjectRoomData({province}).then(({data}) => {
        this.airQualityData = data.outdoor

        const render = ({data,nameMapping,chart,type,name}) => {
          let result = []
          for(let prop in nameMapping) {
            result.push({
              value:data[prop],
              name:nameMapping[prop]
            })
          }
          this.setPieOption(chart,{type,data:result,name})
        }
        
        const renderList = [
          {
            chart:this.temperatureChart,
            data:data.wdData,
            nameMapping:{
              chill:'≤16 ℃     寒冷',
              cold:'16-20 ℃     冷',
              comfort:'20-26 ℃  舒适',
              hot:'26-30 ℃     热',
              torridity:'≥30 ℃     炎热',
            },
            type:'temHum',
            name:'温度 ℃'
          },
          {
            chart:this.humidityChart,
            data:data.sdData,
            nameMapping:{
              dry:'≤30 %RH      干燥',
              microDry:'30-40 %RH   微干',
              suitable:'40-65 %RH   舒适',
              slightlyDamp:'65-75 %RH   微湿',
              wet:'≥75 %RH      潮湿',
            },
            type:'temHum',
            name:'湿度 %RH'
          },
          {
            chart:this.PMChart,
            data:data.pm2_5Data,
            nameMapping:{
              actor:'≤50 μg/m³        优',
              fine:'50-100 μg/m³   良',
              poor:'100-200 μg/m³ 差',
              range:'≥200 μg/m³   极差',
            },
            type:'env',
            name:'PM2.5 μg/m³'
          },
          {
            chart:this.CO2Chart,
            data:data.co2Data,
            nameMapping:{
              coActor:'≤450 ppm          优',
              coFine:'450-1000 ppm   良',
              coPoor:'1000-2000 ppm 差',
              coRange:'≥2000 ppm     极差',
            },
            type:'env',
            name:'CO2 ppm'
          },
          {
            chart:this.TVOCChart,
            data:data.TVOCData,
            nameMapping:{
              tvActor:'≤0.3 mg/m3       优',
              tvFine:'0.3-1.0 mg/m3   良',
              tvPoor:'1.0-3.0 mg/m3   差',
              tvRange:'≥3.0 mg/m3    极差',
            },
            type:'env',
            name:'TVOC mg/m3'
          },

        ]

        renderList.forEach(config => {
          render(config)
        })

      }).catch(err => err)
    },
    //获取项目
    getProvinceProjectView({jsonData,center}) {
      const province = this.chooseName == '全国' ? null : this.chooseName
      getProvinceProjectView({province}).then(({data}) => {
        this.projectCountInfo = {
          allProjectCount:data.allProjectCount,
          allOnlineProjectCount:data.allOnlineProjectCount,
          allOffLineProjectCount:data.allOffLineProjectCount
        }

        let projectList = data.projectMessage
        
        this.projectList = projectList.map(item => {
          item.value = [item.longitude,item.latitude]
          return item
        })
        this.setMapOption({
          data:jsonData,
          center,
        });

      }).catch(err => err)
    },
    //耗电量统计
    getPowerConsumptionData() {
      const province = this.chooseName == '全国' ? null : this.chooseName
      getPowerConsumptionData(province).then(({data}) => {
        data.forEach(projectType => {
          let xData=[],yData=[],title = projectType.projectName,chart
          chart = title=='住宅项目'?this.apartmentChart:this.villaChart
          for(let prop in projectType.powerConsumption) {
            xData.push(prop+'月')
            yData.push(projectType.powerConsumption[prop])
          }
          this.setLineOption(chart,{xData,yData,title})
        })
        
      }).catch(err => err)
    }
  },
};
</script>
<style>
body,
html {
  width: 100%;
  height: 100%;
  padding: 0;
  margin: 0;
}
body {
  background: -webkit-radial-gradient(
    50% 35%,
    farthest-corner,
    #034f8e,
    #034987,
    #02366d,
    #002353
  );
  color: #fff;
  font-size: 16px;
}
p,
ul,
li {
  margin: 0;
  padding: 0;
  list-style: none;
}
.select-province input {
  background: none;
  border: none;
  /* border-bottom: 1px solid cyan; */
  border-radius: 0;
  width: 190px;
  color: cyan;
  text-align: right;
  font-size: 18px;
}
.select-province .el-input .el-select__caret {
  color: cyan;
}
.select-province-popper {
  border-radius: 0;
  margin: 0 !important;
  background-color: rgba(1, 57, 117, 0.9);
  border:rgba(1, 57, 117, 0.9);
}
.select-province-popper li {
  color:#fff;
}
.select-province-popper li.el-select-dropdown__item.selected {
  background-color: #003668;
}
.select-province-popper li.el-select-dropdown__item.hover {
  background-color: #003668;
}
.select-province-popper .popper__arrow {
  display: none;
}
.select-province-popper .el-scrollbar::-webkit-scrollbar {
  width: 0;
}
</style>
<style lang='scss' scoped>
$border: 1px solid rgba(7, 118, 181, 0.5);
$boxShadow: inset 0 0 20px rgba(7, 118, 181, 0.4);
.title {
  line-height: 32px;
  text-align: center;
  font-size: 14px;
  color: cyan;
  border-bottom: $border;
}
//添加阴影
.bg-shadow::before {
  width: 100%;
  height: 1px;
  content: "";
  position: absolute;
  left: 0;
  bottom: -1px;
  background: linear-gradient(to right, #076ead, #4ba6e0, #076ead);
  box-shadow: 0 0 5px rgb(131, 189, 227);
  opacity: 0.6;
}
.border {
  position:absolute;
  left:20px;
  right:20px;
  top:8px;
  height: 43px;
  border:$border;
  box-shadow: $boxShadow;
}
.screen-container {
  width: 100%;
  height: 100%;
  min-width: 1200px;
  min-height: 860px;
  display: flex;
  box-sizing: border-box;
  padding: 10px 20px;
  user-select: none;
  position: relative;
  .left {
    width: 70%;
    box-sizing: border-box;
    margin-right: 10px;
    position: relative;
    .header {
      font-size: 24px;
      letter-spacing: 10px;
      height: 42px;
      line-height: 42px;
      font-weight: 600;
      color: #daf9ff;
      display: inline-block;
      position: absolute;
      left:52.5%;
      // transform: translateX(-50%);
    }
    .logo {
      display: inline-block;
      width:120px;
      height: 38px;
      margin-bottom: 10px;
      padding-left: 10px;
      img {
        width: 100%;
        height: 100%;
      }
    }
  }
  .right {
    // width: 30%;
    width: calc(30% - 10px);
    box-sizing: border-box;
    .header {
      font-size: 20px;
      height: 42px;
      line-height: 42px;
      font-weight: 600;
      color: #daf9ff;
      text-align: right;
      margin-bottom: 6px;
      padding-right: 10px;
    }
  }
}
.left-content {
  display: flex;
  flex-direction: column;
  height: calc(100% - 42px);
  .left-content-top {
    width: 100%;
    height: 70%;
    display: flex;
    margin-bottom: 4px;
  }
  .left-content-bottom {
    width: 100%;
    height: 30%;
    box-sizing: border-box;
    border: $border;
    box-shadow: $boxShadow;
  }
}

.left-content-top .data-wrap {
  min-width: 300px;
  max-width: 300px;
  margin-right: 4px;
  border: $border;
  box-shadow: $boxShadow;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  ul {
    li {
      width: 280px;
      height: 86px;
      display: flex;
      flex-direction: column;
      justify-content: space-around;
      box-sizing: border-box;
      padding: 10px 20px;
      background-color: #174d85;
      margin-top: 10px;
      margin-left: 10px;
      .label {
        font-size: 12px;
      }
      .data {
        font-size: 22px;
        color: #76ebeb;
        letter-spacing: 4px;
        strong {
          display: inline-block;
          width: 17px;
          background-color:#013c72;
          margin-right: 4px;
          padding: 2px 0 2px 3px;
        }
      }
      span {
        text-align: center;
      }
      span i {
        font-style: normal;
        color: yellow;
        font-size: 12px;
      }
    }
  }
  .legend-wrap {
    font-size: 14px;
    box-sizing: border-box;
    padding: 10px;
    margin-bottom: 40px;
    .count {
      line-height: 42px;
      span {
        font-size: 16px;
        color: cyan;
      }
    }
    .legend {
      span {
        display: inline-block;
        line-height: 28px;
        font-size: 12px;
        margin-right: 20px;
        i {
          display: inline-block;
          width: 10px;
          height: 10px;
          border-radius: 50%;
          margin-right: 6px;
        }
      }
    }
  }
}
.left-content-top .map-wrap {
  flex: 1;
  border: $border;
  box-shadow: $boxShadow;
  position: relative;
  #map {
    // position: absolute;
    width: 100%;
    height: 100%;
    background-image: url("./assets/img/screen/map-bg.png");
    background-size: 100% 100%;
  }
  .back {
    position: absolute;
    left: 20px;
    top: 20px;
    font-size: 12px;
    cursor: pointer;
    z-index: 10;
    border: $border;
    padding: 2px 14px;
  }
  .select-area {
    position: absolute;
    z-index: 10;
    right: 10px;
    top: 10px;
  }
  .power-consumption {
    height: 240px;
    border: $border;
    box-shadow: $boxShadow;
    // margin:0 0 10px 10px;
    bottom:6px;
    left: 6px;
    width: 50%;
    position: absolute;
    ul {
      height: calc(100% - 32px);
      display: flex;
      li {
        width: 50%;
      }
    }
    &:hover {
      background-color: rgba(1, 57, 117,0.9);;
    }
  }
  .system-runStatus {
    position: absolute;
    top: 6px;
    left: 6px;
    ul {
      display: flex;
      justify-content: space-around;
      li {
        width: 110px;
        height: 80px;
        box-sizing: border-box;
        padding:10px 0 ;
        background: url('./assets/img/screen/data-bg.png') no-repeat;
        background-size: 100% auto;
        margin-right: 10px;
        span {
          display: block;
          line-height: 30px;
          text-align: center;
        }
        span.label {
          font-size: 12px;
        }
        span.data {
          font-weight: 600;
          font-size: 20px;
          color: #fffb57;
        }
      }
    }
  }
}
.left-content-bottom ul {
  width: 100%;
  height: 100%;
  display: flex;
  li {
    width:33.33333333%;
    height: 100%;
    div {
      height: calc(100% - 32px);
    }
  }
  // li.chart {
  //   width: 30%;
  // }
  // li.table {
  //   width:40%
  // }
}

@keyframes alarmAnim {
  0% {
    transform: translateY(0);
  }
  100% {
    transform: translateY(-100%);
  }
}
.left-content-bottom #alarm {
  height: calc(100% - 72px);
  margin-top: 20px;
  overflow: hidden;
  ul {
    flex-wrap: wrap;
    animation: alarmAnim 15s linear infinite;
    box-sizing: border-box;
    padding-right:10px;
  }
  li {
    width: 100%;
    height: 32px;
    line-height: 32px;
    display: flex;
    padding: 0 10px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    font-size: 14px;
    // display: flex;
    // justify-content: space-between;
    span {
      color: cyan;
    }
    span.not-recovered {
      color:#ff9e5b;
      margin-right:10px;
    }
    span.restored {
      color:#30eca6;
      margin-right:10px;
    }
  }
}

.right {
  .project-category {
    ul {
      li {
        min-width: 180px;
        width: 100%;
        height: 92px;
        box-sizing: border-box;
        padding:6px;
        background-color: #023d77;
        margin-bottom: 4px;
        display: flex;
        justify-content: space-around;
        border:$border;
        box-shadow: $boxShadow;
        .data-wrap{
          display: flex;
          font-size: 12px;
          line-height: 32px;
          flex-direction: column;
          justify-content: center;
          align-items: center;
          background-color: #174d85;
          width:45%;
        }
        .data-wrap {
          .data {
            color:cyan;
            font-size: 22px;
          }
          i {
            font-style: normal;
            color: yellow;
            font-size: 12px;
          }
        }
        strong {
          display: inline-block;
          width: 17px;
          background-color:#013c72;
          margin-right: 4px;
          padding: 2px 1px 2px 2px;
          text-align: center;
        }
        .category {
          background-color: #174d85;
          font-size: 12px;
          font-weight: 600;
          color:cyan;
          width: 10%;
          margin-right: 4px;
          text-align: center;
          padding-top:8px;
          span {
            display: block;
          }
        }
      }
    }
  }
  .air-index {
    height: calc(100% - 235px);
    border: $border;
    box-shadow: $boxShadow;
    ul {
      height: calc(100% - 34px);
      display: flex;
      flex-wrap: wrap;
      li {
        width: calc(50% - 9px);
        height: calc(33% - 6px);
        margin: 6px 0 0 6px;
        background-color: #174d85;
      }
      .air-quality {
        div.bubble {
          height: calc(100% - 34px);
          display: flex;
          overflow: hidden;
        }
        p.tem {
          width: 60px;
          height: 60px;
          border: 2px solid rgba(5,118,254,1);
          box-shadow: inset 0 0 20px rgba(5,118,254,1);
        }
        p.hum {
          width: 70px;
          height: 70px;
          border: 2px solid rgba(1,187,181,1);
          box-shadow: inset 0 0 20px rgba(1,187,181,1);
          
        }
        p.pm {
          width: 80px;
          height: 80px;
          border: 2px solid rgba(238,255,65,1);  
          box-shadow: inset 0 0 20px rgba(238,255,65,1);
        }
        .data-wrap {
          display: block;
          border-radius: 50%;
          color: #fff;
          font-weight: bold;
          font-size: 12px;
          display: flex;
          flex-direction: column;
          align-items: center;
          justify-content: center;
        }
      }
    }
  }
}
</style>