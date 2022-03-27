<template>
  <div class="hello">
    <el-button type="primary">111</el-button>
    <MyTree
      v-if="data.length > 0"
      :data.sync="data"
      :fileDrop="fileDrop"
      :diectoryDrop="diectoryDrop"
      :delete="deleteFile"
    />
  </div>
</template>

<script>
import MyTree from './MyTree'
export default {
  name: 'HelloWorld',
  components: {
    MyTree
  },
  data() {
    return {
      data: [],
      fileDrop: [
        {text: 'rm', value: '删除文件'}
      ],
      diectoryDrop: [
        {text: 'rn', value: '修改名字'},
        {text: 'rm', value: '删除文件'}
      ],
       parent: [
        {name:'文件夹1', pid: 0, id: 1},
        {name:'文件夹2', pid: 0, id: 2},
        {name:'文件夹3', pid: 0, id: 3},
        {name:'文件夹1-1', pid: 1, id: 4},
        {name:'文件夹2-1', pid: 2, id: 5},
      ],
      child: [
        {name:'文件夹1', pid: 1, id: 10001},
        {name:'文件夹2', pid: 1, id: 10002},
        {name:'文件夹2-1', pid: 2, id: 10003},
        {name:'文件夹2-2', pid: 2, id: 10004},
        {name:'文件夹1-1-1', pid: 4, id: 10005},
        {name:'文件夹2-1-1', pid: 5, id: 10006},
      ],
    }
  },
  created() {
    this.init()
  },
  methods: {
    init() {
      this.parent.forEach((p) => p.type = 'parent')
      this.data = [...this.parent, ...this.child]
    },
    deleteFile(id) {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          resolve()
        })
      })
    }
  }
}
</script>
<style scoped lang="scss">
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
