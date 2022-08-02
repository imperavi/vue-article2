<template>
  <textarea ref="arx" :name="name" :placeholder="placeholder" />
</template>

<script>
export default {
  props: {
    modelValue: String,
    placeholder: {
      type: String,
      default: null,
    },
    name: {
      type: String,
      default: null,
    },
    config: {
      default: {},
      type: Object,
    },
  },
  mounted() {
    this.init()
  },
  beforeDestroy() {
    this.destroy()
  },
  methods: {
    init() {
      var me = this
      var subscribe = {
        "editor.change": function (event) {
          var html = event.get("html")
          me.handleInput(html)
          return html
        },
      }

      // extend config
      if (typeof this.config.subscribe === "undefined") {
        this.config.subscribe = subscribe
      } else {
        this.config.subscribe["editor.change"] = subscribe["editor.change"]
      }

      // call Article Editor
      var app = ArticleEditor(this.$refs.arx, this.config)

      // set instance
      this.arx = app
      this.$parent.arx = app
    },
    destroy() {
      // Call destroy on redactor to cleanup event handlers
      ArticleEditor(this.$refs.arx, "destroy")

      // unset instance for garbage collection
      this.arx = null
      this.$parent.arx = null
    },
    handleInput(val) {
      this.$emit("update:modelValue", val)
    },
  },
}
</script>