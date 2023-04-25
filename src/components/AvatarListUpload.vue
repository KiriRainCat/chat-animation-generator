<template>
  <div style="margin-bottom: 20px;">
    <div style="font-size: 14px; margin-bottom: 10px;">{{ label }}</div>
    <el-upload
      class="avatar-uploader"
      v-model:file-list="imageList"
      list-type="picture-card"
      :on-remove="handleRemove"
      :auto-upload="false"
    >
      <el-icon><Plus /></el-icon>
    </el-upload>
  </div>
</template>

<script setup>
import {Plus} from "@element-plus/icons-vue";
import {ref, watch} from "vue";

defineProps({
  label: String,
})

const emit = defineEmits(["urls"])

// ==== Image ==== //
const imageList = ref([]);

watch(imageList, () => {
  emit("urls", imageList.value);
})
</script>

<style>
.avatar-uploader .avatar {
  width: 144px;
  height: 144px;
  display: block;
  object-fit: cover;
}

.avatar-uploader .el-upload {
  border: 1px dashed var(--el-border-color);
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
  transition: var(--el-transition-duration-fast);
}

.avatar-uploader .el-upload:hover {
  border-color: var(--el-color-primary);
}

.el-icon.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 144px;
  height: 144px;
  text-align: center;
}
</style>