<template>
  <div class="root">
    <div class="bubble">
      <span class="text">
        <span v-html="textMessage"></span>
      </span>
      <span class="time">
        {{message.lt}}
        <ICSending
          v-if="message.userId === me.user_id && (message.status === MessageStatus.SENDING || message.status === MessageStatus.PENDING)"
          class="icon"
        />
        <ICSend
          v-else-if="message.userId === me.user_id && message.status === MessageStatus.SENT"
          class="icon"
        />
        <ICRead
          v-else-if="message.userId === me.user_id && message.status === MessageStatus.DELIVERED"
          class="icon wait"
        />
        <ICRead
          v-else-if="message.userId === me.user_id && message.status === MessageStatus.READ"
          class="icon"
        />
      </span>
    </div>
  </div>
</template>
<script>
import ICSending from '@/assets/images/ic_status_clock.svg'
import ICSend from '@/assets/images/ic_status_send.svg'
import ICRead from '@/assets/images/ic_status_read.svg'
import { MessageStatus, NameColors } from '@/utils/constants.js'
export default {
  data: function() {
    return {
      MessageStatus: MessageStatus
    }
  },
  components: {
    ICSending,
    ICSend,
    ICRead
  },
  props: ['conversation', 'message', 'me', 'showName'],
  props: ['message', 'me'],
  computed: {
    textMessage: function() {
      let { message } = this
      let tagsToReplace = {
        '&': '&amp;',
        '<': '&lt;',
        '>': '&gt;'
      }
      return message.content
        .replace(/[&<>]/g, tag => {
          return tagsToReplace[tag]
        })
        .replace(/\r?\n/g, '<br />')
        .replace(
          /(?:(?:https?|ftp|file):\/\/|www\.|ftp\.)(?:\([-A-Z0-9+&@#\/%=~_|$?!:,.]*\)|[-A-Z0-9+&@#\/%=~_|$?!:,.])*(?:\([-A-Z0-9+&@#\/%=~_|$?!:,.]*\)|[A-Z0-9+&@#\/%=~_|$])/gim,
          tag => {
            let l = tag
            if (!tag.startsWith('http')) {
              l = 'https://' + tag
            }
            return `<a href='${l}' target='_blank'>${tag}</a> `
          }
        )
    }
  }
}
</script>
<style lang="scss" scoped>
.root {
  width: 100%;
  padding-bottom: 8px;
  display: flex;
  .bubble {
    background: white;
    padding: 3px;
    border-radius: 3px;
    display: flex;
    .text {
      text-align: left;
      word-break: break-all;
      user-select: text;
      font-size: 1rem;
    }
    .time {
      color: #8799a5;
      display: flex;
      float: right;
      font-size: 0.75rem;
      align-self: flex-end;
    }
  }
}
</style>
