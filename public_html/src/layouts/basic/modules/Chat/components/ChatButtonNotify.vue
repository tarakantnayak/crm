<!-- /* {[The file is published on the basis of YetiForce Public License 3.0 that can be found in the following directory: licenses/LicenseEN.txt or yetiforce.com]} */ -->
<template>
  <q-btn
    :loading="isWaitingForPermission"
    :color="isDesktopNotification ? 'info' : ''"
    dense
    round
    flat
    @click="toggleDesktopNotification()"
  >
    <YfIcon
      :style="styles"
      :size="size"
      :icon="isDesktopNotification ? 'yfi-chat-notification-on' : 'yfi-chat-notification-off'"
    />
    <q-tooltip>{{ translate('JS_CHAT_NOTIFICATION') }}</q-tooltip>
  </q-btn>
</template>
<script>
import { createNamespacedHelpers } from 'vuex'
const { mapGetters, mapMutations } = createNamespacedHelpers('Chat')

export default {
  name: 'ChatButtonNotify',
  props: {
    size: {
      type: String
    },
    styles: {
      type: Object
    }
  },
  data() {
    return {
      isWaitingForPermission: false
    }
  },
  computed: {
    ...mapGetters(['isDesktopNotification', 'config'])
  },
  methods: {
    ...mapMutations(['setDesktopNotification']),
    isNotificationPermitted() {
		return PNotify.modules.Desktop.checkPermission() === 0
	},
    toggleDesktopNotification() {
      if (!this.isDesktopNotification && !this.isNotificationPermitted()) {
        this.isWaitingForPermission = true
        PNotify.modules.Desktop.permission()
        setTimeout(() => {
          if (!this.isNotificationPermitted()) {
            Vtiger_Helper_Js.showPnotify({
              text: app.vtranslate('JS_NO_DESKTOP_PERMISSION'),
              type: 'info',
              animation: 'show'
            })
          } else {
            this.setDesktopNotification(!this.isDesktopNotification)
          }
          this.isWaitingForPermission = false
        }, 3000)
      } else {
        this.setDesktopNotification(!this.isDesktopNotification)
      }
    }
  },
  created() {
    if (this.isDesktopNotification && !this.isNotificationPermitted()) {
      this.setDesktopNotification(false)
    }
  }
}
</script>
<style lang="sass">
</style>
