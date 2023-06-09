<template>
  <div>
    <transition :name="slideSide">
      <component :is="tag" :class="className" v-show="isThere || OpenedFromOutside" :style="sideNavStyle">
        <mdb-scrollbar>
          <ul class="custom-scrollbar list-unstyled">
            <li v-if="logo" :class="logoLi" @click="wave">
              <div :class="logoWrapperClasses">
                <router-link v-if="to" :to="to">
                  <img :src="logo" :class="logoClasses" :alt="logoAlt" />
                  <span v-if="!collapsed && slim && name">{{name}}</span>
                </router-link>
                <a v-else :href="href">
                  <img :src="logo" :class="logoClasses" :alt="logoAlt"/>
                  <span v-if="!collapsed && slim && name">{{name}}</span>
                </a>
              </div>
            </li>
            <slot></slot>
          </ul>
        </mdb-scrollbar>
        <div v-if="mask" :class="maskClasses" />
      </component>
    </transition>
    <transition enter-active-class="animated fadeIn" leave-active-class="animated fadeOut">
    <div v-if="!isThere && OpenedFromOutside" @click="handleOverlayClick" id="sidenav-overlay"></div>
    </transition>
  </div>
</template>

<script>
import mdbScrollbar from '../../Advanced/Scrollbar';
import waves from '../../../mixins/waves';

const SideNav = {
  props: {
    tag: {
      type: String,
      default: "div"
    },
    fixed: {
      type: Boolean,
      default: false
    },
    right: {
      type: Boolean,
      default: false
    },
    hidden: {
      type: Boolean
    },
    logo: {
      type: String
    },
    logoClass: {
      type: String
    },
    logoAlt: {
      type: String
    },
    href: {
      type: String,
      default: '#'
    },
    to: {
      type: String
    },
    breakWidth: {
      type: Number,
      default: 1440
    },
    OpenedFromOutside: {
      type: Boolean,
      default: false
    },
    color: {
      type: String
    },
    sideNavClass: {
      type: String
    },
    mask: {
      type: String
    },
    logoSn: {
      type: Boolean,
      default: false
    },
    logoRound: {
      type: Boolean,
      default: false
    },
    sideNavStyle: {
      type: [Object, String]
    },
    slim: {
      type: Boolean,
      default: false
    },
    isCollapsed: {
      type: Boolean,
      default: false
    },
    name: String
  },
  data() {
    return {
      isThere: false
    };
  },
  computed: {
    collapsed(){
      return this.isCollapsed;
    },
    slideSide() {
      if (this.right) {
        return "slideRight";
      }
      return "slideLeft";
    },
    className() {
      return [
        'side-nav',
        'wide',
        this.collapsed && 'slim',
        this.fixed && 'fixed',
        this.color,
        this.sideNavClass,
        this.right ? 'right-aligned ' : ''
      ];
    },
    logoClasses() {
      return [
        this.logoSn ? '' : 'img-fluid',
        this.logoClass,
        this.logoRound ? 'rounded-circle' : ''
      ];
    },
    logoLi() {
      return [
        this.logoSn && 'logo-sn',
        'ripple-parent'
      ];
    },
    logoWrapperClasses() {
      return [
        this.logoSn ? '' : 'logo-wrapper',
        this.slim ? 'sn-ad-avatar-wrapper' : '',
        this.name ? '' : 'text-center'
      ];
    },
    maskClasses() {
      return [
        'sidenav-bg',
        'mask-' + this.mask
      ];
    }
  },
  methods: {
    updatePredicate() {
      if (!this.hidden) {
        this.isThere = window.innerWidth > this.breakWidth;
      }
    },
    handleOverlayClick() {
      this.$emit("update:OpenedFromOutside", !this.OpenedFromOutside);
    },
    handleEscClick(e) {
      if (e.keyCode === 27) {
        this.$emit("update:OpenedFromOutside", false);
      }
    }
  },
  mounted() {
    this.updatePredicate();
    window.addEventListener("resize", this.updatePredicate);
    window.addEventListener("keyup", this.handleEscClick);
  },
  beforeDestroy() {
    window.removeEventListener("resize", this.updatePredicate);
    window.removeEventListener("keyup", this.handleEscClick);
  },
  components: {
    mdbScrollbar,
  },
  mixins: [waves]
};

export default SideNav;
export { SideNav as mdbSideNav };
</script>

<style scoped>
.side-nav {
  transform: translate(0);
  right: 0; 
  transition-property: all;
}
.side-nav.wide.slim.right-aligned {
  right: 0;
}
.slideLeft-enter, .slideLeft-leave-to {
  transform: translate(-100%);
  opacity: 0;
}
.slideLeft-enter-to, .slideLeft-leave {
  opacity: 1;
}
.slideLeft-leave-active, .slideLeft-enter-active {
  transition: 300ms;
}

.slideRight-enter, .slideRight-leave-to {
  transform: translate(100%);
  opacity: 0;
}
.slideRight-enter-to, .slideRight-leave {
  opacity: 1;
}
.slideRight-leave-active, .slideRight-enter-active {
  transition: 300ms;
}

.side-nav.wide .logo-wrapper.sn-ad-avatar-wrapper {
  padding: 0;
}

.side-nav.wide .logo-wrapper.sn-ad-avatar-wrapper a {
  height: 100%;
  width: 100%;
  padding: 19px 10px;
}

.ps {
  height: 100%;
}

#sidenav-overlay {
  transition: opacity .3s linear;
}
</style>