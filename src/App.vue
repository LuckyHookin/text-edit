<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <h1>多功能文本编辑器</h1>
    </v-app-bar>
    <v-content>
      <v-container>
        <v-textarea
          counter
          autofocus
          outlined
          id="maintext"
          v-model="text"
          label="在此输入内容："
          @input="input"
        ></v-textarea>
        <!-- <textarea @input="OnInput" style="overflow-y:hidden;" name v-model="text" id="maintext"></textarea> -->
        <v-row>
          <v-col cols="12" md="6">
            <v-card outlined>
              <v-card-title>
                <h3>统计</h3>
              </v-card-title>
              <v-card-text>
                <v-row>
                  <v-col cols="12" md="6">
                    <h4>汉字个数</h4>
                  </v-col>
                  <v-col cols="12" md="6" class="d-flex justify-end">
                    <span>{{tj.hz}}</span>
                  </v-col>
                </v-row>
                <v-divider></v-divider>

                <v-row>
                  <v-col cols="12" md="6">
                    <h4>字母个数</h4>
                  </v-col>
                  <v-col cols="12" md="6" class="d-flex justify-end">
                    <span>{{tj.zm}}</span>
                  </v-col>
                </v-row>
                <v-divider></v-divider>

                <v-row>
                  <v-col cols="12" md="6">
                    <h4>符号个数</h4>
                  </v-col>
                  <v-col cols="12" md="6" class="d-flex justify-end">
                    <span>{{tj.fh}}</span>
                  </v-col>
                </v-row>
                <v-divider></v-divider>

                <v-row>
                  <v-col cols="12" md="6">
                    <h4>空格个数</h4>
                  </v-col>
                  <v-col cols="12" md="6" class="d-flex justify-end">
                    <span>{{tj.kg}}</span>
                  </v-col>
                </v-row>
                <v-divider></v-divider>

                <v-row>
                  <v-col cols="12" md="6">
                    <h4>字符总数</h4>
                  </v-col>
                  <v-col cols="12" md="6" class="d-flex justify-end">
                    <span>{{tj.all}}</span>
                  </v-col>
                </v-row>
              </v-card-text>
            </v-card>
          </v-col>
          <v-col>
            <v-card outlined>
              <v-card-title>
                <h3>操作</h3>
              </v-card-title>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
import text from "@/assets/text.json";
export default {
  name: "App",
  data: () => ({
    text: "null",
    tj: {
      hz: 0,
      zm: 0,
      fh: 0,
      kg: 0,
      all: 0
    }
  }),
  methods: {
    btnclick() {
      const obj = document.getElementById("maintext");
      obj.focus();
      obj.selectionStart = 0; // 选中开始位置
      obj.selectionEnd = 52; // 获取输入框里的长度。
    },
    input() {
      this.tj.hz=this.text.match(/[\u4E00-\u9FFF]/g).length;
      this.tj.zm=this.text.match(/[a-zA-Z]/g).length;
      this.tj.fh=this.text.match(/(?=[^\u4E00-\u9FFF])[^\w\s]/g).length;
      this.tj.kg = this.text.match(/[^\S\n]/g).length;
      this.tj.all = this.text.length;
    }
  },
  created() {
    this.text = text.body;
    this.input();
  },
  mounted() {
    var tx = document.getElementsByTagName("textarea");
    for (var i = 0; i < tx.length; i++) {
      tx[i].setAttribute(
        "style",
        "height:" + tx[i].scrollHeight + "px;overflow-y:hidden;"
      );
      tx[i].addEventListener("input", OnInput, false);
    }
    function OnInput() {
      // this.style.height = 'auto';// 当rows减少时使高度变小
      this.style.height = (this.scrollHeight) + 'px';
    }
  }
};
</script>
