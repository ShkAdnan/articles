<template>
    <div>
        <h2>Articles</h2>

         <div class="form-group">
            <input type="text" class="form-control"  v-model="search" placeholder="Search">
        </div>

        <nav aria-label="Page navigation example">
            <ul class="pagination">
                <li v-bind:class="[{ disabled: !pagination.prev_page_url }]" class="page-item">
                    <a class="page-link" @click="fetchArticle(pagination.prev_page_url)" href="#">Previous</a>
                </li>

                <li class="page-item disabled">
                    <a class="page-link text-dark" href="#">{{ pagination.current_page }} of {{ pagination.last_page }}</a>
                </li>

                <li v-bind:class="[{ disabled: !pagination.next_page_url }]" class="page-item">
                    <a class="page-link" @click="fetchArticle(pagination.next_page_url)" href="#">Next</a>
                </li>
            </ul>
        </nav>
        <div class="card card-body" v-for="article in filterArticles" :key="article.id">
            <h3>{{ article.title }}</h3>
            <p> {{ article.body }}</p>
        </div>
    </div>
</template>

<script>

export default({
    data(){
        return{
            articles : [],
            article : {
                id    : '',
                title : '',
                body  : ''
            },
            article_id : '',
            pagination : {},
            edit       : false,
            search     : ''
        }
    },
    created(){
        this.fetchArticle();
    },
    methods : {
        
        fetchArticle(page_url){
            let vm = this;
            page_url = page_url || 'api/articles'
            fetch(page_url)
            .then(res => res.json() )
            .then( res => {
                this.articles = res.data
                vm.makePaginations( res.meta , res.links ) 
            })
            .catch(err => console.log( "Error" ) )
        },

        makePaginations( meta , links ){
            let pagination = {
                current_page  : meta.current_page,
                last_page     : meta.last_page,
                next_page_url : links.next,
                prev_page_url : links.prev
            }

            this.pagination = pagination;
        }
    },
    computed : {
        filterArticles : function() {
            return this.articles.filter( ( article ) => {
                return article.title.match( this.search );
            } )
        }
    }
})

</script>