<template>
<div>
  <dl class="m-categroy">
    <dt>按拼音首字母选择：</dt>
    <dd v-for="item in list" :key="item">
      <a :href="'#city-'+item">{{item}}</a>
    </dd>
  </dl>
  <dl v-for="item in block" :key="item.title" class="m-categroy-section">
    <dt :id="'city-'+item.title">{{item.title}}</dt>
    <dd>
      <span v-for="c in item.city">{{ c }}</span>
    </dd>
  </dl>
</div>
</template>

<script>
import pinyin from 'js-pinyin';
  export default {
    data() {
      return {
        list: 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'.split(''),
        block:[]
      }
    },
    async mounted () {
      let blocks = []
      let {status, data:{city}} = await this.$axios.get('/geo/city')
      if(status === 200){
        let p 
        let c 
        let d = {} //保存每个拼音字母对应的属性
        city.forEach(item => {
          p = pinyin.getFullChars(item.name).slice(0,1)  //第一个字母
          c = p.charCodeAt(0) //编码
          if(c>64 && c<91){
            if(!d[p]){
              d[p] = []
            }
            d[p].push(item.name)
          }
        })
        for(let [k,v] of Object.entries(d)){
          blocks.push({
            title:k,
            city:v
          })
        }
        blocks.sort((a,b)=>a.title.charCodeAt(0) - b.title.charCodeAt(0))
        this.block = blocks
      }
    },
  }
</script>

<style lang="scss">
@import "@/assets/css/changeCity/categroy.scss"
</style>