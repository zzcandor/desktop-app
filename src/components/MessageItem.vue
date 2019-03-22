<template>
  <div>
    <TextItem
      v-if="message.type.endsWith('TEXT') && !message.quoteId"
      :message="message"
      :me="me"
      :showName="this.showUserName()"
      :conversation="conversation"
    ></TextItem>
    <QuoteItem
      v-else-if="message.type.endsWith('TEXT')"
      :message="message"
      :me="me"
      :showName="this.showUserName()"
      :conversation="conversation"
    ></QuoteItem>
    <ImageItem
      v-else-if="message.type.endsWith('IMAGE')"
      :message="message"
      :me="me"
      :showName="this.showUserName()"
      :conversation="conversation"
    ></ImageItem>
    <StickerItem
      v-else-if="message.type.endsWith('STICKER')"
      :message="message"
      :me="me"
      :showName="this.showUserName()"
      :conversation="conversation"
    ></StickerItem>
    <AudioItem
      v-else-if="message.type.endsWith('AUDIO')"
      :message="message"
      :me="me"
      :showName="this.showUserName()"
      :conversation="conversation"
    ></AudioItem>
    <VideoItem
      v-else-if="message.type.endsWith('VIDEO')"
      :message="message"
      :me="me"
      :showName="this.showUserName()"
      :conversation="conversation"
    ></VideoItem>
    <FileItem
      v-else-if="message.type.endsWith('FILE')"
      :message="message"
      :me="me"
      :showName="this.showUserName()"
      :conversation="conversation"
    ></FileItem>
    <ContactItem
      v-else-if="message.type.endsWith('CONTACT')"
      :message="message"
      :me="me"
      :showName="this.showUserName()"
      :conversation="conversation"
    ></ContactItem>
    <UnkownItem
      v-else
      :message="message"
      :me="me"
      :showName="this.showUserName()"
      :conversation="conversation"
    ></UnkownItem>
  </div>
</template>
<script>
import TextItem from '@/components/item/TextItem.vue'
import QuoteItem from '@/components/item/QuoteItem.vue'
import SystemItem from '@/components/item/SystemItem.vue'

import ImageItem from '@/components/item/ImageItem.vue'
import StickerItem from '@/components/item/StickerItem.vue'

import AudioItem from '@/components/item/AudioItem.vue'
import VideoItem from '@/components/item/VideoItem.vue'
import FileItem from '@/components/item/FileItem.vue'
import ContactItem from '@/components/item/ContactItem.vue'
import UnkownItem from '@/components/item/UnkownItem.vue'
import {
  NameColors,
  ConversationCategory,
  MessageStatus,
  SystemConversationAction,
  MessageCategories
} from '@/utils/constants.js'
export default {
  props: ['conversation', 'message', 'me', 'showName', 'prev'],
  components: {
    TextItem,
    QuoteItem,
    SystemItem,
    ImageItem,
    StickerItem,
    AudioItem,
    VideoItem,
    FileItem,
    ContactItem,
    UnkownItem
  },
  methods: {
    showUserName: function() {
      if (
        this.conversation.category === ConversationCategory.CONTACT &&
        this.message.userId !== this.conversation.ownerId &&
        this.message.userId !== this.me.user_id &&
        (!this.prev || (!!this.prev && this.prev.userId !== this.message.userId))
      ) {
        return true
      }
      return (
        this.conversation.category === ConversationCategory.GROUP &&
        this.message.userId !== this.me.user_id &&
        (!this.prev || (!!this.prev && this.prev.userId !== this.message.userId))
      )
    }
  }
}
</script>

<style lang="scss" scoped>
</style>
