<template>
  <div class="form-container">
    <!-- __x003c_PROJECTINFOPANEL_x003e_ -->

    <!-- __x003c_LOGOPANEL_x003e_ -->
    <!-- __x003c_PROJECTIMAGEPANEL_x003e_ -->
    <!-- &lt;PROJECTNAME&gt; -->
    <!-- &lt;VERSIONDATE&gt; -->
    <div>
      <label for="projectName">Project Name </label>
      <input type="text" v-model="projectName" id="projectName">
    </div>
    <div>
      <label for="versionDate"> Version Date </label>
      <input type="date" v-model="versionDate" id="versionDate">
    </div>
    <div>
      <label for="projectImage"> Project Image </label>
      <input
        type="file"
        accept="image/*"
        name="projectImage"
        id="projectImage"
        @change="onProjectImageUpload"
      >
      <img :src="projectImage" width="300px" />
    </div>
    <div>
      <label for="projectLogo"> Logo </label>
      <input
        type="file"
        accept="image/*"
        name="projectLogo"
        id="projectLogo"
        @change="onProjectImageUpload"
      >
      <img :src="projectLogo" width="300px" />
    </div>
    <!-- <div>
      <button @click="applyTemplate">
        Apply
      </button>
    </div> -->
    <div >
      <img v-for="image in templates" :key="image.key"
        name="svg-images"
        type="image/svg+xml"
        :src="image.pathLong"
        width="300px"
        @click="clickImage(image.pathLong)"
      />
    </div>
    <hr />

    <object :data="selectedTemplate" type="" id="selectedTemplate" @load="applyTemplate"></object>
  </div>
</template>

<script>
export default {
  name: 'DataSetForm',
  data() {
    return {
      projectName: '',
      versionDate: '',
      projectLogo: '',
      projectImage: '',
      templates: [],
      ojbURL: '',
      selectedTemplate: ''
    }
  },
  methods: {
    onProjectImageUpload(e) {
      const files = e.target.files || e.dataTransfer.files
      if (!files.length)
        return
      this.createImage(files[0], e.target.id)
    },
    createImage(file, id) {
      const image = new Image()
      const reader = new FileReader()
      reader.onload = (e) => {
        if (id === 'projectImage') {
          this.projectImage = e.target.result
        } else {
          this.projectLogo = e.target.result
        }
      }
      reader.readAsDataURL(file)
    },
    applyTemplate(evt) {
      console.log(evt)
      const obj = document.getElementById('selectedTemplate').contentDocument
      this.setImages(obj, '__x003c_LOGOPANEL_x003e_', this.projectLogo)
      this.setImages(obj, '__x003c_PROJECTIMAGEPANEL_x003e_', this.projectImage)
      console.log(this.projectImage)
    },
    importAll(r) {
      r.keys().forEach((key, index) => (this.templates.push({key: index, pathLong: r(key), pathShort: key })));
    },
    clickImage(key) {
      this.selectedTemplate = key
    },
    setImages(obj, id, src) {
      const parent = obj.getElementById('Layer_x0020_1')
      const img = obj.getElementById(id)
      const x = img.getAttribute('x') || 0
      const y = img.getAttribute('y') || 0
      const width = img.getAttribute('width') || 0
      const height = img.getAttribute('height') || 0

      img.setAttribute('class', '')
      img.setAttribute('fill', 'transparent')

      const newSVG = this.createSVG(x, y, width, height, src)
      parent.insertBefore( newSVG, parent.firstChild)
    },
    createSVG(x, y, width, height, src) {
      const svgns = "http://www.w3.org/2000/svg";
      const newImg = document.createElementNS(svgns, 'image');
      newImg.setAttribute('x', x)
      newImg.setAttribute('y', y)
      newImg.setAttribute('width', width)
      newImg.setAttribute('height', height)
      newImg.setAttribute('href', src)

      return newImg
    }
  },
  watch: {
    selectedTemplate(val) {
      console.log(val)
    }
  },
  mounted() {
    this.importAll(require.context('../assets/templates', true, /\.svg$/));
},
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
