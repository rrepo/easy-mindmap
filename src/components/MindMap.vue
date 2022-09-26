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
            <div class="title_nodes" id="title" contenteditable="true">
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
        "id": 0,
        'text': 'ch0',
        'url': '',
        'parent': 'title',
        'direction': ''
      },
      {
        "id": 1,
        'text': 'ch1',
        'url': '',
        'parent': 'title',
        'direction': ''
      },
      {
        "id": 2,
        'text': 'ch2',
        'url': '',
        'parent': 0,
        'direction': ''
      },
      {
        "id": 3,
        'text': 'ch3',
        'url': '',
        'parent': 0,
        'direction': ''
      },
      {
        "id": 4,
        'text': 'ch4',
        'url': '',
        'parent': 1,
        'direction': ''
      },
      {
        "id": 5,
        'text': 'ch5',
        'url': '',
        'parent': 1,
        'direction': ''
      },
      {
        "id": 6,
        'text': 'ch6',
        'url': '',
        'parent': 'title',
        'direction': ''
      },
      {
        "id": 7,
        'text': 'ch7',
        'url': '',
        'parent': 'title',
        'direction': ''
      },
      {
        "id": 8,
        'text': 'ch8',
        'url': '',
        'parent': '2',
        'direction': ''
      },
      {
        "id": 9,
        'text': 'ch9',
        'url': '',
        'parent': '2',
        'direction': ''
      },
      {
        "id": 10,
        'text': 'ch10',
        'url': '',
        'parent': '4',
        'direction': ''
      },
      {
        "id": 11,
        'text': 'ch11',
        'url': '',
        'parent': '4',
        'direction': ''
      },
      {
        "id": 12,
        'text': 'ch12',
        'url': '',
        'parent': '5',
        'direction': ''
      },
      {
        "id": 13,
        'text': 'ch13',
        'url': '',
        'parent': 'title',
        'direction': ''
      },
      {
        "id": 14,
        'text': 'ch14',
        'url': '',
        'parent': '7',
        'direction': ''
      },
      {
        "id": 15,
        'text': 'ch15',
        'url': '',
        'parent': '6',
        'direction': ''
      },
      {
        "id": 16,
        'text': 'ch16',
        'url': '',
        'parent': '7',
        'direction': ''
      },
      {
        "id": 17,
        'text': 'ch17',
        'url': '',
        'parent': '14',
        'direction': ''
      },
    ]);
    let lines = ref([

    ])
    let count = 0
    const LeaderLine = window.LeaderLine;

    // fix input move

    const makeFromParent = (el, node_text, rap_node, node_childes, text) => {
      node_text.classList.add("p_nodes");
      rap_node.classList.add("rap_node");
      node_childes.id = `${el["id"]}`
      node_text.id = `${"node" + el["id"]}`
      node_text.appendChild(text)

      count++

      return [rap_node, node_text, node_childes]
    }

    const makeFromChild = (el, node_text, rap_node, node_childes, text) => {
      const parent = document.getElementById(`${el["parent"]}`)
      node_text.classList.add("c_nodes");
      node_childes.id = `${el["id"]}`
      node_text.id = `${"node" + el["id"]}`
      node_text.appendChild(text)
      rap_node.appendChild(node_childes)
      rap_node.appendChild(node_text)
      parent.appendChild(rap_node)

      el.direction = nodes.value[el.parent].direction
      if (el.direction == 'right') {
        rap_node.appendChild(node_text)
        rap_node.appendChild(node_childes)
        parent.appendChild(rap_node)
        rap_node.classList.add("rap_node");
      } else {
        rap_node.appendChild(node_childes)
        rap_node.appendChild(node_text)
        parent.appendChild(rap_node)
        rap_node.classList.add("rap_node_left");
      }
    }

    const makeline = (el, direction, parent) => {
      console.log(direction)
      let line = new LeaderLine(
        document.getElementById(parent),
        LeaderLine.pointAnchor(document.getElementById(`${"node" + el["id"]}`), { x: `${50 + "%"}`, y: '50%' })
      );
      // straight
      line.path = "magnet"
      line.endPlug = "behind"
      lines.value.push(line)
    }

    const makelines = () => {
      const right = 1
      const left = 99

      nodes.value.forEach(el => {
        if (el.parent == 'title') {
          if (el.direction == "right") {
            makeline(el, right, 'title')
          } else {
            makeline(el, left, 'title')
          }
        } else {
          if (el.direction == "right") {
            makeline(el, right, `${"node" + el["parent"]}`)
          } else {
            makeline(el, left, `${"node" + el["parent"]}`)
          }

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

    onMounted(() => {
      console.log("mounted")

      document.getElementById('title').scrollIntoView({ block: 'center', inline: 'center', })

      const right = document.getElementById("right_center");
      const left = document.getElementById("left_center");

      nodes.value.forEach(el => {
        const node_text = document.createElement("div");
        const rap_node = document.createElement("div");
        const node_childes = document.createElement("div");
        const text = document.createTextNode(el["text"]);
        node_text.contentEditable = true;

        if (el["parent"] == 'title') {
          console.log()
          if (count % 2 == 0) {
            let nodeFromParent = makeFromParent(el, node_text, rap_node, node_childes, text)

            nodeFromParent[0].appendChild(nodeFromParent[1])
            nodeFromParent[0].appendChild(nodeFromParent[2])

            right.appendChild(nodeFromParent[0])
            el.direction = 'right'
          } else {
            let nodeFromParent = makeFromParent(el, node_text, rap_node, node_childes, text)

            nodeFromParent[0].appendChild(nodeFromParent[2])
            nodeFromParent[0].appendChild(nodeFromParent[1])

            left.appendChild(nodeFromParent[0])
            node_childes.classList.add("margin-left")
            el.direction = 'left'
          }
        } else {
          makeFromChild(el, node_text, rap_node, node_childes, text)
        }
      });

      console.log(nodes.value)

      makelines()
    });

    return {
      title,
      nodes,
      lines,
      line_reset,
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
  width: 900px;
  height: 900px;
  overflow: hidden;
  border: 1px solid #000;
}

.field {
  width: 5000px;
  height: 5000px;
  /* z-index: 1; */
  display: flex;
  position: relative;
}

.center {
  width: 10%;
  height: 100%;
  /* z-index: 1; */
  /* background-color: coral; */
}

.right {
  width: 45%;
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
  width: 45%;
  height: 100%;
  display: flex;
  align-items: center;
}

.left_vertical {
  width: 100%;
  margin: 0 auto;
  text-align: left;
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
  z-index: 2;
}

.title_selector {
  width: fit-content;
  height: fit-content;
  border-radius: 10px;
  /* width: 100px;
  height: 100px; */
  /* border: 4px solid; */

  /* background-color: antiquewhite; */
  position: relative;
}

.selector_focus {
  border: 4px solid #00aaff;
  border-radius: 10px;
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

  /* pointer-events: none; */
  position: relative;
  z-index: -10
}

.title_nodes:focus {
  outline: none;
}

.p_nodes {
  background-color: #F5F5F5;
  border-radius: 10px;
  box-shadow: 0 3px 3px 0 rgba(0, 0, 0, .5);
  /* width: fit-content;
  height: fit-content; */
  width: -moz-fit-content;
  /* Firefox */
  width: fit-content;
  /* other browsers */
  font-size: 23px;
  padding: 10px 10px;
  font-family: sans-serif;
  margin: auto 0;

  z-index: 1;
}

.c_nodes {
  background-color: #F5F5F5;
  border-radius: 10px;
  box-shadow: 0 3px 3px 0 rgba(0, 0, 0, .5);
  /* width: fit-content;
  height: fit-content; */
  width: -moz-fit-content;
  width: fit-content;
  font-size: 23px;
  padding: 10px 10px;
  font-family: sans-serif;
  margin: 30px 30px;

  z-index: 1;
}

.rap_node {
  display: flex;
  align-items: center;
  width: 100%;
  /* height: 100%; */
  /* padding: 10px 0px; */
  /* margin-left: auto;
  margin-right: 0; */
}

.rap_node_left {
  display: flex;
  align-items: center;
  padding: 10px 0px;
  width: 100%;

  justify-content: flex-end;
}

.margin-left {
  /* margin: auto; */
  margin-left: auto;
  margin-right: 0;
}

.node_childes {
  /* width: 100%;
  height: 100%; */
  /* background-color: brown; */
}

.right_node_childes {
  width: 100%;
  height: 100%;
  background-color: brown;
  width: -moz-fit-content;
  /* Firefox */
  width: fit-content;
  /* other browsers */
}

.left_node_childes {
  width: 100%;
  height: 100%;
  width: -moz-fit-content;
  width: fit-content;
  margin-right: auto;
  background-color: forestgreen;
}


</style>