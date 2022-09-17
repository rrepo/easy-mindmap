<template>
  <div class="">
    <div class="edge" @scroll="line_reset" v-dragscroll="true">
      <div class="field" id="field">
        <!-- <button class="btn" @click="click()">center</button> -->
        <div class="left">
          <div class="left_vertical">
            <div class="left_horizontal" id="left_center">
              <div></div>
            </div>
          </div>
        </div>

        <div class="center">
          <div class="title_node_rap">
            <div class="title_nodes" id="0" contenteditable="true" @:keydown="input_title($event)">
              <!-- @:keydown.tab="push_tab($el)" -->
              {{ title }}
            </div>
          </div>

        </div>

        <div class="right">
          <div class="right_vertical">
            <div class="right_horizontal" id="right_center">
              <div></div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import {
  onMounted,
  ref,

} from "vue"
import { dragscroll } from 'vue-dragscroll'

export default {
  name: 'MindMap',
  props: {
  },
  directives: {
    dragscroll
  },
  setup() {
    let title = ref("title")
    let nodes = ref([
      {
        "id": 1,
        'text': 'ch1',
        'url': '',
        'parent': 0,
      },
      {
        "id": 2,
        'text': 'ch2',
        'url': '',
        'parent': 0,
      },
      {
        "id": 3,
        'text': 'ch3',
        'url': '',
        'parent': 0,
      },
      {
        "id": 4,
        'text': 'ch4',
        'url': '',
        'parent': 1,
      },
      {
        "id": 5,
        'text': 'ch5',
        'url': '',
        'parent': 1,
      },
      {
        "id": 6,
        'text': 'ch6',
        'url': '',
        'parent': 1,
      },
    ]);
    let lines = ref([

    ])
    let count = 0
    const LeaderLine = window.LeaderLine;

    const makeFromParent = (d, el, node_text, rap_node, node_childes, text) => {
      node_text.classList.add("p_nodes");
      rap_node.classList.add("rap_node");
      node_childes.classList.add("node_childes");
      node_childes.id = `${el["id"]}`
      node_text.id = `${"node" + el["id"]}`
      node_text.appendChild(text)

      count++

      return [rap_node, node_text, node_childes]
    }

    const makeFromChild = (el, node_text, rap_node, node_childes, text) => {
      // console.log("child", el)
      const parent = document.getElementById(`${el["parent"]}`)
      node_text.classList.add("c_nodes");
      rap_node.classList.add("rap_node");
      node_childes.classList.add("node_childes");
      node_childes.id = `${el["id"]}`
      node_text.id = `${"node" + el["id"]}`
      node_text.appendChild(text)
      rap_node.appendChild(node_childes)
      rap_node.appendChild(node_text)
      parent.appendChild(rap_node)
    }

    const makelines = () => {
      nodes.value.forEach(el => {
        if (el.parent == 0) {
          let line = new LeaderLine(
            document.getElementById('0'),
            LeaderLine.pointAnchor(document.getElementById(`${"node" + el["id"]}`), { x: '20%', y: '50%' })
          );
          // line.setOptions({startSocket: 'auto', endSocket: 'auto'});
          line.path = "magnet"
          line.endPlug = "behind"
          lines.value.push(line)
        } else {
          let line = new LeaderLine(
            document.getElementById(`${"node" + el["parent"]}`),
            LeaderLine.pointAnchor(document.getElementById(`${"node" + el["id"]}`), { x: '20%', y: '50%' })
          );
          line.path = "magnet"
          line.endPlug = "behind"
          lines.value.push(line)
        }
      })
    }

    const removelines = () => {
      lines.value.forEach(el => {
        el.remove()
      })
      lines.value.length = 0
    }

    const line_reset = () => {
      removelines()
      makelines()
    }

    const input_title = (e) => {
      // console.log(e.srcElement.innerText)
      console.log(e.target.offsetWidth)
      title.value = e.srcElement.innerText
      console.log(title)
      line_reset()
    }

    const keydown_node = (e) => {
      console.log(e)
      // console.log(e.srcElement.innerText)
      // console.log(e.target.offsetWidth)
      // 横幅を動的に変更する
      line_reset()
    }

    const keyup_node = (e) => {
      let id = Number(e.srcElement.id.replace("node", ""))
      nodes.value[id].text = e.srcElement.innerText
      line_reset()
    }

    onMounted(() => {
      console.log("mounted")

      document.getElementById('0').scrollIntoView({ block: 'center', inline: 'center', })

      const right = document.getElementById("right_center");
      const left = document.getElementById("left_center");

      nodes.value.forEach(el => {
        const node_text = document.createElement("div");
        const rap_node = document.createElement("div");
        const node_childes = document.createElement("div");
        const text = document.createTextNode(el["text"]);
        node_text.contentEditable = true;

        if (count % 2 == 0) {
          if (el["parent"] == 0) {
            let nodeFromParent = makeFromParent(right, el, node_text, rap_node, node_childes, text)

            nodeFromParent[0].appendChild(nodeFromParent[1])
            nodeFromParent[0].appendChild(nodeFromParent[2])

            right.appendChild(nodeFromParent[0])
          } else {
            makeFromChild(el, node_text, rap_node, node_childes, text)
          }
        } else {
          if (el["parent"] == 0) {
            let nodeFromParent = makeFromParent(right, el, node_text, rap_node, node_childes, text)

            nodeFromParent[0].appendChild(nodeFromParent[2])
            nodeFromParent[0].appendChild(nodeFromParent[1])

            left.appendChild(nodeFromParent[0])
          } else {
            makeFromChild(el, node_text, rap_node, node_childes, text)
          }
        }

        document.getElementById(`${'node'+el['id']}`).addEventListener('keydown', keydown_node);
        document.getElementById(`${'node'+el['id']}`).addEventListener('keyup', keyup_node);

      });

      makelines()
    });

    return {
      title,
      nodes,
      lines,
      line_reset,
      input_title,
      keydown_node,
      keyup_node
    };

  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
.btn {
  z-index: 10000;
  /* display: flex; */
}

.edge {
  width: 800px;
  height: 800px;
  overflow: hidden;
  border: 1px solid #000;
}

.field {
  width: 1000px;
  height: 1000px;
  z-index: 1000000;
  display: flex;
  position: relative;
}

.center {
  width: 30%;
  height: 100%;
  z-index: 1000000000000;
  /* background-color: coral; */
}

.right {
  width: 35%;
  height: 100%;
  display: flex;
  align-items: center;
}

.right_vertical {
  width: 100%;
  /* background-color: olive; */
  margin: 0 auto;
  display: flex;
  justify-content: flex-start;
}

.right_horizontal {
  width: fit-content;
  /* background-color: orchid; */
}

.left {
  width: 35%;
  height: 100%;
  display: flex;
  align-items: center;
}

.left_vertical {
  width: 100%;
  margin: 0 auto;
  text-align: right;
  display: flex;
  justify-content: flex-end;
  /* background-color: olive; */
}

.left_horizontal {
  width: fit-content;
  /* background-color: orchid; */
}

.title_node_rap {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
}

.title_nodes {
  border-radius: 10px;
  box-shadow: 0 3px 3px 0 rgba(0, 0, 0, .5);
  width: fit-content;
  font-size: 35px;
  font-family: sans-serif;
  padding: 10px 10px;
  background-color: #F5F5F5;
  max-width: 200px;
}

.rap_node {
  display: flex;
  margin: 60px 0px;
  align-items: center;
  /* background-color: rebeccapurple; */
}

.p_nodes {
  background-color: #F5F5F5;
  border-radius: 10px;
  box-shadow: 0 3px 3px 0 rgba(0, 0, 0, .5);
  width: fit-content;
  height: fit-content;
  font-size: 23px;
  padding: 10px 10px;
  font-family: sans-serif;
  margin: auto 0;
}

.c_nodes {
  background-color: #F5F5F5;
  border-radius: 10px;
  box-shadow: 0 3px 3px 0 rgba(0, 0, 0, .5);
  width: fit-content;
  height: fit-content;
  font-size: 23px;
  padding: 10px 10px;
  font-family: sans-serif;
  margin: 60px 60px;
}

.node_childes {
  width: 100%;
  height: 100%;
  /* background-color: brown; */
}

#line {
  position: absolute;
  left: 100px;
  top: 100px;
  width: 200px;
  height: 1px;
  background-color: red;
  transform-origin: left top;
  transform: skewY(45deg);
}
</style>
  
