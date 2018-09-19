<template>
  <div id="table">
    <GlobalEvents @keyup="keyMonitor" v/>
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
  </div>
</template>

<script>
import GlobalEvents from 'vue-global-events'
export default {
  components: {
    GlobalEvents
    },
  name: "Table",
  data: function() {
    return {
      gridSize: 4,
      matrix : [
        {array: [1, 1, 1, 1]},
        {array: [1, 1, 1, 1]},
        {array: [1, 1, 1, 1]},
        {array: [1, 1, 1, 1]}
      ],
      maxValue: 2,
      wonValue: 2048,
      startCells: 2,
      freeCells: Number
    }
  },
  beforeMount: function () {
    for(let i = 0; i < this.startCells; i++) {
      this.addTile()
    }
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
      this.freeCells = freeArray.length
      return freeArray.length !== 0 ? {i: randomElem.i, j: randomElem.j} : {}
    },

    addTile: function () {
      let position = this.freePosition()
      let value = Math.random() < 0.9 ? 2 : 4;
      this.$set(this.matrix[position.i].array, position.j, value)
      if(this.freeCells === 1) {
        return this.isGameOver()
      }
    },

    isGameOver: function () {
      if(!this.moveLeft(false) && !this.moveUp(false) && !this.moveRight(false) && !this.moveDown(false)) alert('Game over!')
    },

    moveLeft: function(real) {
      let able = false
      for(let i = 0; i < this.gridSize; i++) {
        let row = []
        for(let j = 0; j < this.gridSize; j++) {
          row.push(this.matrix[i].array[j]);
        }
        if(this.checkMovable(row)) {
          able = true
          if(real) {
            row = this.move(row)
            row = this.mergeCells(row)
            row = this.move(row)
            this.$set(this.matrix, i, {array : row})
          }
        }
      }
      if(able && real) this.addTile()
      return able
    },

    moveRight: function(real) {
      let able = false
      for(let i = 0; i < this.gridSize; i++) {
        let row = []
        for(let j = this.gridSize - 1; j >= 0; j--) {
          row.push(this.matrix[i].array[j]);
        }
        if(this.checkMovable(row)) {
          able = true
          if(real) {
            row = this.move(row)
            row = this.mergeCells(row)
            row = this.move(row)
            this.$set(this.matrix, i, {array : row.reverse()})
          }
        }
      }
      if(able && real) this.addTile()
      return able
    },

    moveUp: function(real) {
      let able = false
      for(let j = 0; j < this.gridSize; j++) {
        let row = []
        for(let i = 0; i < this.gridSize; i++) {
          row.push(this.matrix[i].array[j]);
        }
        if(this.checkMovable(row)) {
          able = true
          if(real) {
            row = this.move(row)
            row = this.mergeCells(row)
            row = this.move(row)
            for(let i = 0; i < this.gridSize; i++) {
              this.$set(this.matrix[i].array, j, row[i])
            }
          }
        }
      }
      if(able && real) this.addTile()
      return able
    },

    moveDown: function(real) {
      let able = false
      for(let j = 0; j < this.gridSize; j++) {
        let row = []
        for(let i = this.gridSize - 1; i >= 0; i--) {
          row.push(this.matrix[i].array[j]);
        }
        if(this.checkMovable(row)) {
          able = true
          if(real) {
            row = this.move(row)
            row = this.mergeCells(row)
            row = this.move(row)
            for(let i = this.gridSize - 1; i >= 0; i--) {
              this.$set(this.matrix[i].array, j, row[this.gridSize - i - 1])
            }
          }
        }
      }
      if(able && real) this.addTile()
      return able
    },

    checkMovable: function(row) {
      for(let i = 1; i < this.gridSize; i++) {
        if(row[i] > 1 && (row[i-1] === row[i] || row[i-1] === 1)) return true
      }
      return false
    },

    move: function (row) {
      let modifiedRow = []
      for(let i = 0; i < this.gridSize; i++) {
        if(row[i] > 1) modifiedRow.push(row[i])
      }
      for(let i = modifiedRow.length; i < this.gridSize; i++) modifiedRow.push(1)
      return modifiedRow
    },

    mergeCells: function (row) {
      let modifiedRow = []
      modifiedRow = row
      for(let i = 1; i < this.gridSize; i++) {
        if(modifiedRow[i] === modifiedRow[i-1] && modifiedRow[i] !== 1) {
          modifiedRow[i-1] *= 2
          modifiedRow[i] = 1
          if(modifiedRow[i-1] > this.maxValue) this.maxValue = modifiedRow[i-1]
          if(this.maxValue === this.wonValue) alert('you won!')
          i++
        }
      }
      return modifiedRow
    },
    keyMonitor: function(event) {
      if(event.key === 'ArrowLeft') this.moveLeft(true)
      if(event.key === 'ArrowRight') this.moveRight(true)
      if(event.key === 'ArrowUp') this.moveUp(true)
      if(event.key === 'ArrowDown') this.moveDown(true)
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
