# 小马天气
![](https://raw.githubusercontent.com/wangchenyan/ponyweather/master/app/src/main/res/drawable-xxhdpi/ic_launcher.png)

## 前言
部分设计参考[xcc3641](https://github.com/xcc3641)的开源项目[SeeWeather](https://github.com/xcc3641/SeeWeather)，在此表示感谢！<br>
第一次开始这个项目是2014年4月份，我还在大二的时候，当时有一个程序设计实践课，想着简单点就选了天气预报这个题目，时隔两年又重新拾起，给它脱胎换骨，就是想拿他作为一个学习新知识的实战项目，期间也做过改动，比如曾经把网络请求模块从[android-async-http](https://github.com/loopj/android-async-http)改为[Volley](https://android.googlesource.com/platform/frameworks/volley)。

- 项目地址：https://github.com/wangchenyan/ponyweather
- 有问题请提Issues
- 如果喜欢，欢迎Star！

## 简介
小马天气是一款开源Android天气信息查询软件。
- 实景天气，寻找你身边的美景
- 多城市天气预报，记得提醒远方的TA
- 语音播报，一键听天气
- Material Design 设计风格，从未如此简约
- 自动夜间模式，更加贴心

## 使用
1. clone 项目
2. 打开项目根目录下的 `local.properties` 文件，添加如下内容
```
# 和风天气，影响天气数据
HE_KEY=xxxxxx
# bmob，影响实景天气
BMOB_KEY=xxxxxx
# 百度语音合成，影响语音播报
BD_TTS_API_KEY=xxxxxx
BD_TTS_SECRET_KEY=xxxxxx
# 高德地图，影响定位
AMAP_KEY=xxxxxx
# bughd，非必需
BUG_HD_KEY=xxxxxx
```
3. 到相应的网站申请需要的 key （如[和风天气](https://www.heweather.com/)），替换对应位置的 `xxxxxx`

## 更新说明
`v 2.2`
- 升级和风天气API为V5

`v 2.1`
- 新增搜索城市
- 新增设置自动更新时间间隔

`v 2.0`
- 新增实景天气
- 新增城市管理
- 新增语音播报
- 支持自动夜间模式

`v 1.5`
- 重构代码，全新风格设计
- 增加支持国内全部县级以上城市可选

`v 1.4`
- 支持Android 6.0

## 下载地址
[点击下载](https://github.com/wangchenyan/ponyweather/releases)

## TODO
- [x] 实景天气
- [x] 城市管理
- [x] 语音播报
- [x] 自动夜间模式
- [ ] 桌面小部件

## 项目
### 公开API
- 天气数据：[和风天气](http://www.heweather.com/)
- 定位服务：[高德定位](http://lbs.amap.com/api/android-location-sdk/)
- 语音合成：[百度语音](http://yuyin.baidu.com/)
- 云存储：[Bmob后端云](http://www.bmob.cn/)

### 开源技术
- [Rxjava](https://github.com/ReactiveX/RxJava)
- [Retrofit](https://github.com/square/retrofit)
- [ASimpleCache](https://github.com/yangfuhai/ASimpleCache)
- [Glide](https://github.com/bumptech/glide)
- [android-floating-action-button](https://github.com/futuresimple/android-floating-action-button)

### 关键代码
网络请求`RxJava+Retrofit`
```
private void fetchDataFromNetWork(final CityEntity city) {
    Api.getIApi().getWeather(city.name, ApiKey.HE_KEY)
            .subscribeOn(Schedulers.io())
            .observeOn(AndroidSchedulers.mainThread())
            .filter(new Func1<WeatherData, Boolean>() {
                @Override
                public Boolean call(final WeatherData weatherData) {
                    boolean success = weatherData.weathers.get(0).status.equals("ok");
                    return success;
                }
            })
            .map(new Func1<WeatherData, Weather>() {
                @Override
                public Weather call(WeatherData weatherData) {
                    return weatherData.weathers.get(0);
                }
            })
            .doOnNext(new Action1<Weather>() {
                @Override
                public void call(Weather weather) {
                    mACache.put(city.name, weather);
                    SystemUtils.saveRefreshTime(WeatherActivity.this);
                }
            })
            .subscribe(new Subscriber<Weather>() {
                ...
				updateView(weather);
				...
            });
}
```

## 截图
![](https://raw.githubusercontent.com/wangchenyan/ponyweather/master/art/screenshot_01.jpg)
![](https://raw.githubusercontent.com/wangchenyan/ponyweather/master/art/screenshot_02.jpg)
![](https://raw.githubusercontent.com/wangchenyan/ponyweather/master/art/screenshot_03.jpg)
![](https://raw.githubusercontent.com/wangchenyan/ponyweather/master/art/screenshot_04.jpg)

## 关于作者
掘金：https://juejin.im/user/2313028193754168<br>
微博：https://weibo.com/wangchenyan1993

## License

    Copyright 2017 wangchenyan

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
