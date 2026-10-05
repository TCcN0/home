<template>
  <div class="weather" v-if="weatherData.city && weatherData.weather">
    <span>{{ weatherData.city }}&nbsp;</span>
    <span>{{ weatherData.weather }}&nbsp;</span>
    <span>{{ weatherData.temperature }}℃</span>
    <span class="sm-hidden">
      &nbsp;{{
        weatherData.wind_direction?.endsWith("风")
          ? weatherData.wind_direction
          : weatherData.wind_direction + "风"
      }}&nbsp;
    </span>
    <span class="sm-hidden">{{ weatherData.wind_power }}&nbsp;</span>
  </div>
  <div class="weather" v-else>
    <span>天气数据获取失败</span>
  </div>
</template>

<script setup>
import { getOtherWeather } from "@/api";
import { Error } from "@icon-park/vue-next";

// 天气数据
const weatherData = reactive({
  city: null,
  weather: null,
  temperature: null,
  wind_direction: null,
  wind_power: null,
});

// 获取天气数据（uapis.cn）
const getWeatherData = async () => {
  try {
    const data = await getOtherWeather();
    console.log("uapis 天气返回：", data);

    if (!data || !data.city) {
      throw new Error("返回数据异常");
    }

    weatherData.city = data.city || "未知地区";
    weatherData.weather = data.weather || "--";
    weatherData.temperature = data.temperature ?? "--";
    weatherData.wind_direction = data.wind_direction || "";
    weatherData.wind_power = data.wind_power || "";
  } catch (error) {
    console.error("天气信息获取失败:" + error);
    onError("天气信息获取失败");
  }
};

// 报错信息
const onError = (message) => {
  ElMessage({
    message,
    icon: h(Error, {
      theme: "filled",
      fill: "#efefef",
    }),
  });
  console.error(message);
};

onMounted(() => {
  getWeatherData();
});
</script>
