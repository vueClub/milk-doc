<template>
  <div class="zan-doc-footer-nav">
    <div
      v-if="leftNav"
      class="zan-doc-footer-nav__link zan-doc-footer-nav__left"
      @click="handleNavClick('prev')">
      <div class="zan-doc-footer-nav__arrow-left"></div>
      <span>{{ leftNav.title }}</span>
    </div>
    <div
      v-if="rightNav"
      class="zan-doc-footer-nav__link zan-doc-footer-nav__right"
      @click="handleNavClick('next')">
      <span>{{ rightNav.title }}</span>
      <div class="zan-doc-footer-nav__arrow-right"></div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'zan-doc-footer-nav',

  props: {
    base: String,
    navConfig: Array
  },

  data() {
    return {
      nav: [],
      currentPath: null,
      leftNav: null,
      rightNav: null
    };
  },

  watch: {
    '$route.path'() {
      this.setNav();
      this.updateNav();
    }
  },

  created() {
    this.setNav();
    this.updateNav();
    this.keyboardHandler();
  },

  methods: {
    setNav() {
      const nav = this.navConfig;
      for (let i = 0; i < nav.length; i++) {
        const navItem = nav[i];
        if (!navItem.groups) {
          this.nav.push(nav[i]);
        } else {
          for (let j = 0; j < navItem.groups.length; j++) {
            this.nav = this.nav.concat(navItem.groups[j].list);
          }
        }
      }
    },

    updateNav() {
      let currentIndex;

      this.currentPath = '/' + this.$route.path.split('/').pop();

      for (let i = 0, len = this.nav.length; i < len; i++) {
        if (this.nav[i].path === this.currentPath) {
          currentIndex = i;
          break;
        }
      }
      this.leftNav = this.nav[currentIndex - 1];
      this.rightNav = this.nav[currentIndex + 1];
    },

    handleNavClick(direction) {
      const nav = direction === 'prev' ? this.leftNav : this.rightNav;
      if (nav.path) {
        this.$router.push(this.base + nav.path);
      } else if (nav.link) {
        window.location.href = nav.link;
      }
    },

    keyboardHandler() {
      window.addEventListener('keyup', (event) => {
        switch (event.keyCode) {
          case 37: // left
            this.handleNavClick('prev');
            break;
          case 39: // right
            this.handleNavClick('next');
            break;
        }
      });
    }
  }
};
</script>

<style lang="postcss">
@import '../style/variable.css';

.zan-doc-footer-nav {
  left: 0;
  right: 0;
  bottom: 0;
  display: flex;
  padding: 24px 40px;
  position: absolute;
  border-top: 1px solid $zan-doc-border-color;

  &__link {
    flex: 1;
    font-size: 15px;
    line-height: 1.5;
    cursor: pointer;
    opacity: .7;
    color: $zan-doc-code-color;
    transition: .3s;

    &:hover {
      opacity: 1;
      color: $zan-doc-blue;
    }

    .van-icon {
      font-size: 12px;
      line-height: 16px;
    }

    span {
      vertical-align: middle;
    }
  }

  &__left,
  &__right {
    padding: 0 20px;
    position: relative;
  }

  &__right {
    text-align: right;
  }

  &__arrow-left,
  &__arrow-right {
    top: 50%;
    width: 8px;
    height: 8px;
    position: absolute;
    border: solid currentColor;
    border-width: 0 1px 1px 0;
  }

  &__arrow-left {
    left: 0;
    transform: rotate(135deg) translateY(50%);
  }

  &__arrow-right {
    right: 0;
    transform: rotate(-45deg) translateY(-50%);
  }
}
</style>
