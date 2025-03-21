<template>
  <div>
    <h1><translate>Save changeset</translate></h1>
    <div v-if="status == 'save'">
      <form id="editor_save_form">
        <div class="form-group">
          <label for="editor-modify-count">
            <translate>Objects edited</translate>
          </label>
          <input
            id="editor-modify-count"
            type="text"
            readonly
            class="form-control-plaintext"
            :value="edition_stack.length"
          />
        </div>
        <div class="form-group">
          <label for="comment">
            <translate>Comment</translate>
          </label>
          <input
            id="comment"
            v-model="comment"
            class="form-control"
            type="text"
          />
        </div>
        <div class="form-group">
          <label for="source">
            <translate>Source</translate>
          </label>
          <input
            id="source"
            v-model="source"
            class="form-control"
            type="text"
          />
        </div>
        <div class="form-group">
          <label for="type">
            <translate>Type</translate>
          </label>
          <input id="type" v-model="type" class="form-control" type="text" />
        </div>
        <div class="form-check">
          <input
            id="reuse_changeset"
            v-model="reuse_changeset"
            class="form-check-input"
            type="checkbox"
          />
          <label class="form-check-label" for="reuse_changeset">
            <translate>Reuse changeset</translate>
          </label>
        </div>
        <br />
        <div id="buttons">
          <button
            type="button"
            class="btn btn-secondary"
            @click="$emit('cancel')"
          >
            <translate>Cancel</translate>
          </button>
          <button type="button" class="btn btn-primary" @click="save()">
            <translate>Save</translate>
          </button>
        </div>
      </form>
    </div>
    <div v-if="status == 'upload'">
      <center>
        <img src="~../../../static/images/throbbler.gif" alt="downloading" />
      </center>
    </div>
    <div v-if="status == 'error'">
      {{ error }}
      <br />
      <div id="buttons">
        <button
          type="button"
          class="btn btn-secondary"
          @click="$emit('cancel')"
        >
          <translate>Cancel</translate>
        </button>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import Vue, { PropType } from 'vue'

export default Vue.extend({
  props: {
    edition_stack: {
      type: Array as PropType<string[]>,
      required: true,
    },
  },

  data(): {
    comment: string
    source: string
    type: 'fix'
    reuse_changeset: boolean
    status: 'upload' | 'save' | 'error'
    error?: string
  } {
    return {
      comment: this.$t('Fixed with Osmose'),
      source: '',
      type: 'fix',
      reuse_changeset: true,
      status: 'save',
      error: null,
    }
  },

  methods: {
    save() {
      this.status = 'upload'
      fetch(API_URL + '/en/editor/save', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          tag: {
            comment: this.comment,
            source: this.source,
            type: this.type,
          },
          reuse_changeset: this.reuse_changeset,
          modify: this.edition_stack,
        }),
      })
        .then(() => {
          this.$emit('saved')
        })
        .catch((error) => {
          this.status = 'error'
          this.error = error
        })
    },
  },
})
</script>

<style scoped>
#buttons {
  float: right;
}
</style>
