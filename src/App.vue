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
          label="请输入内容"
          @input="input"
          @mouseup="mouseup"
          @mouseout="mouseup"
          @keyup="mouseup"
        ></v-textarea>
        <!-- <textarea @input="OnInput" style="overflow-y:hidden;" name v-model="text" id="maintext"></textarea> -->
        <v-row>
          <v-col cols="12" md="4">
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
                <v-row>
                  <v-col>
                    <h3>自定义字符统计</h3>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="12" md="6">
                    <v-text-field outlined dense label="请输入内容" v-model="customText" @input="input"></v-text-field>
                  </v-col>
                  <v-col cols="12" md="6" class="d-flex justify-end">
                    <span>{{tj.custom}}</span>
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
              <v-card-text>
                <v-row>
                  <v-col>
                    <h3>文本选区：</h3>
                    ({{selection.x1}},{{selection.x2}})
                  </v-col>
                  <v-col>
                    <h3>{{selection.selectionStartStr!=""?selection.selectionStartStr+"→"+selection.selectionEndStr:""}}</h3>
                  </v-col>
                  <v-divider vertical></v-divider>

                  <v-col>
                    <h3>光标位置：</h3>
                    [{{position}}]
                  </v-col>
                  <v-col></v-col>
                </v-row>
              </v-card-text>
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
    customText: "",
    selection: {
      selectionStartStr: "",
      selectionEndStr: "",
      x1: 0,
      x2: 0
    },
    position: 0,
    tj: {
      hz: 0,
      zm: 0,
      fh: 0,
      kg: 0,
      all: 0,
      custom: 0
    }
  }),
  methods: {
    mouseup() {
      // 选区
      const activeTextarea = document.activeElement;
      const selection = activeTextarea.value.substring(
        activeTextarea.selectionStart,
        activeTextarea.selectionEnd
      );
      const selectionStartStr = selection.slice(0, 2);
      const selectionEndStr = selection.slice(-2);
      if (selectionStartStr != selectionEndStr) {
        this.selection.selectionStartStr = selectionStartStr;
        this.selection.selectionEndStr = selectionEndStr;
        this.selection.x1 = activeTextarea.selectionStart;
        this.selection.x2 = activeTextarea.selectionEnd;
      }
      this.position = activeTextarea.selectionEnd;
    },
    btnclick() {
      const obj = document.getElementById("maintext");
      obj.focus();
      obj.selectionStart = 0; // 选中开始位置
      obj.selectionEnd = 52; // 获取输入框里的长度。
    },
    input() {
      let a =
        this.customText != ""
          ? this.text.match(new RegExp(this.customText, "g"))
          : null;
      this.tj.custom = a ? a.length : 0; // 自定义
      a = this.text.match(/[\u4E00-\u9FFF]/g);
      this.tj.hz = a ? a.length : 0; // 汉字
      a = this.text.match(/[a-zA-Z]/g);
      this.tj.zm = a ? a.length : 0; // 字母
      a = this.text.match(/(?=[^\u4E00-\u9FFF])[^\w\s]/g);
      this.tj.fh = a ? a.length : 0; // 符号
      a = this.text.match(/[^\S\n]/g);
      this.tj.kg = a ? a.length : 0; // 空格
      this.tj.all = this.text.length; // 总长
    }
  },
  created() {
    this.text = text.body;
    this.input();
  },
  mounted() {
    // https://stackoverflow.com/questions/454202/creating-a-textarea-with-auto-resize
    // 高度自适应
    var tx = document.getElementsByTagName("textarea");
    for (var i = 0; i < tx.length; i++) {
      tx[i].setAttribute(
        "style",
        "height:" + tx[i].scrollHeight + "px;overflow-y:hidden;"
      );
      tx[i].addEventListener("input", OnInput, false);
    }
    function OnInput() {
      // this.style.height = 'auto';// 当rows减少时使高度变小，会使光标趋向视口底部
      this.style.height = this.scrollHeight + "px";
    }
  }
};
</script>
