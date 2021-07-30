<template>
  <div id="app">
    <h1>Feed</h1>
    <ul></ul>
    <div v-show="loading">
      <p class="loading">Загрузка данных...</p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',

  data() {
    return {
      feed: null,
      feedSize: 10,
      ranges: [],
      loading: false,
    }
  },

  methods: {
    getFeed() {
      this.feed = require("./assets/feed.json");
      for (let msg of this.feed) {
        msg.date = this.setDate(msg.date);
      }
      console.log("\nFeed:\n", this.feed);
    },

    mountFeed() {
      let ul = document.querySelector("ul");

      for (let i = this.feedSize - 10; i < this.feedSize; i++) {
        let li = document.createElement("li");
        let h4 = document.createElement("h4");
        let p = document.createElement("p");

        h4.innerHTML = `${this.feed[i].date} / ${this.feed[i].authorName} / ${this.feed[i].authorUrl}`;
        p.innerHTML = this.feed[i].content;

        li.appendChild(h4);
        li.appendChild(p);
        ul.appendChild(li);
      }
      console.log("\nul:\n", ul);
      this.contentTone();
    },

    contentTone() {
      let nodesList = document.querySelectorAll("p");

      for (let i = this.feedSize - 10; i < this.feedSize; i++) {
        let node = nodesList[i];

        for (let item of this.feed[i].contentPostTones) {
          let range = new Range();
          range.setStart(node.firstChild, item.position);
          range.setEnd(node.firstChild, item.position + item.length);
          this.ranges.push({range: range, tone: item.tone});
        }

        for (let item of this.ranges) {
          try {
            let elem = document.createElement("span");
            elem.style.backgroundColor = this.setColor(item.tone);
            item.range.surroundContents(elem);
          } catch (error) {
            console.log("\nError:\n", error);
          }
        }
        this.ranges = [];
      }
    },

    setColor(tone) {
      let color = "";

      if (tone === -1) {
        color = "rgb(255, 0, 0)";
      }
      if (tone === 0) {
        color = "rgb(255, 255, 0)";
      }
      if (tone === 1) {
        color = "rgb(0, 255, 0)";
      }
      if (tone < 0) {
        let g = Math.round(255 * (1+tone));
        color = `rgb(255, ${g}, 0)`;
      }
      if (tone > 0) {
        let r = Math.round(255 * (1-tone));
        color = `rgb(${r}, 255, 0)`;
      }
      return color;
    },

    setDate(str) {
      let date = new Date(str);

      let hours = date.getHours();
      let minutes = date.getMinutes();
      let day = date.getDate();
      // let month = date.getMonth();
      let year = date.getFullYear();

      return `${hours}:${minutes}, ${day} сентября ${year} г`;
    },

    async checkPosition() {
      const height = document.body.offsetHeight;
      const screenHeight = window.innerHeight;
      const scrolled = window.scrollY;
      const threshold = height - screenHeight / 4;
      const position = scrolled + screenHeight;

      if (position >= threshold) {
        if (this.loading) return;
        if (this.feedSize < this.feed.length) {
          this.loading = true;
          await setTimeout(() => {
            this.loading = false;
            this.feedSize += 10;
            this.mountFeed();
          }, 3000);
        }
      }
    },

    scrolling() {
      window.addEventListener("scroll", this.checkPosition);
      window.addEventListener("resize", this.checkPosition);
    },
  },

  async created() {
    await this.getFeed();
    await this.mountFeed();
    await this.scrolling();
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

li {
  border-bottom: 3px solid gray;
}

ul {
  list-style-type: none;
  padding: 0 3%;
}

p {
  text-align: start;
}

.loading {
  text-align: center;
}
</style>
