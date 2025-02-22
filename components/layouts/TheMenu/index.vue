<template>
  <div class="menu">
    <section class="menu__mobile" tabindex="-1">
      <div class="menu__mobile-inner-container">
        <BasicLink
          class="
            menu__entry
            menu__home-link
          "
          kind="secondary"
          v-bind="homeLink"
        >
          <AppLogo
            class="menu__logo"
            :class="{ 'menu__logo_active': isActiveHome(homeLink) }"
          />
        </BasicLink>
        <label
          class="
            menu__hamburger-toggle
            menu__hamburger-toggle_menu-hidden
          "
          for="mobile-menu-toggle"
        >
          <component :is="isMobileMenuVisible ? 'Close20' : 'Menu20'" />
        </label>
      </div>
      <input
        id="mobile-menu-toggle"
        v-model="isMobileMenuVisible"
        class="menu__mobile-menu-toggle"
        type="checkbox"
      >
      <MobileMenu
        class="menu__mobile-menu"
        :class="{ 'menu__mobile-menu_visible': isMobileMenuVisible }"
        :is-visible="isMobileMenuVisible"
      />
    </section>
    <section class="menu__main-level">
      <nav
        class="menu__navigation-level"
        :class="{ 'bx--grid': !oldContainer }"
      >
        <BasicLink
          class="
            menu__entry
            menu__home-link
          "
          kind="secondary"
          v-bind="homeLink"
        >
          <AppLogo
            class="menu__logo"
            :class="{ 'menu__logo_active': isActiveHome(homeLink) }"
          />
        </BasicLink>
        <template v-for="link in mainLevelLinks">
          <BasicLink
            v-if="!link.sublinks"
            :key="link.url"
            class="menu__entry"
            :class="{ 'menu__entry_active': isActive(link) }"
            v-bind="link"
          >
            {{ link.label }}
          </BasicLink>
          <cv-dropdown
            v-else
            :key="link.url"
            class="menu__entry"
            :class="{ 'menu__entry_active': isCommunityActive() }"
            placeholder="Community"
          >
            <li
              v-for="sublink in link.sublinks"
              :key="sublink.url"
              class="bx--dropdown-item"
            >
              <BasicLink
                class="
                  menu__entry
                  menu__entry_second-level
                "
                :class="{ 'menu__entry_active': isActive(sublink) }"
                v-bind="sublink"
              >
                {{ sublink.label }}
              </BasicLink>
            </li>
          </cv-dropdown>
        </template>
      </nav>
    </section>
  </div>
</template>

<script lang="ts">
import { Watch, Component, Mixins, Prop } from 'vue-property-decorator'
import MenuMixin from '~/mixins/menu'

@Component
export default class TheMenu extends Mixins(MenuMixin) {
  @Prop({ type: Boolean, default: false, required: false }) oldContainer!: boolean;

  isMobileMenuVisible: boolean = false

  @Watch('isMobileMenuVisible')
  toggleScroll () {
    this.$emit('change-visibility', this.isMobileMenuVisible ? 'shown' : 'hidden')
    if (this.isMobileMenuVisible) {
      this.$root.$el.classList.add('no-scroll')
    } else {
      this.$root.$el.classList.remove('no-scroll')
    }
  }

  @Watch('$route')
  hideMenu () {
    this.isMobileMenuVisible = false
  }
}
</script>

<style lang="scss" scoped>
@import '~carbon-components/scss/globals/scss/typography';

.menu {
  &__main-level {
    --link-color: #{$text-color-light};
  }

  &__mobile {
    position: relative;

    @include mq($from: large) {
      display: none;
    }

    &:focus {
      outline: none;
    }
  }

  &__mobile-menu-toggle {
    // remove from page flow and hid
    visibility: hidden;
    position: absolute;
  }

  .menu__mobile-menu {
    position: fixed;
    top: 3.5rem; // taking into account the height of the top menu
    left: auto;
    bottom: 0;
    right: 0;
    z-index: 150;
    width: 12rem;
    box-shadow: 0 .5rem .5rem rgba(0,0,0,.25);
    visibility: hidden;
    pointer-events: none;

    &_visible {
      visibility: visible;
      pointer-events: all;
    }

    @include mq($until: medium) {
      width: 100%;
    }
  }

  &__mobile-inner-container,
  &__navigation-level {
    padding-top: $spacing-05;
    padding-bottom: $spacing-05;
    display: flex;
    justify-content: flex-end;
    align-items: center;

    &_wider {
      max-width: $max-size;
    }
  }

  &__mobile-inner-container {
    @include contained();
  }

  &__navigation-level {
    padding-top: 0;
    padding-bottom: 0;

    @include mq($until: large) {
      display: none;
    }

    &:not(.bx--grid) {
      @include contained();
    }
  }

  &__logo {
    height: 1.5rem;
    width: auto;
    color: $text-color-lighter;

    &_active {
      color: $text-active-color;
    }
  }

  &__entry {
    @include type-style('body-long-02');

    flex: 0 0 auto;
    display: inline-flex;
    flex-direction: column;
    justify-content: center;
    color: var(--link-color);
    text-decoration: none;
    margin-right: $spacing-09;

    &:last-child {
      margin-right: 0;
    }

    // targeting specific links for consistent spacing
    &:nth-child(3) {
      margin-right: $spacing-07;
    }

    &:hover {
      text-decoration: underline;
    }

    &:visited  {
      color: var(--link-color);
    }

    &_second-level {
      color: var(--link-color);
      @include type-style('body-long-02');

      display: block;
      padding: $spacing-03 $spacing-05;
      margin-right: 0;
    }

    &_active {
      color: $text-active-color;
       ::v-deep {
          .bx--list-box__label {
            color: $link-visited-color;
        }
      }
    }
  }

  &__home-link {
    margin-left: 0;
    margin-right: auto;

    &:hover {
      text-decoration: none;
    }
  }

  &__hamburger-toggle {
    display: inline-flex;
    flex-direction: column;
    justify-content: center;
  }
}
</style>

<style lang="scss">
@import '~carbon-components/scss/globals/scss/typography';
// Override component styling to match qiskit design
.menu {
  .bx--form-item {
    @include mq($from: large) {
      margin-right: $spacing-07;
    }

    &:hover {
      text-decoration: none;
    }
  }

  .bx--dropdown {
    background: $background-color-white;
    height: initial;
    max-height: initial;
    border-bottom: 1px solid transparent;
  }

  .bx--dropdown--open {
    border-bottom: 1px solid $cool-gray-30;
  }

  .bx--list-box__menu {
    background-color: $background-color-white;
    top: calc(3.25rem + 1px);
    box-shadow: initial;

    &:focus {
      outline: none;
    }
  }

  .bx--dropdown--open,
  .bx--dropdown--open .bx--list-box__menu {
    background: $cool-gray-10;
  }

  .bx--list-box__label {
    @include type-style('body-long-02');
  }

  .bx--dropdown--open .bx--list-box__label {
    color: $text-color;
  }

  // Dropdown button
  .bx--list-box__field {
    display: flex;
    height: calc(3.25rem);

    &:focus {
      outline: none;
    }

    &:hover .bx--list-box__label {
      text-decoration: underline;
    }
  }

  .bx--dropdown-item {
    position: relative;

    // using pseudo element to achieve partial underline
    &:first-child::after {
      content: '';
      border-bottom: 1px solid $cool-gray-30;
      position: absolute;
      width: 80%;
      left: 10%;
      bottom: 0;
    }

    &:hover {
      background-color: $background-color-light;
    }
  }

  .bx--list-box__menu-icon {
    top: initial;
    & > svg {
      fill: var(--link-color);
    }
  }
}
</style>
