<template>
  <v-card>

    <v-card-title class="layout row">
      <!--添加品牌的按钮-->


      <v-btn color="primary" @click="show=true">新增品牌</v-btn>

      <!--填充中间空间-->
      <v-spacer/>

      <v-text-field label="请输入要搜索的关键字" append-icon="search" single-line hide-details class="flex sm3"
                    v-model="search"></v-text-field>
    </v-card-title>
    <!--分割线-->
    <v-divider></v-divider>

    <v-data-table
      :headers="headers"
      :items="brands"
      :pagination.sync="pagination"
      :total-items="totalBrands"
      :loading="loading"
      class="elevation-1"
    >
      <template slot="items" slot-scope="props">
        <td class="text-sm-center">{{ props.item.id }}</td>
        <td class="text-sm-center">{{ props.item.name }}</td>
        <td class="text-sm-center"><img :src="props.item.image"></td>
        <td class="text-sm-center">{{ props.item.letter }}</td>
        <td class="justify-center layout px-1">
          <v-btn small color="info">编辑</v-btn>
          <v-btn small color="warning">删除</v-btn>
        </td>
      </template>
    </v-data-table>

    <!--dialog弹窗-->
    <v-dialog v-model="show" max-width="500" scrollable persistent>
      <v-card>
        <v-toolbar color="primary" dense dark class="subheading px-2">
          <span> 新增品牌</span>
          <v-spacer/>
          <v-btn icon @click="show=false">
            <v-icon>close</v-icon>
          </v-btn>
        </v-toolbar>
        <v-card-text style="height: 600px" class="px-5">
          <!--这里是写表单的-->
          <my-brand-form>

          </my-brand-form>
        </v-card-text>

      </v-card>

    </v-dialog>

  </v-card>


</template>

<script>
  export default {
    name: "MyBrand",
    data() {
      return {
        headers: [
          {text: "id", value: "id", align: 'center', sortable: true},
          {text: "名称", value: "name", align: 'center', sortable: false},
          {text: "图片", value: "image", align: 'center', sortable: false},
          {text: "首字母", value: "letter", align: 'center', sortable: false},
          {text: '操作', value: 'name', align: 'center', sortable: false}
        ],
        brands: [],
        pagination: {},
        totalBrands: 0,
        loading: false,
        search: "",
        show: false


      }
    },

    watch: {
      pagination: {
        deep: true,
        handler() {
          this.getDatasFromService()
        }
      },
      search() {
        this.getDatasFromService()
      }

    },
    created() {
      this.getDatasFromService()
    },

    methods: {
      getDatasFromService() {
        this.loading = true
        this.$http.get("/item/brand/page", {
          params: {
            page: this.pagination.page,
            rows: this.pagination.rowsPerPage,
            sortBy: this.pagination.sortBy,
            desc: this.pagination.descending,
            key: this.search,
          }
        }).then(respon => {
          console.log(respon);
          //赋值

          this.brands = respon.data.items
          this.totalBrands = respon.data.total
          this.loading = false

        }).catch(rejest => {
          console.log(rejest);
        })


        // setTimeout(() => {
        //   this.brands = datas
        //   this.totalBrands = 10
        //   this.loading = false
        // }, 1000)
      }

    },
    components: {
      //  导入表单
      MyBrandForm: () => import("./MyBrandForm")
    }
  }
</script>

<style scoped>

</style>
