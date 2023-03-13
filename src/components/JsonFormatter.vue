<template>
  <div class="json_box">
    <div class="title">
      <span>下方是为标准功能和自定义功能自动生成的JSON格式协议</span>
      <span>
        <a-icon
          @click="downloadJSON"
          type="download"
          style="cursor: pointer"
        />&nbsp;
        <a-icon @click="copyText" type="copy" style="cursor: pointer" />
      </span>
    </div>
    <div class="format_box">
      <div class="idx">
        <span v-for="i in lineNum" :key="i">{{ i }}</span>
      </div>
      <div class="format" ref="pre" v-html="stringData"></div>
    </div>
  </div>
</template>

<script>
export default {
  name: "JsonFormatter",
  props: {
    data: {
      required: true,
      type: Object
    }
  },
  data() {
    return {
      stringData: "",
      lineNum: 0
    };
  },
  methods: {
    init() {
      this.stringData = JSON.stringify(this.data, null, 2);
    },
    copyText() {
      const input = document.createElement("input"); // 创建input对象
      input.value = JSON.stringify(this.data); // 设置复制内容
      document.body.appendChild(input); // 添加临时实例
      input.select(); // 选择实例内容
      document.execCommand("copy"); // 执行复制
      document.body.removeChild(input); // 删除临时实例
      this.$message.success("复制成功！");
    },
    downloadJSON() {
      let str = JSON.stringify(this.data);
      const blob = new Blob([str], { type: "application/json;charset=utf-8" });
      const href = URL.createObjectURL(blob);
      const alink = document.createElement("a");
      alink.style.display = "none";
      alink.download =
        this.data?.profile?.productID || Math.floor(Math.random() * 10) + ""; // 下载后文件名
      alink.href = href;
      document.body.appendChild(alink);
      alink.click();
      document.body.removeChild(alink); // 下载完成移除元素
      URL.revokeObjectURL(href); // 释放掉blob对象
    }
  },
  mounted() {
    this.init();
    setTimeout(() => {
      // console.log(this.$refs.pre.scrollHeight)
      this.lineNum = Math.floor(this.$refs.pre.scrollHeight / 16.5); // 16.5 为行高
    }, 100);
  }
};
</script>

<style lang="less" scoped>
.json_box {
  width: 470px;

  .title {
    display: flex;
    justify-content: space-between;
    margin-bottom: 10px;
  }
  .format_box {
    width: 100%;
    margin-right: 10px;
    border: 1px solid #000;
    padding: 5px;
    max-height: 500px;
    overflow-y: scroll;
    display: flex;
    line-height: 16.5px;
    font-family: "Roboto Mono", Monaco, monospace;
    font-size: 11px;
    .idx {
      width: 30px;
      display: flex;
      flex-direction: column;
      user-select: none;
    }
    .format {
      flex: 1;
      white-space: pre;
      font-family: "Roboto Mono", Monaco, monospace;
    }
  }
}
</style>
