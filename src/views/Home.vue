<template>
    <div class="app-container">
       <img src="@/assets/head_bg2.png" >
        <div class="main">
            <div class="contents">
                <span id="update-time"><b>{{ stats.updateTime }}</b></span>
                <div class="cards">
                    <v-card id="confirm-cases" width="23vw" flat style="border-radius: 5px 0 0 5px">
                    <v-card-text>
                        <div class="mini">New: <inc>{{stats.confirmCases.Yesterday >0? '+' + stats.confirmCases.Yesterday : '-' + stats.confirmCases.Yesterday}}</inc></div>
                        <b id="current-confirm">
                            {{stats.confirmCases.Current}}<br>
                        </b>
                        <b>Confirmed</b>
                    </v-card-text>
                </v-card>
                    <v-card id="prob-cases" width="22vw" flat>
                        <v-card-text>
                            <div class="mini">New: <inc>{{stats.ProbableCases.Yesterday >=0? '+' + stats.ProbableCases.Yesterday : '-' + stats.ProbableCases.Yesterday}}</inc></div>
                                <b id="current-prob">
                                    {{stats.ProbableCases.Current}}<br>
                                </b>
                            <b>Probable</b>
                        </v-card-text>
                    </v-card>
                    <v-card id="total-recover" width="23vw" flat>
                        <v-card-text>
                            <div class="mini">New: <incr>{{stats.totalRecovered.Yesterday >=0? '+' + stats.totalRecovered.Yesterday : '-' + stats.totalRecovered.Yesterday}}</incr></div>
                            <b id="current-recover">
                                {{stats.totalRecovered.Current}}<br>
                            </b>
                            <b>Recovered</b>
                        </v-card-text>
                    </v-card>
                    <v-card id="total-death" width="22vw" flat style="border-radius: 0 5px 5px 0">
                        <v-card-text>
                            <div class="mini">New: <inc>{{stats.totalDeaths.Yesterday >=0? '+' + stats.totalDeaths.Yesterday : '-' + stats.totalDeaths.Yesterday}}</inc></div>
                            <b id="current-death">
                                {{stats.totalDeaths.Current}}<br>
                            </b>
                            <b>Deaths</b>
                        </v-card-text>
                    </v-card>
                </div>
                <div class="charts">
                    <div ref="newChart" style="width: 93vw; height: 38vh; min-height: 240px;"/>
                    <div ref="totalChart" style="width: 93vw; height: 40vh; min-height: 300px;"/>
                    <div id="bar" ref="byRegion" style="width: 100vw; height: 65vh; min-height: 550px;"/>
                    <span style="z-index: 1">
                    Data source:
                    <a href="https://www.health.govt.nz/our-work/diseases-and-conditions/covid-19-novel-coronavirus/covid-19-current-cases/covid-19-current-cases-details" >
                    Ministry of Health.
                    </a>
                    </span>
                </div>
                <div class="world-stats">
<!--                    <p><b>World Now:</b></p>-->
<!--                    <iframe src="https://www.youtube.com/embed/qgylp3Td1Bw"-->
<!--                            frameborder="0"-->
<!--                            allow="accelerometer;-->
<!--                            autoplay; encrypted-media;-->
<!--                            gyroscope; fullscreen;"-->
<!--                            ></iframe>-->
<!--                    <p><em>Credit to: Roylab Stats</em></p>-->

                    <a href="https://covid19.govt.nz/">
                        <v-card id="unite">
                            <img src="../assets/lockup.svg" alt="Unite logo">
                        </v-card>
                    </a>
                </div>
            </div>

            <div class="footer">
                <p>© <a href="http://www.mytimerec.com/">TIMEREC</a> LIMITED 2020</p>
            </div>
        </div>
    </div>
</template>

<script>
    import stats from '@/data/stats'

    // 引入ECharts基本模板
    const echarts = require('echarts/lib/echarts')
    // 引入折线图组件
    require('echarts/lib/chart/line')
    // 引入柱状图组件
    require('echarts/lib/chart/bar')
    // 引入提示框和标题组件
    require('echarts/lib/component/tooltip');
    require('echarts/lib/component/title');

    export default {
        name: "Home",
        component:{
            stats
        },
        data() {
            return {
                stats: stats,

                // 数据图表变量声明
                newChart:{},
                dailyNew: {
                    dateList: [],
                    valueList: []
                },
                totalChart: {},
                dailyTotal: {
                    dateList: [],
                    valueList: []
                },
                regionsChart:{},
                byRegion:{
                    nameList: [],
                    valueList: []
                },
                recover:{
                    dateList: [],
                    valueList: []
                },
            }
        },
        mounted() {
            this.initData()
            this.initNewChart()
            this.initTotalChart()
            this.initBarChart()
        },
        methods: {
            // 初始化数据
            initData(){
                this.dailyNew.dateList = Object.keys(this.stats.dailyNew).reverse()
                this.dailyNew.valueList = Object.values(this.stats.dailyNew).reverse()
                this.dailyTotal.dateList = Object.keys(this.stats.dailyTotal).reverse()
                this.dailyTotal.valueList = Object.values(this.stats.dailyTotal).reverse()
                this.byRegion.nameList = Object.keys(this.stats.byRegion).reverse()
                this.byRegion.valueList = Object.values(this.stats.byRegion).reverse()
                this.recover.dateList = Object.keys(this.stats.dailyRecovered).reverse()
                this.recover.valueList = Object.values(this.stats.dailyRecovered).reverse()
            },

            // 初始化新增趋势图表
            initNewChart(){
                this.newChart = echarts.init(this.$refs.newChart)
                this.newChart.setOption({
                    title: {
                        left: 'center',
                        text: 'Numbers of New Cases'
                    },
                    tooltip: {
                        trigger: 'axis'
                    },
                    legend: {
                        data: ['Confirmed', 'Recovered']
                    },
                    xAxis: {
                        type: 'category',
                        boundaryGap: false,
                        show: true,
                        data: this.dailyNew.dateList
                    },
                    yAxis: {
                        type: 'value',
                        splitLine: {show: true},
                    },
                    series:
                    [
                        {
                            name: 'Confirmed',
                            type: 'line',
                            data: this.dailyNew.valueList,
                        },
                        {
                            name: 'Recovered',
                            type: 'bar',
                            data: this.recover.valueList,
                        }
                    ],
                    color:
                        [
                        {
                        type: 'linear',
                        x: 0,
                        y: 0,
                        x2: 0,
                        y2: 1,
                        colorStops: [{
                            offset: 0, color: '#F08080' // 0% 处的颜色
                        }, {
                            offset: 1, color: '#FFA07A' // 100% 处的颜色
                        }],
                        global: false // 缺省为 false
                    }
                    , '#90EE90']
                })
            },

            // 初始化累计确诊图表
            initTotalChart(){
                this.totalChart = echarts.init(this.$refs.totalChart)
                this.totalChart.setOption({
                    title: {
                        left: 'center',
                        text: 'Total Number of Cases',
                        subtext: '(confirmed & probable )'
                    },
                    tooltip: {
                        trigger: 'axis'
                    },
                    xAxis: {
                        type: 'category',
                        boundaryGap: false,
                        show: true,
                        data: this.dailyTotal.dateList
                    },
                    yAxis: {
                        type: 'value',
                        splitLine: {show: true},
                    },
                    series:
                        {
                            name: 'total',
                            type: 'line',
                            data: this.dailyTotal.valueList,
                        },
                    color:
                        {
                            type: 'linear',
                            x: 0,
                            y: 0,
                            x2: 0,
                            y2: 1,
                            colorStops: [{
                                offset: 0, color: '#d6573a' // 上端的颜色
                            }, {
                                offset: 1, color: '#FFA07A' // 下端的颜色
                            }],
                            global: false // 缺省为 false
                        }
                })
            },

            // 初始化患者区域图表
            initBarChart(){
                this.regionsChart = echarts.init(this.$refs.byRegion)
                this.regionsChart.setOption({
                    title: {
                        left: 'center',
                        text: 'Total Cases by Region',
                        subtext: this.stats.regionUpdate
                    },
                    grid: {containLabel: true},
                    xAxis: {show: false},
                    yAxis: {
                        offset: 12,
                        axisLine:{show: false},
                        data: this.byRegion.nameList,
                        boundaryGap: false,
                    },
                    series: [
                        {
                            type: 'bar',
                            data: this.byRegion.valueList,
                            label: {
                                show: true,
                                position: 'right'
                            },
                            itemStyle:{
                                barBorderRadius: 5
                            }
                        }
                    ],
                    color: {
                        type: 'linear',
                        x: 0,
                        y: 0,
                        x2: 1,
                        y2: 0,
                        colorStops: [{
                            offset: 0, color: '#FFA07A ' // 0% 处的颜色
                        }, {
                            offset: 1, color: '#F08080' // 100% 处的颜色
                        }],
                        global: false // 缺省为 false
                    },
                })
            }
        }

    }
</script>

<style lang="scss">
    .app-container{
        position: relative;
        overflow-x: hidden;
        font-size: 1em;

        img{
            width: 100vw;
            height: auto;
            z-index: 0;
        }

        /*.heading{*/
        /*    background-image: url(../assets/head_bg.png);*/
        /*    background-repeat: no-repeat;*/
        /*    background-size: cover;*/
        /*    width: 100vw;*/
        /*    height: 22.867vh;*/
        /*    z-index: 0;*/
        /*    h1{*/
        /*        padding: 9.867vw 0 0 4.867vw;*/
        /*        width: 48.867vw;*/
        /*        height: 12.867vw;*/
        /*        color: #f6ffd5;*/
        /*    }*/
        /*    h2{*/
        /*        padding: 10.867vw 0 0 5.867vw;*/
        /*        width: 45.867vw;*/
        /*        height: 5.867vw;*/
        /*        color: #f6ffd5;*/
        /*    }*/
        /*}*/

        .main{
            position: relative;
            background-color: #FFFFFF;
            border-radius: 10px;
            width: 100vw;
            height: 95vh;
            z-index: 1;
            margin-top: -30px;

            .contents{
                padding: 20px;
                float: left;
                #update-time{
                    display: flex;
                    font-size: small;
                }
                .cards{
                    display: flex;
                    margin-top: 15px;

                    .v-card{
                        margin-right: 0.45vw;
                        border-radius: 0;
                    }
                    .v-card__text{
                        padding: 5px 0 5px 0;
                        color: #100815;
                    }
                    .mini{
                        font-size: 11px;
                        inc{
                            font-weight: bold;
                            color: #f95e50;
                        }
                        incr{
                            font-weight: bold;
                            color: #38b26e;
                        }
                    }
                    #confirm-cases{background-color: #fff0f1;}
                    #current-confirm{
                        font-size: x-large;
                        color: #f95e61;
                    }
                    #prob-cases{
                        background-color: #faf8e1;
                    }
                    #current-prob{
                        font-size: x-large;
                        color: #F96F76;
                    }

                    #total-recover{background-color: #f0f8f4}
                    #current-recover{
                        font-size: x-large;
                        color: #31c07d;
                    }

                    #total-death{background-color: #f2f6f7}
                    #current-death{
                        font-size: x-large;
                        color: #404040;
                    }

                }

                .charts{
                    margin-top: 30px;
                    #bar{
                        margin-left: -25px;
                        padding-bottom: -15px;
                    }
                }

                .world-stats{
                    margin-top: 20px;
                    width: 90vw;
                    height: 30vh;
                /*    iframe{*/
                /*        height: 30vh;*/
                /*        width: 90vw;*/
                /*        max-height: 1360px;*/
                /*        max-width: 1024px;*/
                /*    }*/
                    #unite{
                        background-image: url("../assets/stripes.svg");
                        width: 100%;
                        height: 100%;
                        img{
                            width: 50vw;
                            height: 30vh;
                        }
                    }
                }
            }
        }
        .footer{
            margin-top: -20px;
            padding-bottom: 10px;
        }
    }
</style>
