<template>
  <div id="app" class="container">

    <nav class="navbar navbar-light bg-light navbar-expand-lg fixed-top">
      <a class="navbar-brand" href="#"><img src="../assets/logo.png" height="40" width="40" alt="Outrigger Logo" /></a>
      <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarNavDropdown">
        <ul class="navbar-nav mr-auto">
          <li class="nav-item"><a class="nav-link" href="http://outrigger.sh" title="Outrigger Homepage">What is Outrigger?</a></li>
          <li class="nav-item"><a class="nav-link" href="http://docs.outrigger.sh" title="Technical Documentation">Documentation</a></li>
          <li class="nav-item"><a class="nav-link" href="https://github.com/search?o=desc&q=topic%3Aoutrigger+org%3Aphase2&s=updated&type=Repositories" title="Review all the Outrigger repositories on Github"><i class="fa fa-github" aria-hidden="true"></i>&thinsp;Contribute</a></li>
          <li class="nav-item"><a class="nav-link" href="http://slack.outrigger.sh/" title="Join us on Slack!"><i class="fa fa-slack" aria-hidden="true"></i>&thinsp;Slack</a></li>
        </ul>
        <ul class="navbar-nav ml-auto">
          <li role="separator" class="dropdown-divider"></li>
          <li class="nav-item dropdown">
            <a class="nav-link dropdown-toggle" href="#" id="supportDropdown" role="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
              File an Issue
            </a>
            <div class="dropdown-menu dropdown-menu-right" aria-labelledby="supportDropdown">
              <a class="dropdown-item" href="https://github.com/phase2/outrigger-dashboard/issues">Dashboard issues</a>
              <a class="dropdown-item" href="https://github.com/phase2/outrigger-docs/issues">Documentation issues</a>
              <a class="dropdown-item" href="https://github.com/phase2/rig/issues">Outrigger CLI (rig) issues</a>
            </div>
          </li>
        </ul>
      </div>
    </nav>

    <header class="jumbotron">
      <h1 class="display-3 mt-5">Outrigger Dashboard</h1>
    </header>

    <main class="container">
      <div class="row">
        <dns-records :entries="dnsRecords"></dns-records>
      </div>
      <project-list :projects="projects"></project-list>
    </main>
  </div>
</template>

<script>
import DnsRecords from './DnsRecords.vue'
import ProjectList from './ProjectList.vue'
import store from '../store'

export default {
  name: 'app',
  data () {
    return {
      dnsRecords: {},
      projects: {}
    }
  },
  components: {
    DnsRecords,
    ProjectList
  },

  created () {
    this.update()

    let self = this
    this.ws = new WebSocket('ws://' + window.location.host + '/api/containers/ws')
    this.ws.addEventListener('message', function (e) {
      self.projects = store.processContainersByProject(JSON.parse(e.data))
      self.loadDnsRecords()
    })
  },

  methods: {
    update () {
      this.loadDnsRecords()
      this.loadContainers()
    },

    loadDnsRecords() {
      store.fetchDnsRecords().then(items => {
        this.dnsRecords = items
      })
    },

    loadContainers() {
      store.fetchContainersByProject().then(items => {
        this.projects = items
      })
    }
  }

}
</script>
<style>
  .jumbotron {
    background-color: #ffffff;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .logo-image {
    max-width: 150px;
  }

  .media-body {
    max-width: 650px;
  }
</style>
