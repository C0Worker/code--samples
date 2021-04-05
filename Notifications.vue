<template>
  <div class="notify--wrap">
    <div class="notify--head">
      <p class="head--text">Событие</p>
      <p class="head--text">Текст уведомления</p>
      <p class="head--text"></p>
      <p class="head--text">Паспорт</p>
      <p class="head--text">Действия</p>
    </div>
    <div class="notify--list">
      <template v-if="nList.length">
        <div
            v-for="item in nList"
            class="notify--item"
            :key="item.id"
        >
<!--          Need to correct template when will see the data, what comes from api -->
          <p
              class="notify--text notify--event"
              :class="{readed : item.read}"
          >
            {{ item.event }}
          </p>
          <p class="notify--text">{{ item.message }}</p>
          <p class="notify--text">{{ item.from }}</p>
          <router-link
            class="notify--text notify--passport"
            :to="{ name: 'BPPassport', params: { id: item.id } }"
          >Паспорт</router-link>
          <!-- <a :href="item.passport || '#'" class="notify--text notify--passport">Паспорт</a> -->
          <p class="notify--text">
            <notification-sub-menu
              @read="readNotif(item.id)"
              @remove="removeNotif(item.id)"
            />
          </p>
        </div>
      </template>
    </div>
  </div>
</template>

<script>
import NotificationSubMenu from "@/app/base_layout/notifications/notification_sub_menu/NotificationSubMenu";

export default {
  name: 'Notifications',
  components: {
    NotificationSubMenu
  },
  data () {
    return {
      nList: []
    }
  },
  created () {
    this.getNList()
  },
  methods: {
    getNList () {
      this.$getRepo('notification').get()
          .then((r) => {
            // Need to correct when get data from API
            this.nList = r.data.list
          })
    },
    readNotif (id) {
      this.$getRepo('notification').put(id)
          .then(() => {
            this.getNList()
          })
    },
    removeNotif (id) {
      this.$getRepo('notification').delete(id)
          .then(() => {
            this.getNList()
          })
    }
  }
}
</script>

<style lang="sass" scoped>

$grid-template: 20% 41% 23% 9% 7%

.notify--wrap
  font-family: ProximaNova-Regular
  margin-top: 72px

  .notify--head
    display: grid
    grid-template-columns: $grid-template
    padding-bottom: 4px
    border-bottom: 1px solid $grey-85
    padding-top: 23px
    margin-bottom: 16px

  .head--text
    margin: 0
    font-size: 13px
    line-height: 18px
    color: $grey-65

    &:last-child
      text-align: right

  .notify--list
    display: flex
    flex-direction: column

    .notify--item
      display: grid
      grid-template-columns: $grid-template
      padding-top: 20px
      padding-bottom: 16px
      border-top: 1px solid $grey-85

    .notify--text
      margin: 0
      font-size: 15px
      line-height: 20px
      color: $dark-grey
      &.notify--event
        font-weight: 600
        color: $blue
        &.readed
          color: $dark-grey

      &.notify--passport
        cursor: pointer
        text-decoration-line: underline
        color: $blue


      &:last-child
        text-align: right
        position: relative
        top: -3px

    .notify--more
      cursor: pointer

</style>
