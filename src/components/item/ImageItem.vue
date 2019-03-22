<template>
  <div class="root">
    <div class="bubble">
      <img
        class="image"
        :src="media(message)"
        :loading="'data:' + message.mediaMimeType + ';base64,' + message.thumbImage"
        :style="borderSetObject(message)"
      >
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
  methods: {
    borderSet: message => {
      if (1.5 * message.mediaWidth > message.mediaHeight) {
        return 'width-set'
      }
      if (3 * message.mediaWidth < message.mediaHeight) {
        return 'width-set'
      }
      return 'height-set'
    },
    media: message => {
      if (message.mediaUrl === null || message.mediaUrl === undefined || message.mediaUrl === '') {
        return 'data:' + message.mediaMimeType + ';base64,' + message.thumbImage
      }
      return message.mediaUrl
    },
    borderSetObject: message => {}
  }
}
</script>
<style lang="scss" scoped>
.root {
  .bubble {
    display: flex;
    .image {
      border-radius: 3px;
      height: 100px;
      width: 100px;
    }
    .time {
      color: white;
    }
  }
}
</style>
