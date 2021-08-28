##railsのプロジェクトでVue.jsとAxiosを使うことで,自分で作ったAPI(djangoで作る)を使ったWebアプリを作成する

https://www.webprofessional.jp/fetching-data-third-party-api-vue-axios/

初めに別のプロジェクトとしてdjangoでAPIを作成する

https://qiita.com/kimihiro_n/items/86e0a9e619720e57ecd8
この記事を参考にさせていただいた．上記通りに実装し，
http://localhost:8000/admin/でEntrysとUsersをいくつか作っておく

$python manage.py runserverでサーバを起動し，

http://localhost:8000/api/entries/
でEntryの情報を受け取れるか,Django REST framwworkが正しく動いているかを確認する．

## railsのプロジェクトを作成

$rails new spa

$cd spa

$rails s

$rails g controller top show
#topコントローラーとビュー(show.html.erb)を作成


routes.rbに以下を記載
get "", to: "top#show"
#ルーティングlocalhost:3000/でshow.html.erbを表示できるようにする

top_controller.rbに以下を記載
def show
end


## vue.js環境構築
以下のコマンドを実行すれば、vue.jsを利用するにあたり必要なファイルを用意してくれます。

$bundle exec rails webpacker:install:vue

show.html.erbに以下を追記
<%= javascript_pack_tag 'hello_vue' %>
<%= stylesheet_pack_tag 'hello_vue' %>


$bundle exec rails s
#サーバーを立ち上げる
#ブラウザでhttps://localhost:3000にアクセス
「Hello Vue!!」と表示されていれば、環境構築は完了です。

$yarn add axios
axiosをインストール

app>javascript>packs>api.jsを作成
以下を記載

import Vue from 'vue/dist/vue.esm'
import axios from 'axios';

document.addEventListener('DOMContentLoaded', () => {
const vm = new Vue({
    el: '#app',
    data() {
      return {
        sections:["1","2","3","4","5","6"],
        section: "1",
        isActive: true,
        results: [],
        info:null,
        django:null,
        url :"http://localhost:8000/api/entries/1/?format=json",
        lead_url:"http://localhost:8000/api/entries/",
        behind_url:"/?format=json",
      }
    },
    methods: {
      active: function () {
          this.isActive = !this.isActive;
          this.url = this.lead_url + this.section + this.behind_url;
          axios.get(this.url)
          .then(response => {this.django = response})
      },
      search: function() {
        this.url = this.lead_url + this.section + this.behind_url;
      }
  },
    mounted() {
      axios.get(this.url)
      .then(response => {this.django = response})
    },
  })

})



app>assets>stylesheets>top.scss



.my-container {
    width: 400px;
    height: 400px;
    margin: auto;
    background-color: #dbdbd9;
    margin-top: 200px;

    select {
        width: 100px;
        margin: 20px;
    }

    .content {
        display: inline-block;
        margin: auto;
        margin-top: 40px;

        span.index {
            display: inline-block;
            padding: 3px;
            border-radius: 5px;
            background-color: #007bff;
            color: white;
            margin-bottom: 10px;
            margin-right: 10px;

        }


    }
}


app>views>top>show.html.erb
show.html.erbに以下を追記
<%= javascript_pack_tag 'api' %>
<%= stylesheet_pack_tag 'api' %>

<head>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css"
        integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
</head>

<div class="my-container">
    <div class="container" id="app">
        <div class="content">
            <div><span class="index">タイトル</span><span>{{django.data.title}}</span></div>
            <div class="blank"><span class="index">本文</span><span>{{django.data.body}}</span></div>
            <div><span class="index">名前</span><span>{{django.data.author.name}}</span></div>
            <div class="blank"><span class="index">メール</span><span>{{django.data.author.mail}}</span></div>
            <div v-if="isActive"></div>
            <div v-else>
            </div>
            <select @click="search" v-model="section">
                <option v-for="section in sections" :value="section">{{ section }}</option>
            </select>
            <br>
            <button class="btn btn-primary" type="submit" @click="active">データを取得</button>
        </div>
    </div>
</div>



動かない場合はdjanogoのサーバが起動しているか確認
参考:https://mebee.info/2021/03/09/post-27210/
