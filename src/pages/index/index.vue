<template>
  <div class="container">
    <img class="weather-img" src="https://s1.ax1x.com/2018/08/05/PD4J4H.png" />
    <div class="weather-div">
      <p class="degree-text">{{degree}}</p>
      <div class="location-div">
        <p class="location-text">{{county}}</p>
        <p class="location-text">{{street}}</p>
      </div>
      <p class="location-text">{{weather}}</p>
    </div>

    <div class="weather-list" id v-for="item in list" :key="item.time">
      <div class="weather-item">
        <div class="date">{{item.time}}</div>
        
        <div class ="day-night-weather-div">
        
          <div class="weather-icon-div">
              <img  class="weather-icon"  :src="'/static/'+ item.day_weather_code + '.png'"/>
          <text class="day_weather">{{item.day_weather}}</text>
          </div>
        <div class="weather-icon-div">
          <img  class="weather-icon"  :src="'/static/'+ item.night_weather_code + '.png'"/>
          <text class="day_weather">{{item.night_weather}}</text>
        </div>
        </div>
        <text class="date">{{item.min_degree}}~{{item.max_degree}}°C</text>
      </div>
    </div>

  </div>
</template>

<script>
import card from "@/components/card";

export default {
    data() {
        return {
            motto: "Hello World",
            userInfo: {},
            list: [],
            listTemp: [],
            lat: "",
            lon: "",
            province: "",
            city: "",
            county: "",
            street: "",
            degree: "",
            weather: ""
        };
    },

    components: {
        card
    },

    async onPullDownRefresh() {
        this.getLatLon();
    },
    methods: {
        getLocation() {
            this.$http
                .get(
                    "https://apis.map.qq.com/ws/geocoder/v1/?key=3BFBZ-ZKD3X-LW54A-ZT76D-E7AHO-4RBD5",
                    {
                        location: this.lat + "," + this.lon
                    }
                )
                .then(res => {
                    this.province = res.data.result.address_component.province;
                    this.city = res.data.result.address_component.city;
                    this.county = res.data.result.address_component.district;
                    this.street = res.data.result.address_component.street;
                    //根据地区获取天气
                    this.getWeather();
                })
                .catch(error => {
                    console.log(error);
                });
        },

        getWeather() {
            this.$http
                .get(
                    "https://wis.qq.com/weather/common?weather_type=observe|forecast_1h|forecast_24h|tips&source=wxa",
                    {
                        province: this.province,
                        city: this.city,
                        county: this.county
                    }
                )
                .then(res => {
                    console.log(res.data);
                    //根据地区获取天气
                    this.degree = res.data.data.observe.degree + "°";
                    this.weather = res.data.data.observe.weather;
                    this.list = res.data.data.forecast_24h;
                    console.log(this.list);
                    wx.stopPullDownRefresh();
                    //wx.hideNavigationBarLoading()
                })
                .catch(error => {
                    console.log(error);
                });
        },
        getLatLon() {
            // wx.showNavigationBarLoading()
            wx.getLocation({
                type: "wgs84",
                success: res => {
                    this.lat = res.latitude;
                    this.lon = res.longitude;
                    this.getLocation();
                }
            });
        },

        bindViewTap() {
            const url = "../logs/main";
            wx.navigateTo({ url });
        },
        getUserInfo() {
            // 调用登录接口
            wx.login({
                success: () => {
                    wx.getUserInfo({
                        success: res => {
                            this.userInfo = res.userInfo;
                        }
                    });
                }
            });
        },
        toMain() {
            const url = "/pages/logs/main";
            wx.navigateTo({ url });
        }
    },

    created() {
        // 调用应用实例的方法获取全局数据
        //this.getUserInfo();
        this.getLatLon();
    }
};
</script>

<style scoped>
.userinfo {
    display: flex;

    flex-direction: column;
    align-items: right;
}

.weather-div {
    display: flex;
    flex-direction: column;
    margin-top: -500rpx;
}
.degree-text {
    margin-left: 20rpx;
    margin-top: 20rpx;
    font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
    font-size: 100rpx;
    color: aliceblue;
}
.location-text {
    margin-top: 10rpx;
    margin-left: 25rpx;
    font-size: 25rpx;
    color: aliceblue;
}
.weather-img {
    width: 100%;
    height: 500rpx;
}
.location-div {
    width: 100%;
    display: flex;
    flex-direction: row;
}

.userinfo-avatar {
    background: #000;
    width: 50rpx;
    display: none;
    height: 50rpx;
    margin: 10rpx;
    align-items: center;
    padding: 10rpx;
    border-radius: 50%;
}

.userinfo-nickname {
    color: #000;
}

.usermotto {
    margin-top: 150px;
}

.form-control {
    display: block;
    padding: 0 12px;
    margin-bottom: 5px;
    border: 1px solid #ccc;
}
.weather-list {
    margin: 20rpx;
    padding: 20rpx;
    border-radius: 10rpx;

    background: #ffffff;
}
.weather-item{
  display: flex;
  flex-direction: row;
}
.day-night-weather-div{
    margin-left: 100rpx;
    padding-right: 50rpx;
 display: flex;
  flex-direction: column;
}
.weather-icon-div{
  display: flex;
  flex-direction: row;
}
.weather-icon{


  width: 60rpx;
  height: 60rpx;
}
.day_weather{
  margin-top: 10rpx;

  font-size: 30rpx;
    color: #515151;
}
.date {
     display: flex;
     margin-top: 40rpx;
   align-content: center;
    text-align: center;
    margin-left: 25rpx;
    font-size: 30rpx;
    color: #515151;
}

.counter {
    display: inline-block;
    margin: 10px auto;
    padding: 5px 10px;
    color: blue;
    border: 1px solid blue;
}
</style>
