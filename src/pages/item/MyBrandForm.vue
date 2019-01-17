<template>
  <v-form v-model="valid" ref="brandForm">

    <v-text-field v-model="brand.name" label="品牌名称" required
                  :rules="[v=>!!v||'品牌名称为空']"
    />
    <v-text-field v-model="brand.letter" label="品牌首字母" required
                  :rules="[v=>!!v||'品牌字母不能为空']" mask="A"
    />
    <!--级联控件-->
    <v-cascader
      url="/item/category/list"
      multiple required
      v-model="brand.categories"
      label="选择商品分类"
    />
    <v-layout row>
      <v-flex xs3 class="subheader">品牌LOGO:</v-flex>
      <v-flex>
        <!--上传图片控件-->
        <v-upload
          v-model="brand.image"
          url="/item/upload"
          :multiple="false"
          :pic-width="250"
          :pic-height="90"
        />

      </v-flex>
    </v-layout>

    <!--添加2个按钮-->
    <v-layout row class="pt-5">
      <v-spacer/>
      <v-btn color="primary" @click="submit">提交</v-btn>

      <v-btn color="warning">重置</v-btn>

    </v-layout>

  </v-form>
</template>

<script>
  export default {
    name: "MyBrandForm",
    data() {
      return {
        valid: false,
        brand: {
          name: "",
          letter: "",
          image: "",
          category: []
        }
      }
    },
    methods: {
      submit() {
        //  form表单的校验 validate
        if (this.$refs.brandForm.validate()) {
          //校验通过
          console.log("通过校验");
          //因为categories 中是对象 有 name  我们只想要id的数组
          const {categories, ...rest} = this.brand
          //这一步取出的只有 rest={name,letter,image}
          //给rest添加属性
          rest.categories = categories.map(c => c.id).join(",")
          //  post 提交表单
          this.$http.post("/item/brand", this.$qs.stringify(rest))
            .then(respon => {
              console.log("提交成功");
            }).catch(() => {
            console.log("提交失败");
          });

        } else {
          //校验没通过
          console.log("没通过");
        }

      },
    }
  }
</script>

<style scoped>

</style>
