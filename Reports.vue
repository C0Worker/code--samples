<template>
    <div>
        <div class="header font-black100-24b">Отчеты</div>
        <div class="grid-cards">
            <div class="grid--left-column">
                <div class="card card--shadow-blue">
                    <div @click=" openedBlock.tree = !openedBlock.tree "  class="card-header card-header--rprt-treebp">
                       <span class="card-name font-black-20b">Дерево/структура БП</span>
                    </div>
                    <div class="card--switcher" :class="{ 'card--switcher-opened' : openedBlock.tree }"></div>
                    <div class="card--content" :class="{ 'card--full' : openedBlock.tree }">
                      <div
                          @click="openModal(modalTypes.TREE)"
                          class="fake-select card-fake-select"
                      >
                        <span  v-if="!bpInTree.name"  class="font-gray85-15r">Выберите бизнес-процесс</span>
                        <span v-else class="font-black40-16r">[ {{ bpInTree.code }} ] {{ bpInTree.name }}</span>
                      </div>
                      <a
                          :href="`/api/report/treeAndStructureBp?id=${ bpInTree.id }`"
                          target="_blank"
                          class="btn-dwnld  btn--right btn--size-long"
                          :class="{ 'btn-active' : bpInTree.id }"
                      >
                        <span class="btn-dwnld--name font-gray85-15sb" >.pdf</span>
                      </a>
                    </div>
                </div>
                <div class="card card--shadow-blue">
                    <div @click=" openedBlock.owner = !openedBlock.owner "  class="card-header card-header--rprt-owner">
                        <span class="card-name font-black-20b">Перечень БП, которыми владеет/ в которых принимает участие Владелец БП</span>
                    </div>
                    <div class="card--switcher" :class="{ 'card--switcher-opened' : openedBlock.owner }"></div>
                    <div class="card--content" :class="{ 'card--full' : openedBlock.owner }">
                        <div
                            @click="openModal(modalTypes.BP_OWNER)"
                            class="fake-select card-fake-select"
                        >
                          <span v-if="!bpOwner.name"  class="font-gray85-15r">Выберите сотрудника</span>
                          <span v-else class="font-black40-16r">{{ bpOwner.name }}</span>
                        </div>
                        <div class="group-btns btn--right">
                            <a
                                :href="`/api/report/bpOwner?f=XLSX&u=${ bpOwner.id }`"
                                target="_blank"
                                :class="{ 'btn-active' : bpOwner.name }"
                                class="btn-dwnld btn--size-long"
                            >
                              <span class="btn-dwnld--name font-gray85-15sb">.xlsx</span></a>
                            <a
                                :href="`/api/report/bpOwner?f=PDF&u=${ bpOwner.id }`"
                                target="_blank"
                                :class="{ 'btn-active' : bpOwner.id }"
                                class="btn-dwnld btn--size-long"
                            >
                              <span class="btn-dwnld--name font-gray85-15sb">.pdf</span>
                            </a>
                        </div>
                    </div>
                </div>
                <div class="card card--shadow-blue">
                    <div @click=" openedBlock.subDivision = !openedBlock.subDivision "  class="card-header card-header--rprt-functions font-black-20b">
                        <span  class="card-name font-black-20b">Перечень БП/ функций подразделения/ коллегиального органа</span>
                    </div>
                    <div class="card--switcher" :class="{ 'card--switcher-opened' : openedBlock.subDivision }"></div>
                    <div class="card--content" :class="{ 'card--full' : openedBlock.subDivision }">
                        <div
                            @click="openModal(modalTypes.BP_SUBDIVISION)"
                            class="fake-select card-fake-select"
                        >
                          <span  v-if="!bpSubdivision.name"  class="font-gray85-15r">Выберите бизнес-процесс</span>
                          <span v-else class="font-black40-16r">{{ bpSubdivision.name }}</span>
                        </div>
                        <div class="group-btns btn--right">
                            <a
                                :href="getCollegialSubdivisionHref('XLSX')"
                                target="_blank"
                                class="btn-dwnld btn--size-long"
                                :class="{ 'btn-active' : bpSubdivision.id }"
                            >
                              <span class="btn-dwnld--name font-gray85-15sb">.xls</span>
                            </a>
                            <a
                                :href="getCollegialSubdivisionHref('PDF')"
                                target="_blank"
                                class="btn-dwnld btn--size-long"
                                :class="{ 'btn-active' : bpSubdivision.id }"
                            >
                              <span class="btn-dwnld--name font-gray85-15sb">.pdf</span>
                            </a>
                        </div>
                    </div>
                </div>
            </div>
            <div>
                <div class="card card--shadow-blue">
                    <div @click=" openedBlock.change = !openedBlock.change "  class="card-header card-header--rprt-changes font-black-20b">
                        <span class="card-name font-black-20b">Изменения в БП</span>
                    </div>
                    <div class="card--switcher" :class="{ 'card--switcher-opened' : openedBlock.change }"></div>
                    <div class="card--content" :class="{ 'card--full' : openedBlock.change }">
                        <div
                            @click="openModal(modalTypes.BP_CHANGE)"
                            class="fake-select card-fake-select"
                        >
                            <span v-if="!bpChange.name"  class="font-gray85-15r">Выберите бизнес-процесс</span>
                            <span v-else class="font-black40-16r"> [ {{ bpChange.code }} ] {{ bpChange.name }};</span>
                        </div>
                        <div class="choose-periods">
                            <div class="choose-periods--name font-dgray60-13sb">Период</div>
                            <div class="periods">
                                <DatePicker @select-date="( date )=>{ this.dateFromBpChange = date }" class="datepicher-start"/>
                                <DatePicker @select-date="( date )=>{ this.dateToBpChange = date }" class="datepicher-end" />
                            </div>
                        </div>
                        <a
                            :href="`/api/report/bpChange?b=${ bpChange.id }&s=${ dateFromBpChange }&e=${ dateToBpChange }`"
                            target="_blank"
                            class="btn-dwnld btn--right btn--size-long"
                            :class="{ 'btn-active' : checkBpChangeFields }"
                        >
                          <span class="btn-dwnld--name font-gray85-15sb">.xls</span>
                        </a>
                    </div>
                </div>
                <div class="card card--shadow-blue">
                    <div @click=" openedBlock.matrix = !openedBlock.matrix "  class="card-header card-header--rprt-member font-black-20b">
                        <span class="card-name font-black-20b">Матрица взаимодействия подразделений</span>
                    </div>
                    <div class="card--switcher" :class="{ 'card--switcher-opened' : openedBlock.matrix }"></div>
                    <div class="card--content" :class="{ 'card--full' : openedBlock.matrix }">
                        <CustomSelector
                          v-model="role"
                          :options="roles"
                          :plain="true"
                          placeholder="Выберите роль"
                        />
                      <div
                          @click="openModal(modalTypes.BP_MATRIX)"
                          class="fake-select card-fake-select"
                      >
                        <span v-if=" !bpMatrixSubdivision.name " class="font-gray85-15r">Выберите подразделение</span>
                        <template v-else>
                              <span class="font-black40-16r">
                                {{ bpMatrixSubdivision.name }}
                              </span>
                        </template>
                      </div>
                      <div class="group-btns btn--right">
                        <a
                            :href="`/api/report/cooperationMatrix?f=PDF&r=${role}&s=${bpMatrixSubdivision.id}`"
                            target="_blank"
                            class="btn-dwnld btn--size-long"
                            :class="{ 'btn-active' : checkBpMatrixFields }">
                          <span class="btn-dwnld--name font-gray85-15sb">.pdf</span>
                        </a>
                          <a
                              :href="`/api/report/cooperationMatrix?f=XLSX&r=${role}&s=${bpMatrixSubdivision.id}`"
                              target="_blank"
                              class="btn-dwnld btn--size-long"
                              :class="{ 'btn-active' : checkBpMatrixFields }">
                            <span class="btn-dwnld--name font-gray85-15sb">.xls</span>
                          </a>
                      </div>
                    </div>
                </div>
            </div>
            <div class="card card--shadow-blue">
                <div class="font-black-20b">Классификатор БП</div>
                <div class="group-btns group-btns--short btn--left">
                    <a
                        href="/api/report/classifier?f=XLSX"
                        target="_blank"
                        class="btn-dwnld btn--size-short btn-active"
                    >
                      <span class="btn-dwnld--name font-gray85-15sb">.xlsx</span>
                    </a>
                    <a
                        href="/api/report/classifier?f=PDF"
                        target="_blank"
                        class="btn-dwnld btn--size-short btn-active"
                    >
                      <span class="btn-dwnld--name font-gray85-15sb">.pdf</span>
                    </a>
                </div>
            </div>
            <div class="card card--shadow-blue">
                <div class="font-black-20b">Анализ покрытия БП НМД</div>
                <div class="group-btns group-btns--short btn--left">
                    <a
                        href="/api/report/nmd?f=XLSX"
                        target="_blank"
                        class="btn-dwnld btn--size-short btn-active"
                    >
                      <span class="btn-dwnld--name font-gray85-15sb">.xlsx</span>
                    </a>
                    <a
                        href="/api/report/nmd?f=PDF"
                        target="_blank"
                        class="btn-dwnld btn--size-short btn-active"

                    >
                      <span class="btn-dwnld--name font-gray85-15sb">.pdf</span>
                    </a>
                </div>
            </div>
            <div class="card card--shadow-blue card--mini">
                <div class="font-black-20b">Анализ покрытия БП ИТ-системами</div>
                <div class="group-btns group-btns--short btn--left">
                  <a
                      href="/api/report/it?f=XLSX"
                      target="_blank"
                      class="btn-dwnld btn--size-short btn-active"
                  >
                    <span class="btn-dwnld--name font-gray85-15sb">.xlsx</span>
                  </a>
                  <a
                      href="/api/report/it?f=PDF"
                      target="_blank"
                      class="btn-dwnld btn--size-short btn-active"
                  >
                    <span class="btn-dwnld--name font-gray85-15sb">.pdf</span>
                  </a>
                </div>
            </div>
        </div>
        <BPSelectorModal />
        <OwnerSelectorModal />
        <SubjectSelectorModal />
    </div>
</template>

<script>
import BPSelectorModal from "@/app/_shared/components/bp_selector_modal/BPSelectorModal";
import OwnerSelectorModal from "@/app/_shared/components/owner_selector_modal/OwnerSelectorModal";
import SubjectSelectorModal from '@/app/_shared/components/subject_selector_modal/SubjectSelectorModal.vue'

const MODAL_TYPES = {
  TREE: 'tree',
  BP_CHANGE: 'bpChange',
  BP_OWNER: 'bpOwner',
  BP_SUBDIVISION: 'bpCollegialSubdivision',
  BP_MATRIX: 'bpMatrix'
}

export default {
  name: 'Reports',
  components: {
    BPSelectorModal,
    OwnerSelectorModal,
    SubjectSelectorModal
  },
  data () {
    return {
      openedBlock:{
        tree: false,
        change: false,
        matrix: false,
        subDivision: false,
        owner: false,
      },
      modalTypes: MODAL_TYPES,
      bpInTree: {},
      bpChange: {},
      bpSubdivision: {},
      bpOwner: {},
      openedModal: '',
      dateFromBpChange: null,
      dateToBpChange: null,
      bpMatrixSubdivision: {},
      dateFromBpMatrix: null,
      dateToBpMatrix: null,
      role: '',
      roles: []
    }
  },
  computed: {
    checkBpChangeFields () {
      return this.bpChange.id && this.dateFromBpChange && this.dateToBpChange
    },
    checkBpMatrixFields () {
      return this.bpMatrixSubdivision.id && this.role
    }
  },
  created () {
    this.getRoles()
  },
  methods: {
    getRoles () {
      this.$getRepo('report', { local: true }).getRoles().then(r => {
        this.roles = r.data
      })
    },
    getCollegialSubdivisionHref (docType) {
      let field = (this.bpSubdivision.type === 'SUBDIVISION') ? 's' : 'c'
      return `/api/report/bpCollegialSubdivision?f=${docType}&${field}=${this.bpSubdivision.id}`
    },
    selectBP ( arg ) {
      switch( this.openedModal ) {
        case this.modalTypes.TREE: {
          this.bpInTree = arg
          break
        }
        case this.modalTypes.BP_CHANGE: {
          this.bpChange = arg
          break
        }
        default:{
          return
        }
      }
    },
    selectOwner ( arg ) {
      switch ( this.openedModal ) {
        case this.modalTypes.BP_OWNER: {
          this.bpOwner = arg
          break
        }
        default:{
          return
        }
      }
    },
    selectSubdivision ( arg ) {
      switch ( this.openedModal ) {
        case this.modalTypes.BP_SUBDIVISION: {
          this.bpSubdivision = arg
          break
        }
        case this.modalTypes.BP_MATRIX: {
          this.bpMatrixSubdivision = arg
          break
        }
        default:{
          return
        }
      }
    },
    openModal ( type ) {
      if ( type === this.modalTypes.TREE ) {
        this.openedModal = type
        this.$showModal('bp_selector', {
          onSelect: this.selectBP,
          targetLevels: [1, 2]
        })
        return
      }
      if ( type === this.modalTypes.BP_OWNER ) {
        this.openedModal = type
        this.$showModal('owner_selector', {
          onSelect:this.selectOwner
        })
        return
      }
      if ( type === this.modalTypes.BP_SUBDIVISION ) {
        this.openedModal = type
        this.$showModal('subject_selector', {
          onSelect: this.selectSubdivision,
          excludeTabs: [1, 3, 5]
        })
        return
      }
      if ( type === this.modalTypes.BP_MATRIX ) {
        this.openedModal = type
        this.$showModal('subject_selector', {
          onSelect: this.selectSubdivision,
          excludeTabs: [1, 3, 4, 5]
        })
        return
      }
      this.openedModal = type
      this.$showModal('bp_selector', {
        onSelect: this.selectBP,
        targetLevels: []
      })
    }
  }
}
</script>

<style lang="sass" scoped>
//кнопки
.btn-dwnld
    display: flex
    align-items: center
    justify-content: center
    cursor: pointer
    box-sizing: border-box
    border: 1px solid $grey-85
    border-radius: 4px
    background: $white
    text-decoration: none
    pointer-events: none
.btn-dwnld:before
    content: ""
    background: url("~@/assets/img/img-download.svg")
    background-repeat: no-repeat
    width: 16px
    height: 16px
.btn-dwnld--name
    margin-left: 8px
.btn--size-long
    width: 160px
    height: 40px
.btn--size-short
    width: 75px
    height: 40px
.btn-dwnld.btn-active
  border: 1px solid $blue
  background-color: $blue
  pointer-events: auto
.btn-dwnld.btn-active:before
  background: url("~@/assets/img/img-download-light.svg")


.btn-dwnld.btn-active .btn-dwnld--name
  color: $white
//выбор
.fake-select
    background: $white
    box-shadow: 0px 1px 4px rgba(65, 103, 160, 0.32)
    border-radius: 4px
    padding: 10px 30px 10px 16px
    background: url("~@/assets/img/arrow.svg")
    background-repeat: no-repeat
    background-position: right .7em top 50%, 0 0
    cursor: pointer

//стили карточек
.card--shadow-blue
    background: #FFFFFF
    box-shadow: 0px 4px 24px rgba(0, 119, 200, 0.15)
    border-radius: 4px
.card--content
    display: none
    margin-top: 32px
.card--full
    display: block
    padding-bottom: 58px

//позиционирование внутри карточек
.card
    position: relative
    padding: 32px 37px 32px 24px
    margin-bottom: 16px
    &-fake-select
        margin-top: 30px
        margin-bottom: 6px
    &--mini
        padding-bottom: 84px

@media (max-width: 1330px)
  .card--mini
    padding-bottom: 105px
.card--switcher
    content: ""
    background: url("~@/assets/img/close-list-32.svg")
    background-repeat: no-repeat
    background-position: center
    width: 32px
    height: 32px
    transform: rotate(180deg)
    position: absolute
    right: 37px
    top: 31px
    transition: 0.2s ease-in-out
    &-opened //пеключение
      transform: none


.choose-periods--name
    margin: 13px 24px 4px 16px
.periods
    display: flex
    justify-content: space-between
    max-width: 575px
/deep/ .custom-date-picker input
    width: 255px

@media (max-width: 1440px)
    /deep/ .custom-date-picker input
        width: 220px

//иконки карточек
.card-header
    display: flex
    align-items: center
    cursor: pointer
.card-header:before
    content: ""
    width: 36px
    height: 36px
    margin-right: 18px
.card-header
    &--rprt-treebp:before
        background: url("~@/assets/img/icons-reports/edit structure.svg")
        background-repeat: no-repeat
        background-position: center
    &--rprt-owner:before
        background: url("~@/assets/img/icons-reports/links.svg")
        background-repeat: no-repeat
        background-position: center
    &--rprt-functions:before
        background: url("~@/assets/img/icons-reports/matrix.svg")
        background-repeat: no-repeat
        background-position: center
    &--rprt-changes:before
        background: url("~@/assets/img/icons-reports/swimlane.svg")
        background-repeat: no-repeat
        background-position: center
    &--rprt-member:before
        background: url("~@/assets/img/icons-reports/sstructure.svg")
        background-repeat: no-repeat
        background-position: center
.card-name
    max-width: 85%

//позиционирование кнопок
.group-btns
    position: absolute
    display: grid
    grid-template-columns: 1fr 1fr
    column-gap: 32px
    &--short
        column-gap: 24px
.btn
    &--right
        position: absolute
        right: 37px
        bottom: 29px
    &--left
        position: absolute
        left: 24px
        bottom: 29px

// сетка для карточек
.grid-cards
    margin-top: 33px
    display: grid
    grid-template-rows: 1fr
    grid-template-columns: 0.9fr 1.1fr 2fr
    column-gap: 32px
.grid--left-column
    grid-column: 1 / 3
.header
    margin-top: 130px
</style>
