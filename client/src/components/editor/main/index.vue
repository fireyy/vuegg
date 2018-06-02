<template>
  <div class="mainegg" :style="editorStyle">
    <stage v-if="selectedPage" :page="selectedPage" :zoom="zoom"></stage>
    <zoom-menu @zoomChange="zoomHandler" :zoom="zoom" class="zoom-menu"></zoom-menu>
  </div>
</template>


<script>
import { mapState, mapGetters, mapMutations } from 'vuex'
import { _changeActivePage, _rebaseActivePage, _updateEditorZoom, getPageIndexById } from '@/store/types'

import ZoomMenu from '@/components/editor/common/ZoomMenu'
import Stage from './Stage'

export default {
  name: 'mainegg',
  components: { Stage, ZoomMenu },
  created: function () {
    this.selectFallbackPage(this.selectedPage)
  },
  computed: {
    ...mapState({
      selectedPage: state => state.app.selectedPage,
      pages: state => state ? state.project.pages : [],
      zoom: state => state.app.editorZoom,
      editorSize: state => state.project.editorSize
    }),
    ...mapGetters([getPageIndexById]),
    editorStyle () {
      return (this.editorSize === 'sm')
        ? {height: '640px', width: '360px', margin: '0 auto'}
        : (this.editorSize === 'md')
          ? {height: '1024px', width: '768px', margin: '0 auto'}
          : {}
    }
  },
  watch: {
    // After a redo/undo action this will apply
    selectedPage: function (val) {
      this.selectFallbackPage(val)
    }
  },
  methods: {
    selectFallbackPage (page) {
      if (!page && this.pages.length > 0) {
        this._changeActivePage(this.pages[0])
      } else {
        this._rebaseActivePage(this.getPageIndexById(page.id))
      }
    },

    zoomHandler (zoomValue) {
      this._updateEditorZoom(zoomValue)
    },

    ...mapMutations([_changeActivePage, _rebaseActivePage, _updateEditorZoom])
  }
}
</script>

<style scoped>
.mainegg {
  margin: 0 57px;
  height: 100%;
  transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
}

.zoom-menu {
  bottom: 24px;
  left: 24px;
  padding: 0 6px;
  position: fixed;
}
</style>
