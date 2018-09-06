<template>
  <div >
    <h2>Articles</h2>
<form class="mb-3" @submit.prevebt="addArticle" action="" method="">     <!--34:38-->
  <div class="form-group">
    <input type="text" class="form-control" placeholder="Title"  v-model="article.title">
  </div>
  <div class="form-group">
    <textarea class="form-control" placeholder="Body"  v-model="article.body"></textarea>
  </div>
  <button type="submit" name="button" class="btn btn-light btn-block">Save</button>
</form>
    <nav aria-label="Page navigation example">     <!--25:49-->
      <ul class="pagination">
          <!--pagination.prev_page_url ln57-->
        <li v-bind:class="[{disabled: !pagination.prev_page_url}]" class="page-item"><a class="page-link" href="#" @click="fetchArticles(pagination.prev_page_url)">Previous</a></li>
         <!--30:00-->
         <li class="page-item disabled"><a class="page-link text-dark" href="#">page{{pagination.current_page}} of {{pagination.last_page}}</a></li>

        <li v-bind:class="[{disabled: !pagination.next_page_url}]" class="page-item"><a class="page-link" href="#" @click="fetchArticles(pagination.next_page_url)">Next</a></li>
      </ul>
    </nav>

    <div class="card card-body" v-for="article in articles" v-bind:key="article.id" mb-2>

    <h3>{{article.title}}</h3>
    <p>{{article.body}}</p>
    <hr> <!--3120-->
   <button @click="editArticle(article)" class="btn btn-warning mb-2">Edit</button>   <!--40:20-->
    <button @click="deleteArticle(article.id)" class="btn btn-danger">Delete</button>

    </div>
  </div>
</template>

<script>
   export default {
     data(){
       return{
         articles:[],
         article: {
           id:'',
           title:'',
           body:''
         },
         article_id:'',   //that field in the db is brouht through the route put and post created in the breviuse broject
         pagination:{},  //?
         edit: false
       }
     },
       created(){
         this.fetchArticles();
       },
       methods:{
         fetchArticles(page_url){
           let vm = this;   //<=giving a diffrent value for this to use in function makePagination ln 39
           page_url = page_url || '/api/articles';  //   ||  <=OR
           fetch(page_url)        //fetch('api/articles') <=breviusly      //he brefer to do it in a custm way not in an components way
           .then(res => res.json())
           .then(res => {
            this.articles = res.data;  //ln17
            vm.makePagination(res.meta,res.links); //from http://127.0.0.1:8000/api/articles
           })
           .catch(err => console.log(err));
         },
         makePagination(meta, links){
           let pagination = {
             current_page: meta.current_page,               //Q:what is meta?
             last_page:meta.last_page,
             next_page_url:links.next,
             prev_page_url:links.prev
           }
           this.pagination = pagination;  //ln44=ln24
         },
//ln10
          deleteArticle(id){
           if(confirm('Are you Sure?')){
             fetch(`api/article/${id}` , {method: 'delete'})
             .then(res=>res.json())
             .then(data=>{alert('Article Removed');
             this.fetchArticles();
           })
           .catch(err =>console.log(err));
         }
         },
  //ln46
         addArticle(){
            if(this.edit === false){
              fetch('api/article' , {method: 'post',
                                           body:JSON.stringify(this.article),
                                          headers:{'content-type':'application/json'}
                                          })
              .then(res=>res.json())
              .then(data => {
                this.article.title = '';
                this.article.body='';
                alert('Article Added');
                this.fetchArticles();
              })
            } else { //updated
              fetch('api/article' , {method: 'put',
                                           body:JSON.stringify(this.article),
                                          headers:{'content-type':'application/json'}
                                          })
              .then(res=>res.json())
              .then(data => {
                this.article.title = '';
                this.article.body='';
                alert('Article updated');
                this.fetchArticles();
              })

            }

          },

         editArticle(article){
           this.edit=true;
           this.article.id=article.id;
           this.article.article_id=article.id;
           this.article.title=article.title;
           this.article.body=article.body;
         }
      }
   };
</script>
