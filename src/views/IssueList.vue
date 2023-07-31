<template>
  <div>
    <h1>issueリスト</h1>
    <!-- getIssue()をクリックイベントに登録する -->
    <el-button type="success" @click="getIssues()">issue取得</el-button>
    <!-- 取得したデータを確認するための暫定要素 -->
    <div v-for="(issue,index) in issues" :key="index">
        {{ issue.title }}
        <button @click="closeIssue(index)" type="success">完了</button>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

const client = axios.create({
  // 環境変数に変更
  baseURL: `${process.env.VUE_APP_GITHUB_ENDPOINT}`, 
  headers: {
    'Accept': 'application/vnd.github.v3+json',
    'Content-Type':'application/json',
    // 環境変数に変更
    'Authorization': `token ${process.env.VUE_APP_GITHUB_TOKEN}`
  },
})


export default {
  name: 'IssueList',
  data() {
    return {
      issues: []
    }
  },

  methods: {
    getIssues() {
      client.get('/issues')
        .then((res) => {
          this.issues = res.data;
          console.log(this);
      })
    },

    closeIssue(test){
      const target = this.issues[test] // --3
      client.patch(`/issues/${target.number}`, // --4
          {
            state: 'closed' // --5
          },
        )
        .then(() => {
          this.issues.splice(test, 1) // --6
      })
    },
  },

  created(){
    this.getIssues();
  }
}
</script>