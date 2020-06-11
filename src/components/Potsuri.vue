<template>
  <v-content>
    <v-container>
      <v-col>
        <v-card>
          <v-card-text>
            <v-textarea
              v-model="newPostStr"
              v-on:keydown.enter.ctrl.exact="makeNewPost"
              :rules="postRules"
              counter="140"
              label="New post"
            ></v-textarea>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn v-on:click="makeNewPost" icon>
              <v-icon>mdi-send</v-icon>
            </v-btn>
          </v-card-actions>
        </v-card>
        <v-col v-for="post in posts" :key="post.id">
          <v-card outlined>
            <v-card-text
              style="white-space:pre-wrap; word-wrap:break-word;"
              v-text="post.text"
              class="headline font-weight-regular"
            ></v-card-text>
            <v-card-actions>
              <p>{{ post.formattedDate }}</p>
              <v-spacer></v-spacer>

              <v-btn icon>
                <v-icon v-on:click="tweet(post.text)" color="#1DA1F2">fab fa-twitter</v-icon>
              </v-btn>
              <v-btn v-on:click="deletePost(post)" icon>
                <v-icon>mdi-trash-can</v-icon>
              </v-btn>
              <v-btn icon>
                <v-icon>mdi-star</v-icon>
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-col>
      </v-col>
    </v-container>
  </v-content>
</template>

<script>
export default {
  name: "Potsuri",

  data: () => ({
    newPostStr: "",
    postRules: [v => v.length <= 140 || "Max 140 characters"],
    posts: [
      {
        id: "00000000",
        date: "Thu Jun 11 2020 01:49:22 GMT+0900 (日本標準時)",
        formattedDate: "2020/05/04 02:02:42",
        text: "Potsuriでつぶやこう。"
      }
    ]
  }),
  methods: {
    tweet(text) {
      const url = `https://twitter.com/intent/tweet?text=${text}`;
      const option = "status=1,width=818,height=400,top=100,left=100";
      window.open(url, "twitter", option);
    },
    getUniqueID() {
      return (
        "potsuri_" +
        Date.now().toString(16) +
        Math.floor(1000 * Math.random()).toString(16)
      );
    },
    getFormattedDate(date) {
      return (
        date.getFullYear() +
        "/" +
        date.getMonth().padStart(2, '0') +
        "/" +
        date.getDay().padStart(2, '0') +
        " " +
        date.getHours().padStart(2, '0') +
        ":" +
        date.getMinutes().padStart(2, '0') +
        ":" +
        date.getSeconds().padStart(2, '0')
      );
    },
    savePost(data) {
      localStorage.setItem(data.id + "_t", data.text);
      localStorage.setItem(data.id + "_d", data.date);
    },
    makeNewPost() {
      if (this.newPostStr.match(/^(\s|\n)*$/)) {
        return;
      }
      const data = this.makePost(
        this.getUniqueID(),
        this.newPostStr,
        new Date()
      );
      this.savePost(data);
      this.newPostStr = "";
    },
    makePost(id, text, date) {
      if (text.match(/^(\s|\n)*$/)) {
        return;
      }
      const data = {
        id: id,
        date: date,
        formattedDate: this.getFormattedDate(date),
        text: text
      };
      this.posts.unshift(data);
      return data;
    },
    deletePost(post) {
      for (let i = 0; i < this.posts.length; i++) {
        if (this.posts[i].id === post.id) {
          this.posts.splice(i, 1);
          break;
        }
      }

      localStorage.removeItem(post.id + "_d");
      localStorage.removeItem(post.id + "_t");
    }
  },
  mounted() {
    let ids = [];
    for (let i = 0; i < localStorage.length; i++) {
      let key = localStorage.key(i);
      if (key.match(/^(potsuri_).+(_d)$/)) {
        ids.push(key.slice(0, key.length - 2));
      }
    }

    let posts = [];
    for (let id of ids) {
      const date = localStorage.getItem(id + "_d");
      const text = localStorage.getItem(id + "_t");
      if (
        typeof date === "undefined" ||
        date === null ||
        typeof text === "undefined" ||
        text === null
      ) {
        continue;
      }
      posts.push({ id: id, text: text, date: date });
    }

    posts.sort(function(a, b) {
      return a.date > b.date ? 1 : -1;
    });

    for (let post of posts) {
      this.makePost(post.id, post.text, new Date(post.date));
    }
  }
};
</script>
