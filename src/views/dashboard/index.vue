<template>
  <div class="dashboard-container">
    <el-table
      :data="list"
      style="width: 100%">
      <el-table-column
        prop="id"
        label="id"
        width="180">
      </el-table-column>
      <el-table-column
        prop="label"
        label="label">
      </el-table-column>
    </el-table>
    <el-button v-on:click="queryList()" style="margin-top: 20px;">queryList</el-button>
    <el-button v-on:click="addList()" style="margin-top: 20px;">addList</el-button>
  </div>
</template>

<script>
import gql from 'graphql-tag'
export default {
  name: 'Dashboard',
  data() {
    return {
      list: []
    }
  },
  methods: {
    queryList() {
      // 调用 graphql 变更
      this.$apollo.query({
        // 查询语句
        query: gql`query list{
          list {
            id,
            label
          }
        }`
      }).then((data) => {
        // 结果
        this.data = res.data && res.data.list || []
      })
    },
    addList() {
      this.$apollo.mutate({
        // 查询语句
        mutation: gql`mutation ($label: String) {
          addList(label: $label) {
            id
            label
          }
        }`,
        update: (store, { data: { addList } }) => {
          // 从缓存中读取这个查询的数据
          const data = store.readQuery({ query: gql`query list{
            list {
              id,
              label
            }
          }` })
          // 将变更中的标签添加到最后
          data.list.push(addList)
          // 将数据写回缓存
          store.writeQuery({ query: gql`query list{
            list {
              id,
              label
            }
          }`, data })
        },
      }).then((data) => {
        // 结果
        console.log(data)
      })
    },
  },
  apollo: {
    list: gql`query list{
      list {
        id,
        label
      }
    }`
  }
}
</script>
