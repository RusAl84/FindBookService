<template>
  <q-page>
    <q-card class="chatmessages">
      <div class="flexrow">
        <div>
          <q-card-section>
            <div class="text-h6">
              Введите описание для книги, которую вы хотите почитать:
            </div>
          </q-card-section>
          <q-input
            class="inputtext"
            v-model="proc_text"
            type="textarea"
            autogrow
            float-label="Введите описание для книги, которую вы хотите почитать"
          />
          <q-card-section>
            <q-btn color="primary" label="Подобрать книгу" @click="onProc" />
          </q-card-section>
          <q-card-section>
            <div class="text-h6">Результат:</div>
            <q-card-section v-if="resp_data.length > 0">
              <div v-for="(line, i) in resp_data" :key="i">
                <div>
                  <b>Наиболее подходящая книга &nbsp; {{ i + 1 }}</b>
                </div>
                <div><b>Название:</b> {{ line.title }}</div>
                <div><b>Авторы: </b>{{ line.authors }}</div>
                <div><b>Описание: </b>{{ line.descr }}</div>
                <br />
              </div>
            </q-card-section>
          </q-card-section>
        </div></div
    ></q-card>
  </q-page>
</template>

<script>
import { defineComponent, ref } from "vue";
import axios from "axios";

export default {
  data() {
    return {
      proc_text: "",
      resp_data: ref([{}]),
    };
  },
  methods: {
    handleFileUpload() {
      this.file = this.$refs.file.files[0];
    },
    fileUploaded({ files, xhr }) {
      let data = JSON.parse(xhr.response);
      this.chatmessages = data["text"];
      this.filename = data["filename"];
    },
    async onProc() {
      const response = await axios({
        method: "post",
        url: "http://127.0.0.1:5000/find_book",
        data: {
          text: this.proc_text,
        },
      });
      console.log(response);
      this.resp_data = response.data;
    },
    async onProcAdd() {
      const response = await axios({
        method: "post",
        url: cfg.hostae + "get_pattern_add",
        data: this.resp_data,
      });
      this.all_data = response.data;
      console.log("this.resp_data");
      console.log(this.all_data);
    },
    async onLoadDB() {
      const response = await axios({
        method: "get",
        url: cfg.hostae + "load_db",
        data: this.resp_data,
      });
      this.all_data = response.data;

      console.log(this.all_data);
    },
    async onClearDB() {
      const response = await axios({
        method: "get",
        url: cfg.hostae + "clear_db",
      });
      console.log(response);
    },
    async onFindCL() {
      const response = await axios({
        method: "post",
        url: cfg.hostae + "findae",
        data: {
          filename: this.filename,
          type: this.ttype,
        },
        headers: {
          "Content-Type": "application/json",
        },
      });
      // console.log(response);
      this.ae_data = response.data;
      console.log("ae_data");
      console.log(this.ae_data);
    },
  },
};
</script>

<style lang="sass">
.page
  padding-bottom: 10px
  padding-top: 10px
  padding-left: 10px
  padding-r: 10px

.chatmessages
  padding: 10px
  margin: 10px
.flexrow
  display: flex
  flex-flow: row wrap
  justify-content: flex-start
.editorclass
  max-height: 500px
.inputtext
  width: 800px
</style>
