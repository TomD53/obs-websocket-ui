<template>
  <div v-for="scene in scene_data.scenes" :key="scene.name">
    <Scene
      :name="scene.name"
      :current_scene="current_scene"
      :ui_selected_scene="ui_selected_scene"
      :detail_scene="detail_scene"
      v-on:changeScene="changeScene"
      v-on:dropDownClick="dropDownClick"
      :obs="obs"
      v-on:OpenSourceSettings="openSourceSettings"
    />
  </div>
</template>

<script>
import Scene from "@/components/dashboard/scenes/Scene.vue";

export default {
  name: "Scenes",
  components: {
    Scene,
  },
  props: {
    obs: Object,
  },
  emits: ["OpenSourceSettings"],
  data() {
    return {
      scene_data: [],
      current_scene: "",
      ui_selected_scene: "",
      detail_scene: ""
    }
  },
  created() {
    this.obs.on("SwitchScenes", (data) => {
      console.log("Scene change detected");
      console.log(data);
      this.queryScenes();
    });

    this.queryScenes();
  },
  methods: {
    openSourceSettings(source_name, scene_name) {
      console.log("Scenes component got open settings event", source_name)
      this.$emit('OpenSourceSettings', source_name, scene_name)
    },
    async queryScenes() {
      const scene_data = await this.obs.send("GetSceneList");
      console.log(scene_data);
      this.scene_data = scene_data;
      this.current_scene = scene_data.currentScene;
      this.ui_selected_scene = scene_data.currentScene;

    },
    changeScene(name) {
      console.log("changing scene to " + name)
      this.ui_selected_scene = name
      this.obs.send("SetCurrentScene", {
        "scene-name": name
      })
    },
    dropDownClick(name) {
      if ( this.detail_scene !== name ){
        this.detail_scene = name
      } else {
        // The scene is already selected with the dropdown
        this.detail_scene = ""  // Set to none
      }
      
    }
  }
};
</script>
