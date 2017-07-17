<template>
    <div id="app">
      <StyleEditor ref="styleEditor" :code="currentStyle"></StyleEditor>
      <ResumeEditor ref="resumeEditor" :markdown="currentMarkdown" :enableHtml="enableHtml"></ResumeEditor>
    </div>
</template>

<script>
import StyleEditor from './components/StyleEditor'
import ResumeEditor from './components/ResumeEditor'
export default {
  name: 'app',
  components:{
    StyleEditor,
    ResumeEditor
  },
  data(){
    return {
      interval:50,
      currentStyle:'',
      enableHtml:false,
      fullStyle:[
`/*
*Inspired by http://strml.net/ &方应航老师
*Hello,我是 wangdan
*想要找一份前端的工作，最近get到了新技能，用一种炫酷的技能来准备一份简历。
*那就开始了！
*/
/*首先给所有元素加上过渡效果*/
*{
  -webkit-transition: all .3s;
  transition: all .3s;
}
/*现在来变一下背景*/
html{
  color:rgb(222,222,222); background:rgb(0,43,54);
}
/*文字离边框太近了*/
.styleEditor{
  padding:.5em;
  border:1px solid;
  margin:.5em;
  overflow:auto;
  width:45vm;
  height:90vh;
}
/*代码高亮*/
.token.selector{ color:rgb(133,153,0);}
.token.property{ color:rgb(187,137,0);}
.token.punctuation{ color:yellow;}
.token.function{ color:rgb(42,161,152);}

/*加点3D效果呗*/
html{
  -webkit-perspective:1000px;
          perspective:1000px;
}
.styleEditor{
  position:fixed;left:0;top:0;
  -webkit-transition:none;
  transition:none;
  -webkit-transform:rotateY(10deg) translateZ(-100px);
          transform:rotateY(10deg) translateZ(-100px);
}
/*接下来我给自己准备了一个编译器*/
.resumeEditor{
  position:fixed; right:0; top:0;
  padding:.5em; margin:.5em;
  width:48vw; height:90vh;
  border: 1px solid;
  background:white; color:#222;
  overflow:auto;
}
/*好了，我开始写简历了*/
      

      `,
          `
/*这个简历好像差了点什么
 *对了，这是Markdown格式的，我需要变成对HR更友好的格式
 *简单，用开源工具翻译成HTML格式就行了
 */          
          `
          ,
          `
/*再对HTML加点样式*/
.resumeEditor{
  padding:2em;
}
.resumeEditor h2{
  display: inline-block;
  border-bottom: 1px solid;
  margin: 1em 0 .5em;
}
.resumeEditor ul,.resumeEditor ol{
  list-style: none;
}
.resumeEditor ul> li::before{
  content: '•';
  margin-right: .5em;
}
.resumeEditor ol {
  counter-reset: section;
}
.resumeEditor ol li::before {
  counter-increment: section;            
  content: counters(section, ".") " ";  
  margin-right: .5em;
}
.resumeEditor blockquote {
  margin: 1em;
  padding: .5em;
  background: #ddd;
}
`],
          currentMarkdown:'',
          fullMarkdown:`王丹
----
前端开发工程师。

技能
----
*前端开发
*HTML、CSS、JS
*vue.js

工作经历
----
1.2016.4-2017.6 上海卓云科技有限公司
2.2015.4-2016.4 杭州诗迈医药科技有限公司
3.2014.10-2015.3 杭州创诺科技有限公司

`

    }
  },
  created(){
    this.makeResume()
  },
  methods:{
    makeResume: async function(){
      await this.progressivelyShowStyle(0)
      await this.progressivelyShowResume()
      await this.progressivelyShowStyle(1)
      await this.showHtml()
      await this.progressivelyShowStyle(2)
    },
    showHtml:function(){
      return new Promise((resolve,reject)=>{
        this.enableHtml=true
        resolve()
      })
    },
    progressivelyShowStyle(n){
      return new Promise((resolve,reject)=>{
        let interval =this.interval
        let showStyle=(async function(){
          let style=this.fullStyle[n]
          if(!style) {return}
          //计算前n个style的字符总数
          let length=this.fullStyle.filter((_,index) => index <=n).map((item)=>item.length).reduce((p,c)=>p+c,0)
          let prefixLength=length-style.length
          if(this.currentStyle.length<length){
            let l=this.currentStyle.length-prefixLength
            let char=style.substring(l,l+1)||' '
            this.currentStyle+=char
            if(style.substring(l-1,l)==='\n'&&this.$refs.styleEditor){
              this.$nextTick(()=>{
                this.$refs.styleEditor.goBottom()
              })
            }
            setTimeout(showStyle,interval)
          }else{
            resolve()
          }
        }).bind(this)
        showStyle()
      })
    },
      progressivelyShowResume() {
        return new Promise((resolve, reject) => {
          let length = this.fullMarkdown.length
          let interval = this.interval
          let showResume = () => {
            if (this.currentMarkdown.length < length) {
              this.currentMarkdown = this.fullMarkdown.substring(0, this.currentMarkdown.length + 1)
              let lastChar = this.currentMarkdown[this.currentMarkdown.length - 1]
              let prevChar = this.currentMarkdown[this.currentMarkdown.length - 2]
              console.log(prevChar)
              if (prevChar === '\n' && this.$refs.resumeEditor) {
                this.$nextTick(() => this.$refs.resumeEditor.goBottom())
              }
              setTimeout(showResume, interval)
            } else {
              resolve()
            }
          }
          showResume()
        })
      }

  }

}
</script>

<style scoped>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
html{
  min-height:100vh;
}
*{
  -webkit-transition:all .3s;
  transition: all .3s;
}
</style>
