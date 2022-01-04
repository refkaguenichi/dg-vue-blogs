<template>
  <div class="row">
    <div class="col-lg-6">
      <form>
        <h2 v-if="!url">Add a blog</h2>
        <h2 v-else>Edit the blog</h2>
        <input
          type="hidden"
          v-model="url"
          class="form-control mt-2"
          placeholder="Url"
        />
        <label for="">Title</label>
        <input
          type="text"
          v-model="title"
          class="form-control mt-2"
          placeholder="Enter title"
        />
        <label for="">Body</label>
        <input
          type="text"
          v-model="body"
          class="form-control mt-2"
          placeholder="Enter body"
        />
        <button @click="postBlog()" class="btn btn-block btn-success mt-2">
          Save
        </button>
      </form>
    </div>
    <div class="col-lg-6">
      <h2>Blogs</h2>
      <table class="table">
        <thead>
          <th>Url</th>
          <th>Title</th>
          <th>Body</th>
          <th>Edit</th>
          <th>Delete</th>
        </thead>
        <tbody>
          <tr v-for="blog in blogs" :key="blog.url">
            <td>{{ blog.url }}</td>
            <td>{{ blog.title }}</td>
            <td>{{ blog.body }}</td>
            <td>
              <button @click="getOne(blog)" class="btn bn-sm btn-success">
                <i class="fa fa-pencil"></i>
              </button>
            </td>
            <td>
              <button @click="deleteOne(blog.url)" class="btn bn-sm btn-danger">
                <i class="fa fa-trash"></i>
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
  import axios from "axios";
  export default {
    name: "Container",
    props: {},
    data() {
      return {
        blogs: [],
        url: "",
        title: "",
        body: "",
      };
    },
    mounted() {
      this.getAll();
    },
    methods: {
      getAll() {
        axios
          .get("http://localhost:8000/blogs")
          .then((res) => {
            this.blogs = res.data;
            this.url = "";
            this.title = "";
            this.body = "";
          })
          .catch((err) => {
            console.log(err);
          });
      },
      getOne(blog) {
        this.url = blog.url;
        this.title = blog.title;
        this.body = blog.body;
      },
      deleteOne(url) {
        if (confirm("Do you really want to delete?")) {
          axios
            .delete(url, {
              auth: {
                username: "admin",
                password: 123456,
              },
            })
            .then(() => {
              this.getAll();
            });
        }
      },
      postBlog() {
        if (this.url === "") {
          axios
            .post(
              "http://localhost:8000/blogs/",

              { title: this.title, body: this.body },
              {
                auth: {
                  username: "admin",
                  password: 123456,
                },
              }
            )
            .then(() => {
              this.getAll();
            });
        } else {
          axios
            .put(
              this.url,

              { title: this.title, body: this.body },
              {
                auth: {
                  username: "admin",
                  password: 123456,
                },
              }
            )
            .then(() => {
              this.getAll();
            });
        }
      },
    },
  };
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
