<template>
<div>

  <!-- ######################################################## -->
  <div>
    <h3>タスクカレンダー/サンプル</h3>
  </div>

  <!-- カレンダーヘッダ -->
  <div id="cal-header">
    <span class="header-arrow" v-on:touchstart="setLastMonth">＜</span>
    <span class="selected-month">{{year}}年{{month}}月</span>
    <span class="header-arrow" v-on:touchstart="setNextMonth">＞</span>
  </div>

  <!-- カレンダーメイン -->
  <div>
    <table id="cal-main">
      <!-- 曜日を表示させる（テーブルヘッダ） -->
      <thead>
        <th v-for="(dayname,index) in weekdays" :key="index">{{dayname}}</th>
      </thead <!-- 日付を表示させる（テーブルボディ） -->
      <tbody>
        <tr v-for="(weekData,index) in calData" :key="index">
          <!-- <td class="cal-day" v-for="(dayNum,index) in weekData" :key="index"> -->
          <td class="cal-day" v-for="(dayNum,index) in weekData" :key="index" v-on:touchstart="dateClick(dayNum)" :class="{'cal-today': isToday(dayNum), active: day === dayNum}">
            <span v-if="isToday(dayNum)">今日</span>
            <span v-else>{{dayNum}}</span>
            <!-- <span>{{dayNum}}</span> -->
          </td>
        </tr>
      </tbody>
    </table>
  </div>


  <!-- ######################################################## -->
  タスク追加
  <div class="task">
    <p>
      ■Title:
      <select v-model="newTitle">
        <option disabled value="">タイトルを選択して下さい</option>
        <option v-for="option in options" v-bind:value="option.name" v-bind:key="option.id">
          {{ option.name }}
        </option>
      </select>
      {{ newTitle }}
    </p>

    <p>
      ■Text:
      <input type="text" v-model="newText" placeholder="テキスト/コメントを入力ください" style="width:80%;">
      {{ newText }}
    </p>

    <p>
      ■Time:
      <select v-model="newTime">
        <option disabled value="">タイトルを選択して下さい</option>
        <option v-for="option2 in options2" v-bind:value="option2.name" v-bind:key="option2.id">
          {{ option2.name }}
        </option>
      </select>
      {{ newTime }}
    </p>
  </div>

  <!-- 追加ボタン -->
  <button v-on:click="addTodo()">タスク追加</button>
  <!-- 追加アラート -->
  <p style="color:red;">{{ alertmsg }}</p>
  <hr />


  <!-- ######################################################## -->
  <!-- ★リスト表示 -->
  {{ msg }}
  <div class="task-list">
    <table class="table">
      <thead>
        <tr>
          <th>Title</th>
          <th>Text</th>
          <th>Time(時間)</th>
          <th>Close</th>
        </tr>
      </thead>

      <tbody>
        <tr v-for="todo in todos" class="task-list__item" v-bind:class="{ 'task-list__item--checked': todo.done }">

          <!-- タスク -->
          <td>
            {{ todo.title }}
          </td>

          <!-- テキスト -->
          <td>
            <input type="checkbox" v-model="todo.editing">
            <input v-if="todo.editing" v-model="todo.text" @keyup.enter="todo.editing = !todo.editing">
            <span v-else>{{ todo.text }}</span>
          </td>

          <!-- 時間 -->
          <td>
            <input type="checkbox" v-model="todo.editing2">
            <input v-if="todo.editing2" v-model="todo.time" @keyup.enter="todo.editing2 = !todo.editing2">
            <span v-else>{{ todo.time }}</span>
          </td>

          <!-- 完了/Close -->
          <td>
            <input type="checkbox" v-model="todo.done">
          </td>
        </tr>
      </tbody>
    </table>

    <!-- 削除ボタン -->
    <button @click="removeTodo()">タスク完了→削除</button>
    <hr />


    <!-- ######################################################## -->
    <!-- ★Vueデータ表示 -->
    Vue-Data
    <div class="json-list">
      <p>{{ todos }}</p>
    </div>

  </div>

  <!-- {{ index }} : {{ item }} -->
  <!-- タスク表示 -->
  <!-- <div v-if="awesome">
      <div id="cal-footer">
        <table class="table">
          <thead>
            <tr>
              <th>科目</th>
              <th>時間（分）</th>
            </tr>
          </thead>
          <tbody>

            <div v-if="this.day == 18">
              <tr v-for="(item, index) in items">
                <td v-on:touchstart="dateClick(0)">
                  {{ item.task }} : {{ item.time }}
                </td>

              </tr>
            </div>
            <div v-if="this.day == 19">
              <tr v-for="(item, index) in items2">
                <td>
                  {{ item.task }} : {{ item.time }}
                </td>
              </tr>
            </div>

          </tbody>
        </table>
      </div>
    </div> -->


</div>
</template>



<!-- ######################################################## -->
<script>
export default {
  data() {
    return {
      weekdays: ['日', '月', '火', '水', '木', '金', '土'],
      year: 2020,
      month: 3,
      day: -1,
      today: '',

      items: [{
          task: "英語",
          time: 11
        },
        {
          task: "数学",
          time: 12
        },
        {
          task: "理科",
          time: 13
        }
      ],
      items2: [{
          task: "英語",
          time: 1
        },
        {
          task: "数学",
          time: 2
        },
        {
          task: "理科",
          time: 3
        }
      ],

      awesome: false,


      msg: 'タスク時間表示',
      todos: [{
          title: '英語',
          text: 'HTTP接続/障害対応',
          time: 11,
          done: false,
          editing: false,
          editing2: false
        },
        {
          title: '数学',
          text: '客先打ち合わせ',
          time: 11,
          done: false,
          editing: false,
          editing2: false
        },
        {
          title: '理科',
          text: 'Vue.js検証',
          done: false,
          time: 11,
          editing: false,
          editing2: false
        }
      ],
      newText: "",

      newTitle: '',
      options: [{
          id: 1,
          name: '英語'
        },
        {
          id: 2,
          name: '数学'
        },
        {
          id: 3,
          name: '理科'
        }
      ],

      newTime: '',
      options2: [
        {
          id: 1,
          name: 1
        },
        {
          id: 2,
          name: 2
        },
        {
          id: 3,
          name: 3
        },
        {
          id: 4,
          name: 4
        },
        {
          id: 5,
          name: 5
        },
        {
          id: 6,
          name: 6
        },
        {
          id: 7,
          name: 7
        },
        {
          id: 8,
          name: 8
        },
        {
          id: 9,
          name: 9
        },
        {
          id: 10,
          name: 10
        },
        {
          id: 11,
          name: 11
        },
        {
          id: 12,
          name: 12
        },
        {
          id: 13,
          name: 13
        },
        {
          id: 14,
          name: 14
        },
        {
          id: 15,
          name: 15
        },
        {
          id: 16,
          name: 16
        },
        {
          id: 17,
          name: 17
        },
        {
          id: 18,
          name: 18
        },
        {
          id: 19,
          name: 19
        },
        {
          id: 20,
          name: 20
        },
        {
          id: 21,
          name: 21
        },
        {
          id: 22,
          name: 22
        },
        {
          id: 23,
          name: 23
        }
      ],

      alertmsg: ''
    }
  },


  ///////////////////////////////////////////////////
  mounted() {
    var date = new Date()
    var y = date.getFullYear()
    var m = ('0' + (date.getMonth() + 1)).slice(-2)
    var d = ('0' + date.getDate()).slice(-2)

    // yearとmonthを設定
    this.year = y
    this.month = Number(m)

    // 今日の日付を設定
    this.today = y + '-' + m + '-' + d

  },


  ///////////////////////////////////////////////////
  // ★タスク追加
  methods: {
    addTodo: function(event) {

      let title = this.newTitle
      let text = this.newText && this.newText.trim()
      let time = this.newTime

      //追加変数のチェック
      if (!title) {
        this.alertmsg = "titleを入力してください"
        return
      }
      if (!text) {
        this.alertmsg = "textを入力してください"
        return
      }
      if (!time) {
        this.alertmsg = "timeを入力してください"
        return
      }

      // Vue-Dataに追加
      this.todos.push({
        title: title,
        text: text,
        time: time,
        done: false,
        editing: false,
        editing2: false
      })

      // 追加変数のリセット
      this.newTitle = ''
      this.newText = ''
      this.newTime = ''
      this.alertmsg = ''
    },

    // ★タスク完了→削除
    removeTodo: function(event) {
      for (let i = this.todos.length - 1; i >= 0; i--) {
        if (this.todos[i].done) this.todos.splice(i, 1)
      }
    },

    ///////////////////////////////////////////////////
    // 先月のカレンダーを取得
    setLastMonth: function() {
      if (this.month === 1) {
        this.year--
        this.month = 12
      } else {
        this.month--
      }
      this.day = -1
    },
    // 翌月のカレンダーを取得
    setNextMonth: function() {
      if (this.month === 12) {
        this.year++
        this.month = 1
      } else {
        this.month++
      }
      this.day = -1
    },
    ///////////////////////////////////////////////////

    //////////////////////////////////////////
    /**
     * カレンダー日付クリック時の処理
     */
    dateClick: function(dayNum) {
      if (dayNum !== '') {
        this.day = dayNum
      }
      this.awesome = true
    },

    //////////////////////////////////////////


    /**
     * 今日かどうかの判定
     * 年、月は現在選択しているページ
     * 日は引数
     */
    isToday: function(day) {
      var date = this.year + "-" + this.month.toFixed(2) + "-" + day
      if (this.today === date) {
        return true
      }
      return false
    },

  },
  computed: {
    calData: function() {
      console.log(this.year + "-" + this.month + "のデータ作成")
      var calData = []

      // 初日の曜日を取得
      var firstWeekDay = new Date(this.year, this.month - 1, 1).getDay()

      // 月の日数
      var lastDay = new Date(this.year, this.month, 0).getDate()

      // 日数カウント用
      var dayNum = 1

      // 週ごとのデータを作成して、calDateにpush
      while (dayNum <= lastDay) {
        var weekData = []

        // 日曜～土曜の日付データを配列で作成
        for (var i = 0; i <= 6; i++) {
          if (calData.length === 0 && i < firstWeekDay) {
            // 初週の1日以前の曜日は空文字
            weekData[i] = ''
          } else if (lastDay < dayNum) {
            // 最終日以降の曜日は空文字
            weekData[i] = ''
          } else {
            // 通常の日付入力
            weekData[i] = dayNum
            dayNum++
          }
        }
        calData.push(weekData)
      }
      return calData
    }
  },


}
</script>


<style scoped>
/*---------------------------------------
カレンダーのcss
---------------------------------------*/
#cal-main {
  font-size: 14px;
  line-height: 20px;
  table-layout: fixed;
  width: 100%;
  margin-bottom: 1rem;
  color: #212529;
  border-bottom: 1px solid #ddd;
  border-collapse: collapse;
}

#cal-main th {
  padding: 0;
  text-align: center;
  vertical-align: middle;
  font-weight: normal;
  color: #999;
}

#cal-main td {
  padding: 8px;
  text-align: center;
  vertical-align: middle;
  border-top: 1px solid #ddd;
}

.cal-today {
  background-color: #fcf8e3;
}

.cal-day.active {
  background-color: #ffc9d7;
}


/*---------------------------------------
カレンダーのヘッダのcss
---------------------------------------*/
#cal-header {
  font-size: 24px;
  padding: 0;
  text-align: center;
  margin-bottom: 10px;
  background-color: darkorange;
  border-bottom: 1px solid #ddd;
  display: flex;
  justify-content: space-between;
}

#cal-header span {
  padding: 15px 20px;
  color: white;
  display: inline-block;
}

/*---------------------------------------
メインCSS
---------------------------------------*/
#cal-header {
  font-size: 18px;
  padding: 0px 0px !important;
}
p {
  margin: 0px;
  padding: 0px;
}

table {
  /* width: 100%; */
  /* text-align: left;
  table-layout: fixed; */
}

/* .task-list {
  padding: 0 10px;
} */

.task-list__item {
  text-align: left;
}

.task-list__item--checked {
  color: #85a6c6;
}

.task {
  text-align: left;
}

.json-list {
  text-align: left;
}

table,
td,
th {
  border: 1px #cccccc solid;
}

td,
th {
  padding: 0 10px 0 10px
}

</style>
