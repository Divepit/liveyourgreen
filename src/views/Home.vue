<template>
  <div style="height: 100%;width: 100%">
    <HelloWorld/>
    <v-card style="position: absolute; bottom: 100px; left: 100px;">
    </v-card>
  </div>
</template>

<script>
import HelloWorld from "@/components/MainMap";
export default {
  name: 'Home',
  components: {
    HelloWorld
  },
};
</script>
