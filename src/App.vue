<template>
  <div id="app">
    <h1>Feed</h1>
    <ul>
      <template v-for="(msg, index) in feed"
                :key="index">
        <li>
          <h4>{{ msg.date }} / {{ msg.authorName }} / {{ msg.authorUrl }}</h4>
          <p>{{ msg.content }}</p>
        </li>
        <li class="divider" role="presentation"></li>
      </template>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'App',

  data() {
    return {
      feed: 0,
      ranges: [],
    }
  },

  methods: {
    getJSON() {
      this.feed = require("./assets/feed.json");
      console.log(this.feed);
    },

    contentTone() {
      for (let msg of this.feed) {
        let nodes = document.querySelectorAll("p");
        let node = nodes[this.feed.indexOf(msg)];

        // if (node.firstChild.nextSibling.nodeName === "SPAN") return;

        console.log("\nCurrent node:\n", node);
        console.log("\nNode's first child:\n", node.firstChild);

        for (let item of msg.contentPostTones) {
          console.log("\nStart offset: ", item.position);
          console.log("\nEnd offset: ", item.position + item.length);

          let range = new Range();

          range.setStart(node.firstChild, item.position);
          range.setEnd(node.firstChild, item.position + item.length);
          this.ranges.push({range: range, tone: item.tone});

          console.log("\nCurrent range:\n", range);
          console.log("\nCurrent tone: ", item.tone);
        }

        console.log("\nRanges:\n", this.ranges);

        for (let item of this.ranges) {
          console.log("\nCurrent item of ranges:\n", item);
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
        let g = Math.round(255 * (-tone));
        color = `rgb(255, ${g}, 0)`;
      }
      if (tone > 0) {
        let r = Math.round(255 * tone);
        color = `rgb(${r}, 255, 0)`;
      }

      console.log("\nCurrent color: ", color);

      return color;
    },
  },

  created() {
    this.getJSON();
  },

  mounted() {
    this.contentTone();
  }
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

span {
  background-color: green;
}
</style>
