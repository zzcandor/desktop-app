 <style scoped>
html {
  background: #eeeeee;
}

html,
body {
  min-height: 100%;
  margin: 0;
  padding: 0;
  overflow: hidden;
  font-family: 'Roboto', sans-serif;
  -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
}
html .wechat-list,
.wechat .recyclerview {
  background: #efefef;
}
.chat-view-footer {
  position: fixed;
  width: 100%;
  bottom: 0;
  display: flex;
  height: 50px;
}
.wechat {
  height: calc(100vh - 130px);
}
.chat-input {
  width: 30%;
  height: 50px;
  padding: 5px;
  line-height: 14px;
}
.chat-send-btn {
  flex-shrink: 0;
  width: 100px;
  height: 50px;
  background: #8bc34a;
  border: none;
  color: #fff;
  font-size: 14px;
}
</style>

<template>
  <div id="app">
    <div class="chat-view">
      <RecyclerView
        ref="RecyclerView"
        key="wechat"
        class="recyclerview-container wechat"
        :fetch="wechatFetch"
        :item="ChatItem"
        :tombstone="Tombstone"
        :prerender="10"
        :remain="10"
        :options="wechatOptions"
        @inited="initScrollToBottom"
      ></RecyclerView>
      <div class="chat-view-footer">
        <textarea v-model="input" @keyup.enter="addMessage" class="chat-input"></textarea>
        <button class="chat-send-btn" @click="addMessage">send</button>
      </div>
    </div>
  </div>
</template>

<script>
import ChatItem from './components/ChatItem.vue'
import Tombstone from './components/Tombstone'
import * as wechatFetch from './data/wechat-fetch'

export default {
  name: 'app',
  data() {
    return {
      input: '',
      wechatOptions: {
        reuseVM: true,
        usePrefix: true,
        props: {
          color: {
            value: ''
          }
        }
      },
      miOptions: {
        usePrefix: true
      },
      waterflowOptions: {
        waterflow: true,
        reuseVM: true
      },
      actionModal: {
        show: false,
        actions: [
          {
            text: 'wechat-recyclerview',
            icon: 'icon-wechat1',
            handler: this.actionHandler
          },
          {
            text: 'listview',
            icon: 'icon-1',
            handler: this.actionHandler
          },
          {
            text: 'mi recyclerview',
            icon: 'icon-mi',
            handler: this.actionHandler
          },
          {
            text: 'mi listview',
            icon: 'icon-mi',
            handler: this.actionHandler
          },
          {
            text: 'Multiple-columns',
            icon: 'icon-mi',
            handler: this.actionHandler
          }
        ]
      },
      list: [], // JSON.parse(JSON.stringify(testData)),
      wechatFetch: wechatFetch.fetch,
      ChatItem,
      Tombstone
    }
  },
  mounted() {},
  methods: {
    actionHandler(action) {
      this.listType = action.text
      this.toogleModal()
    },
    initScrollToBottom() {
      this.$nextTick(() => {
        const RecyclerView = this.$refs.RecyclerView
        RecyclerView && RecyclerView.scrollToIndex(1000)
      })
    },
    wechatScrollToBottom() {
      const RecyclerView = this.$refs.RecyclerView
      RecyclerView._scrollToBottom()
    },
    replyMessage() {
      setTimeout(() => {
        this.addMsg(null, false)
      }, 500 + 1500 * Math.random())
    },
    addMsg(msg, sender, cb) {
      const RecyclerView = this.$refs.RecyclerView
      wechatFetch.getItem().then(data => {
        if (msg) data.message = msg
        data.self = sender
        cb && cb()
        // this.wechatOptions.props.color.value = '#' + (((1 << 24) * Math.random()) | 0).toString(16)
        // RecyclerView.scroller.MAX_COUNT += 1
        // RecyclerView.scroller.addContent([data])
        this.wechatScrollToBottom()
      })
    },
    addMessage() {
      this.addMsg(this.input, true, () => {
        this.input = ''
        this.replyMessage()
      })
    },
    scrollToBottom() {
      const RecyclerView = this.$refs.RecyclerView
      if (!RecyclerView) return
      // const len = RecyclerView.scroller.MAX_COUNT
      // RecyclerView.scrollToIndex(len)
      // this.list = this.list.concat(JSON.parse(JSON.stringify(testData)))
    },
    toogleModal() {
      this.actionModal.show = !this.actionModal.show
    },
    toggle() {
      this.view = !this.view
      this.toogleModal()
    }
  }
}
</script>
