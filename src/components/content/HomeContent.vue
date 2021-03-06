<template>
  <div class="home-content layout-content">
    <iv-row>
      <iv-col :xs="24" :sm="24" :md="24" :lg="17">
        <div class="layout-left">
          <iv-affix style="position: relative;z-index: 12;">
            <section-title v-if="this.specialCategory(this.$Window.__category_info__.article) !== undefined"
                           :mainTitle="this.specialCategory(this.$Window.__category_info__.article).name"
                           :subTitle="this.specialCategory(this.$Window.__category_info__.article).subname"
                           :menus="articlesTitleMenus"
                           :withRefresh="true"
                           :withTimeSelect="false"
                           @refresh="refreshArticles"
                           @menusControl="artclesMenusControl">
            </section-title>
          </iv-affix>
          <article-list-cell v-for="article in articles" :article="article" :key="article.id"></article-list-cell>
          <iv-affix :offset-top="0" style="position: relative;z-index: 12;">
            <section-title v-if="this.specialCategory(this.$Window.__category_info__.album) !== undefined"
                           :mainTitle="this.specialCategory(this.$Window.__category_info__.album).name"
                           :subTitle="this.specialCategory(this.$Window.__category_info__.album).subname"
                           :menus="albumsTitleMenus"
                           :withRefresh="true"
                           :withTimeSelect="false"
                           @refresh="refreshAlbums"
                           @menusControl="albumsMenusControl">
            </section-title>
          </iv-affix>
          <div class="topic-cards">
            <iv-row :gutter="10">
              <iv-col :xs="24" :sm="12" :md="8" :lg="8" :xl="8" v-for="album in albums" :key="album.id">
                <topic-card :album="album"></topic-card>
              </iv-col>
            </iv-row>
          </div>
          <iv-affix style="position: relative;z-index: 12;">
            <section-title v-if="this.specialCategory(this.$Window.__category_info__.reading) !== undefined"
                           :mainTitle="this.specialCategory(this.$Window.__category_info__.reading).name"
                           :subTitle="this.specialCategory(this.$Window.__category_info__.reading).subname"
                           :menus="moviesTitleMenus"
                           :withRefresh="true"
                           :withTimeSelect="false"
                           @refresh="refreshMovies"
                           @menusControl="moviesMenusControl">
            </section-title>
          </iv-affix>
          <div class="movies">
            <iv-row :gutter="10">
              <iv-col :xs="12" :sm="12" :md="8" :lg="8" v-for="movie in movies" :key="movie.id"
                      style="margin-bottom: 10px;">
                <movie-list-item :movie="movie"></movie-list-item>
              </iv-col>
            </iv-row>
          </div>
        </div>
      </iv-col>
      <iv-col :xs="24" :sm="24" :md="24" :lg="7">
        <div class="layout-right">
          <about v-if="responsiveRender(false, false, false, true)"></about>
          <recommend style="margin-top:15px;"></recommend>
          <hot style="margin-top:15px;"></hot>
          <friend-links style="margin-top:15px;"></friend-links>
        </div>
      </iv-col>
    </iv-row>
  </div>
</template>

<script type="text/ecmascript-6">
  import ArticleListCell from '@/components/views/Article/ArticleListCell';
  import SectionTitle from '@/components/views/SectionTitle';
  import TopicCard from '@/components/views/TopicCard';
  import MovieListItem from '@/components/views/Movie/MovieListItem';
  import About from '@/components/views/About';
  import Recommend from '@/components/views/Recommend';
  import Hot from '@/components/views/Hot';
  import FriendLinks from '@/components/views/FriendLinks';
  import SideToc from '@/components/views/SideToc';

  // API
  import {getCategory, getPostBaseInfo} from '@/api/api';

  export default {
    data() {
      return {
        categorys: [],
        articles: [],
        mostCommentArticles: undefined,
        hotArticles: undefined,
        recommendArticles: undefined,
        articlesTitleMenus: [
          {title: '评论最多', selected: false, method: 'mostComment'},
          {title: '最热', selected: false, method: 'hot'},
          {title: '推荐', selected: false, method: 'recommend'}
        ],
        albums: [],
        mostCommentAlbums: undefined,
        hotAlbums: undefined,
        recommendAlbums: undefined,
        albumsTitleMenus: [
          {title: '评论最多', selected: false, method: 'mostComment'},
          {title: '最热', selected: false, method: 'hot'},
          {title: '推荐', selected: false, method: 'recommend'}
        ],
        movies: [],
        mostCommentMovies: undefined,
        hotMovies: undefined,
        recommendMovies: undefined,
        moviesTitleMenus: [
          {title: '评论最多', selected: false, method: 'mostComment'},
          {title: '最热', selected: false, method: 'hot'},
          {title: '推荐', selected: false, method: 'recommend'}
        ]
      };
    },
    created() {
      this.getDatas();
    },
    methods: {
      getDatas() {
        // 分类
        getCategory({
          params: {
            level_min: 1,
            level_max: 1
          }
        }).then((response) => {
          this.categorys = response.data.results;
        }).catch((error) => {
          console.log(error);
        });

        this.getArticles();
        this.getAlbums();
        this.getMovies();
      },
      getArticles() {
        // 文章
        getPostBaseInfo({
          params: {
            is_recommend: this.recommendArticles,
            is_hot: this.hotArticles,
            ordering: this.mostCommentArticles,
            limit: 5,
            offset: 0,
            post_type: 'article'
          }
        }).then((response) => {
          this.articles = response.data.results;
        }).catch((error) => {
          console.log(error);
        });
      },
      getAlbums() {
        // 图集
        getPostBaseInfo({
          params: {
            is_recommend: this.recommendAlbums,
            is_hot: this.hotAlbums,
            ordering: this.mostCommentAlbums,
            limit: 6,
            offset: 0,
            post_type: 'album'
          }
        }).then((response) => {
          this.albums = response.data.results;
        }).catch((error) => {
          console.log(error);
        });
      },
      getMovies() {
        // 电影
        getPostBaseInfo({
          params: {
            is_recommend: this.recommendMovies,
            is_hot: this.hotMovies,
            ordering: this.mostCommentMovies,
            limit: 6,
            offset: 0,
            post_type: 'movie'
          }
        }).then((response) => {
          this.movies = response.data.results;
        }).catch((error) => {
          console.log(error);
        });
      },
      refreshArticles() {
        this.mostCommentArticles = undefined;
        this.hotArticles = undefined;
        this.recommendArticles = undefined;
        this.getArticles();
      },
      refreshAlbums() {
        this.mostCommentAlbums = undefined;
        this.hotAlbums = undefined;
        this.recommendAlbums = undefined;
        this.getAlbums();
      },
      refreshMovies() {
        this.mostCommentMovies = undefined;
        this.hotMovies = undefined;
        this.recommendMovies = undefined;
        this.getMovies();
      },
      specialCategory(id) {
        if (this.categorys.length === 0) return undefined;
        return this.categorys.filter((category) => {
          return category.id === id;
        })[0];
      },
      artclesMenusControl(params) {
        switch (params[0]) {
          case 'mostComment':
            this.mostCommentArticles = params[1] ? '-comment_num' : undefined;
            break;
          case 'hot':
            this.hotArticles = params[1] ? true : undefined;
            break;
          case 'recommend':
            this.recommendArticles = params[1] ? true : undefined;
            break;
        }
        this.getArticles();
      },
      albumsMenusControl(params) {
        switch (params[0]) {
          case 'mostComment':
            this.mostCommentAlbums = params[1] ? '-comment_num' : undefined;
            break;
          case 'hot':
            this.hotAlbums = params[1] ? true : undefined;
            break;
          case 'recommend':
            this.recommendAlbums = params[1] ? true : undefined;
            break;
        }
        this.getAlbums();
      },
      moviesMenusControl(params) {
        switch (params[0]) {
          case 'mostComment':
            this.mostCommentMovies = params[1] ? '-comment_num' : undefined;
            break;
          case 'hot':
            this.hotMovies = params[1] ? true : undefined;
            break;
          case 'recommend':
            this.recommendMovies = params[1] ? true : undefined;
            break;
        }
        this.getMovies();
      }
    },
    components: {
      'article-list-cell': ArticleListCell,
      'section-title': SectionTitle,
      'topic-card': TopicCard,
      'movie-list-item': MovieListItem,
      'about': About,
      'recommend': Recommend,
      'hot': Hot,
      'friend-links': FriendLinks,
      'side-toc': SideToc
    }
  };
</script>

<style lang="stylus" type="text/stylus" rel="stylesheet/stylus">
</style>
