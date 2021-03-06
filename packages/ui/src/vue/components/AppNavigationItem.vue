<template>
  <li
    :class="classes"
    class="navigation__item"
  >
    <span
      v-if="item.isStructural"
      class="navigation__link"
    >{{ item.title }}</span>
    <router-link
      v-else
      :to="item"
      class="navigation__link"
    >{{ item.title }}</router-link>
    <button
      v-if="children"
      :aria-expanded="!isCollapsed | bool2string"
      :title="'navigation.toggle' | localize"
      type="button"
      class="navigation__itemtoggle"
      aria-haspopup="true"
      @click.prevent="setCollapsed(!isCollapsed)"
    >
      <app-icon
        class="navigation__icon"
        symbol="caret-down"
      />
    </button>
    <app-navigation-tree
      v-if="children"
      ref="children"
      :navigation="navigation"
      :items="children"
      :level="level + 1"
    />
  </li>
</template>

<script>
import { mapGetters, mapMutations } from 'vuex'

export default {
  props: {
    navigation: {
      type: Object,
      required: true
    },

    item: {
      type: Object,
      required: true
    },

    level: {
      type: Number,
      required: true
    }
  },

  computed: {
    ...mapGetters('preferences', ['navigationItemsCollapsed']),

    children () {
      return this.item.childIds
    },

    isCurrentPage () {
      return this.$route.path === this.item.path
    },

    isCollapsed () {
      return this.navigationItemsCollapsed[this.item.id] || false
    },

    classes () {
      const classes = [`navigation__item--level-${this.level}`]

      if (this.children) classes.push('navigation__item--children')
      if (this.isCollapsed) classes.push('navigation__item--collapsed')
      if (this.isCurrentPage) classes.push('navigation__item--current')

      return classes
    }
  },

  methods: {
    ...mapMutations('preferences', ['setNavigationItemsCollapsed']),

    setCollapsed (collapsed) {
      const itemsCollapsed = this.navigationItemsCollapsed
      itemsCollapsed[this.item.id] = collapsed
      this.setNavigationItemsCollapsed(itemsCollapsed)

      if (!collapsed) {
        this.$refs.children.$el.querySelector('a.navigation__link').focus()
      }
    }
  }
}
</script>
