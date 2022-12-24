<template>
  <div class="filter-more">
    <div style="width: 100%; min-height: 50px; background: #f9f9fc">
      <a href="#" style="color: #9babc5" class="text-select">筛选结果（125）</a>
      <a
        v-for="(item, index) in selectBox"
        :key="index"
        href="#"
        class="text-select"
      >
        {{ item.text }}
        <i @click="removeCurrentSelect(index,item)">&times;</i>
      </a>
      <a
        href="#"
        class="text-select"
        @click="clearAll"
      >清除筛选结果</a>
    </div>
    <transition name="select">
      <div v-show="boxshow" class="box">
        <div v-for="(item, index) in filterBox" :key="index" class="items">
          <div class="title" span="2">{{ item.name }}</div>
          <div span="22">
            <a
              v-for="(v, i) in item.items"
              :key="i"
              href="#"
              class="text-filter"
              @click="clickrange(index, v, i)"
            >
              <span :class="{ isActive: v.active }">{{ v.text }}</span>
              <i
                v-if="v.active && item.name != '文件类型:'"
                class="el-icon-circle-plus"
                @click.stop="detail"
              />
            </a>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>
<script>
import nextPage from '@/views/test/nextPage.vue'
export default {
  name: 'NextPage',
  comments: { nextPage },
  props: {
    // eslint-disable-next-line vue/require-prop-type-constructor,vue/require-default-prop
    expand: true
  },
  data() {
    return {
      boxshow: false,
      btnTxt: false,
      selectBox: [],
      time: '',
      filterBox: [
        {
          name: '类别:',
          items: [
            { value: '1', text: '全部', active: false },
            { value: '2', text: '物理印章', active: false },
            { value: '3', text: '电子印章', active: false }
          ]
        },
        {
          name: '印章类型:',
          items: [
            { value: 'allAge', text: '全部', active: false },
            { value: 'treeY', text: '公章', active: false },
            { value: 'fourteenY', text: '财务章', active: false },
            { value: 'fortyY', text: '合同章', active: false }
          ]
        },
        {
          name: '文件类型:',
          items: [
            { value: 'allSex', text: '全部', active: false },
            { value: 'man', text: '合同', active: false },
            { value: 'women', text: '发票', active: false },
            { value: 'unknow', text: '普通文件', active: false }
          ]
        },
        {
          name: '部门:',
          items: [
            { value: '1', text: '全部', active: false },
            { value: '2', text: '财务部', active: false },
            { value: '3', text: '法务部', active: false },
            { value: '4', text: '行政部', active: false }
          ]
        }
      ],
      value1: ''
    }
  },
  watch: {
    expand: {
      handler(n) {
        this.boxshow = n
      },
      deep: true
    },
    value1: {
      handler(n) {
        if (n !== '') {
          this.time = n[0] + '至' + n[1]
          this.selectBox.unshift({
            value: '0',
            text: this.time,
            active: false
          })
        }
      }
    }
  },
  methods: {
    togglebox() {
      this.boxshow = this.props.expand
    },
    clickrange(parentIndex, el, childIndex) {
      var item = this.filterBox[parentIndex].items
      console.log('选中了之前', this.selectBox)
      item.filter((v, i) => {
        if (i === childIndex) {
          v.active = !v.active // 选中和反选
          console.log('选中了之前', this.selectBox)
          this.selectBox.unshift(v) // 选中的数组
          console.log('选中了', this.selectBox)
        } else {
          // v.active = false; // 取消选中，实现一行只选一个就放开此段代码
          this.selectBox.filter((childEl, childI) => {
            if (childEl.active === false) {
              this.selectBox.splice(childI, 1) // 反选删除数组中的当前个
            }
          })
        }
      })
      if (this.time !== '') {
        this.selectBox.unshift({ value: '0', text: this.time, active: false })
      }
    },
    removeCurrentSelect(index, item) {
      // 如果是time，就先清空
      if (item.value === 0) {
        this.time = ''
        this.value1 = ''
      }
      this.filterBox.filter((el, i) => {
        el.items.filter((data, childIndex) => {
          if (data.text === this.selectBox[index].text) {
            data.active = false
          }
        })
      })
      this.selectBox.splice(index, 1)
    },
    // 清空所有的选项
    clearAll() {
      this.selectBox.filter((childEl, childI) => {
        childEl.active = false
      })
      this.selectBox = []
      this.time = ''
      this.value1 = ''
    },
    detail() {
      console.log('21312312')
    }
  }
}
</script>
<style lang="scss" scoped>
.filter-more {
  width: 100%;
  font-size: 14px;
  margin: 0 auto;
  border: 1px solid #e8f4fd;
  //   padding: 25px 15px;
}
.box {
  //   height: 400px;
  overflow: hidden;
  color: gray;
  .items {
    height: 50px;
    width: 95%;
    margin-left: 3%;
    display: flex;

    align-items: center;
    .title {
      width: 100px;
      color: black;
    }
  }
}
.text-toggle {
  text-align: center;
  cursor: pointer;
}
.selectbox-leave-active,
.selectbox-enter-active {
  transition: all 1s ease;
}
.selectbox-leave-active,
.selectbox-enter {
  height: 0px !important;
}
.selectbox-leave,
.selectbox-enter-active {
  height: 150px;
}
.text-filter {
  display: inline-block;
  color: gray;
  width: 80px;
  span {
    display: inline-block;
    text-align: center;
    // width: 60px;
    &:hover {
      border-radius: 40px;
      color: #ffffff;
      animation: myfirst 1s;
      -moz-animation: myfirst 1s; /* Firefox */
      -webkit-animation: myfirst 1s; /* Safari and Chrome */
      -o-animation: myfirst 1s; /* Opera */
      animation-fill-mode: forwards;
    }
  }
  .el-icon-circle-plus {
    color: red;
    padding-left: 3px;
  }
}
.text-select {
  display: inline-block;
  padding: 0px 5px;
  border: none;
  border-radius: 40px;
  color: red;
  font-size: 14px;
  padding: 20px;
  i {
    display: inline-block;
    height: 100%;
    font-size: 15px;
    padding: 0px 5px;
  }
}
.isActive {
  border-radius: 40px;
  color: red;
}
@keyframes myfirst {
  from {
    background: #ffffff;
  }
  to {
    background: red;
  }
}
</style>
<style  lang='scss'>
.el-range-editor--medium .el-range-input {
  width: 88px;
  height: 19px;
  font-size: 14px;
  font-family: Rubik-Regular;
  line-height: 17px;
  //background-color: subMenuBg;
  //color: gray;
  opacity: 1;
}
.el-range-editor--medium .el-range-separator {
  text-align: center;
}
.el-range-editor--medium.el-input__inner {
  //background-color: $subMenuBg;
  width: 297px;
  height: 36px;
  overflow: hidden;
}
.el-range-editor--medium .el-range-separator {
  //color: $gray;
}
</style>
