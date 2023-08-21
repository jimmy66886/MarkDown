# 项目介绍

通关github官方的api`https://api.github.com/repos/jimmy66886/picgo_two`

实现一个图形化的界面,来查看仓库的详细信息(主要是看大小,因为图床和这个仓库深度绑定).有点脱裤子放屁哈哈哈

# 接口介绍

就拿我的一个仓库来说

有用的内容:
1. `full_name`:用户名/仓库名
2. `owner.avatar_url`用户头像
3. `html_url`仓库界面
4. `pushed_at`更新时间
5. `created_at`创建时间
6. `size`仓库大小(其实主要就是用这个)

```json
{
    "id": 573326251,
    "node_id": "R_kgDOIixDqw",
    "name": "picgo_two",
    "full_name": "jimmy66886/picgo_two",
    "private": false,
    "owner": {
        "login": "jimmy66886",
        "id": 77395721,
        "node_id": "MDQ6VXNlcjc3Mzk1NzIx",
        "avatar_url": "https://avatars.githubusercontent.com/u/77395721?v=4",
        "gravatar_id": "",
        "url": "https://api.github.com/users/jimmy66886",
        "html_url": "https://github.com/jimmy66886",
        "followers_url": "https://api.github.com/users/jimmy66886/followers",
        "following_url": "https://api.github.com/users/jimmy66886/following{/other_user}",
        "gists_url": "https://api.github.com/users/jimmy66886/gists{/gist_id}",
        "starred_url": "https://api.github.com/users/jimmy66886/starred{/owner}{/repo}",
        "subscriptions_url": "https://api.github.com/users/jimmy66886/subscriptions",
        "organizations_url": "https://api.github.com/users/jimmy66886/orgs",
        "repos_url": "https://api.github.com/users/jimmy66886/repos",
        "events_url": "https://api.github.com/users/jimmy66886/events{/privacy}",
        "received_events_url": "https://api.github.com/users/jimmy66886/received_events",
        "type": "User",
        "site_admin": false
    },
    "html_url": "https://github.com/jimmy66886/picgo_two",
    "description": null,
    "fork": false,
    "url": "https://api.github.com/repos/jimmy66886/picgo_two",
    "forks_url": "https://api.github.com/repos/jimmy66886/picgo_two/forks",
    "keys_url": "https://api.github.com/repos/jimmy66886/picgo_two/keys{/key_id}",
    "collaborators_url": "https://api.github.com/repos/jimmy66886/picgo_two/collaborators{/collaborator}",
    "teams_url": "https://api.github.com/repos/jimmy66886/picgo_two/teams",
    "hooks_url": "https://api.github.com/repos/jimmy66886/picgo_two/hooks",
    "issue_events_url": "https://api.github.com/repos/jimmy66886/picgo_two/issues/events{/number}",
    "events_url": "https://api.github.com/repos/jimmy66886/picgo_two/events",
    "assignees_url": "https://api.github.com/repos/jimmy66886/picgo_two/assignees{/user}",
    "branches_url": "https://api.github.com/repos/jimmy66886/picgo_two/branches{/branch}",
    "tags_url": "https://api.github.com/repos/jimmy66886/picgo_two/tags",
    "blobs_url": "https://api.github.com/repos/jimmy66886/picgo_two/git/blobs{/sha}",
    "git_tags_url": "https://api.github.com/repos/jimmy66886/picgo_two/git/tags{/sha}",
    "git_refs_url": "https://api.github.com/repos/jimmy66886/picgo_two/git/refs{/sha}",
    "trees_url": "https://api.github.com/repos/jimmy66886/picgo_two/git/trees{/sha}",
    "statuses_url": "https://api.github.com/repos/jimmy66886/picgo_two/statuses/{sha}",
    "languages_url": "https://api.github.com/repos/jimmy66886/picgo_two/languages",
    "stargazers_url": "https://api.github.com/repos/jimmy66886/picgo_two/stargazers",
    "contributors_url": "https://api.github.com/repos/jimmy66886/picgo_two/contributors",
    "subscribers_url": "https://api.github.com/repos/jimmy66886/picgo_two/subscribers",
    "subscription_url": "https://api.github.com/repos/jimmy66886/picgo_two/subscription",
    "commits_url": "https://api.github.com/repos/jimmy66886/picgo_two/commits{/sha}",
    "git_commits_url": "https://api.github.com/repos/jimmy66886/picgo_two/git/commits{/sha}",
    "comments_url": "https://api.github.com/repos/jimmy66886/picgo_two/comments{/number}",
    "issue_comment_url": "https://api.github.com/repos/jimmy66886/picgo_two/issues/comments{/number}",
    "contents_url": "https://api.github.com/repos/jimmy66886/picgo_two/contents/{+path}",
    "compare_url": "https://api.github.com/repos/jimmy66886/picgo_two/compare/{base}...{head}",
    "merges_url": "https://api.github.com/repos/jimmy66886/picgo_two/merges",
    "archive_url": "https://api.github.com/repos/jimmy66886/picgo_two/{archive_format}{/ref}",
    "downloads_url": "https://api.github.com/repos/jimmy66886/picgo_two/downloads",
    "issues_url": "https://api.github.com/repos/jimmy66886/picgo_two/issues{/number}",
    "pulls_url": "https://api.github.com/repos/jimmy66886/picgo_two/pulls{/number}",
    "milestones_url": "https://api.github.com/repos/jimmy66886/picgo_two/milestones{/number}",
    "notifications_url": "https://api.github.com/repos/jimmy66886/picgo_two/notifications{?since,all,participating}",
    "labels_url": "https://api.github.com/repos/jimmy66886/picgo_two/labels{/name}",
    "releases_url": "https://api.github.com/repos/jimmy66886/picgo_two/releases{/id}",
    "deployments_url": "https://api.github.com/repos/jimmy66886/picgo_two/deployments",
    "created_at": "2022-12-02T07:40:06Z",
    "updated_at": "2023-05-27T14:27:08Z",
    "pushed_at": "2023-08-20T07:35:57Z",
    "git_url": "git://github.com/jimmy66886/picgo_two.git",
    "ssh_url": "git@github.com:jimmy66886/picgo_two.git",
    "clone_url": "https://github.com/jimmy66886/picgo_two.git",
    "svn_url": "https://github.com/jimmy66886/picgo_two",
    "homepage": null,
    "size": 139019,
    "stargazers_count": 0,
    "watchers_count": 0,
    "language": null,
    "has_issues": true,
    "has_projects": true,
    "has_downloads": true,
    "has_wiki": true,
    "has_pages": false,
    "has_discussions": false,
    "forks_count": 0,
    "mirror_url": null,
    "archived": false,
    "disabled": false,
    "open_issues_count": 0,
    "license": null,
    "allow_forking": true,
    "is_template": false,
    "web_commit_signoff_required": false,
    "topics": [],
    "visibility": "public",
    "forks": 0,
    "open_issues": 0,
    "watchers": 0,
    "default_branch": "main",
    "temp_clone_token": null,
    "network_count": 0,
    "subscribers_count": 1
}
```

# 项目搭建

首先`vue create github-tools`新建这个项目

然后发现进度条一直卡住...

换成`vue ui`,还是卡住了,但是还好创建成功.

## 整体结构

>`App.vue`中,还是用到了简单的路由:
```html
<template>
    <router-view/>
</template>

<style>

</style>
```

>`index.js`
```js
import Vue from 'vue'
import VueRouter from 'vue-router'
import HomeInfo from '../components/HomeInfo'

Vue.use(VueRouter)


export default new VueRouter({
  routes: [
    {
      path: '/',
      name: 'home',
      component: HomeInfo
    },
  ]
})
```

>`vue-beauty`组件库

这里使用vue-beauty,想换换口味,引入也很简单

```js
import 'vue-beauty/package/style/vue-beauty.min.css'
import vueBeauty from 'vue-beauty'
Vue.use(vueBeauty)
```

这三行即可引入成功

>全局事件总线的使用

这个好奇怪啊,好像有延迟似的,如果发消息的一方在普通函数中,接收方在mounted,那么消息大概率接收不到,可能是因为消息量有点大,所以传不过去?

所以在发消息的组件中放在了
```js
    beforeDestroy() {
        this.$bus.$emit('allData', this.allData)
    },
```

接收消息的组件放在了
```js
    // 接收数据
    created() {
        this.$bus.$on('allData', (data) => {
            // 对需要用到的值进行赋值
            this.fullName = data.full_name
            this.htmlUrl = data.html_url
            this.avatar = data.owner.avatar_url
            this.size = data.size
            this.pushedAt = data.pushed_at
            this.createdAt = data.created_at
        })
    },
```

这样就没问题了.

但是又有了新的问题,因为信息是传递过来的,如果刷新,就又没了,所以还是放到本地存储中吧

所以存储信息的组件:
```js
    beforeDestroy() {
        localStorage.setItem('allData', JSON.stringify(this.allData))
    },
```

获取信息的组件:
```js
    // 接收数据
    created() {
        let data = JSON.parse(localStorage.getItem('allData'))
        // 对需要用到的值进行赋值
        this.fullName = data.full_name
        this.htmlUrl = data.html_url
        this.avatar = data.owner.avatar_url
        this.size = data.size
        this.pushedAt = data.pushed_at
        this.createdAt = data.created_at
    },
```

## 页面

输入信息界面:
![20230821150906](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230821150906.png)

信息界面(很简陋啊很简陋)
![20230821091205](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230821091205.png)

优化之后:
![20230821140304](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230821140304.png)

