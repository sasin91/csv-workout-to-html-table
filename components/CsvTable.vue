<template>
  <div>
    <div>
      <h1 class="display-4">Toggle headers</h1>

      <div class="row">
        <span
          v-for="(header, toggleIndex) in headers"
          :key="toggleIndex"
          class="col badge p-4 m-2"
          style="cursor:pointer;"
          :class="{
            'badge-primary': header.visible,
            'badge-secondary': !header.visible
          }"
          @click="header.visible = !header.visible"
        >
          {{ header.value }}
        </span>
      </div>
    </div>

    <table ref="table" class="table">
      <thead>
        <tr>
          <th v-for="(header, index) in visibleHeaders" :key="index">
            {{ header.value }}
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(column, columnIndex) in columns" :key="columnIndex">
          <td
            v-for="(header, headerIndex) in visibleHeaders"
            :key="headerIndex"
          >
            {{ column[header.value] }}
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import { unionBy } from 'lodash'
import { parse } from 'papaparse'
import saveState from 'vue-save-state'

export default {
  mixins: [saveState],

  props: {
    csvInput: {
      type: String,
      required: true
    }
  },

  data: () => ({
    headers: [],
    columns: []
  }),

  computed: {
    visibleHeaders() {
      return this.headers.filter(header => header.visible)
    }
  },

  watch: {
    csvInput(value) {
      const { meta, data, errors } = parse(value, {
        header: true,
        dynamicTyping: true
        // error: (error) => this.$toast.error(error.message)
      })

      const csvFields = meta.fields.map(field => ({
        value: field,
        visible: true
      }))

      const existingFields = this.headers.filter(
        h => csvFields.findIndex(field => field.value === h.value) !== -1
      )

      this.headers = unionBy(existingFields, csvFields, 'value')
      this.columns = data

      if (errors.length > 0) {
        errors.forEach(error => this.$toast.error(error.message))
      }
    }
  },

  methods: {
    copyRenderedHtmlToClipboard() {
      try {
        const el = document.createElement('textarea')
        el.value = this.$refs.table.outerHTML

        document.body.appendChild(el)

        el.select()

        document.execCommand('copy')
        document.body.removeChild(el)

        this.$toast.success('Copied and ready to be pasted on TFBB :)')
      } catch (e) {
        this.$toast.error(e)
      }
    },

    getSaveStateConfig() {
      return {
        cacheKey: 'CsvWorkoutToHTML::CsvTable',
        saveProperties: ['headers']
      }
    }
  }
}
</script>
