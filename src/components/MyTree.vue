<template>
  <div class="container">
    <el-tree
      :data="allData"
      default-expand-all
      :expand-on-click-node="false"
      :render-content="render"
    >
    </el-tree>
  </div>
</template>

<script>
export default {
  name: 'MyTree',
  props: {
    data: {
      type: Array,
      default: () => []
    },
    fileDrop: {
      type: Array,
      default: () => []
    },
    diectoryDrop: {
      type: Array,
      default: () => []
    },
    delete: {
      type: Function
    }
  },
  data() {
    return {
      allData: [],
      currentId: '',
      currentContent: ''
    };
  },
  watch: {
    data() {
      this.formatData()
    }
  },
  created() {
    },
  mounted() {
    this.formatData()
  },

  methods: {
    isParent(data) {
      return data.type === 'parent'
    },
    handleInput(v) {
      this.currentContent = v
    },
    ok(data) {
      let list = JSON.parse(JSON.stringify(this.data))
      let item = this.data.find(i => i.id == data.id)
      item.name = this.currentContent
      this.currentId = ''
      this.$emit('update:data', list)
      this.$message({
        type: 'success',
        message: '修改成功！'
      })
    },
    cancel(data) {
      this.currentId = ''
    },
    handleCommand(data, value) {
      console.log('data: ', data)
      switch (value) {
        case 'rn':
          this.handleRename(data)
          break
        case 'rm':
          this.handleRemove(data)
          break
      }

    },
    handleRename(data) {
      this.currentId = data.id
      this.currentContent = data.name
    },
    remove(id) {
      let list = JSON.parse(JSON.stringify(this.data))
      list = this.data.filter(i => i.id !== id)
      this.$emit('update:data', list)
      this.$message({
        type: 'success',
        message: '删除成功！'
      })
    },
    handleRemove(data) {
      this.$confirm(`此操作将永久删除: ${data.name}, 是否继续?`, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.delete? this.delete(data.id).then(() => {
          this.remove(data.id)
        }): this.remove(data.id)
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        })         
      })
    },
    render(h, {node, data, store}) {
      const list = this.isParent(data) ? this.diectoryDrop : this.fileDrop
      return (
        <div style={{width:'100%', textAlign: 'left'}}>
          {
            this.isParent(data) ?
            node.expanded ? 
              <i class="el-icon-folder-opened"></i> :
              <i class="el-icon-folder"></i> :
            <i class="el-icon-document"></i>
          }
          {
            data.id === this.currentId ?
              <el-input
                style={{width:'50%'}}
                value={this.currentContent}
                on-input={this.handleInput}
                /> : data.name
          }

          {
            data.id !== this.currentId ?
              <el-dropdown
                placement="bottom-start"
                trigger="click"
                style={{float: 'right'}}
                on-command={this.handleCommand.bind(this, data)}
              >
                <span class="el-dropdown-link">
                  <i class="el-icon-arrow-down el-icon--right"></i>
                </span>
                <el-dropdown-menu slot="dropdown">
                  { list.map((i) => (
                    <el-dropdown-item command={i.text}>{i.value}</el-dropdown-item>
                  ))}
                </el-dropdown-menu>
              </el-dropdown> : <span style={{float: 'right'}}>
                <el-button type="text" on-click={this.ok.bind(this, data)}>确认</el-button>
                <el-button type="text"  on-click={this.cancel.bind(this, data)}>取消</el-button>
              </span>
          }

          
        </div>
      )

    },
    formatData() {
      const _allData = this.data
      let treeMapList = _allData.reduce((memo, current) => {
        memo[current.id] = current
        return memo
      }, {})
      const result = _allData.reduce((array, current) => {
        const pid = current.pid
        const parent = treeMapList[pid]
        if (parent) {
          parent.children ? parent.children.push(current) : parent.children = [current]
        } else if (pid === 0) {
          array.push(current)
        }
        return array
      }, [])
      this.allData = result
      console.log('_allData: ', this.allData);
    }
  },
};
</script>

<style lang="scss" scoped>
.container {
  width: 40%;
}
.el-dropdown {
  float: right;
}
.el-tree {
  width: 100%;
  &>>> .el-input {
    width: 30%!important;
  }
}
.el-tree .el-tree-node__content .el-input {
  width: 30%!important;
}
</style>