<template>
  <div class="layout" v-bind:class="messageOwnership()">
    <div>
      <span
        class="username"
        v-if="showName"
        v-bind:style="{color: getColor(message.userId)}"
        @click="$emit('user-click')"
      >{{message.userFullName}}</span>
      <BadgeItem @handleMenuClick="$emit('handleMenuClick')" :type="message.type">
        <div class="content">
          <audio class="media" :src="message.mediaUrl" controls="controls">不支持</audio>
          <div class="bottom">
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
      </BadgeItem>
    </div>
  </div>
</template>
<script>
import ICSending from '@/assets/images/ic_status_clock.svg'
import ICSend from '@/assets/images/ic_status_send.svg'
import ICRead from '@/assets/images/ic_status_read.svg'
import BadgeItem from './BadgeItem'
import { MessageStatus } from '@/utils/constants.js'
import { mapGetters } from 'vuex'
import { getNameColorById } from '@/utils/util.js'
export default {
  props: ['conversation', 'message', 'me', 'showName'],
  components: {
    ICSending,
    ICSend,
    ICRead,
    BadgeItem
  },
  data: function() {
    return {
      MessageStatus: MessageStatus
    }
  },
  methods: {
    messageOwnership: function() {
      let { message, me } = this
      return {
        send: message.userId === me.user_id,
        receive: message.userId !== me.user_id
      }
    },
    getColor: function(id) {
      return getNameColorById(id)
    }
  },
  computed: {
    loading: function() {
      return this.attachment.includes(this.message.messageId)
    },
    ...mapGetters({
      attachment: 'attachment'
    })
  }
}
</script>
<style lang="scss" scoped>
.layout {
  display: flex;
  margin-left: 0.4rem;
  margin-right: 0.4rem;
  .username {
    margin-left: 0.4rem;
    display: inline-block;
    font-size: 0.85rem;
    max-width: 100%;
    text-overflow: ellipsis;
    overflow: hidden;
    white-space: nowrap;
    margin-bottom: 0.2rem;
    min-width: 2rem;
    min-height: 0.85rem;
  }
  .content {
    display: flex;
    flex: 1;
    flex-direction: column;
    text-align: start;
    overflow: hidden;
    .media {
      width: 280px;
    }
    .name {
      font-size: 1rem;
      overflow: hidden;
      white-space: nowrap;
      text-overflow: ellipsis;
    }
    .bottom {
      display: flex;
      justify-content: flex-end;
      .time {
        color: #8799a5;
        display: flex;
        float: right;
        font-size: 0.75rem;
        bottom: 0.2rem;
        right: 0.2rem;
        align-items: flex-end;
        .icon {
          padding-left: 0.2rem;
        }
        .wait {
          path {
            fill: #859479;
          }
        }
      }
    }
  }
}
.layout.send {
  flex-direction: row-reverse;
}
.layout.receive {
  flex-direction: row;
}
</style>
