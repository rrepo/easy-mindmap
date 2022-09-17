<template>
  <div>
    <!-- <div>{{ countRef }}</div>
    <div>{{ obj.count }}</div> -->
    <!-- <div id="start">start</div>
    <div id="end">end</div> -->
  </div>
</template>

<script>
import { ref, reactive } from "vue";
import { onMounted } from "vue"

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  setup() {
    const countRef = ref(1);
    const obj = reactive({
      count: 2,
    });
    const test = () => {
      console.log("test")
      obj.count = 5
      countRef.value = 4
    };
    onMounted(() => {
      // const LeaderLine = window.LeaderLine;
      // new LeaderLine(
      //   document.getElementById('start'),
      //   document.getElementById('end')
      // );


      console.log("mounted")
      console.log(countRef)
      console.log(obj)
      test()
    });
    return {
      countRef,
      obj,
    };

  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
