<template>
  <div class="dialogue_div" style="display: flex">
    <div :class="isCollapse ? 'writ_log' : 'collapwrit_log'">
      <div v-if="writFlag">
        <p style="display: flex; justify-content: space-between">
          <span style="font-size: 16px; color: rgba(0, 0, 0, 0.85)"
            >AI制作PPT</span
          >
        </p>
        <div style="width: 100%; margin-top: 24px">
          <div class="writ_name" style="margin-bottom: 16px">
            <a-radio-group v-model="mode">
              <a-radio-button value="top"> AI智能生成 </a-radio-button>
              <a-radio-button value="left"> 导入文档生成 </a-radio-button>
            </a-radio-group>
          </div>
          <a-textarea
            v-model="requireValue"
            style="border: none"
            placeholder="无限的宇宙"
            :auto-size="{ minRows: 5, maxRows: 5 }"
          />
        </div>

        <!-- 立即生成 -->
        <div
          class="generatebtn"
          :style="
            requireValue
              ? 'cursor: pointer;background:#6B70FB'
              : 'cursor: not-allowed;background: rgba(107, 112, 251, 0.4);'
          "
          @click="immediate_generation"
        >
          立即生成
        </div>
        <!-- 清除记录按钮 -->
        <div
          class="eliminatebutton"
          style="width: 260px"
          @click="empty_content"
        >
          <a-icon type="delete" style="margin-right: 6px" />清除所有内容
        </div>
      </div>
      <!-- 对话记录展开收起按钮 -->
      <img
        src="@/assets/img/unfold.png"
        alt=""
        class="isCollapsebtn"
        @click="menu_folding"
        v-if="isCollapse"
      />
      <img
        v-else
        src="@/assets/img/takeback.png"
        alt=""
        class="isCollapsebtn"
        @click="menu_folding"
      />
    </div>
    <!-- 有内容显示 -->
    <div
      v-if="contentFlag"
      class="hasContent"
      :style="isCollapse ? 'width: calc(100% - 320px)' : 'width: 100%'"
    >
      <!-- 标题 == 生成大纲 -->
      <div>
        <a-icon type="menu" style="margin-right: 4px; color: #6b70fb" /><span
          >生成大纲</span
        >
        <!-- 卡片内容 -->
        <div class="outlineContent">
          <!-- 目录 -->
          <p>
            您可以在下方随意编辑大纲内容，您可以在下方随意编辑大纲内容，您可以在下方随意编辑大纲内容
            ，您可以在下方随意编辑大纲内容
          </p>
          <a-tree
            :show-line="showLine"
            :show-icon="showIcon"
            :default-expanded-keys="['0-0-0', '0-0-1', '0-0-2']"
            @select="onSelect"
          >
            <a-icon slot="icon" type="carry-out" />
            <a-tree-node key="0-0">
              <a-icon slot="icon" type="carry-out" />
              <span slot="title" style="color: #1890ff">parent 1</span>
              <a-tree-node key="0-0-0" title="parent 1-0">
                <a-icon slot="icon" type="carry-out" />
                <a-tree-node key="0-0-0-0" title="leaf">
                  <a-icon slot="icon" type="carry-out" />
                </a-tree-node>
                <a-tree-node key="0-0-0-1" title="leaf">
                  <a-icon slot="icon" type="carry-out" />
                </a-tree-node>
                <a-tree-node key="0-0-0-2" title="leaf">
                  <a-icon slot="icon" type="carry-out" />
                </a-tree-node>
              </a-tree-node>
              <a-tree-node key="0-0-1" title="parent 1-1">
                <a-icon slot="icon" type="carry-out" />
                <a-tree-node key="0-0-1-0" title="leaf">
                  <a-icon slot="icon" type="carry-out" />
                </a-tree-node>
              </a-tree-node>
              <a-tree-node key="0-0-2" title="parent 1-2">
                <a-icon slot="icon" type="carry-out" />
                <a-tree-node key="0-0-2-0" title="leaf">
                  <a-icon slot="icon" type="carry-out" />
                </a-tree-node>
                <a-tree-node key="0-0-2-1" title="leaf">
                  <a-icon slot="icon" type="carry-out" />
                  <a-icon slot="switcherIcon" type="form" />
                </a-tree-node>
              </a-tree-node>
            </a-tree-node>
          </a-tree>
          <!-- 两个按钮 -->
          <div style="margin-top: 24px; display: flex; align-items: center">
            <a-button style="flex-shrink: 0; width: 110px" icon="redo">
              重新生成
            </a-button>
            <a-button style="flex-grow: 1" class="pptBtnCss">
              挑选PPT模板
            </a-button>
          </div>
        </div>
      </div>
    </div>
    <!-- 空内容显示 -->
    <div
      v-else
      :style="
        isCollapse
          ? 'padding: 60px 0; width: calc(100% - 320px)'
          : 'padding: 60px 0; width: 100%'
      "
      class="writ_conatiner"
    >
      <!-- 提示语 -->
      <div class="dialogue_container" style="margin-top: 180px">
        <template>
          <a-empty>
            <span
              slot="description"
              style="
                color: rgba(0, 0, 0, 0.85);
                font-size: 14px;
                line-height: 32px;
              "
            >
              当前任务是空的哦
            </span>
            <div
              slot="description"
              style="
                color: rgba(0, 0, 0, 0.65);
                font-size: 14px;
                line-height: 22px;
              "
            >
              在左侧输入描述，创建你的作品吧！
            </div>
          </a-empty>
        </template>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: 'writing',
  data () {
    return {
      isCollapse: true, // 写作抽屉展开收起绑定值
      writFlag: true, // 抽屉里内容显示隐藏绑定值
      requireValue: '123', // 写作要求绑定值
      contentFlag: true, // 写作内容和提示语显示隐藏绑定值
      writDialogue: [], // 写作返回数组
      // 按钮绑定值
      mode: 'top',
      showLine: true,
      showIcon: false
    }
  },
  methods: {
    // 写作抽屉展开收起点击事件
    menu_folding () {
      this.isCollapse = !this.isCollapse
      if (this.isCollapse) {
        setTimeout(() => {
          this.writFlag = true
        }, 400)
      } else {
        this.writFlag = false
      }
    },
    // 立即生成
    immediate_generation () {
      if (this.requireValue) {
        this.contentFlag = true
        this.writDialogue.push({ text: this.subjectValue, isSelf: false })
        // 发送后清空输入框
        this.requireValue = ''
      }
    },
    onSelect (selectedKeys, info) {
      console.log('selected', selectedKeys, info)
    },
    // 清除所有内容按钮
    empty_content () {
      const that = this
      this.$confirm({
        title: '删除',
        okText: '确定',
        cancelText: '取消',
        icon: 'info-circle',
        content: (h) => <div>确定要清除所有内容吗?</div>,
        onOk () {
          that.subjectValue = ''
          that.requireValue = ''
        },
        onCancel () { },
        class: 'test'
      })
    }
  }
}
</script>
<style scoped lang="scss">
.hasContent {
  padding: 60px 60px 117px 60px;
}

.outlineContent {
  // width: 760px;
  max-height: 670px;
  overflow: auto;
  margin-top: 24px;
  padding: 24px;
  box-shadow: 0px 1px 8px 0px rgba(0, 0, 0, 0.06);
}

// 点击挑选ppt按钮
.pptBtnCss {
  margin-left: 16px;
  color: #fff;
  background-image: linear-gradient(to left, #b06ab3 0%, #6b70fb 100%);
}
</style>
