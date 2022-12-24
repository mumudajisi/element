<template>
  <div class="demo">
    <!-- 头部显示所选分类，可清空或删除 -->
    <div style="padding-bottom: 8px;">
      <div class="header">
        <div class="item type"><i class="el-icon-delete delete" @click="tabClickAll(null)" />所有分类 ></div>
        <div v-for="(item) in selectTab" :key="item.dictLabel" class="item select">
          <div>{{ item.title + item.dictLabel }}<i class="el-icon-close close" @click="tabClick(null,item.typeIndex,item.valIndex,item.valIndex2)" /></div>
        </div>
      </div>
    </div>
    <div class="demo-warp">
      <div v-for="(item,typeIndex) in filterList" :key="typeIndex" class="demo-flex">
        <span class="demo-title">{{ item.title }}</span>
        <div class="demo-content">
          <!-- <div class="demo-tab"> -->
          <!--数据多时只显示一行-->
          <div ref="content" class="demo-tab">
            <span
              :class="{'demo-active': isAll(typeIndex)}"
              class="tab-item"
              @click="tabClickAll(typeIndex)"
            >全部</span>
            <template v-for="(val, valIndex) in item.children">
              <!-- 存在子类 -->
              <template v-if="val.children">
                <el-popover
                  :key="valIndex"
                  placement="bottom"
                  width="400"
                  trigger="hover"
                >
                  <!--一级分类-->
                  <template slot="reference">
                    <span
                      :class="{'demo-active': isActive(typeIndex,valIndex)}"
                      class="tab-item spanClass"
                    >
                      {{ val.dictLabel }}
                      <span class="el-icon-arrow-down" />
                    </span>
                  </template>
                  <!--二级分类-->
                  <span
                    v-for="(val2, valIndex2) in val.children"
                    :key="valIndex2"
                    :class="{'demo-active': isActive(typeIndex,valIndex,valIndex2)}"
                    class="tab-item"
                    style="display:inline-block;margin: 0 5px 15px 5px;cursor: pointer;padding: 5px 10px;color: #999;"
                    @click="tabClick(val2,typeIndex,valIndex,valIndex2)"
                  >{{ val2.dictLabel }}</span>
                </el-popover>
              </template>

              <!-- 不存在子类 -->
              <template v-else>
                <span
                  :key="valIndex"
                  :class="{'demo-active': isActive(typeIndex,valIndex)}"
                  class="tab-item spanClass"
                  @click="tabClick(val,typeIndex,valIndex)"
                >{{ val.dictLabel }}</span>
              </template>
            </template>
          </div>
        </div>
        <div ref="more" class="demo-more" @click="changeShow(typeIndex)">更多</div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: 'Index',
  props: {
    getList: {
      type: Array,
      default: () => []
    }
  },
  data() {
    return {
      isShow: false,
      selected: {},
      displayTab: false
    }
  },
  computed: {
    // 这里对传入的数据做了处理，初始化了超过一行的都进行隐藏
    filterList() {
      const list = [...this.getList]
      list.map(item => {
        this.$set(item, 'isShow', false)
      })
      return list
    },

    // 头部已选项
    selectTab() {
      // 格式化需要在头部显示和操作的数据
      const allInfo = []
      // 遍历已选数组
      for (const typeIndex in this.selected) {
        // （父）所有分类
        const childs = this.getList[typeIndex].children
        // 分类标题
        const title = this.getList[typeIndex].title

        const val = this.selected[typeIndex]
        // 遍历选中的父类,valChildren:存储的子类/无子类的分类索引值 valIndex:父类索引值
        val.forEach((valChildren, valIndex) => {
          // 存在子类
          if (valChildren instanceof Array) {
            // 遍历选中的子类（存储的是子类的索引值）
            valChildren.forEach(valIndex2 => {
              // 找到子类在原数组中对应的对象 如：{dictLabel: '子类1'}，可以直接扩展
              const val2 = childs[valIndex].children[valIndex2]
              allInfo.push({
                title, // 分类标题
                typeIndex, // 当前分类索引值
                valIndex, // 父类索引值
                ...val2, // 子类名
                valIndex2 // 子类索引值
              })
            })
          } else {
            // 不存在子类
            const val2 = childs[valChildren]
            allInfo.push({
              title, // 分类标题
              typeIndex, // 当前分类索引值
              valIndex: valChildren, // 无子类的分类索引值
              ...val2// 父类名
            })
          }
        })
      }
      return allInfo
    }
  },
  mounted() {
    this.$refs.content.map((item, index) => {
      // 这里一行高度是46
      if (item.clientHeight > 46) {
        // 超过高度显示“更多”，隐藏超出部分
        this.$refs.more[index].style.visibility = 'visible'
        this.$refs.content[index].classList.add('demo-hide')
      }
    })
  },
  methods: {
    // 清空全部或当前分类全部选择，即显示全部或显示当前分类全部
    tabClickAll(typeIndex) {
      if (typeIndex === null) {
        // 清空全部选择，即显示全部
        this.selected = {}
      } else {
        // 清空当前分类，即显示当前分类全部
        this.selected[typeIndex] = []
      }
      this.selected = Object.assign({}, this.selected)
      this.$emit('get-sel-data', this.selected)
      this.$forceUpdate()
    },
    // data 选中的数据  typeIndex 第几条筛选条件  valIndex 父类  valIndex2子类
    tabClick(data, typeIndex, valIndex, valIndex2) {
      // 初始化该类选择为空数组
      if (!this.selected[typeIndex]) {
        this.selected[typeIndex] = []
      }

      // 不存在子类
      if (valIndex2 === undefined) {
        // 是否已选中该项
        const idx = this.selected[typeIndex].indexOf(valIndex)
        if (idx === -1) {
          // 未选中，将选中值的索引值存入
          this.selected[typeIndex].push(valIndex)
        } else {
          // 已选中，再点击，将选中值的索引值删除
          this.selected[typeIndex].splice(idx, 1)
        }
      } else {
        // 存在子类（不需要存储父类，所以不需要进行上述操作）
        // 初始化该子类选择为空数组
        if (!this.selected[typeIndex][valIndex]) {
          this.selected[typeIndex][valIndex] = []
        }
        // 是否已选中该项
        const idx2 = this.selected[typeIndex][valIndex].indexOf(valIndex2)
        if (idx2 === -1) {
          // 未选中，将选中值的索引值存入
          this.selected[typeIndex][valIndex].push(valIndex2)
        } else {
          // 已选中，再点击，将选中值的索引值删除
          this.selected[typeIndex][valIndex].splice(idx2, 1)
        }
      }

      this.selected = Object.assign({}, this.selected)
      this.$emit('get-sel-data', this.selected)
      this.$forceUpdate()
    },
    // 高亮显示
    isActive(typeIndex, valIndex, valIndex2) {
      // 不存在子类、存在子类的父类
      // 两种情况使用或者判断，不存在子类：当前分类存在索引值；存在子类的父类：父类的数组长度不为0
      if (valIndex2 === undefined) {
        // 选中再清空 存在数组 长度为0不能高亮
        return this.selected[typeIndex] &&
          (
            (this.selected[typeIndex][valIndex] && this.selected[typeIndex][valIndex].length) ||
            this.selected[typeIndex].includes(valIndex)
          )
      }
      // 父类的子类：包含子类索引值
      return this.selected[typeIndex] &&
        this.selected[typeIndex][valIndex] &&
        this.selected[typeIndex][valIndex].some(it => it === valIndex2)
    },
    isAll(typeIndex) {
      // 未选中过为空，选中再清除后会有记录，所以判断数组长度为0
      // 含有子项的要判断遍历父类是否为空，以及长度为0
      return (this.selected[typeIndex] || []).length === 0 &&
        (this.selected[typeIndex] || []).every(it => (it || []).length === 0)
    },
    // 显示和隐藏
    changeShow(index) {
      this.filterList[index].isShow = !this.filterList[index].isShow
      if (this.filterList[index].isShow) {
        this.$refs.content[index].classList.remove('demo-hide')
      } else {
        this.$refs.content[index].classList.add('demo-hide')
      }
    }

  }
}
</script>

<style>
.demo {
  height: auto !important;

.header {
  display: flex;

.type {
  padding: 0 5px 0 0;

.delete {
  padding: 0 5px;
  cursor: pointer;

}

&:hover {
.delete {
  color:rgb(129, 169, 255);
  font-weight: bold;
}
}
}

.select {
  padding: 0 5px;
  border: 1px solid silver;
  font-size: 13px;
  color: #999;
  margin-right: 5px;
  cursor: pointer;
  user-select: none;

.close {
  padding: 0 0 0 5px;
}

&:hover {
   border-color: rgb(129, 169, 255);

.close {
  color: rgb(129, 169, 255);
  font-weight: bold;
}
}
}
}

.demo-warp {
  border: #ccc 1px solid;
  margin-bottom: 15px;
  display: flex;
  margin: auto;
  height: 100%;
  flex-direction: column;
  padding: 15px 15px 5px 15px;
  position: relative;

.demo-flex {
  display: flex;
  margin-bottom: 15px;

.demo-title {
  flex-basis: 100px;
  margin-top: 5px;
}

.demo-content {
  display: flex;
  flex: 1;

.demo-tab {
  flex: 1;
  margin-right: 15px;
  min-height: 35px;

.tab-item {
  display: inline-block;
  margin: 0 5px 15px 5px;
  cursor: pointer;
  padding: 5px 10px;
  color: #999;
}
}
}

.demo-more {
  visibility: hidden;
  margin-top: 5px;
  cursor: pointer;
}
}

.demo-flex:last-of-type {
  margin-bottom: 0;
}
}
}

.demo-active {
  background-color: rgb(129, 169, 255);
  color: white !important;
  border-radius: 3px;
}

.demo-tab .tab-item:hover {
  background-color:rgb(129, 169, 255);
  color: white !important;
  border-radius: 3px;
}

.demo-hide {
  height: 35px;
  overflow: hidden;
}
</style>
