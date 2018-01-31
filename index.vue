<template>
  <div>
    <list>
      <refresh class="refresh" @refresh="onrefresh" @pullingdown="onpullingdown" :display="refreshing">
          <text class="indicator">Refreshing ...</text>
      </refresh>
      <cell v-for="news in newslist">
        <div class="panel">
          <text class="text">{{news.newsTitle}}</text>
          <text class="content">{{news.newsContent}}</text>
        </div>
      </cell>
      <loading class="loading" @loading="onloading" :display="showLoading">
          <text class="indicator">Loading...</text>
      </loading>
    </list>
  </div>
</template>
<style scoped>
    .panel{
    width:600px;
    height:250px;
    margin-left:75px;
    margin-top:35px;
    margin-bottom:35px;
    flex-direction: column;
    justify-content: center;
    border-width: 2px;
    border-style: solid;
    border-color:rgb(162,217,192);
    background-color:rgba(162, 217, 192, 0.2);
    padding-top:15px;
    padding-left:10px;
    padding-right:10px;
  }
  .text{
    font-size:50px;
    text-align: center;
    color:#41B883;
  }
  .indicator{
    font-size: 42px;
    text-align: center;
  }
  .content{
    lines:3;
    font-size: 28px;
  }
</style>
<script>
  const modal = weex.requireModule('modal');
  //用于实现网络请求
  const stream = weex.requireModule('stream');
  export default {
    data () {
      return {
        newslist:[],
        showLoading:"hide",
        refreshing:"hide",
        newsData:[],
      }
    },
    created(){
      let url="http://www.jspang.com/DemoApi/newsApi.php";
      this.getUrl(url,res=>{
        modal.toast({
          message:"请求成功！",
          duration: 1 
        });
        this.newslist=res.data;
        this.newsData=res.data;
        console.log(res.data);
      });
    },
    methods:{
        getUrl(url,callback){
          return stream.fetch({
            method:'GET',
            type:'json',
            url:url
          },callback)
        },
        //下拉加载
        onloading(event) {
          modal.toast({ message: 'loading', duration: 1 });
          this.showLoading = 'show';
            setTimeout(() => {
              const length = this.newslist.length;
              for (let i = length; i < length + 4; i++) {
                this.newslist.push(i + 1);
                // newslist的数据结构你都定义好了， 你还往里push数值？？？
              }
              this.showLoading = 'hide';
            }, 1500)
          },
        //上拉刷新
        onrefresh(event){
          modal.toast({message:'refresh',duration:1});
          this.refreshing="show";
            setTimeout(() => {
              this.newslist=this.newsData;
              this.refreshing="hide";
            }, 1500);
        },
         //上拉刷新(只拉一点也能刷新,不用拉到底)
        onpullingdown(event){
          modal.toast({message:'onpullingdown',duration:1});
          this.refreshing="show";
          setTimeout(() => {
            this.newslist=this.data;
            this.refreshing="hide";
          }, 1500);
        }
    }
  }
</script>
