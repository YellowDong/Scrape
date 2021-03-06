<template>
  <div id="index" v-infinite-scroll="onLoadMore" infinite-scroll-disabled="disabled">
    <el-row>
      <el-col :span="18" :offset="3" class="m-b-lg">
        <el-card v-for="movie in movies" shadow="hover" class="item m-t" :key="movie.name">
          <el-row>
            <el-col :xs="8" :sm="6" :md="4">
              <router-link :to="{ name: 'detail', params: { id: movie.id }}">
                <img :src="movie.cover" class="cover">
              </router-link>
            </el-col>
            <el-col :xs="9" :sm="13" :md="16" class="p-h">
              <router-link :to="{ name: 'detail', params: { id: movie.id }}">
                <h2 class="m-b-sm">{{ movie.name }} - {{ movie.alias }}</h2>
              </router-link>
              <div class="categories">
                <el-button class="category" size="mini" type="primary" :key="category"
                           v-for="category in movie.categories">{{ category }}
                </el-button>
              </div>
              <div class="m-v-sm info">
                <span>{{ movie.regions.join('、') }}</span>
                <span> / </span>
                <span>{{ movie.minute }} 分钟</span>
              </div>
              <div class="m-v-sm info">
                <span>{{ movie.published_at }} 上映</span>
              </div>
            </el-col>
            <el-col :xs="5" :sm="5" :md="4">
              <p class="score m-t-md m-b-n-sm">{{ movie.score.toFixed(1) }}</p>
              <p>
                <el-rate
                    :value="movie.score / 2"
                    disabled
                    :max="5"
                    text-color="#ff9900">
                </el-rate>
              </p>
            </el-col>
          </el-row>
        </el-card>
        <el-card class="item item-flat m-t" shadow="never" v-loading="loading" v-if="!disabled"></el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
  export default {
    name: 'Index',
    components: {},
    data() {
      return {
        loading: false,
        total: null,
        page: parseInt(this.$route.params.page || 1),
        limit: 10,
        movies: null
      }
    },
    computed: {
      disabled() {
        return this.page === 10
      }
    },
    mounted() {
      this.onFetchData()
    },
    methods: {
      onLoadMore() {
        this.page += 1
        this.onFetchData()
      },
      onFetchData() {
        this.loading = true
        this.$axios.get(this.$store.state.url.index, {
          params: {
            limit: this.limit,
            offset: (this.page - 1) * this.limit
          }
        }).then(({data: {results: results, count: total}}) => {
          this.loading = false
          this.movies = this.movies ? this.movies.concat(results) : results
          this.total = total
        })
      }
    }
  }
</script>

<style lang="scss">
  .item {
    .el-card__body {
      padding: 0;
    }
  }

  #index {
    height: 1500px;
    overflow: auto;
  }
</style>
<style lang="scss" scoped>
  .item {
    $height: 220px;
    width: 100%;
    height: $height;

    &.item-flat {
      height: 100px;
    }

    .categories {
      .category {
        padding: 5px 10px;
      }
    }

    .cover {
      width: 100%;
      height: $height;
    }

    .info {
      font-size: 14px;
    }

    .score {
      color: #ffb400;
      font-size: 40px;
      font-weight: bold;
    }
  }

  .pagination {
    float: right;
  }
</style>