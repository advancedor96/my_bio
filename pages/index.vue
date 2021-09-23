<template>
  <v-app>
    <v-main>
      <v-text-field
        v-model="name"
        label="名稱"
        placeholder="名稱"
        outlined
      />
      <v-textarea
        v-model="msg"
        outlined
        label="留言"
        placeholder="說點什麼嗎？"
        value=""
      /><br>
      <v-btn @click="sendMessage">
        送出
      </v-btn>
      <hr>
      <div v-for="(m, idx) in messageList" :key="idx" class="message">
        <span>{{ m.name }}：</span>
        <span>{{ m.msg }}</span>
      </div>
    </v-main>
  </v-app>
</template>

<script>
import dayjs from 'dayjs'
export default {
  data () {
    return {
      name: '',
      msg: '',
      messageList: []
    }
  },
  created () {
    this.load()
  },
  methods: {
    load () {
      this.$fire.firestore.collection('messages').onSnapshot((querySnapshot) => {
        this.messageList = []
        querySnapshot.forEach((e) => {
          console.log('元素:', e.data())
          this.messageList.push(e.data())
        })
      })
    },
    sendMessage () {
      this.$fire.firestore.collection('messages')
        .add({ name: this.name, msg: this.msg, timestamp: dayjs().format() })
        .then(() => {
          this.name = ''
          this.msg = ''
          this.load()
        }).catch((error) => {
          console.error('錯誤:', error)
        })
    }
  }
}
</script>
