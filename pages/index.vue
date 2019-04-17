<template>
  <section class="container">
    <div>
      <logo />
      <h1 class="title">
        csv-workout-to-html
      </h1>
      <h2 class="subtitle">
        Takes CSV in text field and spits out a html table ready to be copied to
        TFBB
      </h2>

      <div class="form-group">
        <label for="csvInput">CSV</label>
        <textarea
          id="csvInput"
          v-model.lazy="csvInput"
          class="form-control"
          rows="3"
        ></textarea>
      </div>

      <transition name="fade">
        <div v-show="csvInput && csvInput.length > 0" class="card">
          <h5 class="card-header">Table</h5>
          <div class="card-body">
            <button
              class="btn btn-success btn-block"
              @click="() => $refs.table.copyRenderedHtmlToClipboard()"
            >
              Copy
            </button>
            <csv-table ref="table" :csv-input="csvInput" />
          </div>
        </div>
      </transition>
    </div>
  </section>
</template>

<script>
import Logo from '~/components/Logo.vue'
import CsvTable from '~/components/CsvTable'

export default {
  components: {
    Logo,
    CsvTable
  },

  data: () => ({
    csvInput: ''
  })
}
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
