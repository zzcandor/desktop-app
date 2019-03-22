<template>
  <div>
    <div class="bubble">
      <span>
        <span>{{message.type}}</span>
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
  props: ['conversation', 'message', 'me', 'showName']
}
</script>
<style lang="scss" scoped>
</style>
