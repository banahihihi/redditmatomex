<script setup>
import axios from 'axios';
import { ref } from "vue";

const res = ref("res初期値");
const input = ref("https://www.reddit.com/r/OsakaTravel/comments/1e8na56/is_this_area_safe/");

async function getUrl() {
  let url = input.value;

  let out = await matome(url);

  let values = [[out.title.body, out.title.bodyjp],[out.title.selftext, out.title.selftestjp]];
  out.comments.forEach((comment) => {
    values.push([comment.body,comment.bodyjp])
  })

  res.value = values;
}

async function matome(url) {
  
  let jsonUrl = url + ".json";

  let out;

  await axios
  .get(jsonUrl)
  .then((response) => {

    let body = response.data;

    //タイトルの取得、selfは投稿者のコメント
    let title = {
      body: body[0].data.children[0].data.title,
      bodyjp : "LanguageApp.translate(body[0].data.children[0].data.title, `en`, `ja`)",
      selftext : body[0].data.children[0].data.selftext,
      selftestjp : "LanguageApp.translate(body[0].data.children[0].data.selftext, `en`, `ja`)",
      author: body[0].data.children[0].data.author,
      id: body[0].data.children[0].data.id,
      url: body[0].data.children[0].data.url,
    };

    let comments = [];//コメント

    //コメントbodyとrepliesを取得
    body[1].data.children.forEach((child) => {
      extractCommentsAndReplies(child, comments);
    });

    out = {
      title: title,
      comments: comments
    }

  })
  .catch(error => console.error('There was a problem with your axios operation:', error));  

  return out;

}

const extractCommentsAndReplies = (child, comments) => {
  //Utilities.sleep(100);

  comments.push({
    body: child.data.body,
    bodyjp : "LanguageApp.translate(child.data.body, `en`, `ja`)",
    author: child.data.author,
    id: child.data.id,
    parent_id: child.data.parent_id,
  });

  if(child.data.replies != ""){
    //再帰関数で処理
    child.data.replies.data.children.forEach((child) => {
      extractCommentsAndReplies(child, comments);
    });
  }
}

</script>

<template>
  <div class="template">
    <p>以下vue単一コンポーネント</p>
    <input type="text" v-model.lazy="input" />
    <button @click="getUrl">Click</button>
    <p>{{ res }}</p>
  </div>
</template>

<style scoped>
  .template{
    background-color: aquamarine;
  }
</style>
