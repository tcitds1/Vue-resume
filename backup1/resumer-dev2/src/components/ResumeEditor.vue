<template>
    <div id="resumeEditor">
        <!--i am resumeEditor-->
        <nav>
            <ol>
                <li v-for="(item,index) in resumeConfig" 
                     :class="{active: item.field === selected}"
                     @click="selected = item.field;">

                    <svg class="icon">
                        <use :xlink:href="`#icon-${item.icon}`"></use>
                    </svg>
                </li>
            </ol>
        </nav>
        <ol class = "panels">
            <li v-for="item in resumeConfig" v-show="item.field === selected">
                <div v-if = "item.type === 'array'">
                    <div v-for="(subitem, i) in resume[item.field]">
                        <button class="button remove small" @click="removeResumeSubfield(item.field, i)">删除</button>
                        <div class="resumeField"v-for="(value,key) in subitem">
                            <label>{{key}}</label>
                            <input type="text" :value="value"  @input="changeResumeField(`${item.field}.${i}.${key}`, $event.target.value)">
                            <hr>
                        </div>
                    </div>
                    <button class="button" @click="addResumeSubfield(item)">新增</button>
                </div>
                <div v-else class="resumeField" v-for="(value,key) in resume[item.field]">
                    <label> {{key}} </label>
                    <input type="text" :value="value" @input="changeResumeField(`${item.field}.${key}`, $event.target.value)">
                </div>
                <!--<button @click = "addResumeSubfield(item)">新建</button>-->
            </li>
            <!--<li>
                {{count}}
                <button @click="add">test</button>
            </li>-->
        </ol>
    </div>
</template>
<script>
import getAVUser from '../getAVUser'
export default {
  name: 'ResumeEditor',
  methods: {
    changeResumeField (path, value) {
      this.$store.commit('updateResume', {path, value})
    },
    test (value) {
      console.log(value)
    },
    addResumeSubfield (item) {
      this.$store.commit('addResumeSubfield', item)
      if (getAVUser().id) {
        this.$store.dispatch('saveResume')
      }
      console.dir(this.$store.state.resume[item['field']])
    },
    removeResumeSubfield (field, index) {
      this.$store.commit('removeResumeSubfield', {field, index})
      if (getAVUser().id) {
        this.$store.dispatch('saveResume')
      }
    }
  },
  computed: {
    // count () {
    //   return this.$store.state.count
    // },
    resumeConfig () {
      return this.$store.state.resumeConfig
    },
    resume () {
      return this.$store.state.resume
    },
    selected: {
      get () {
        return this.$store.state.selected
      },
      set (val) {
        return this.$store.commit('switchTab', val)
      }
    },
    profile () {
      this.$store.state.profile
    }
  }
}
</script>
<style lang="scss" scoped>
  #resumeEditor{
    background:#ffffff;
    box-shadow:0 1px 3px 0 rgba(0,0,0,0.25);
    display: flex;
    flex-direction: row;
    overflow: auto;
    > nav{
      width: 80px;
      background: black;
      color: white;
      > ol {
        > li{
          height: 48px;
          display: flex;
          justify-content: center;
          align-items: center;
          margin-top: 16px;
          margin-bottom: 16px;
          &.active{
            background: white;
            color: black;
          }
        }
      }
    }
    > .panels{
      flex-grow: 1;
      > li {
        padding: 24px;
        h2{
          margin-bottom: 24px;
        }
      }
      overflow: auto
    }
    svg.icon{
      width: 24px; // 原设计稿 32px 不好看，改成 24px
      height: 24px;
    }
  }
  ol{
    list-style: none;
  }
  .resumeField{
    > label{
      display: block;
    }
    input[type=text]{
      margin: 16px 0;
      border: 1px solid #ddd;
      box-shadow:inset 0 1px 3px 0 rgba(0,0,0,0.25);
      width: 100%;
      height: 40px;
      padding: 0 8px;
    }
  }
  hr{
    border: none;
    border-top: 1px solid #ddd;
    margin: 24px 0;
  }
  .subitem{
    position: relative;
    .button.remove{
      position: absolute;
      right: 0;
      top: -3px;
    }
  }
  </style>