<template>
  <div class="form-container">
    <h1>Pro-UP</h1>
    <hr />
    <div class="inputs-container">
      <div>
        <label for="projectName">Project Name </label>
        <input type="text" v-model="projectName" id="projectName">
      </div>
      <div>
        <label for="versionDate"> Version Date </label>
        <input type="date" v-model="versionDate" id="versionDate">
      </div>
      <div class="template-image">
        <label for="projectImage"> Project Image <span>(click to Select)</span></label>
        <input
          type="file"
          accept="image/*"
          name="projectImage"
          id="projectImage"
          style="display: none"
          @change="onProjectImageUpload"
        >
        <img :src="projectImage" width="80px" />
      </div>
      <div class="template-image">
        <label for="projectLogo"> Logo <span>(click to Select)</span></label>
        <input
          type="file"
          accept="image/*"
          name="projectLogo"
          id="projectLogo"
          style="display: none"
          @change="onProjectImageUpload"
        >
        <img :src="projectLogo" width="80px" />
      </div>
    </div>
    <div class="templates">
      <div class="templates__choices">
        <img v-for="image in templates" :key="image.key"
          name="svg-images"
          type="image/svg+xml"
          :src="image.pathLong"
          width="300px"
          @click="clickImage(image.pathLong)"
        />
      </div>
      <div class="templates__seleceted">
        <object :data="selectedTemplate" type="" id="selectedTemplate" @load="applyTemplate"></object>
      </div>
    </div>
    <hr />

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
      const obj = document.getElementById('selectedTemplate').contentDocument
      if (this.projectLogo && this.projectImage && this.projectName && this.versionDate) {
        this.setImages(obj, '__x003c_LOGOPANEL_x003e_', this.projectLogo)
        this.setImages(obj, '__x003c_PROJECTIMAGEPANEL_x003e_', this.projectImage)
        this.applyText(obj)
      } else {
        alert('All fields are required')
      }
      // console.log(this.projectImage)
    },
    importAll(path) {
      path.keys().forEach((key, index) => (this.templates.push({key: index, pathLong: path(key), pathShort: key })));
    },
    clickImage(key) {
      if (this.projectLogo && this.projectImage && this.projectName && this.versionDate){
        this.selectedTemplate = key
      } else {
        alert('All fields are required')
      }
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
    },
    applyText(obj) {
      const texts = obj.getElementsByTagName('text')
      const infoPanel = obj.getElementById('__x003c_PROJECTINFOPANEL_x003e_')

      if (infoPanel) {
        infoPanel.setAttribute('class', '')
        infoPanel.setAttribute('fill', 'transparent')
      }

      for (var key in texts) {
        if (texts.hasOwnProperty(key)) {
          if(texts[key].innerHTML.includes('PROJECTNAME')) {
            texts[key].innerHTML = this.projectName
          }
          if(texts[key].innerHTML.includes('VERSIONDATE')) {
            texts[key].innerHTML = this.versionDate
          }
        }
      }
    }
  },
  mounted() {
    this.importAll(require.context('../assets/templates', true, /\.svg$/));
},
}
</script>

<style scoped>


.form-container h1 {
  margin: 10px 30px;
  
}

.inputs-container {
  margin: 0 30px;
  line-height: 2;
  max-width: 300px;
}

.template-image label {
  cursor: pointer;
}
.template-image span {
  color: silver;
  font-size: 10px;
}

.template-image {
  display: flex;
  flex-direction: column;
}

.template-image label:hover {
  font-weight: 600;
  color: #5a4dff
}

.templates {
  display: grid;
  grid-template-columns: 1fr 3fr;
}

.templates__choices {
  padding: 30px 15px
}

.templates__choices img {
  margin-bottom: 10px;
  cursor: pointer;
}
.templates__choices img:hover {
  outline: solid 5px aqua;
}

.templates__seleceted {
  width: 100%;
  padding: 30px
}

#selectedTemplate {
  max-width: 100%;
  max-height: 100%;
}
</style>
