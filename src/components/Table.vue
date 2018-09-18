<template>
  <table>
    <tbody>
    <tr v-for="el in matrix">
      <td v-for="value in el.array">
        <div v-if="value === 1"></div>
        <div v-else>{{value}}</div>
      </td>
    </tr>
    </tbody>
  </table>
</template>

<script>
export default {
  name: "Table",
  data: function () {
    return {
      gridSize: 4,
      matrix : [
        {array: [1, 1, 1, 1]},
        {array: [1, 1, 1, 1]},
        {array: [1, 1, 1, 1]},
        {array: [1, 1, 1, 1]}
      ]
    }
  },
  beforeMount: function () {
    this.setNew(2)
    this.setNew(2)
  },
  methods: {
    freePosition: function() {
      let freeArray = []
      for(let i = 0; i < this.gridSize; i++) {
        for(let j = 0; j < this.gridSize; j++) {
          if(this.matrix[i].array[j] === 1) freeArray.push({i: i, j: j})
        }
      }
      let randomElem = freeArray[Math.floor(Math.random()*freeArray.length)]
      return freeArray.length !== 0 ? {i: randomElem.i, j: randomElem.j} : {}
    },
    setNew: function (number) {
      let position = this.freePosition()
      if(Object.keys(position).length === 0) {
        alert('Game over!')
        return
      }
      this.$set(this.matrix[position.i].array, position.j, number)
    },


    findMoveLeft: function() {
      array = []
      for(let i = 0; i < this.gridSize; i++) {
        for(let j = 0; j < this.gridSize; j++) {
          if(this.matrix[i].array[j] === 1) freeArray.push({i: i, j: j})
        }
      }
    }
  }
}
</script>

<style scoped>
  table {
    border: 2px solid #42b983;
    border-radius: 3px;
    background-color: #fff;
  }

  th {
    background-color: #42b983;
    color: rgba(255,255,255,0.66);
    cursor: pointer;
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
  }

  td {
    background-color: #f9f9f9;
    height: 10px;
  }

  th, td {
    min-width: 120px;
    padding: 10px 20px;
  }

</style>
