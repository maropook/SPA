
# DRFで作ったAPIを使用したSPAをRailsで作成する



  __開発環境__
  
* OS: `macOS Big Sur v11.5.2`
* Python: `Python 3.0.6`
* Django: `3.2.5`
* ruby:`2.6.5`
* Rails: `6.1.4.1`


## :horse: Djangoのプロジェクトを作成

### 別のプロジェクトとしてdjangoでAPIを実装



  __API実装__

[Django REST Frameworkを使って爆速でAPIを実装する](https://qiita.com/kimihiro_n/items/86e0a9e619720e57ecd8)

__CORS設定__

DRFを使って、APIエンドポイントをフロントエンドから叩く場合にはCORSの設定が必要

[【drf】django-cors-headersを使ってCORS設定を行う](https://self-methods.com/drf-cors-headers/)


__動作確認__

```bash

# 開発サーバーを起動
$python manage.py runserver

```


http://localhost:8000/admin/
管理画面に入り,EntrysとUsersをいくつか作っておく


-----

<img width="739" alt="スクリーンショット 2021-08-28 9 51 46" src="https://user-images.githubusercontent.com/84751550/131200964-69ea9e5a-1697-48ed-92dd-153927740112.png">

<img width="734" alt="スクリーンショット 2021-08-28 9 52 00" src="https://user-images.githubusercontent.com/84751550/131200967-238bbd44-c0ca-4de0-a146-40f294a856e0.png">



-----

http://localhost:8000/api/entries/
でEntryの情報を受け取れるか確認

<img width="1230" alt="スクリーンショット 2021-08-28 9 51 25" src="https://user-images.githubusercontent.com/84751550/131200984-2510d1e9-6dad-4f6b-b99b-b90cde084605.png">

-----


## :gem: railsのプロジェクトを作成

### 別のプロジェクトとしてrailsでSPAを実装


```bash
#spaディレクトリにrailsプロジェクトを作成
$rails new spa

# spaディレクトリに移動
$cd spa

# 開発サーバーの起動
$rails s

```

__動作確認__

https://localhost:3000
サーバが立ち上がることを確認する

***

<img width="1139" alt="スクリーンショット 2021-08-28 11 15 20" src="https://user-images.githubusercontent.com/84751550/131203153-3c1a7a0b-a61c-40ec-94dd-9a844fe223e2.png">


***

__コントローラーとビューを作成__


```bash
#topコントローラー(top_controller.rb)とビュー(show.html.erb)を作成
$rails g controller top show
```

`$rails g controller top show`でtopコントローラ-とビューを作成し，ルーティングとアクション追加までしてくれます

__コントローラーの確認__

top_controller.rb
```top_controller.rb
class TopController < ApplicationController
  def show
  end
end

```

`$rails g controller top show`でshowアクションは自動で定義されるが一応のため確認．

showアクションが実行されると， app/views/top/show.html.erbを探して表示してくれる

__ルーティングを定義__

routes.rb

```routes.rb
Rails.application.routes.draw do
  get "", to: "top#show"
end

```

https://localhost:3000/
でtopコントローラのshowアクションが呼び出し,show.html.erbを表示できるようにした



__ビューをを編集__


show.html.erb


```show.html.erb

<h2>HelloWorld</h2>

```

HelloWorldを表示させるようにする


__動作確認__

```bash
#開発サーバーの起動
$rails s
```

https://localhost:3000
show.html.erbが表示されることを確認

***

<img width="606" alt="スクリーンショット 2021-08-28 11 17 49" src="https://user-images.githubusercontent.com/84751550/131203162-f0e1ad85-ca0b-4fca-b1af-427323dfe54a.png">

***

HelloWorldが表示されたらOK


## vue.js環境構築

__vueをインストール__

```bash
#vue.js環境構築
$bundle exec rails webpacker:install:vue
```

vue.jsを利用するにあたり必要なファイルを用意してくれます。


__ビューを編集__

show.html.erb
```show.html.erb
<%= javascript_pack_tag 'hello_vue' %>
<%= stylesheet_pack_tag 'hello_vue' %>
```

__動作確認__


```bash
$bundle exec rails s
```
https://localhost:3000
「Hello Vue!!」と表示されていれば、環境構築は完了です。

***
![image-176-1024x525](https://user-images.githubusercontent.com/84751550/131203290-b723c9c7-8f38-489c-8971-24a142fafa0b.png)

***

## APIを使用したSPAを作成する

__axiosをインストール__


```bash
#axiosをインストール
$yarn add axios
```



__ビューを編集__

app/views/top/show.html.erb

```app/views/top/show.html.erb
<%= javascript_pack_tag 'api' %>
<%= stylesheet_pack_tag 'api' %>

<head>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css"
        integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
</head>

<div class="my-container">
    <div class="container" id="app">
        <div class="content">
            <div>
                <span class="index">タイトル</span><span>{{django.data.title}}</span>
            </div>
            <div class="blank">
                <span class="index">本文</span><span>{{django.data.body}}</span>
            </div>
            <div>
                <span class="index">名前</span><span>{{django.data.author.name}}</span>
            </div>
            <div class="blank">
                <span class="index">メール</span><span>{{django.data.author.mail}}</span>
            </div>
            <select @click="search" v-model="section">
                <option v-for="section in sections" :value="section">{{ section }}</option>
            </select>
            <br>
            <button class="btn btn-primary" type="submit" @click="active">データを取得</button>
        </div>
    </div>
</div>

```

Bootstrapを使用しています．

`<%= javascript_pack_tag 'api' %>
<%= stylesheet_pack_tag 'api' %>`でapp/javascript/packs/api.jsを読み込んでいます．

`v-model="section"`より， セレクトボックスは双方向にバインディングしています．

`v-for="section in sections" :value="section"`より， sections配列に格納されている値一つ一つをvalueとして選択できるようなセレクトボックスのoptionを作っています．

`@click="search"`より，クリックされた時にsearch関数が呼び出されます.

同様に, ボタンは`@click="active"`より，クリックされた時にactive関数が呼び出されます.

***



__axiosを使用__

app/javascript/packs/api.jsを作成

```app/javascript/packs/api.js

import Vue from 'vue/dist/vue.esm'
import axios from 'axios';

document.addEventListener('DOMContentLoaded', () => {
const vm = new Vue({
    el: '#app',
    data() {
      return {
        sections:["1","2","3","4","5","6"],
        section: "1",
        django:null,
        url :"http://localhost:8000/api/entries/1/?format=json",
        lead_url:"http://localhost:8000/api/entries/",
        behind_url:"/?format=json",
      }
    },
    methods: {
      active: function () {
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

```


「情報を取得する」 ボタンが押された時に実行されるactive関数では,セレクトボックスにセットされている取得したいEntryのid`section`を,HTTPメソッドを格納する変数`url`に埋め込み,そのHTTPメソッドでAPIと通信を行い， データを取得しています．

`axios.get(this.url)`で, HTTPメソッドを実行し， APIと通信を行う．

必要とする情報はAPIからのレスポンスの中にある.

`.then(response => {this.django = response})`で, this.djangoにAPIからのレスポンスであるJSONデータを取得.

show.html.erbで`{{django.data.title}}` や`{{django}}`などとすることでapp.jsで宣言や取得した値を表示させることができる．

とりあえず1~6番目に作成したEntryを取得できるように`sections:["1","2","3","4","5","6"]`としました

任意で減らしたり増やしたり，取得したいEntryのidに変えてください

***


__デザインを適用__

app/assets/stylesheets/top.scss

```app/assets/stylesheets/top.scss



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
````

デザインは好みでお願いします


***

__動作確認__


```bash
$bundle exec rails s
```

https://localhost:3000

***
<img width="737" alt="スクリーンショット 2021-08-28 11 14 45" src="https://user-images.githubusercontent.com/84751550/131203171-4015328f-63a6-411a-a864-f1836fbbb287.png">

***

selectボタンで取得したいEntryの番号を選択し， データを取得を押すとAPIからデータを取得することができる


***

SPA完成！！

GETしかできないが，他も実装予定

何も表示されない場合はdjangoのサーバが起動しているか確認したり, 検証でエラーを確認


# :octocat: ソース

[djangoのプロジェクト](https://github.com/maropook/django_rest_framework)
```bash

#django_rest_frameworkディレクトリに移動
$cd django_rest_framework

#マイグレーションファイルをもとにデータベースを作成
$python manage.py migrate

#django-cors-headersをインストール
$pip install django-cors-headers

#Userを作成
$python manage.py createsuperuser

#サーバーの起動
$python manage.py runserver

```

[railsのプロジェクト](https://github.com/maropook/SPA)

```bash
#SPAディレクトリに移動
$cd SPA

#gemをインストール
$bundle install

#vueをインストール
$bundle exec rails webpacker:install:vue

#axiosをインストール
$yarn add axios

#サーバーの起動
$rails s

```

上記を実行してください


# :book: 参考URL

* [Django REST Frameworkを使って爆速でAPIを実装する](https://qiita.com/kimihiro_n/items/86e0a9e619720e57ecd8)
* [【drf】django-cors-headersを使ってCORS設定を行う](https://self-methods.com/drf-cors-headers/)
* [rails6 vue.jsでaxiosを利用する](https://mebee.info/2021/03/09/post-27210/)
* [Vue.jsとAxiosなら驚くほど簡単に作れる！外部APIを使ったWebアプリの実例](https://www.webprofessional.jp/fetching-data-third-party-api-vue-axios/)
* [axios を利用した API の使用](https://jp.vuejs.org/v2/cookbook/using-axios-to-consume-apis.html)
