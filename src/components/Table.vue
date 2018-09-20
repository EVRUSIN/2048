<template>
  <div id="table">
    <GlobalEvents @keyup="keyMonitor" v/>
    <table id="bootstrap-overrides">
      <thead>
      <tr>
        <th> <span class="score">Score:</span> </th>
        <th> <span class="score">{{score}}</span> </th>
        <th> </th>
        <th> <button v-on:click="reset">New Game</button></th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="el in matrix">
        <td v-for="value in el.array" v-bind:class="'tile-' + value">
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
  import bButton from 'bootstrap-vue/es/components/button/button';

  export default {
    components: {
      GlobalEvents,
      bButton
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
        freeCells: Number,
        score: 0
      }
    },

    mounted: function() {
      if (localStorage.getItem('matrix') && localStorage.score) {
        try {
          this.matrix = JSON.parse(localStorage.getItem('matrix'));
          this.score = parseInt(localStorage.score)
        } catch(e) {
          localStorage.removeItem('matrix');
        }
        }  else {
        for (let i = 0; i < this.startCells; i++) {
          this.addTile()
        }
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
        this.saveMatrix();
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
            this.score += modifiedRow[i-1]
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
      },
      reset: function() {
        Object.assign(this.$data, this.$options.data());
        for(let i = 0; i < this.startCells; i++) {
          this.addTile()
        }
      },

      saveMatrix: function() {
        const parsed = JSON.stringify(this.matrix);
        localStorage.setItem('matrix', parsed);
        localStorage.score = this.score;
      }
    }





  }
</script>

<style scoped>
   table {
    border: 22px solid #faf8ef;
  }

   td {
    width: 97px;
    height: 97px;
    font-size: 40px;
  }
  .tile-1 {
    background: #bbada0;
  }
  .tile-2 {
    background: #eee4da;
  }
  .tile-4 {
    background: #ede0c8;
  }
  .tile-8 {
    color: #f9f6f2;
    background: #f2b179;
  }
  .tile-16 {
    color: #f9f6f2;
    background: #f59563;
  }
  .tile-32 {
    color: #f9f6f2;
    background: #f67c5f;
  }
  .tile-64 {
    color: #f9f6f2;
    background: #f65e3b;
  }
  .tile-128 {
    color: #f9f6f2;
    background: #edcf72;
  }
  .tile-256 {
    color: #f9f6f2;
    background: #edcc61;
  }
  .tile-512 {
    color: #f9f6f2;
    background: #edc850;
  }
  .tile-1024 {
    color: #f9f6f2;
    background: #edc53f;
  }
  .tile-2048 {
    color: #f9f6f2;
    background: #edc22e;
  }
  th{
    position: relative;
    background: #a0ada0;
    font-size: 15px;
    height: 40px;
    font-weight: bold;
    border-radius: 3px;
    color: white;
    text-align: center;
  }

</style>
