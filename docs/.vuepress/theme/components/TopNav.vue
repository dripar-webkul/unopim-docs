<template>
  <div class="top-nav">
    <div class="nav-text" v-html="displayText"> </div>
  </div>
</template>

<script>
export default {
  name: 'TopNav',

  data() {
    return {
      displayText: '',
    };
  },

  mounted() {
    this.changeText();

    this.$router.afterEach(() => {
      this.isSidebarOpen = false;
      this.changeText();
    });
  },

  methods: {
    changeText() {
      let currentPath = this.$route.path.split('/');
      let version = currentPath[1];

      if (['0.1'].includes(version)) {
        this.displayText = `This is the documentation for the current version (v0.1) of UnoPim. Stay informed and make the most of UnoPim's capabilities.`;
      } else if (['0.2'].includes(version)) {
        this.displayText = `This is the documentation for the current version (v0.2) of UnoPim. Stay informed and make the most of UnoPim's capabilities.`;
      } else if (version === 'master') {
        this.displayText = `WARNING: You're browsing the documentation for an upcoming version. Features of this release are subject to change.`;
      } else {
        this.displayText = `Heads up: You are viewing outdated documentation for UnoPim. Please consider upgrading to <a class="nav-text latest-version-link" href="/0.1/prologue">v1.x</a> for the latest information.`;
      }
    },
  },
}
</script>

<style>
  .top-nav {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    display: flex;
    justify-content: space-between;
    align-items: center;
    background: rgb(255, 164, 35);
    color: #fff;
    padding: 0.5rem;
    z-index: 20;
  }

  .nav-text {
    color: #0e0d0d;
    text-align: center;
    margin: 0 auto;
    font: bold;
  }

  .latest-version-link {
    text-decoration: underline;
  }

  /* Responsive Styles */
  @media (max-width: 500px) {
    .top-nav {
      flex-direction: column;
      align-items: flex-start;
    }
    .nav-text {
      margin-right: 0;
      margin-bottom: 0.5rem;
    }
  }
</style>
