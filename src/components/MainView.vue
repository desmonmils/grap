<template>
  <transition-group>
    <b-alert show variant="danger" v-show="show">{{ warning_text }}</b-alert>
  </transition-group>
  <div class="container">
    <h1>Создание графа знаний</h1>
    <div>
      <h3>Заполните таблицу</h3>
      <div class="input-group">
      <span class="input-group-text">Полное имя факта, краткое обозначение и значения фактов</span>
      <input type="text" aria-label="First name" class="form-control" data-toggle="tooltip" data-placement="top" title="Введите полное имя факта" v-model="infotemp.fakt" >
      <input type="text" aria-label="Last name" class="form-control" data-toggle="tooltip" data-placement="top" title="Введите краткое обозначение" v-model="infotemp.descr">
      <input type="text" aria-label="Last name" class="form-control" data-toggle="tooltip" data-placement="top" title="Введите значения фактов через ';'" v-model="infotemp.infofakt">
      </div>
      <p></p>
      <b-button variant="btn btn-success" @click="save" v-if="infotemp.fakt !== '' && infotemp.faktinfo !== ''">Сохранить</b-button>
      <b-button variant="btn btn-success" @click="save" v-else disabled>Сохранить</b-button>
      <h3>Введеные факты</h3>
      <b-form-textarea
        id="textarea1"
        v-model="textArray1"
        placeholder=""
        rows="3"
        max-rows="6"
        readonly
    ></b-form-textarea>
    </div>
    <div>
      <p></p>
    </div>
    <div>
      <select v-model="temp.f1" class="form-select" aria-label="Default select example">
        <option value="" disabled selected>Факт 1</option>
        <option v-for="i in fact_array" :key="i">{{ i.fakt }}</option>
      </select>
    </div>
    <div>
      <select v-model="temp.r" class="form-select" aria-label="Default select example">
        <option value="" disabled selected>Тип отношения</option>
        <option value="И">И</option>
        <option value="ИЛИ">Или</option>
      </select>
    </div>
    <div>
      <select v-model="temp.f2" class="form-select" aria-label="Default select example">
        <option value="" disabled selected>Факт 2</option>
        <option v-for="i in fact_array" :key="i">{{ i.fakt }}</option>
      </select>
    </div>
    <div class="form-floating">
    <textarea class="form-control" v-model="temp.val1" placeholder="Leave a comment here" id="floatingTextarea2" style="height: 100px"></textarea>
    <label for="floatingTextarea2">Значение факта 1</label>
    </div>
    <div class="form-floating">
    <textarea class="form-control" v-model="temp.val2" placeholder="Leave a comment here" id="floatingTextarea2" style="height: 100px"></textarea>
    <label for="floatingTextarea2">Значение факта 2</label>
    </div>
    <div>
      <h3>Итоговый факт</h3>
      <select v-model="temp.f3" class="form-select" aria-label="Default select example">
        <option value="" disabled selected>Факт 3</option>
        <option v-for="i in fact_array" :key="i">{{ i.fakt }}</option>
      </select>
      <div class="form-floating">
      <textarea class="form-control" v-model="temp.val3" placeholder="Leave a comment here" id="floatingTextarea2" style="height: 100px"></textarea>
      <label for="floatingTextarea2">Значение факта 3</label>
      </div>  
    </div>
    <h3>Данные</h3>
    <b-form-textarea
        id="textarea"
        v-model="textArray"
        placeholder=""
        rows="3"
        max-rows="6"
        readonly
    ></b-form-textarea>
    <p></p>
    <b-button @click="addText" variant="btn btn-primary" v-if="temp.f1 !=='' && temp.r !== '' && temp.f2 !== '' && temp.val1 !== '' && temp.val2 !== '' && temp.val3 !== ''">Добавить отношение</b-button>
    <b-button @click="addText" variant="btn btn-primary" v-else disabled>Добавить отношение</b-button>
    <b-button  variant="btn btn-warning" v-if="textArray !== ''" @click="sendData">Создать граф</b-button>
    <b-button  variant="btn btn-warning" v-else disabled>Создать граф</b-button>
    <b-button  variant="btn btn-danger" v-if="textArray !== ''" @click="clearAll">Очистить</b-button>
    <b-button  variant="btn btn-danger" v-else disabled>Очистить</b-button>
    <b-button  variant="btn btn-info" @click="savefile">Сохранить отчет</b-button>
  </div>
  <div v-show="ready">
    <v-network-graph
        class="graph"
        :nodes="nodes"
        :edges="edges"
        :layouts="layouts"
    >
      <template #edge-label="{ edge, ...slotProps }">
        <v-edge-label :text="edge.label" align="center" vertical-align="above" v-bind="slotProps" />
      </template>
    </v-network-graph>
  </div>
</template>

<script>
import make_http from "@/api/base";

export default {
  name: "MainView",
  title: "Observer",
  components: {

  },
  data() {
    return {
      fact_array: [],
      infotemp: {
        fakt: "",
        descr: "",
        infofakt: ""
      },
      temp: {
        f1: "",
        r: "",
        f2: "",
        f3: "",
        val1: "",
        val2: "",
        val3: ""
      },
      array: [],
      textArray: "",
      show: false,
      warning_text: "",
      nodes: {
        node1: { name: "Node 1" },
        node2: { name: "Node 2" },
        node3: { name: "Node 3" },
        node4: { name: "Node 4" },
        node5: { name: "Node 5" },
        node6: { name: "Node 6" },
      },
      edges: {
        edge1: { source: "node1", target: "node2" },
        edge2: { source: "node2", target: "node3" },
        edge3: { source: "node3", target: "node4" },
        edge4: { source: "node4", target: "node5" },
        edge5: { source: "node5", target: "node6" },
      },
      layouts: {
          nodes: {
            node1: { x: 0, y: 0 },
            node2: { x: 50, y: 50 },
            node3: { x: 100, y: 0 },
            node4: { x: 150, y: 50 },
            node5: { x: 200, y: 0 },
            node6: { x: 250, y: 50},
          }
        },
      kout : 0,
      ready: false,
      coutt : 0
    }
  },
  methods: {
    closeAlert() {
      this.show = false;
      this.warning_text = "";
    },
    clearFields() {
      this.temp.f1 = "";
      this.temp.r = "";
      this.temp.f2 = "";
      this.temp.val1 = "";
      this.temp.val2 = "";
      this.temp.val3 = "";
    },
    addText() {
      if ((this.temp.f1 === this.temp.f2) || (this.temp.f1 === this.temp.f3) || (this.temp.f2 === this.temp.f3)){
        this.warning_text = "Петли не допустимы!";
        this.show = true;
        this.clearFields();
        setTimeout(this.closeAlert, 2000);
        return;
      }
      var obj = { ...this.temp };
      this.array.push(obj);
      this.kout += 1;
      this.textArray += `Правило `+ `${this.kout}: `+` ${this.temp.f1}: ` + ` ${this.temp.val1} ` + ` ${this.temp.r} ` + ` ${this.temp.f2}: ` + ` ${this.temp.val2} ` + ` => ${this.temp.val3}\n`;
      this.clearFields();
    },
    async sendData() {
      try {
        const response = await make_http.post(`api/makegraph`, {
          data: this.array
        });
        if (response.status === 200) {
          localStorage.setItem("nodes", JSON.stringify(response.data.response.nodes));
          localStorage.setItem("edges", JSON.stringify(response.data.response.edges));
          localStorage.setItem("layouts", JSON.stringify(response.data.response.layouts));
          this.router.push("/GraphView");
        }
      } catch (e){
        this.warning_text = "Ошибка отправки запроса!"
        this.show = true;
        setTimeout(this.closeAlert, 2000);
      } finally {
        this.nodes = JSON.parse(localStorage.getItem("nodes"));
        this.edges = JSON.parse(localStorage.getItem("edges"));
        this.layouts = JSON.parse(localStorage.getItem("layouts"));
        this.ready = true;
      }
    },
    clearAll() {
      this.array = [];
      this.clearFields();
      this.textArray = "";
      this.textArray1 = "";
    },
    save() {
      let fact = { ...this.infotemp };
      fact.infofakt = this.parse_string(fact.infofakt);
      this.fact_array.push(fact);
      this.textArray1 += `${this.infotemp.fakt} ` + ` ${this.infotemp.descr} ` + ` ${this.infotemp.infofakt}\n`;
      this.clear_fact();
      this.coutt += 1;
    },
    clear_fact() {
      this.infotemp.fakt = "";
      this.infotemp.descr = "";
      this.infotemp.infofakt = "";
    },
    parse_string(infofakt) {
      var arr = [];
      for (var el of infofakt.split(';')) {
        arr.push(el);
      }
      return arr;
    },
    reformatToString(data) {
      let str = "";
      for (let el of data) {
        str = str + `??(${el.fakt})=` + el.descr + "\n";
        str = str + `==(${el.fakt})=` + el.infofakt.join(", ") + "\n";
        str = str + "\n";
      }
      return str;
    },
    download(data, filename, type) {
      var file = new Blob([data], {type: type});
      if (window.navigator.msSaveOrOpenBlob) // IE10+
        window.navigator.msSaveOrOpenBlob(file, filename);
      else { // Others
        var a = document.createElement("a"), url = URL.createObjectURL(file);
        a.href = url;
        a.download = filename;
        document.body.appendChild(a);
        a.click();
        setTimeout(function() {
          document.body.removeChild(a);
          window.URL.revokeObjectURL(url);
        }, 0);
      }
    },
    savefile() {
      if (this.fact_array.length === 0) {
        this.warning_text = "Отсутствуют факты!";
        this.show = true;
        setTimeout(this.closeAlert, 2000);
        return;
      }
      let data_str = this.createRule(this.array) + this.reformatToString(this.fact_array);
      this.download(data_str, "Отчёт.txt", "text/plain");
    },
    createRule(data) {
      let rule_counter = 1;
      let str = "";
      for (let el of data) {
        str = str + `Правило ${rule_counter}:\nЕСЛИ\n${el.f1}=${el.val1}\n${el.r}\n${el.f2}=${el.val2}\nТО ${el.val3}\n`;
        rule_counter += 1;
      }
      return str;
    }
  }
}
</script>

<style scoped>
</style>
