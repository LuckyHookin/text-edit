<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <h1>多功能文本编辑器</h1>
    </v-app-bar>
    <v-content>
      <v-container @mouseup="mouseup">
        <v-textarea
          counter
          autofocus
          outlined
          id="maintext"
          v-model="text"
          label="请输入内容"
          @input="input"
          @keyup="mouseup"
        ></v-textarea>
        <!-- <textarea @input="OnInput" style="overflow-y:hidden;" name v-model="text" id="maintext"></textarea> -->
        <v-row>
          <v-col cols="12" md="4">
            <v-card class="mb-4" outlined>
              <v-card-text>
                <v-row>
                  <v-col>
                    <v-btn rounded color="primary">打开文件</v-btn>
                  </v-col>
                  <v-spacer></v-spacer>
                  <v-col>
                    <v-btn rounded color="primary">保存文件</v-btn>
                  </v-col>
                </v-row>
                <v-divider></v-divider>
                <v-row>
                  <v-col>
                    <v-btn rounded color="primary">加密</v-btn>
                  </v-col>
                  <v-spacer></v-spacer>
                  <v-col>
                    <v-btn rounded color="primary">解密</v-btn>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col>
                    <v-text-field
                      dense
                      outlined
                      v-model="keyText"
                      label="密钥"
                    ></v-text-field>
                  </v-col>
                </v-row>
              </v-card-text>
            </v-card>
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
                    <h3>{{selection.smallText}}</h3>
                  </v-col>
                  <v-divider vertical></v-divider>

                  <v-col>
                    <h3>光标位置：</h3>
                    [{{position}}]
                  </v-col>
                  <v-col></v-col>
                </v-row>
                <br />
                <v-row class="justify-md-space-around">
                  <v-btn color="primary" @click="copy">复制</v-btn>
                  <v-btn color="primary" @click="cut">剪切</v-btn>
                  <v-btn color="primary" @click="del">删除</v-btn>
                  <v-btn color="primary" @click="paste">黏贴</v-btn>
                </v-row>
                <v-row>
                  <v-col>
                    <v-textarea
                      id="clipText"
                      :value="clipText"
                      outlined
                      label="获取剪贴板内容"
                      @mouseenter="getClipText"
                    ></v-textarea>
                  </v-col>
                </v-row>
                <v-divider></v-divider>
                <v-row>
                  <v-col cols="12">
                    <h3>查找：</h3>
                  </v-col>
                  <v-col>
                    <v-text-field
                      dense
                      outlined
                      v-model="search.text"
                      label="按回车查找"
                      @keyup.enter="searchFunc"
                      @input="search.array=[]"
                    ></v-text-field>
                    <h4>-->找到的位置：</h4>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="2" v-for="x in search.array" :key="x.id">{{ x===-1?"End":x }}</v-col>
                </v-row>
                <v-col class="mx-auto" cols="6">
                  <v-divider></v-divider>
                </v-col>
                <v-row>
                  <v-col cols="12">
                    <h3>替换：</h3>
                  </v-col>
                  <v-col>
                    <v-text-field
                      dense
                      outlined
                      v-model="exchangeText"
                      label="按回车替换全部"
                      @keyup.enter="exchangeFunc"
                    ></v-text-field>
                  </v-col>
                </v-row>
              </v-card-text>
            </v-card>
          </v-col>
        </v-row>
        <v-snackbar v-model="snackbar.show" color="primary">
          {{ snackbar.text }}
          <v-btn color="red" text @click="snackbar.show = false">关闭</v-btn>
        </v-snackbar>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
import text from "@/assets/text.json";
export default {
  name: "App",
  data: () => ({
    keyText:"",
    exchangeText: "",
    search: {
      text: "",
      array: []
    },
    clipText: "[获取剪切板内容]",
    text: "null",
    customText: "", // 自定义统计要匹配的文本
    selection: {
      text: "",
      smallText: "", // 显示选区头尾各两字符
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
    },
    snackbar: {
      // 提示
      show: false,
      text: ""
    }
  }),
  methods: {
    exchangeFunc() {
      if (this.search.text != "") {
        this.text = this.text.replace(
          new RegExp(this.search.text, "g"),
          this.exchangeText
        );
        this.snackbar.text = "替换成功！";
        this.snackbar.show = true;
      }
    },
    searchFunc() {
      let searchArr = this.search.array[this.search.array.length - 1];
      if (searchArr != -1) {
        searchArr = this.text.indexOf(this.search.text, searchArr + 1);
        this.search.array.push(searchArr);
        return 0;
      }
      this.snackbar.text = "查找完毕！";
      this.snackbar.show = true;
      // const obj = document.getElementById("maintext");
      // obj.focus();
      // obj.selectionStart = 2106; // 选中开始位置
      // obj.selectionEnd = 2108; // 获取输入框里的长度。
      // this.$vuetify.goTo(0);
    },
    getClipText() {
      navigator.clipboard.readText().then(clipText => {
        this.clipText = clipText;
      });
      setTimeout(() => {
        const ele = document.getElementById("clipText");
        ele.style.height = "auto"; // 当rows减少时使高度变小，会使光标趋向视口底部
        ele.style.height = ele.scrollHeight + 10 + "px";
      }, 10);
    },
    mouseup() {
      // https://developer.mozilla.org/en-US/docs/Web/API/DocumentOrShadowRoot/activeElement
      // 选区
      const activeTextarea = document.activeElement;
      if (activeTextarea.id != "maintext") {
        return -1;
      }
      const selection = this.text.substring(
        activeTextarea.selectionStart,
        activeTextarea.selectionEnd
      );
      const selectionStartStr = selection.slice(0, 2);
      const selectionEndStr = selection.slice(-2);

      this.selection.text = selection;
      this.selection.smallText = selectionStartStr + "→" + selectionEndStr;
      this.selection.x1 = activeTextarea.selectionStart;
      this.selection.x2 = activeTextarea.selectionEnd;

      this.position = activeTextarea.selectionEnd;
    },
    copy() {
      // const obj = document.getElementById("maintext");
      // obj.focus();
      // obj.selectionStart = 0; // 选中开始位置
      // obj.selectionEnd = 52; // 获取输入框里的长度。

      // https://developer.mozilla.org/en-US/docs/Web/API/Clipboard
      // 剪切板
      this.snackbar.text = "请确定选区！";
      if (this.selection.x1 != this.selection.x2) {
        // 把 selection.text 写到剪贴板
        navigator.clipboard.writeText(this.selection.text);
        this.snackbar.text = "复制成功！";
      }
      this.snackbar.show = true;
      // 读
      // navigator.clipboard.readText().then(clipText => alert(clipText));

      // https://developer.mozilla.org/en-US/docs/Web/API/Selection/deleteFromDocument
      // 选区删除
    },
    cut() {
      this.snackbar.text = "请确定选区！";

      if (this.selection.x1 != this.selection.x2) {
        // 把 selection.text 写到剪贴板
        navigator.clipboard.writeText(this.selection.text);
        // 删除选区
        // let selection = window.getSelection();
        // selection.deleteFromDocument();
        this.text =
          this.text.substring(0, this.selection.x1) +
          this.text.substring(this.selection.x2, this.text.length);
        this.snackbar.text = "剪切成功！";
      }
      this.snackbar.show = true;
    },
    del() {
      this.snackbar.text = "请确定选区！";
      if (this.selection.x1 != this.selection.x2) {
        // let selection = window.getSelection();
        // selection.deleteFromDocument(); // 删除选区
        this.text =
          this.text.substring(0, this.selection.x1) +
          this.text.substring(this.selection.x2, this.text.length);
        this.snackbar.text = "删除成功！";
      }
      this.snackbar.show = true;
    },
    paste() {
      // https://www.w3school.com.cn/jsref/jsref_indexOf.asp
      // search
      // https://www.w3school.com.cn/jsref/jsref_replace.asp
      // 正则替换

      // function changeStr(allstr,start,end,str,changeStr){ //allstr:原始字符串，start,开始位置,end：结束位置,str：要改变的字，changeStr:改变后的字
      // return allstr.substring(0,start-1)+changeStr+allstr.substring(end,allstr.length);
      // let tclipText;
      // navigator.clipboard.readText().then(clipText => tclipText=clipText);
      if (this.selection.x2 > this.text.length) {
        // 超出范围
        this.snackbar.text = "超出范围！";
        this.snackbar.show = true;
        return -1;
      }
      navigator.clipboard.readText().then(clipText => {
        this.text =
          this.text.substring(0, this.selection.x1) +
          clipText +
          this.text.substring(this.selection.x2, this.text.length);
      });
      this.input();
      setTimeout(() => {
        const ele = document.getElementById("maintext");
        ele.style.height = ele.scrollHeight + "px";
        this.snackbar.text = "黏贴成功！";
        this.snackbar.show = true;
      }, 10);

      // this.text =
      //   this.text.substring(0, this.selection.x1) +
      //   navigator.clipboard.readText().then(clipText => {return clipText.toString()})
      // +this.text.substring(this.selection.x2, this.text.length);
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
