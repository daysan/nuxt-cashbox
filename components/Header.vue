<template>
  <div>
    <b-navbar toggleable="lg" type="light" variant="light">
    <div class="container">
    <b-navbar-brand >FreeКасса</b-navbar-brand>

    <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

    <b-collapse id="nav-collapse" is-nav>
      <b-navbar-nav>
        <nuxt-link to="/" class="nav-link" exact>Продажа</nuxt-link>
        <nuxt-link to="/returnsale" class="nav-link">Возврат</nuxt-link>
        <nuxt-link to="/database" class="nav-link">База товаров</nuxt-link>
        <nuxt-link to="/settings" class="nav-link">Настройки</nuxt-link>
      </b-navbar-nav>

      <!-- Right aligned nav items -->
      <b-navbar-nav class="ml-auto">
        <b-button size="lg" variant="primary">Закрыть кассу</b-button>
      </b-navbar-nav>
    </b-collapse>
    </div>
  </b-navbar>
  </div>
</template>

<script>
export default {}
</script>

<style>
  a.nuxt-link-active {
    color: #000 !important;
  }
  .navbar {
  margin-bottom: 20px;
}
</style>
