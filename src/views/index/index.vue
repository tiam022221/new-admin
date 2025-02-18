<template>
  <div class="cus">
    <div class="flex">
      <v-button @click="loading = !loading">loading</v-button>
      <v-button @click="getDetails">发送请求</v-button>
      <v-button @click="confirm" status="success">提交</v-button>
    </div>
    <div class="flex">
      <v-button type="primary" :loading="loading">
        <template #default>按钮</template>
        <template #icon>
          <IconPlus />
        </template>
      </v-button>
      <v-button type="secondary">按钮</v-button>
      <v-button type="dashed">按钮</v-button>
      <v-button type="outline">按钮</v-button>
      <v-button type="text">按钮</v-button>
    </div>
    <div class="flex">
      <v-button shape="square">按钮</v-button>
      <v-button radius="4px">按钮</v-button>
      <v-button shape="circle">按钮</v-button>
      <v-button shape="round" :loading="loading">
        <template #icon>
          <IconPlus />
        </template>
      </v-button>
    </div>
    <div class="flex">
      <v-button status="default">按钮</v-button>
      <v-button status="success">按钮</v-button>
      <v-button status="warning">按钮</v-button>
      <v-button status="danger">按钮</v-button>
    </div>
    <div class="flex">
      <v-button size="mini">按钮</v-button>
      <v-button size="small">按钮</v-button>
      <v-button size="medium">按钮</v-button>
      <v-button size="large">按钮</v-button>
    </div>
    <div class="flex">
      <v-upload :buttonProps="{ loading }" :isDirectory="true" v-model="files"
        >选择文件夹</v-upload
      ><v-upload
        :file-size="10 * 1024"
        :buttonProps="{ loading }"
        v-model="files"
        >选择文件</v-upload
      >
    </div>
    <div class="flex">
      <v-form ref="formRef" @confirm="confirm" :loading="loading">
        <v-form-item
          ref="avatarRef"
          required
          label="用户头像:"
          :value="formData.avatar"
          :requiredErr="requiredErr"
          info="文件大小不超过10KB,支持jpg/png格式."
        >
          <v-upload
            :multiple="false"
            :file-size="10 * 1024"
            isClear
            type="avatar"
            :buttonProps="{ loading }"
            :model-value="formData.avatar"
            @update:model-value="handleAvatarChange"
          />
        </v-form-item>
        <v-form-item label="姓名:">{{ formData.name }}</v-form-item>
        <v-form-item label="性别:" :value="formData.sex"></v-form-item>
        <v-form-item
          :regex="/^\d+$/"
          required
          label="身高:"
          :value="formData.height"
        >
          <a-input v-model="formData.height"
        /></v-form-item>
        <v-form-item
          ref="inputRef"
          required
          :value="formData.weight"
          label="体重:"
        >
          <a-input v-model="formData.weight" />
        </v-form-item>
        <v-form-item
          :regex="/^\d+$/"
          required
          label="资产:"
          :value="formData.money"
        >
          <a-input v-model="formData.money"
        /></v-form-item>
        <v-form-item
          ref="cascaderValidate"
          required
          label="地址:"
          :value="formData.address"
        >
          <a-cascader
            :allow-clear="true"
            v-model="formData.address"
            :options="options"
            expand-trigger="hover"
            placeholder="Please select ..."
            @change="handleChange"
        /></v-form-item>
      </v-form>
    </div>
  </div>
</template>

<script setup lang="ts">
import { IconPlus } from "@arco-design/web-vue/es/icon";
import type VFrom from "@/components/v-form/v-form.vue";
import type VFormItem from "@/components/v-form/v-form-item.vue";
// import axios from "axios";

const loading = ref(false);
const options = ref([
  {
    value: "beijing",
    label: "Beijing",
    children: [
      {
        value: "chaoyang",
        label: "ChaoYang",
        children: [
          {
            value: "datunli",
            label: "Datunli",
          },
        ],
      },
      {
        value: "haidian",
        label: "Haidian",
      },
      {
        value: "dongcheng",
        label: "Dongcheng",
      },
      {
        value: "xicheng",
        label: "Xicheng",
        children: [
          {
            value: "jinrongjie",
            label: "Jinrongjie",
          },
          {
            value: "tianqiao",
            label: "Tianqiao",
          },
        ],
      },
    ],
  },
  {
    value: "shanghai",
    label: "Shanghai",
    children: [
      {
        value: "huangpu",
        label: "Huangpu",
      },
    ],
  },
]);
const cascaderValidate = ref() as Ref<InstanceType<typeof VFormItem>>;
const handleChange = async (value: string) => {
  formData.value.address = value;
  await cascaderValidate.value?.validate();
};
const avatarRef = ref() as Ref<InstanceType<typeof VFormItem>>;
const requiredErr = ref("");

const handleAvatarChange = async (
  value: {
    name: string;
    type: string;
    id: string | number;
    url: string;
    isError: boolean;
  }[]
) => {
  formData.value.avatar = value;
  requiredErr.value = value[0]?.isError
    ? "文件大小超出限制，请重新上传！"
    : value.length === 0
      ? "用户头像为必填项！"
      : "";
  await avatarRef.value?.validate();
};
const formData = ref<
  Partial<{
    avatar: {
      name: string;
      type: string;
      id: string | number;
      url: string;
      isError: boolean;
    }[];
    name: string;
    sex: string;
    height: string;
    weight: string;
    money: string;
    address: string;
  }>
>({});
const files = ref<
  {
    name: string;
    type: string;
    id: string | number;
    url: string;
    isError: boolean;
  }[]
>([]);
const formRef = ref() as Ref<InstanceType<typeof VFrom>>;
const getDetails = async () => {
  formData.value = {
    name: "张三",
    sex: "男",
    height: "180",
    weight: "60kg",
    money: "180w",
  };
  await formRef.value.valid();
  // const url = "https://test-api.yokoyee.com/api/goods/Index/item";
  // await axios
  //   .post(url, { id: 251 })
  //   .then((response) => {
  //     // 请求成功后的处理
  //     console.log("响应数据:", response.data);
  //     const data = response.data;
  //     files.value = data.data.item.image.slice(0, 2).map((item, index) => {
  //       return {
  //         name: "图片",
  //         type: "image",
  //         id: index,
  //         url: item,
  //       };
  //     });
  //   })
  //   .catch((error) => {
  //     // 请求失败后的处理
  //     console.error("请求失败:", error);
  //   });
};
const confirm = async () => {
  const isValid = await formRef.value.valid();
  if (!isValid) return;
  loading.value = true;
  setTimeout(() => {
    loading.value = false;
  }, 2000);
};
</script>

<style lang="scss" scoped>
.cus {
  display: flex;
  flex-direction: column;
  gap: 20px;
  padding: 100px;
}
.flex {
  display: flex;
  align-items: center;
  gap: 20px;
}
</style>
