# Vue组件中高德地图展示

## vue-amap是一套基于Vue 2.0和高德地图的地图组件。

### 安装
```
npm install -S vue-amap

```
### 在main.js中引入

```
//引入 vue-amap
import VueAMap from 'vue-amap';
Vue.use(VueAMap);
VueAMap.initAMapApiLoader({
  key: 'your amap key',
  plugin: ['AMap.Autocomplete', 'AMap.PlaceSearch', 'AMap.Scale', 'AMap.OverView', 'AMap.ToolBar', 'AMap.MapType', 'AMap.PolyEditor', 'AMap.CircleEditor'],
  // 默认高德 sdk 版本为 1.4.4
  v: '1.4.4'
});
```

### 组件
* 地图
```
<el-amap vid="amapDemo" :zoom="zoom" :center="center">
</el-amap>
```

* 点坐标
```
<el-amap vid="amapDemo" :zoom="zoom" :center="center">
  <el-amap-marker v-for="marker in markers" :position="marker.position"></el-amap-marker>
</el-amap>
```

* 折线
```
<el-amap vid="amapDemo" :zoom="zoom" :center="center">
  <el-amap-polyline :path="polyline.path"></el-amap-polyline>
</el-amap>
```

* 多边形
```
<el-amap vid="amapDemo" :zoom="zoom" :center="center">
  <el-amap-polygon v-for="polygon in polygons" :path="polygon.path" :events="polygon.events"></el-amap-polygon>
</el-amap>
```

* 圆
```
<el-amap vid="amapDemo" :zoom="zoom" :center="center">
  <el-amap-circle v-for="circle in circles" :center="circle.center" :radius="circle.radius"></el-amap-circle>
</el-amap>
```

* 图片覆盖物
```
<el-amap vid="amapDemo" :zoom="zoom" :center="center">
  <el-amap-ground-image v-for="groundimage in groundimages" :url="groundimage.url"></el-amap-ground-image>
</el-amap>
```

* 文本
```
<el-amap vid="amapDemo" :zoom="zoom" :center="center">
  <el-amap-text v-for="text in texts"></el-amap-text>
</el-amap>
```

* 贝塞尔曲线
```
<el-amap vid="amapDemo" :zoom="zoom" :center="center">
  <el-amap-bezier-curve v-for="line in lines"></el-amap-bezier-curve>
</el-amap>
```

* 圆点标记
```
<el-amap vid="amapDemo" :zoom="zoom" :center="center">
  <el-amap-circle-marker v-for="marker in markers"></el-amap-circle-marker>
</el-amap>
```

* 椭圆
```
<el-amap vid="amapDemo" :zoom="zoom" :center="center">
  <el-amap-ellipse v-for="ellipse in ellipses"></el-amap-ellipse>
</el-amap>
```

* 矩形
```
<el-amap vid="amapDemo" :zoom="zoom" :center="center">
  <el-amap-rectangle v-for="rectangle in rectangles"></el-amap-rectangle>
</el-amap>
```

* 信息窗体
```
<el-amap vid="amapDemo" :zoom="zoom" :center="center">
  <el-amap-info-window v-for="window in windows" :position="window.position" :content="window.content" :open="window.open"></el-amap-info-window>
</el-amap>
```

* Search-Box
```
<el-amap-search-box class="search-box" :search-option="searchOption" :on-search-result="onSearchResult"></el-amap-search-box>
<el-amap vid="amapDemo">
</el-amap>
```
















