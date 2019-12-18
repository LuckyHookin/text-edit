<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <h1>多功能文本编辑器</h1>
    </v-app-bar>
    <v-content>
      <v-container>
        
          <v-textarea
            
            auto-grow
            id="maintext"
            :value="text"
            label="在此输入内容："
            @keyup="change"
            autofocus
          ></v-textarea>
        
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
                    <h4>总共字数</h4>
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
    change() {
      this.tj.kg=this.text.match(/\s/g).length;
      this.tj.all=this.text.length;
    }
  },
  mounted() {
    this.text = text.body;
  }
};
</script>
