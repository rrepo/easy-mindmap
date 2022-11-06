<template>
  <div class="edge" id="edge" v-dragscroll="true">
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
          <!-- v-on:click="focus_node($event)" -->


          <!-- v-on:click.self="click_title_selector($event)" -->

          <div class="selector" id="selector" tabindex="0" v-on:click="update_focus($event)"
            v-on:dblclick="input_node($event)">
            <!-- @:keydown="keydown_title($event)" -->

            <div class="title_nodes" id="title" contenteditable="true" @blur="line_reset()">
              <!-- @:keydown.tab="push_tab($el)" -->
              {{ title }}
            </div>
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

    let title = ref(props.title_props)
    let nodes = ref(props.node_props)
    let lines = ref([

    ])
    let count = 0
    let focus
    const LeaderLine = window.LeaderLine;
    // LeaderLine.positionByWindowResize = false

    const right_append = (rap_node, node_text, node_childes, node_selector) => {
      node_selector.appendChild(node_text)
      rap_node.appendChild(node_selector)
      rap_node.appendChild(node_childes)
    }

    const left_append = (rap_node, node_text, node_childes, node_selector) => {
      node_selector.appendChild(node_text)
      rap_node.appendChild(node_childes)
      rap_node.appendChild(node_selector)
    }

    const makeFromParent = (node_text, rap_node, node_childes) => {
      node_text.classList.add("p_nodes");
      rap_node.classList.add("rap_node");

      count++
      return { rap_node, node_text, node_childes }
    }

    const makeFromChild = (el, node_text, rap_node, node_childes, node_selector) => {
      const parent = document.getElementById(`${el["parent"]}`)
      node_text.classList.add("c_nodes");

      el.direction = nodes.value[el.parent].direction
      if (el.direction == 'right') {
        right_append(rap_node, node_text, node_childes, node_selector)
        rap_node.classList.add("rap_node");
      } else {
        left_append(rap_node, node_text, node_childes, node_selector)
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



    const input_node = () => {
      // console.log("dbclick_title_selector")
      // console.log("$event", e.srcElement.id)
      console.log(document.getElementById('title').id)
      document.getElementById("title").focus();
      const el = document.getElementById('title');
      const selection = window.getSelection();
      const range = document.createRange();
      selection.removeAllRanges();
      range.selectNodeContents(el);
      range.collapse(false);
      selection.addRange(range);
      el.focus();
      return false;
    }

    const input_node_test = (e) => {
      console.log('inputnode db', e)
      console.log(document.getElementById(`node${e}`))

      const target = document.getElementById(`node${e}`)

      target.focus();
      // const el = document.getElementById('title');
      const selection = window.getSelection();
      const range = document.createRange();
      selection.removeAllRanges();
      range.selectNodeContents(target);
      range.collapse(false);
      selection.addRange(range);
      target.focus();
      return false;
    }


    const update_focus = (e) => {
      if (focus != null) {
        focus.classList.remove("selector_focus");
      }
      e.srcElement.classList.add("selector_focus");
      focus = null
      focus = e.srcElement
    }


    const focus_node = () => {
      // console.log("focusfocusfocus",focus)
      if (focus) {
        focus.focus()
        // childnodeの時ダブルクリックでフォーカスできるようにする
      }
    }

    onMounted(() => {
      console.log("mounted")

      const el_title = document.getElementById('title')
      const el_edge = document.getElementById('edge')

      el_title.scrollIntoView({ block: 'center', inline: 'center', })

      const right = document.getElementById("right_center");
      const left = document.getElementById("left_center");

      nodes.value.forEach(el => {
        const node_text = document.createElement("div");

        const node_selector = document.createElement("div");
        node_selector.classList.add("selector")
        node_selector.id = `selector${el["id"]}`
        node_selector.tabIndex = "0";

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
            right_append(nodeFromParent.rap_node, nodeFromParent.node_text, nodeFromParent.node_childes, node_selector)
            right.appendChild(nodeFromParent.rap_node)
            el.direction = 'right'
          } else {
            let nodeFromParent = makeFromParent(node_text, rap_node, node_childes)
            left_append(nodeFromParent.rap_node, nodeFromParent.node_text, nodeFromParent.node_childes, node_selector)
            left.appendChild(nodeFromParent.rap_node)
            node_childes.classList.add("margin-left")
            el.direction = 'left'
          }
        } else {
          makeFromChild(el, node_text, rap_node, node_childes, node_selector)
        }

        // 以下イベントの追加
        const el_node = document.getElementById('node' + el.id)
        const el_selector = document.getElementById('selector' + el.id)

        el_node.addEventListener('blur', () => {
          line_reset()
        });

        el_selector.addEventListener('click', (e) => {
          update_focus(e)
        });

        el_selector.addEventListener('dblclick', (e) => {
          input_node_test(e.srcElement.id.substr(8))
        })

        el_selector.addEventListener('keydown', (e) => {
          if ((e.keyCode == 10 || e.keyCode == 13) && e.ctrlKey) {
            console.log('keydown + ctrl')
            input_node_test(e.srcElement.id.substr(8))
          }
        })

        // 入力中はスクロールを解除する
        el_node.addEventListener('focus', () => {
          console.log("childnode")
          el_edge.removeEventListener('scroll',
            focus_node
          )
        })

        // 解除時のスクロールを戻す
        el_node.addEventListener('blur', () => {
          el_edge.addEventListener('scroll', focus_node)
        })

      });




      console.log(nodes.value)
      makelines()

      // 親要素へのイベントの追加
      document.getElementById('selector').addEventListener('keydown', (e) => {
        if ((e.keyCode == 10 || e.keyCode == 13) && e.ctrlKey) {
          console.log('keydown + ctrl')
          input_node(e)
        }
      })

      el_edge.addEventListener('scroll', focus_node)

      el_title.addEventListener('focus', () => {
        console.log("focussss")
        el_edge.removeEventListener('scroll', focus_node)
      })

      el_title.addEventListener('blur', () => {
        el_edge.addEventListener('scroll', focus_node)
      })



    });

    return {
      title,
      nodes,
      lines,
      line_reset,
      input_node,
      update_focus,
      focus_node,
    };

  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
#line-wrapper {
  z-index: -100;
}

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
  z-index: 1;
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

.selector {
  width: fit-content;
  height: fit-content;
  border-radius: 10px;
  /* background-color:#00aaff; */
  position: relative;
}

.selector:focus {
  outline: none;
}

.title_selector:focus {
  outline: none;
}

.title_nodes {
  background-color: white;
  border-radius: 10px;
  /* box-shadow: 0 3px 3px 0 rgba(0, 0, 0, .5); */

  width: fit-content;

  font-size: 35px;
  font-family: sans-serif;
  padding: 10px 10px;
  max-width: 200px;

  /* pointer-events: none; */
  position: relative;
  z-index: -10;
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
  font-family: sans-serif;
  padding: 10px 10px;
  margin: auto 0;

  position: relative;
  z-index: -10;
}

.p_nodes:focus {
  outline: none;
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
  font-family: sans-serif;

  padding: 10px 10px;
  margin: 30px 30px;

  position: relative;
  z-index: -10;
}

.c_nodes:focus {
  outline: none;
}

.rap_node {
  display: flex;
  align-items: center;
  width: 100%;
  /* height: 100%; */
  /* padding: 10px 0px; */
  /* margin-left: auto;
  margin-right: 0; */

  /* background-color: aqua; */
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

.selector_focus {
  border: 4px solid #00aaff;
  border-radius: 10px;
}
</style>