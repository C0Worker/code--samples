<template>
    <div>
        <SwimlaneHeader
            :editorMode="true"
            class="editor-mode"
        />
        <div class="list--bp-names font-white-12r">
            <div class="bp-name">ORG.1.3.2</div>
            <div class="bp-name">ORG.1.3.2</div>
            <div class="bp-name">ORG.1.3.2</div>
            <div class="bp-name">ORG.1.3.2</div>
            <div class="bp-name">ORG.1.3.2</div>
        </div>
        <div @contextmenu.prevent="showSubmenu">
            <ScrollLayout class="graph-wrapper">
                <template v-slot:header-top style="height: 50px">
                    <div class="header-columns">
                        <div v-for="el in swimlaneData.columns" :key="el.id" :style="'width:' + (el.width - 0.5) + 'px'" class="header-column font-white-13r">{{el.name}}
                            <!-- <div v-if="EditorMode" class="burger-light"></div> -->
                        </div>
                    </div>    
                </template>
                <template v-slot:header-left>
                    <div class="header-rows">
                        <div v-for="(el,i) in swimlaneData.rows" :key="el.id" :style="'width:' + el.height + 'px; margin-top: ' + (i == 0 ? '72.5' : el.height - 72)  + 'px'" class="header-row font-black-13r">{{el.role}}
                            <!-- <div v-if="EditorMode" class="burger"></div>
                            <div v-if="EditorMode" class="close-row"></div> -->
                        </div>
                    </div>
                </template>
                <div
                    ref = "graph_container"
                    id = "graph-container"
                ></div>
            </ScrollLayout>
        </div>
        <VueContext ref="submenu" tag="div">
            <SubmenuActions :position="submenuPosition" @click="addValue"/>
        </VueContext>

        <div class="footer">
            <div class="add-row" @click="$showModal('role_swimlane')" ></div>
            <div class="list--bp-names list--bp-names--red font-white-12r">
                <div class="bp-name">ORG.1.3.2</div>
                <div class="bp-name">ORG.1.3.2</div>
                <div class="bp-name">ORG.1.3.2</div>
                <div class="bp-name">ORG.1.3.2</div>
                <div class="bp-name">ORG.1.3.2</div>
            </div>
        </div>
        <RoleModal />
    </div>
</template>

<script>
import { mapState, mapActions } from 'vuex'
import { SWIMLANE_STATE_NAME } from './swimlane.state'

import SwimlaneHeader from './swimlane_header/SwimlaneHeader.vue';
import SubmenuActions from './submenu_actions/SubmenuActions.vue';
import mxgraphService from './_shared/services/mxgraph.service';
// import createService from './_shared/services/create.servise';
import RoleModal from './role_modal/RoleModal'
import ScrollLayout from './scroll_layout/ScrollLayout.vue'
import VueContext from 'vue-context'

export default {
    components: {
        VueContext,
        ScrollLayout,
        SwimlaneHeader,
        SubmenuActions,
        RoleModal,
    },
    name: 'Swimlane',
    data() {
        return {
            graph: {},
            json: {},
            submenuPosition: {
                x: 0, y: 0
            }
        }
    },
    computed: {
        ...mapState(SWIMLANE_STATE_NAME, {
            swimlaneData: state => state.swimlaneData
        })
    },
    mounted() {
        this.getSwimlaneData();
        mxgraphService.init(this.$refs.graph_container);
        // createService.createModel();
    },
    methods: {
        ...mapActions(SWIMLANE_STATE_NAME, ['getSwimlaneData']),
        showSubmenu (e) {
            this.submenuPosition = { x: e.clientX, y: e.clientY }
            this.$refs.submenu.open(e)
        }
    }
}
</script>

<style lang="sass" scoped>
#graph-container
    margin-left: 60px
    margin-top: 0px
.graph-wrapper
    margin-top: 333px
    width: 91%
    height: 740px
    // position: fixed

.list--bp-names
    margin-left: 72px
    background: $brand-green
    display: flex
    position: absolute
    top: 276px
    padding: 14px 15px
    border-radius: 500px
    &--red
        position: fixed
        background: $brand-pink
        top: 1100px
        right: 4.5%

.bp-name
    margin-right: 20px

.font-white-12r
    text-decoration: underline

.footer
    position: absolute
    top: 1072px 

.add-row
    display: block
    width: 72px
    height: 70px
    background: url("~@/assets/img/add.svg")
    background-repeat: no-repeat
    background-size: cover 


// заголовки столбцов
.header-columns
    display: flex
    padding-left: 100px
    background: $white

часть стелки
.header-columns:before
    display: block
    content: ""
    background: $blue
    width: 1px
    height: 41px
    position: absolute
    left: 84px

.header-column
    position: relative
    display: flex
    align-items: center
    justify-content: center
    text-align: center
    background: $blue
    height: 41px
    border-right: 1px solid $blue-30

.header-column:after
    display: block
    background: $blue-30
    width: 5px
    height: 41px
    
// заголовки столбцов
.header-rows
    display: flex
    flex-direction: column
    height: auto
    justify-content: space-around
    padding-top: 41px
    width: 80px
    align-items: center
    margin-left: -4px

.header-row
    position: relative
    display: flex
    align-items: center
    justify-content: center
    text-align: center
    height: 72px
    border-right: 1px solid $grey-96
    background: $blue-30
    transform: rotate(-90deg)
    
.header-row:nth-child(1)
    border-right: none

.burger
    display: block
    width: 24px
    height: 24px
    background: url("~@/assets/img/burger.svg")
    background-repeat: no-repeat
    background-size: cover
    position: absolute
    top: 0
    left: 0
    cursor: pointer

.burger-light
    display: block
    width: 24px
    height: 24px
    background: url("~@/assets/img/burger-light.svg")
    background-repeat: no-repeat
    background-size: cover
    position: absolute
    top: 0
    left: 0
    cursor: pointer

.close-row
    display: block
    width: 6px
    height: 6px
    margin: 9px
    background: url("~@/assets/img/del_goal.svg")
    background-repeat: no-repeat
    background-size: cover
    position: absolute
    top: 0
    right: 0
    cursor: pointer

</style>
