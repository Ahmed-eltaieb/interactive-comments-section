<template>
  <main>
    <CommentsComponent
      v-for="(comment, index) in mainData.comments"
      :key="index"
      :commentProp="comment"
      :currentUser="this.mainData.currentUser"
      @replied="addReply"
      @deleteComment="deleteComment"
      @localStorage="setLocalStorage"
    />
    <form class="comment-form" @submit="addComment($event)">
      <img :src="require(`${mainData.currentUser.image.png}`)" alt="person" />
      <input type="text" class="comment-text" />
      <input type="submit" value="SEND" />
    </form>
  </main>
</template>

<script>
import CommentsComponent from "./components/CommentsComponent.vue";
import usersData from "./data.json";

export default {
  name: "App",
  components: {
    CommentsComponent,
  },
  data() {
    return {
      fileData: usersData,
      // mainData: usersData,
      mainData: {},
    };
  },
  methods: {
    addComment(e) {
      e.preventDefault();
      let input = document.querySelector(".comment-text");
      let comment = input.value;
      this.mainData.comments.push({
        id: this.mainData.comments.length + 1,
        content: comment,
        createdAt: "1 minute ago",
        score: 12,
        user: this.mainData.currentUser,
        replies: [],
      });
      input.value = "";
      localStorage.setItem("mainData", JSON.stringify(this.mainData));
    },
    addReply(reply) {
      this.mainData.comments[reply.commentIndex - 1].replies.push(reply);
      localStorage.setItem("mainData", JSON.stringify(this.mainData));
    },
    deleteComment(id) {
      this.mainData.comments.forEach((element, index, arr) => {
        if (element.id == id) {
          arr.splice(index, 1);
        }
      });
      localStorage.setItem("mainData", JSON.stringify(this.mainData));
    },
    setLocalStorage() {
      localStorage.setItem("mainData", JSON.stringify(this.mainData));
    },
  },
  beforeMount() {
    this.mainData =
      JSON.parse(localStorage.getItem("mainData")) || this.fileData;
  },
};
</script>

<style lang="scss">
@mixin breakpoints($point) {
  @if $point == mobile {
    @media (max-width: 767px) {
      @content;
    }
  } @else if $point == small {
    @media (min-width: 768px) and (max-width: 991px) {
      @content;
    }
  } @else if $point == medium {
    @media (min-width: 992px) and (max-width: 1199px) {
      @content;
    }
  } @else if $point == large {
    @media (min-width: 1200px) {
      @content;
    }
  }
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  // text-align: center;
  color: #2c3e50;
  padding-top: 60px;
  background-color: #f5f6fa;
  font-size: 20px;
  main {
    // width: 60%;
    max-width: 600px;
    @include breakpoints(mobile) {
      max-width: calc(100vw - 20px);
    }
    margin: 0 auto;

    .comment-form {
      width: 100%;

      // height: 60px;
      padding: 20px;
    }
  }
  form {
    width: 100%;
    display: flex;
    align-items: flex-start;
    gap: 10px;
    @include breakpoints(mobile) {
      img {
        display: none;
      }
    }
    input[type="submit"] {
      background: #5358b6;
      border: none;
      padding: 10px;
      border-radius: 7px;
      color: #fff;
      font-weight: bold;
      cursor: pointer;
    }
    input[type="text"] {
      flex: 1;
      @include breakpoints(mobile) {
        flex-basis: 75%;
      }
      align-self: normal;
      border-radius: 7px;
      outline: none;
      border-color: #d6d7e0;
    }
  }
}

button {
  cursor: pointer;
}
</style>
