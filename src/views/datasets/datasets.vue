<template>
  <div>
    <!--地图控件-->
    <div class="map-card">
      <div class="map-container">
        <div ref="mapbox"
             class="map"></div>
      </div>
    </div>
    <!--间隔-->
    <div class="spacer"></div>
    <div class="card">
      <div class="left">
        <!-- 输入框搜索 -->
        <input type="text"
               v-model="searchTerm"
               placeholder="Search...">
      </div>
      <div class="right">
        <!-- 标签搜索 -->
        <el-tag class="tag"
                v-for="(tag, index) in tags"
                :key="index"
                :class="{ active: selectedTags.includes(tag) }"
                @click="toggleTag(tag)">{{ tag }}
        </el-tag>
      </div>
    </div>

    <div><!--数据集列表-->
      <div v-for="(item,index) in filteredItems"
           :key="index "
           :value="item">
        <div class="dataset-card">
          <h3>{{ item.title }}</h3>
          <p>{{ item.description }}</p>
          <p>{{ item.tags }}</p>
        </div>
      </div>
    </div>

    <footer> <!-- 底部 -->
      <!-- 底部内容 -->
    </footer>
  </div>
</template>

<script>
import mapboxgl from "mapbox-gl"


export default {
  data () {
    return {
      map: null,
      searchTerm: "",
      selectedTags: [],
      tags: ["tag1", "tag2", "tag3"],
      items: [
        { title: "Item 1", description: "Description 1", tags: ["tag1", "tag2"] },
        { title: "Item 2", description: "Description 2", tags: ["tag2", "tag3"] },
        { title: "Item 3", description: "Description 3", tags: ["tag3", "tag1"] },
        { title: "a", description: "你好", tags: ["tag1", "tag2"] },
        { title: "sdf", description: "士大夫", tags: ["tag2", "tag3"] },
        { title: "asdaf", description: "得浑身发抖", tags: ["tag3", "tag1"] },
        { title: "asd", description: "asfd", tags: ["tag1", "tag2"] },
        { title: "sd", description: "adsf", tags: ["tag2", "tag3"] },
        { title: "ky", description: "Dd", tags: ["tag3", "tag1"] }
      ]
    }
  },
  computed: {
    // 搜索逻辑
    filteredItems () {
      const regex = new RegExp(this.searchTerm, "i")
      return this.items.filter(item => {
        const matchesSearchTerm = regex.test(item.title) || regex.test(item.description)
        const matchesSelectedTags = this.selectedTags.length === 0 || this.selectedTags.some(tag => item.tags.includes(tag))
        return matchesSearchTerm && matchesSelectedTags
      })
    }
  },
  name: "Dataset",
  // 组件逻辑和数据处理
  mounted () {
    this.initMap()
  },
  methods: {
    // 标签选中
    toggleTag (tag) {
      if (this.selectedTags.includes(tag)) {
        this.selectedTags = this.selectedTags.filter((t) => t !== tag)
      } else {
        this.selectedTags.push(tag)
      }
    },
    // 初始化地图
    initMap () {
      mapboxgl.accessToken = "pk.eyJ1IjoibGlmZW5nMTExIiwiYSI6ImNsZ201Z2tnMzAyaGYzcnAzcXN1NTU5c3IifQ.iSuM-U4bOnTlKApmXsXSig"
      this.map = new mapboxgl.Map({
        container: this.$refs.mapbox,
        style: "mapbox://styles/mapbox/satellite-v9", // 设置为遥感影像样式
        center: [114, 30],
        zoom: 9
      })
      // 地图控件,比例尺,指南针等
      this.map.addControl(new mapboxgl.NavigationControl(), "top-left")
      this.map.addControl(new mapboxgl.ScaleControl(), "bottom-left")
      // 创建本地切换地图源控件
      class StyleSwitcherControl {
        onAdd (map) {
          this._map = map
          this._container = document.createElement("div")
          this._container.className = "mapboxgl-ctrl"
          this._container.innerHTML = `
        <select>
          <option value="mapbox://styles/mapbox/streets-v11">Streets</option>
          <option value="mapbox://styles/mapbox/satellite-v9">Satellite</option>
          <option value="mapbox://styles/mapbox/outdoors-v11">Outdoors</option>
        </select>
      `
          this._container.querySelector("select").addEventListener("change", (event) => {
            map.setStyle(event.target.value)
          })
          return this._container
        }

        onRemove () {
          this._container.parentNode.removeChild(this._container)
          this._map = undefined
        }
      }

      // 添加地图源切换控件
      const styleSwitcherControl = new StyleSwitcherControl()
      this.map.addControl(styleSwitcherControl, "top-right")
    }

  }
}
</script>

  <style scoped>
/* 样式 */

/* 地图容器的样式 */
.map-card {
  margin: 0 auto;
  width: 80%;
  background-color: #ecdfdf;
  border-radius: 10px;
}

.map-container {
  padding: 10px;
}

.map {
  border-radius: 10px;
}
.dataset-card {
  border: 1px solid #ccc;
  padding: 10px;
  margin-bottom: 10px;
}
/* 间隔 */
.spacer {
  height: 20px;
}
/* 搜索栏 */
.card {
  margin: 0 auto;
  width: 80%;
  background-color: #ecdfdf;
  border-radius: 10px;
  display: flex;
}
.left {
  width: 30%;
  display: flex;
  justify-content: center;
  align-items: center;
}
.right {
  width: 70%;
}
.tag {
  display: inline-block;
  padding: 5px;
  margin: 5px;
  border: 1px solid #ccc;
}
.tag.active {
  background-color: #ccc;
}
</style>

