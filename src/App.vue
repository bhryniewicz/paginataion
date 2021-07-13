<template>
    <div id="app">
        <div class="post">
            <h1 class="post__title">Pagination Vue Project with REST API</h1>
            <div class="list" v-for="p in displayedPosts" v-bind:key="p.id">
                <ul class="list__list">
                    <li class="list__wrapper">
                        <div class="list__titleWrapper">
                            <h1 class="list__id">#{{ p.id }}</h1>
                            <h2 class="list__title">{{ p.title }}</h2>
                        </div>
                        <button
                            class="list__button"
                            v-on:click="removeElement(p.id)"
                        >
                            X
                        </button>
                        <p v-if="open" class="list__description">
                            {{ p.body.substring(0, 80) }}
                        </p>
                        <p class="list__description" v-else>
                            {{ p.body }}
                        </p>
                        <button
                            class="list__interested"
                            v-if="open"
                            @click="open = !open"
                        >
                            read more
                        </button>
                        <button
                            class="list__interested"
                            v-else
                            @click="open = !open"
                        >
                            read less
                        </button>
                    </li>
                </ul>
            </div>
            <nav class="pagination">
                <ul class="pagination__list">
                    <li class="pagination__item">
                        <button
                            type="button"
                            class="pagination__previous"
                            v-if="page != 1"
                            @click="page--"
                        >
                            Previous
                        </button>
                    </li>
                    <li class="pagination__item">
                        <button
                            type="button"
                            class="pagination__link"
                            v-bind:key="pageNumber"
                            v-for="pageNumber in pages.slice(
                                page - 1,
                                page + 5
                            )"
                            @click="page = pageNumber"
                        >
                            {{ pageNumber }}
                        </button>
                    </li>
                    <li class="pagination__item">
                        <button
                            type="button"
                            @click="page++"
                            v-if="page < pages.length"
                            class="pagination__next"
                        >
                            Next
                        </button>
                    </li>
                </ul>
            </nav>
        </div>
    </div>
</template>

<script>
import axios from "axios";
// import ReadMore from 'vue-read-more';
export default {
    data() {
        return {
            usersData: [],
            postsData: [],
            page: 1,
            perPage: 10,
            pages: [],
            posts: [],
            users: [],
            open: true,
        };
    },
    mounted() {
        const posts = "https://jsonplaceholder.typicode.com/posts";
        const users = "https://jsonplaceholder.typicode.com/users";
        axios.get(posts).then((response) => {
            this.postsData = response.data;
            this.posts = this.postsData;
        });
        axios
            .get(users)
            .then((response) => {
                return response.data;
            })
            .then((data) => {
                this.usersData = data;
                this.users = this.usersData
            });
    },

    methods: {
        setPages() {
            console.log(this.postsData);
            console.log(this.users)
            let numberOfPages = Math.ceil(this.postsData.length / this.perPage);
            for (let index = 1; index <= numberOfPages; index++) {
                this.pages.push(index);
            }
        },
        paginate() {
            console.log(this.postsData);
            let page = this.page;
            let perPage = this.perPage;
            let from = page * perPage - perPage;
            let to = page * perPage;

            return this.postsData.slice(from, to);
        },
        removeElement(id) {
            this.postsData = this.postsData.filter((post) => post.id !== id);
        },
    },
    computed: {
        displayedPosts() {
            return this.paginate();
        },
    },
    watch: {
        posts() {
            this.setPages();
        },
    },
};
</script>

<style lang="scss">
$primary-bgc: rgb(233, 233, 233);
$button-bgc: rgb(12, 91, 28);
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
}

li {
    list-style-type: none;
}

.post {
    background-color: $primary-bgc;
    padding: 10px;
    position: absolute;
    left: 50%;
    transform: translateX(-50%);

    &__title {
        font-size: 50px;
        margin-bottom: 30px;
    }
}

.list {
    margin: 50px 0;

    &__wrapper {
        display: grid;
        grid-template-columns: 50px 1fr 60px;
        grid-template-rows: 60px 1fr;
    }

    &__id {
        padding-right: 20px;
    }

    &__titleWrapper {
        grid-column: 1/3;
        display: flex;
        align-items: center;
    }

    &__title {
        text-transform: capitalize;
        grid-column: 2/3;
        text-align: center;
    }

    &__description {
        grid-column: 1/3;
        grid-row: 2/3;
        padding: 0 40px;
        margin-top: 30px;

        &::first-letter {
            text-transform: uppercase;
        }
    }

    &__interested {
        grid-column: 1/3;
        position: relative;
        left: 50%;
        width: 250px;
        text-transform: uppercase;
        transform: translateX(-50%);
        font-size: 16px;
        padding: 7px 0;
        background-color: transparent;
        border: 2px solid $button-bgc;
        color: $button-bgc;
        border-radius: 20px;
    }

    &__button {
        grid-column: 3/4;
        grid-row: 1/2;
        border: none;
        background-color: rgb(101, 237, 128);
        color: white;
        width: 20px;
        height: 20px;
        margin-left: 20px;
        cursor: pointer;
    }
}
.pagination {
    margin-top: 100px;
    // flex: 1;

    &__link {
        border: 2px solid $button-bgc;
        border-radius: 4px;
        margin: 5px;
        font-size: 18px;
        padding: 2px 8px;
        color: $button-bgc;
        background-color: transparent;
        cursor: pointer;
    }

    &__next {
        background-color: $button-bgc;
        width: 100px;
        margin-top: 20px;
        padding: 7px 0;
        color: $primary-bgc;
        border: none;
        font-weight: 700;
        font-size: 18px;
        border-radius: 9px;
    }

    &__previous {
        @extend .pagination__next;
        color: $primary-bgc;
        background-color: #961515;
        margin-bottom: 20px;
    }
}
</style>
