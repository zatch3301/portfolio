<template>
  <div
    class="themer"
    :title="helpText">
    <Flip
      color="green"
      :shortkey="['t']"
      v-model="isFlipped">
      <template #left>
        <Icon icon="sun"/>
      </template>
      <template #right>
        <Icon icon="moon"/>
      </template>
    </Flip>
  </div>
</template>

<script>
  import Flip from '@/components/common/flip/Flip'
  import Icon from '@/components/common/icon/Icon'

  /**
   * This component enables switching between the light and dark variants
   * of [Solarized by Ethan Schoonover](https://ethanschoonover.com/solarized/).
   */
  export default {
    name: 'Themer',
    components: {
      Icon,
      Flip
    },
    data () {
      return {
        themes: [
          'light',
          'dark'
        ],
        theme: null,
        default: 'dark'
      }
    },
    computed: {
      /**
       * Create a model to use with the flip switch component.
       */
      isFlipped: {
        /**
         * Get the current state of the flip. The switch is considered to be
         * flipped when the component is not in the default state.
         */
        get () {
          return this.theme !== this.default
        },
        /**
         * Set the new value for the theme based on the new flip status.
         * @param {boolean} val - the new status of the flip
         */
        set (val) {
          if (this.isFlipped !== val) {
            this.switchTheme()
          }
        }
      },

      /**
       * Get the theme variant opposite to the current one.
       * @returns {string} the name of the opposite theme
       */
      otherTheme () {
        let index = this.themes.indexOf(this.theme)
        return this.themes[++index % this.themes.length]
      },
      /**
       * Get the title text that describes the action this button will perform.
       */
      helpText () {
        return `[T] Switch to the ${this.otherTheme} theme.`
      }
    },
    watch: {
      /**
       * Sync changes from the theme data variable to the root class and
       * also updates local storage for persistence.
       * @param {string} to - the new value of the theme
       * @param {string} from - the old value of the theme
       */
      theme (to, from) {
        if (to !== from) { // Breaks recursion
          document.documentElement.setAttribute('theme', this.theme)

          // Persist theme to local storage
          localStorage.theme = to
        }
      }
    },
    methods: {
      /**
       * Change the theme of the app to the other one.
       */
      switchTheme () {
        this.theme = this.otherTheme
      }
    },
    created () {
      if (localStorage.theme) {
        // Switch to last used theme
        this.theme = localStorage.theme
      } else {
        // Set theme to the default value
        this.theme = this.default
      }
    }
  }
</script>

<style scoped lang="scss" src="./Themer.scss"/>