<template>
  <div class="message" :style="abg">
    <div class="diver" :style="bg"></div>
    <div class="layout">
      <span class="name" :style="font">{{message.userFullName}}</span>
      <span class="content">
        <ICRecall class="replay_icon" v-if="message.type === 'MESSAGE_RECALL'" />
        <ICMessageAudio class="replay_icon" v-else-if="messageType() === 'audio'" />
        <ICMessagePhoto class="replay_icon" v-else-if="messageType() === 'image'" />
        <ICMessageVideo class="replay_icon" v-else-if="messageType() === 'video'" />
        <ICMessageVideo class="replay_icon" v-else-if="messageType() === 'live'" />
        <ICMessageFile class="replay_icon" v-else-if="messageType() === 'file'" />
        <ICMessageContact class="replay_icon" v-else-if="messageType() === 'contact'" />
        <ICMessageTransfer class="replay_icon" v-else-if="messageType() === 'transfer'" />
        <ICMessageBotMenu
          class="replay_icon"
          v-else-if="messageType() === 'app_card' ||messageType() === 'app_button'"
        />
        {{getContent}}
      </span>
    </div>
    <img
      class="image"
      v-if="message.type.endsWith('_IMAGE') && (message.mediaUrl || message.assetUrl)"
      v-bind:loading="'data:' + message.mediaMimeType + ';base64,' + message.thumbImage"
      v-bind:src="mediaUrl"
    />
    <img
      class="image"
      v-if="message.type.endsWith('_VIDEO') && (message.mediaUrl || message.assetUrl)"
      v-bind:loading="'data:' + message.mediaMimeType + ';base64,' + message.thumbImage"
      v-bind:src="mediaUrl"
    />

    <img
      class="image"
      v-if="message.type.endsWith('_LIVE') && message.thumbUrl"
      v-bind:src="message.thumbUrl"
    />
    <img
      class="image"
      v-if="message.type.endsWith('_STICKER') && (message.mediaUrl || message.assetUrl)"
      v-bind:loading="'data:' + message.mediaMimeType + ';base64,' + message.thumbImage"
      v-bind:src="mediaUrl"
    />
    <img
      class="image"
      v-if="message.type.endsWith('_CONTACT')"
      v-bind:loading="'data:' + message.mediaMimeType + ';base64,' + message.thumbImage"
      v-bind:src="message.sharedUserAvatarUrl"
    />
  </div>
</template>
<script>
import ICRecall from '@/assets/images/if_recall.svg'
import { getNameColorById } from '@/utils/util.js'
import ICMessageAudio from '@/assets/images/ic_message_audio.svg'
import ICMessagePhoto from '@/assets/images/ic_message_photo.svg'
import ICMessageVideo from '@/assets/images/ic_message_video.svg'
import ICMessageFile from '@/assets/images/ic_message_file.svg'
import ICMessageContact from '@/assets/images/ic_message_contact.svg'
import ICMessageTransfer from '@/assets/images/ic_message_transfer.svg'
import ICMessageBotMenu from '@/assets/images/ic_message_bot_menu.svg'
export default {
  props: ['message', 'me'],
  data() {
    return {}
  },
  components: {
    ICRecall,
    ICMessageAudio,
    ICMessagePhoto,
    ICMessageVideo,
    ICMessageFile,
    ICMessageContact,
    ICMessageTransfer,
    ICMessageBotMenu
  },
  computed: {
    bg: function() {
      let color = getNameColorById(this.message.userId)
      return { background: color }
    },
    abg: function() {
      let color = getNameColorById(this.message.userId)
      return { background: color + '0D' }
    },
    font: function() {
      let color = getNameColorById(this.message.userId)
      return { color: color }
    },
    mediaUrl: function() {
      if (this.message.mediaUrl) {
        return this.message.mediaUrl
      } else if (this.message.assetUrl) {
        return this.message.assetUrl
      }
      return null
    },
    getContent: function() {
      if (this.message.type.endsWith('_TEXT')) {
        return this.message.content
      } else if (this.message.type.endsWith('_STICKER')) {
        return this.$t('chat.chat_sticker')
      } else if (this.message.type.endsWith('_IMAGE')) {
        return this.$t('chat.chat_pic')
      } else if (this.message.type === 'MESSAGE_RECALL') {
        let { message, me } = this
        if (message.userId === me.user_id) {
          return this.$t('chat.chat_recall_me')
        } else {
          return this.$t('chat.chat_recall_delete')
        }
      } else if (this.message.type.endsWith('_VIDEO')) {
        return this.$t('chat.chat_video')
      } else if (this.message.type.endsWith('_LIVE')) {
        return this.$t('chat.chat_live')
      } else if (this.message.type.endsWith('_AUDIO')) {
        return this.formatSeconds(this.message.mediaDuration)
      } else if (this.message.type.endsWith('_DATA')) {
        return this.message.mediaName
      } else if (this.message.type.endsWith('_CONTACT')) {
        return this.message.sharedUserIdentityNumber
      } else {
        return null
      }
    }
  },
  methods: {
    media: message => {
      if (message.mediaUrl === null || message.mediaUrl === undefined || message.mediaUrl === '') {
        return 'data:' + message.mediaMimeType + ';base64,' + message.thumbImage
      }
      return message.mediaUrl
    },
    formatSeconds(msd) {
      var time = parseFloat(msd) / 1000
      if (time !== null && time !== '') {
        // let h = parseInt(time / 3600.0)
        let m = parseInt((parseFloat(time / 3600.0) - parseInt(time / 3600.0)) * 60)
        let s = parseInt(
          (parseFloat((parseFloat(time / 3600.0) - parseInt(time / 3600.0)) * 60) -
            parseInt((parseFloat(time / 3600.0) - parseInt(time / 3600.0)) * 60)) *
            60
        )
        if (m < 10) {
          m = '0' + m
        }
        if (s < 10) {
          s = '0' + s
        }
        time = m + ':' + s
      }
      return time
    },
    messageType() {
      let type = this.message.type
      if (type.endsWith('_STICKER')) {
        return 'sticker'
      } else if (type.endsWith('_IMAGE')) {
        return 'image'
      } else if (type.endsWith('_TEXT')) {
        return 'text'
      } else if (type.endsWith('_VIDEO')) {
        return 'video'
      } else if (type.endsWith('_LIVE')) {
        return 'live'
      } else if (type.endsWith('_AUDIO')) {
        return 'audio'
      } else if (type.endsWith('_DATA')) {
        return 'file'
      } else if (type.endsWith('_CONTACT')) {
        return 'contact'
      } else if (type.startsWith('APP_')) {
        if (type === 'APP_CARD') {
          return 'app_card'
        } else {
          return 'app_button'
        }
      } else if (type === 'SYSTEM_ACCOUNT_SNAPSHOT') {
        return 'transfer'
      } else {
        return 'unknown'
      }
    }
  }
}
</script>
<style lang="scss" scoped>
.message {
  border-radius: 0.2rem;
  overflow: hidden;
  margin-bottom: 3px;
  display: flex;
  flex-direction: row;
  justify-content: flex-start;
  .diver {
    width: 0.4rem;
  }
  .layout {
    flex: 1;
    padding: 3px;
    display: flex;
    overflow: hidden;
    flex-direction: column;

    .name {
      font-size: 0.7rem;
      overflow: hidden;
      text-overflow: ellipsis;
      white-space: nowrap;
    }
    .content {
      text-overflow: ellipsis;
      white-space: nowrap;
      overflow: hidden;
      color: #9b9b9b;
      font-size: 0.8rem;
    }
  }
  .image {
    width: 40px;
    height: 40px;
    margin-left: 0.4rem;
    object-fit: cover;
  }
  .replay_icon {
    height: 14px;
    vertical-align: text-top;
  }
}
</style>
