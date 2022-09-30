<template>
  <div class="edge" v-dragscroll="true">
    <!-- @scroll="line_reset()" -->
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
          <div class="title_nodes" id="title" contenteditable="true" @blur="line_reset()">
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

      <div id="line-wrapper"></div>
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
  props: ['title_props', 'node_props'],
  directives: {
    dragscroll
  },
  setup(props) {
    console.log(props.title_props)
    console.log(props.node_props)
    console.log(typeof (props.node_props))

    let title = ref(props.title_props)
    let nodes = ref(props.node_props)
    let lines = ref([

    ])
    let count = 0
    const LeaderLine = window.LeaderLine;
    // LeaderLine.positionByWindowResize = false

    const right_append = (rap_node, node_text, node_childes) => {
      rap_node.appendChild(node_text)
      rap_node.appendChild(node_childes)
    }

    const left_append = (rap_node, node_text, node_childes) => {
      rap_node.appendChild(node_childes)
      rap_node.appendChild(node_text)
    }

    const makeFromParent = (node_text, rap_node, node_childes) => {
      node_text.classList.add("p_nodes");
      rap_node.classList.add("rap_node");

      count++
      return { rap_node, node_text, node_childes }
    }

    const makeFromChild = (el, node_text, rap_node, node_childes) => {
      const parent = document.getElementById(`${el["parent"]}`)
      node_text.classList.add("c_nodes");

      el.direction = nodes.value[el.parent].direction
      if (el.direction == 'right') {
        right_append(rap_node, node_text, node_childes)
        rap_node.classList.add("rap_node");
      } else {
        left_append(rap_node, node_text, node_childes)
        rap_node.classList.add("rap_node_left");
      }
      parent.appendChild(rap_node)
      return { rap_node, node_text, node_childes }
    }

    const makeline = (el, direction, parent) => {
      direction
      let line = new LeaderLine(
        document.getElementById(parent),
        LeaderLine.pointAnchor(document.getElementById(`${"node" + el["id"]}`), { x: `${50 + "%"}`, y: '50%' })
      );
      // straight
      line.path = "magnet"
      line.endPlug = "behind"

      const elmWrapper = document.getElementById('line-wrapper');
      const el_line = document.querySelectorAll('.leader-line')

      function position() {
        elmWrapper.style.transform = 'none';
        var rectWrapper = elmWrapper.getBoundingClientRect();
        // Move to the origin of coordinates as the document
        elmWrapper.style.transform = 'translate(' +
          ((rectWrapper.left + pageXOffset) * -1) + 'px, ' +
          ((rectWrapper.top + pageYOffset) * -1) + 'px)';
        line.position();
      }
      elmWrapper.appendChild(el_line[el_line.length - 1]);
      position()
      lines.value.push(el_line[el_line.length - 1])
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
        el.parentNode.removeChild(el)
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

        node_childes.id = `${el["id"]}`
        node_text.id = `${"node" + el["id"]}`
        node_text.appendChild(text)

        if (el["parent"] == 'title') {
          console.log()
          if (count % 2 == 0) {
            let nodeFromParent = makeFromParent(node_text, rap_node, node_childes)
            right_append(nodeFromParent.rap_node, nodeFromParent.node_text, nodeFromParent.node_childes)
            right.appendChild(nodeFromParent.rap_node)
            el.direction = 'right'
          } else {
            let nodeFromParent = makeFromParent(node_text, rap_node, node_childes)
            left_append(nodeFromParent.rap_node, nodeFromParent.node_text, nodeFromParent.node_childes)
            left.appendChild(nodeFromParent.rap_node)
            node_childes.classList.add("margin-left")
            el.direction = 'left'
          }
        } else {
          makeFromChild(el, node_text, rap_node, node_childes, text)
        }

        document.getElementById('node' + el.id).addEventListener('blur', () => {
          line_reset()
        });
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
  width: 100vh;
  height: 100vh;
  overflow: hidden;
  /* border: 1px solid #000; */
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
</style>