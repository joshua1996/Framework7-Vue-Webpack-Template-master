<template>
    <f7-page>
        <f7-navbar :title="'Search In ' + searchUrl[$route.params.engine].name" back-link="Back" sliding></f7-navbar>
        <f7-searchbar cancel-link="Cancel"
            search-list="#search-list"
            placeholder="Search in items"
            customSearch
            :clear-button="true"
            @searchbar:search="onSearch"
            @searchbar:clear="onClear"
            @searchbar:disable="onDisable"
            v-model="keyword"
            >
        </f7-searchbar>
        <f7-list class="searchbar-not-found">
            <f7-list-item title="Nothing found"></f7-list-item>
        </f7-list>
        <f7-list class="searchbar-found" id="search-list">
            <f7-list-item v-for="item in result" :title="item" :link="'/'" @click="goTo(searchUrl[$route.params.engine].url + item)"></f7-list-item>
        </f7-list>
    </f7-page>
</template>

<script>
    export default {
        
        data() {
            return {
                searchUrl: [{
                    name: 'Google',
                    url: 'https://www.google.com/search?q='
                },{
                    name: 'Baidu',
                    url: 'https://www.baidu.com/s?ie=utf-8&f=8&rsv_bp=1&rsv_idx=1&tn=baidu&wd='
                }],
                keyword: '',
                result: []
            }
        },
        methods: {
            onSearch (query, found) {
                this.$http.jsonp('https://suggestqueries.google.com/complete/search?client=chrome&q=' + this.keyword,{
                    before(request) {
                        if (this.previousRequest) {
                            this.previousRequest.abort()
                        }
                        this.previousRequest = request
                    }
                }).then(response => {
                    this.result = response.body[1]
                }, response => {

                });
            },
            onClear () {
                this.keyword = ''
            },
            onDisable () {
                this.keyword = ''
            },
            goTo (url) {
                window.open(url)
            }
        }
    }
</script>