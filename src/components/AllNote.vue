<template>
  <div id="all-note">
    <NoteHead />
    <NoteNav />
    <div class="note-box">
      <div class="note-view-left">
          <div class="note" v-for="(note,index) in leftNotes" :key="note.title">
            <router-link :to="'/note/' + note.id">
              <div class="note_info">
                <img class="note_info__cover" :src="note.image[0].img" alt="">
                <p class="note_info__title">{{note.title}}</p>
              </div>
            </router-link>
              <div class="note_author">
                <router-link v-bind:to="'/note/' + note.id">
                  <div class="author_info">
                    <div class="photo">
                      <img :src="note.headPortrait" alt="">
                    </div>
                    <span class="name">{{note.userName}}</span>
                  </div>
                </router-link>
                <div class="like">
                  <i class="iconfont icon-aixin notLike" v-if="!note.isLike" @click="isLiked_left(note.id,index)"></i>
                  <i class="iconfont icon-xin1 like" v-if="note.isLike" @click="isLiked_left(note.id,index)"></i>
                  <span>{{note.like}}</span>
                </div>
              </div>
          </div>
      </div>
      <div class="note-view-right">
        <div class="note" v-for="(note,index) in rightNotes" :key="note.title">
          <router-link v-bind:to="'/note/' + note.id">
            <div class="note_info">
              <img class="note_info__cover" :src="note.image[0].img" alt="">
              <p class="note_info__title">{{note.title}}</p>
            </div>
          </router-link>
            <div class="note_author">
            <router-link v-bind:to="'/note/' + note.id">
              <div class="author_info">
                <div class="photo">
                  <img :src="note.headPortrait" alt="">
                </div>
                <span class="name">{{note.userName}}</span>
              </div>
            </router-link>
              <div class="like">
                  <i class="iconfont icon-aixin notLike" v-if="!note.isLike" @click="isLiked_right(note.id,index)"></i>
                  <i class="iconfont icon-xin1 like" v-if="note.isLike" @click="isLiked_right(note.id,index)"></i>
                  <span>{{note.like}}</span>
              </div>
            </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

import axios from 'axios'
import NoteHead from './NoteHead'
import NoteNav from './NoteNav'

export default {
  name: "all-note",
  data() {
    return {
      notes: [],//笔记列表
      likeNotes:[]
    }
  },
  watch:{
    likeNotes:function(){
      console.log('change')
    }
  },
  components:{
    NoteHead,
    NoteNav
  },
  methods:{
    isLiked_left(id,index){
      this.leftNotes[index].isLike = !this.leftNotes[index].isLike;
      if(this.leftNotes[index].isLike) {
        this.leftNotes[index].like++
      }else{
        this.leftNotes[index].like--
      }
      //把点赞了的笔记的信息放入store中做统一整理
      this.$store.dispatch("likeNotes",{"id":id,"likeNum":this.leftNotes[index].like,"isLike":this.leftNotes[index].isLike});
      
    },

    isLiked_right(id,index){
      this.rightNotes[index].isLike = !this.rightNotes[index].isLike;
      if(this.rightNotes[index].isLike) {
        this.rightNotes[index].like++
      }else{
        this.rightNotes[index].like--
      }
      this.$store.dispatch("likeNotes",{"id":id,"likeNum":this.rightNotes[index].like,"isLike":this.rightNotes[index].isLike});
    
    }
  },
  created() {
    var that = this;
    axios.get('https://www.easy-mock.com/mock/5aa1e2e4edefdc232c585994/getInfo/data')
    .then(function(data) {
      that.notes = data.data.data.noteDetails;
      console.log(that.likeNotes)
      var aaa = that.notes.map(note => {
        return {
          id: note.id,
          likeNum: note.like
        }
      })
    });
    

  },
  computed: {
    leftNotes() {
      var searchText = this.$store.getters.searchContent;
      return this.notes.filter((note) => {
        if(searchText) {
          // 根据输入搜索框中的内容来筛选数据
          return (note.title.match(searchText) || note.userName.match(searchText) ) && note.page === "left";
        }else {
          return note.page === "left";
        }
      })
    },
    rightNotes() {
      var searchText = this.$store.getters.searchContent;
      return this.notes.filter((note) => {
        if(searchText) {
          return (note.title.match(searchText) || note.userName.match(searchText) ) && note.page === "right";
        }else {
          return note.page === "right";
        }
      })
    }
  }
}
</script>

<style scoped>
#all-note{
  width: 100%;
  height: 100%;
  position: relative;
  top: 3.653333rem;
  background-color: #F5F8FA;
}
#all-note a{
  text-decoration: none;
  color:#000;
}
.note-box{
  width: 10rem;
  margin: 0 auto;
  overflow-x: auto;
  padding-top: .133333rem /* 10/75 */;
}
.note-view-left{
  width: 4.666667rem /* 350/75 */;
  float: left;
  margin: .08rem /* 6/75 */ .186667rem /* 14/75 */;
}
.note-view-right{
  width: 4.666667rem /* 350/75 */;
  float: left;
  margin: .08rem /* 6/75 */ .186667rem /* 14/75 */ 0.08rem 0;
}
.note{
  background-color: #fff;
  width: 4.666667rem /* 350/75 */;
  margin: 0  .053333rem /* 4/75 */ .24rem /* 18/75 */;
  box-shadow: 0 1px 2px 0 rgba(0,0,0,.1);
  border-radius: .16rem /* 12/75 */;
  position: relative;
  text-decoration: none;
  cursor: pointer;
}
.note-info{
  position: relative;
  overflow: hidden;
  width: 100%;
  height: 100%;
  display: block;
  text-decoration: none;
}
.note_info__cover{
  width: 4.666667rem /* 350/75 */;
  border-top-left-radius: .213333rem /* 16/75 */;
  border-top-right-radius: .213333rem /* 16/75 */;
}
.note_info__title{
  font-size: .32rem /* 24/75 */;
  overflow: initial;
  white-space: normal;
  font-weight: 700;
  margin: .266667rem /* 20/75 */ .266667rem /* 20/75 */;
  overflow: hidden;
}
.note_author{
  padding: 0 .266667rem /* 20/75 */ .346667rem /* 26/75 */;
  display: flex;
  align-items: center;
}
.author_info{
  flex: 1;
  width: 2.7rem;
}
.photo{
  position: relative;
  display: block;
  float: left;
}
.photo img{
  width: .746667rem /* 56/75 */;
  height: .746667rem /* 56/75 */;
  border-radius: 50%;
  display: inline-block;
}
.name{
  display: block;
  float: left;
  margin-left: .08rem /* 6/75 */;
  line-height: .8rem /* 60/75 */;
  color: #616161;
  font-weight: 350;
  font-size: .266667rem /* 20/75 */;
}
.like{
  color: #8F8F8F;
  /* width: 3rem; */
}
.like i{
  margin-right: -.053333rem /* 4/75 */;
}
.icon-xin1{
  color: red
}
</style>
