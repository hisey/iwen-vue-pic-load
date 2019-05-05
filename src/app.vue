<template>
  <div class="upload-box">
    <div :class="['clearfix',listType?'pic-list-box':'pic-box']">
      <div
        v-for="(item,index) in picList"
        :key=index
        class="f-l pic-item"
      >
        <img
          :src="item.url"
          alt=""
          :style="`width:${imgWidth};height: ${imgHeight};border-radius:${round?'50%':'0'}`"
        >
        <i
          v-if="isShowClose"
          class="el-icon-circle-close close"
          @click="handleRemove(index)"
        ></i>
      </div>
      <div
        v-show="picList.length < limit"
        class="add-box f-l"
        :style="`width:${imgWidth};height: ${imgHeight};line-height:${imgHeight}`"
      >
        <i
          v-if="isLoading"
          class="el-icon-loading"
        ></i>
        <i
          v-if="!isLoading"
          class="el-icon-plus"
        ></i>
        <input
          :disabled="disabled"
          type="file"
          class="input-file"
          :style="`width:${imgWidth};height:${imgHeight};`"
          :name="index"
          ref="picInput"
          @change="changeImage($event)"
          accept="image/gif,image/jpeg,image/jpg,image/png"
        >
      </div>
    </div>
    <p class="tip">{{tip}}</p>
  </div>
</template>
<script>
export default {
  name: "upload",
  data() {
    return {
      isLoading: false,
      picList: this.value
    };
  },
  props: {
    value: {
      type: [String, Array, Object],
      default: () => {
        return [];
      }
    },
    imgUrl: {
      type: String,
      default: ""
    },
    imgWidth: {
      type: String,
      default: ""
    },
    imgHeight: {
      type: String,
      default: ""
    },
    tip: {
      type: String,
      default: ""
    },
    listType: {
      type: Boolean,
      default: false
    },
    round: {
      type: Boolean,
      default: false
    },
    index: {
      type: Number,
      default: 0
    },
    limit: {
      type: Number,
      default: 1
    },
    fileName: {
      type: String,
      default: "file"
    },
    maxSize: {
      type: Number,
      default: 8
    },
    tokenName: {
      type: String,
      default: "token"
    },
    token: {
      type: String,
      default: ""
    },
    disabled: {
      type: Boolean,
      default: false
    },
    uploadUrl: {
      type: String,
      default: ""
    }
  },
  computed: {
    isShowClose() {
      return this.picList.length > 0 ? true : false;
    }
  },
  methods: {
    clearValue() {
      this.$refs.picInput.value = "";
    },
    changeImage: function(e) {
      let file = e.target.files[0];
      if (file) {
        let formData = new FormData();
        const isLt8M = file.size / 1024 / 1024 < this.maxSize;
        if (!isLt8M) {
          this.$message.error(`上传的图片大小不能超过 ${this.maxSize}MB!`);
          return false;
        }
        // 文件对象
        formData.append(this.fileName, file);
        formData.append(this.tokenName, this.token);
        // formData.append("licenseCode", this.fileInput);
        this.isLoading = true;
        this.clearValue();
        xhr = new XMLHttpRequest(); // XMLHttpRequest 对象
        xhr.open("post", this.uploadUrl, true); //post方式，url为服务器请求地址，true 该参数规定请求是否异步处理。
        xhr.send(formData); //开始上传，发送form数据
        xhr.onreadystatechange = function() {
          if (res.status == 200 && res.data) {
            let data = xhr.responseText.data.result;
            this.$message.success("上传成功！");
            this.isLoading = false;
            this.picList.push({ id: data.id, url: data.url });
            this.$emit("input", this.picList);
            this.$emit("uploadSuccess", this.picList);
          } else {
            this.$message.error("上传失败！");
          }
        };
      }
    },
    handleRemove(index) {
      this.clearValue();
      this.picList.splice(index, 1);
      this.$emit("input", this.picList);
      this.$emit("onRemove", this.picList);
    }
  }
};
</script>
<style lang="less" scope>
.upload-box {
  width: 830px;
  .tip {
    margin-top: 5px;
    color: #999;
  }
  .add-box {
    position: relative;
    background: rgba(255, 255, 255, 1);
    border: 1px solid rgba(214, 214, 214, 1);
    border-radius: 2px;
    text-align: center;
    i {
      font-size: 35px;
      color: #cfcfcf;
    }
    .input-file {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      opacity: 0;
      cursor: pointer;
    }
    .text {
      padding-top: 10px;
      color: lightblue;
    }
  }
  .pic-item {
    position: relative;
    margin-right: 20px;
    margin-bottom: 20px;
    border: 1px solid #ececec;
    .close {
      cursor: pointer;
      position: absolute;
      top: -10px;
      right: -10px;
      color: #333;
      font-size: 20px;
    }
    img {
      display: block;
    }
  }
}

.clearfix:after {
  content: ".";
  display: block;
  height: 0;
  font-size: 0;
  clear: both;
  visibility: hidden;
}

.clearfix {
  zoom: 1;
}
.f-l {
  float: left;
}
.f-r {
  float: right;
}
</style>
