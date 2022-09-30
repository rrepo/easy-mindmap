<template>
  <div class="rapmap">
    <MindMap title_props="props" :node_props="[
    {
      'id': 0,
      'text': 'ch0',
      'url': '',
      'parent': 'title',
      'direction': ''  
    },
    {
      'id': 1,
      'text': 'ch1',
      'url': '',
      'parent': 'title',
      'direction': ''
    },
    {
      'id': 2,
      'text': 'ch2',
      'url': '',
      'parent': 'title',
      'direction': ''
    },
    {
      'id': 3,
      'text': 'ch3',
      'url': '',
      'parent': 'title',
      'direction': ''
    },
    {
      'id': 4,
      'text': 'ch4',
      'url': '',
      'parent': '0',
      'direction': ''
    },
    {
      'id': 5,
      'text': 'ch5',
      'url': '',
      'parent': '0',
      'direction': ''
    },
    {
      'id': 6,
      'text': 'ch6',
      'url': '',
      'parent': '4',
      'direction': ''
    },
    {
      'id': 7,
      'text': 'ch7',
      'url': '',
      'parent': '4',
      'direction': ''
    },
    {
      'id': 8,
      'text': 'ch8',
      'url': '',
      'parent': '1',
      'direction': ''
    },
    {
      'id': 9,
      'text': 'ch9',
      'url': '',
      'parent': '1',
      'direction': ''
    },
    {
      'id': 10,
      'text': 'ch10',
      'url': '',
      'parent': '9',
      'direction': ''
    },
    {
      'id': 11,
      'text': 'ch11',
      'url': '',
      'parent': '9',
      'direction': ''
    },
    {
      'id': 12,
      'text': 'ch12',
      'url': '',
      'parent': '8',
      'direction': ''
    },
    {
      'id': 13,
      'text': 'ch13',
      'url': '',
      'parent': '5',
      'direction': ''},]">

    </MindMap>

  </div>

</template>

<script>
import MindMap from './components/MindMap.vue';

export default {
  name: 'App',
  components: {
    MindMap
  }
}
</script>

<style>
body {
  margin: 0px;
  padding: 0px;
}

#app {
  margin: 0px;
  padding: 0px;
}

.rapmap {
  width: 900px;
  height: 900px;
  overflow: hidden;
  border: 1px solid #000;
}
</style>
