<template>
  <section class="comment-comp">
    <div class="comment">
      <div class="comment-header">
        <img :src="require(`${comment.user.image.png}`)" alt="person" />

        <span class="user-name">{{ comment.user.username }} </span>
        <span class="createdat">{{ comment.createdAt }} </span>
        <span
          @click="reply = !reply"
          v-if="comment.user.username != currentUser.username"
          class="reply-button"
          >reply</span
        >
        <div
          class="comment-modifiers"
          v-if="comment.user.username == currentUser.username"
        >
          <button class="comment-edit" @click="editcomment(comment)">
            Edit
          </button>
          <button class="comment-delete" @click="deletecomment(comment.id)">
            Delete
          </button>
        </div>
      </div>
      <div class="comment-content">
        {{ comment.content }}
      </div>
    </div>
    <div v-if="comment.replies.length > 0" class="comment-replies">
      <div
        v-for="(reply, index) in comment.replies"
        :key="index"
        class="comment-reply"
      >
        <div class="reply-header">
          <img :src="require(`${reply.user.image.png}`)" alt="person" />

          <span class="reply-username">{{ reply.user.username }} </span>
          <span class="createdat">{{ reply.createdAt }} </span>
          <div
            class="reply-modifiers"
            v-if="reply.user.username == currentUser.username"
          >
            <button class="reply-edit" @click="editReply(reply)">Edit</button>
            <button class="reply-delete" @click="deleteReply(reply.id)">
              Delete
            </button>
          </div>
        </div>
        <div class="reply-content">
          {{ reply.content }}
        </div>
      </div>
    </div>
    <form class="reply-form" v-if="reply" @submit="addReply($event)">
      <img :src="require(`${currentUser.image.png}`)" alt="person" />
      <input type="text" class="reply-text" />
      <input type="submit" value="UPDATE" />
    </form>
  </section>
</template>

<script>
export default {
  name: "HelloWorld",
  props: {
    commentProp: Object,
    currentUser: Object,
  },
  data() {
    return {
      reply: false,
      comment: this.commentProp,
    };
  },
  methods: {
    addReply(e) {
      e.preventDefault();
      let input = document.querySelector(".reply-text");
      let reply = input.value;
      let dataToSend = {
        commentIndex: this.comment.id,
        id: this.comment.replies.length + 1,
        content: reply,
        createdAt: "1 minute ago",
        score: 4,
        replyingTo: this.comment.user.username,
        user: this.currentUser,
      };
      input.value = "";
      this.$emit("replied", dataToSend);
    },
    editReply(reply) {
      this.reply = true;
      let input = document.querySelector(".reply-text");
      input.value = reply.content;
      this.deleteReply(reply.id);
    },
    deleteReply(id) {
      this.comment.replies.forEach((element, index) => {
        if (element.id == id) {
          this.comment.replies.splice(index, 1);
        }
      });
      this.$emit("localStorage");
    },
    editcomment(comment) {
      const input = document.querySelector(".comment-text");
      input.value = comment.content;
      this.deletecomment(comment.id);
    },
    deletecomment(id) {
      this.$emit("deleteComment", id);
    },
  },
  mounted() {
    // console.log(this.index);
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
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
.comment,
.comment-reply,
form {
  background-color: #ffffff;
  padding: 15px;
  border-radius: 15px;
  margin: 13px auto;
}
.comment-comp {
  .user-name {
    color: #474d54;
    font-weight: bold;
  }
  .createdat {
    color: #c7cbd2;
    @include breakpoints(mobile) {
      display: none;
    }
  }
  .comment-header {
    position: relative;
    .reply-button {
      color: #5c5dbb;
      font-weight: bold;
      text-decoration: underline;
      cursor: pointer;
      position: absolute;
      right: 10px;
      // @include breakpoints(mobile) {
      //   bottom: 0;
      // }
    }
    display: flex;
    @include breakpoints(mobile) {
      flex-direction: column;
    }
    align-items: center;
    gap: 10px;
    margin-bottom: 18px;
    .comment-modifiers {
      position: absolute;
      right: 10px;
      button {
        border: none;
        background-color: transparent;
        font-weight: bold;
      }
      .comment-edit {
        color: #5358b4;
      }
      .comment-delete {
        color: #f16266;
      }
    }
  }
  .comment-replies {
    position: relative;
    width: 80%;

    margin: 20px 0 10px auto;
    &::before {
      content: "";
      height: 100%;
      width: 2px;
      position: absolute;
      left: -10%;
      background-color: #dadce6;
    }
    .comment-reply {
      .reply-header {
        display: flex;
        @include breakpoints(mobile) {
          flex-direction: column;
        }
        align-items: center;
        gap: 7px;
        position: relative;
        margin-bottom: 15px;
        .reply-username {
          color: #474d54;
          font-weight: bold;
          justify-self: flex-start;
        }
        .reply-modifiers {
          // justify-self: flex-end;
          position: absolute;
          right: 10px;
          button {
            border: none;
            background-color: transparent;
            font-weight: bold;
          }
          .reply-edit {
            color: #5358b4;
          }
          .reply-delete {
            color: #f16266;
          }
        }
      }
    }
  }
  .reply-form {
    width: 80%;
    margin: 0 0 0 auto;
  }
}
</style>
