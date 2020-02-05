<template>
  <header>
    <nav class="navbar navbar-expand-lg navbar-light bg-info">
      <div class="container">
        <nuxt-link to="/" class="navbar-brand">Music App</nuxt-link>
        <button
          class="navbar-toggler"
          type="button"
          data-toggle="collapse"
          data-target="#navbarNav"
          aria-controls="navbarNav"
          aria-expanded="false"
          aria-label="Toggle navigation"
        >
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse justify-content-end" id="navbarNav">
          <ul class="navbar-nav">
            <li class="nav-item active">
              <nuxt-link to="/" class="nav-link">Player</nuxt-link>
            </li>
            <li class="nav-item">
              <nuxt-link to="/manager" class="nav-link">Manager</nuxt-link>
            </li>
          </ul>
        </div>
      </div>
    </nav>
  </header>
</template>
<style scoped>
.nav-link,
.navbar-brand {
  color: #ffff !important;
}
</style>