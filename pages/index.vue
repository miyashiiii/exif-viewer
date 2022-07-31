<template>
  <v-col>

    <v-file-input accept="image/*" @change="getExif"></v-file-input>
    <v-img :src="imgSrc" max-height="300" contain> </v-img>
    <v-simple-table>
      <template v-slot:default>
        <thead>
          <tr>
            <th class="text-left">key</th>
            <th class="text-left">value</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in items" :key="item.name">
            <td>{{ item.key }}</td>
            <td>{{ item.value }}</td>
          </tr>
        </tbody>
      </template>
    </v-simple-table>
  </v-col>
</template>

<script>
import { EXIF } from 'exif-js'
export default {
  name: 'index',
  data: function () {
    return {
      items: [],
      imgSrc: '',
    }
  },
  methods: {
    getExif(e) {
      let reader = new FileReader()
      reader.readAsDataURL(e)
      reader.onload = () => {
        this.imgSrc = reader.result
        var img = new Image()
        img.src = this.imgSrc
        img.onload = () => {
          const _this = this
          EXIF.getData(img, function () {
            _this.items = _this.dictToList(EXIF.getAllTags(this))
          })
        }
      }
    },
    dictToList(dict) {
      const list = []
      for (const key in dict) {
        if (key==="MakerNote")continue
        list.push({ key: key, value: dict[key] })
      }
      return list
    },
  },
}
</script>
