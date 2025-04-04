<template lang="slim">
.dropdown
  button (
    :id="id" ref="button" type="button" :class="{ active: showMenu }"
    :disabled="disabled" @click="showMenu = !showMenu"
  )
    .placeholder v-if="!displayValue" Select a value…
    .value v-if="value"
      i.icon.fa.fa-fw> v-if="value.icon" :class="value.icon"
      | {{ displayValue }}
    i.caret.fa.fa-caret-down
  dropdown-menu (
    :items="choices" :show="showMenu" :multiple="multiple" :button="$refs.button"
    :checked=="multiple ? value : []" @input="setValue" @leave="showMenu = false"
  )
    slot
</template>

<script lang="coffee">
import DropdownMenu from './dropdown-menu.vue'

export default
  components: { DropdownMenu }
  props:
    choices: Array
    disabled: Boolean
    id: String
    multiple: Boolean
    value: null
  data: ->
    showMenu: false
  computed:
    displayValue: ->
      if Array.isArray @value
        (@formatValue(v) for v in @value).join ', ' if @value.length > 0
      else
        @formatValue @value
  methods:
    setValue: (val) ->
      return if @disabled || @value == val

      if @multiple
        index = @value.indexOf val
        if index > -1
          @value.splice index, 1
        else
          @value.push val
          @value.sort (a, b) => @choices.indexOf(a) - @choices.indexOf(b)
      else
        @$emit 'input', val
    formatValue: (val) -> val?.name || val
</script>

<style lang="sass" scoped>
@use 'config'
@use 'upload-mixins'

.dropdown
  position: relative // make the menu (absolute) follow us when scrolling

button
  align-items: baseline
  display: flex
  justify-content: space-between
  max-width: 100%
  text-align: left

  &.active
    box-shadow: upload-mixins.$input-inset-shadow, upload-mixins.input-glow(config.$foreground)

.value, .placeholder
  overflow: hidden
  text-overflow: ellipsis
  white-space: nowrap

.caret
  padding-left: 10px
  padding-right: 5px
</style>
