<template>
  <div>
    <section id="header">
      <span class="before">Before</span>
      <span class="after">After</span>
      <div class="marvel-device macbook" :style="`font-size: ${deviceFontSize}`">
        <div class="top-bar" />
        <div class="camera" />
        <div class="screen">
          <div class="before" />
          <div class="after" />
        </div>
        <div class="bottom-bar" />
      </div>
      <div class="landing">
        <h1 v-html="title" />
        <div class="members">
          <template v-for="({ name, avatar }, i) in members">
            <Avatar :key="`m-${i}`" :name="name" :avatar="avatar" />
          </template>
        </div>
        <div class="intro">
          <template v-for="(line, i) in introLines">
            <p :key="`l-${i}`">
              {{ line }}
            </p>
          </template>
        </div>
      </div>
      <a id="view-now" href="" class="button">看現行網站</a>
      <a id="view-after" href="" class="button">看改造版本</a>
    </section>
    <section id="abstract">
      <nuxt-content class="container" :document="abstract" />
    </section>
    <section id="mc">
      <nuxt-content class="container" :document="mc" />
    </section>
    <section id="conclusion">
      <nuxt-content class="container" :document="conclusion" />
    </section>
  </div>
</template>

<script>
export default {
  layout: 'project',
  async asyncData ({ params, $content }) {
    const { title, intro, screenshot, tags, members, gallery, participating_photos: participatingPhotos, issuu } = (await $content(`${params.project}`).where({ slug: 'index' }).fetch())[0]
    const meta = { title, intro, screenshot, tags, members, gallery, participatingPhotos, issuu }

    const abstract = (await $content(`${params.project}`).where({ slug: 'abstract' }).fetch())[0]
    const mc = (await $content(`${params.project}`).where({ slug: 'mc' }).fetch())[0]
    const conclusion = (await $content(`${params.project}`).where({ slug: 'conclusion' }).fetch())[0]
    return {
      meta, abstract, mc, conclusion
    }
  },
  data () {
    return {
      deviceFontSize: 'calc(720 / 1048 * 1px)'
    }
  },
  computed: {
    title () {
      return this.meta.title.replace('\\n', '<br />')
    },
    introLines () {
      return this.meta.intro.split('\\n')
    },
    members () {
      const projectMetas = require('~/static/project_meta.json')
      const project = projectMetas.find(project => project.slug === this.$route.params.project)
      const collaborators = project ? project.members : projectMetas[0].members
      return this.meta.members.map(member => collaborators.find(collaborator => collaborator.name === member))
    }
  },
  mounted () {
    this.resizeing()
    window.addEventListener('resize', this.resizeing)
  },
  methods: {
    resizeing () {
      const vw = window.innerWidth

      if (vw >= 992) {
        const scaling = Math.min(vw, 1440) / 1440 * 720 / 1048
        this.deviceFontSize = `calc(${scaling} * 1px)`
      } else {
        this.deviceFontSize = '.47328px'
      }
    }
  }
}
</script>

<style>
#header {
  background: linear-gradient(to right, #E0E0E0 25%, #1EBCC6 25%);
  min-height: 1024px;
  display: grid;
  grid-template-rows: 1fr auto 1fr;
  grid-template-columns: repeat(4, 1fr);
  grid-template-areas: 'before after landing landing' 'macbook macbook landing landing' 'view_now view_after landing landing';
}

span.before {
  grid-area: before;
  font-size: 1.5rem;
  font-weight: 700;
  color: rgb(76, 76, 76);
  justify-self: end;
  align-self: end;
  margin: 1.5em;
}

span.after {
  grid-area: after;
  font-size: 1.5em;
  font-weight: 700;
  color: white;
  justify-self: start;
  align-self: end;
  margin: 1.5em;
}

#view-now {
  grid-area: view_now;
  justify-self: end;
  align-self: start;
  background-color: var(--color-gray);
  border-color: var(--color-gray);
}

#view-after {
  grid-area: view_after;
  justify-self: start;
  align-self: start;
  background-color: var(--color-ray);
  border-color: var(--color-ray);
}

.landing {
  grid-area: landing;
  align-self: center;
  justify-self: center;
  max-width: 400px;
  color: white;
}

.landing h1 {
  font-size: 2rem;
  line-break: strict;
}

.landing .members {
  display: grid;
  margin: 1.5em 0 0 0;
  grid-template-columns: repeat(4, auto);
  align-items: start;
}

#header > .macbook {
  grid-area: macbook;
}

#header > .macbook > .screen {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
}

#header > .macbook > .screen > div {
  width: 100%;
  height: 100%;
}

#header > .macbook > .screen > .before {
  background-image: url('~assets/projects/sample/before.jpg');
  background-size: 200%;
  background-position: left top;
}

#header > .macbook > .screen > .after {
  background-image: url('~assets/projects/sample/after.jpg');
  background-size: 200%;
  background-position: left top;
}

#header > .button {
  margin: 3em;
  color: white;
  font-weight: 700;
  padding: .5rem 2rem;
  width: max-content;
  border-radius: 20px;
  border-style: solid;
  font-size: 1.25rem;
  line-height: 1.2;
}

@media screen and (max-width: 991px) {
  #header {
    background: linear-gradient(to right, #E0E0E0 50%, #1EBCC6 50%);
    grid-template-rows: auto auto auto 1fr;
    grid-template-columns: repeat(2, 1fr);
    grid-template-areas: 'before after' 'macbook macbook' 'view_now view_after' 'landing landing';
  }

  span.before, #view-now {

  }

  #header > .macbook {
  }

  .landing {
    max-width: unset;
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    grid-template-areas: 'members title' 'members intro';
  }

  .landing .members {
    grid-area: members;
    grid-template-columns: repeat(2, auto);
    width: 25vw;
    justify-self: end;
    margin-right: 2rem;
    color: initial;
  }

  .landing h1 {
    grid-area: title;
    margin-left: 2rem;
    margin-right: 1rem;
    align-self: end;
  }

  .landing .intro {
    grid-area: intro;
    margin-left: 2rem;
    margin-right: 1rem;
    align-self: start;
  }
}

#abstract .nuxt-content {
  display: grid;
  grid-template-columns: auto 1fr auto;
  align-items: center;
  grid-gap: 3em;
}

#abstract .nuxt-content > figure > img {
  max-width: 550px;
  width: 100%;
}

#abstract .nuxt-content > figure:nth-child(4n+1) {
  grid-column: 1 / 2;
}

#abstract .nuxt-content > figure:nth-child(4n) {
  grid-column: 3 / 4;
}

#abstract .nuxt-content > .content:nth-child(4n+2) {
  grid-column: 2 / 4;
}

#abstract .nuxt-content > .content:nth-child(4n+3) {
  grid-column: 1 / 3;
}

@media screen and (max-width: 767px) {
  #abstract .nuxt-content {
    grid-template-columns: 1fr;
  }

  #abstract .nuxt-content > figure:nth-child(4n+1) {
    grid-column: 1 / 2;
  }

  #abstract .nuxt-content > figure:nth-child(4n) {
    grid-column: 1 / 2;
  }

  #abstract .nuxt-content > .content:nth-child(4n+2) {
    grid-column: 1 / 2;
  }

  #abstract .nuxt-content > .content:nth-child(4n+3) {
    grid-column: 1 / 2;
  }
}
</style>
