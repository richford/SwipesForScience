<template name="tutorial">
  <div class="tutorial" ref="tutorial">
    <Bubbles v-if="backgroundAnimation == 'Bubbles'" />
    <!-- Title -->
    <div class="mb-3">
      <h1>Tutorial</h1>
      <p class="lead mt-2">Scroll down to learn how to play</p>
    </div>

    <!-- Progress Bar -->
    <!-- <div class="pbar pt-3 pb-3" id="div-pbar" v-if="currentBin.bin">
      <b-progress
        :value="progressPosition"
        :max="1"
        show-progress
        class="ml-3 mr-3"
      ></b-progress>
    </div> -->

    <!-- Introduction steps -->
    <div
      v-for="(step, index) in steps.intro"
      :key="'intro' + index"
      class="fullpage"
    >
      <div class="pt-3" :id="'intro' + index">
        <p v-html="step.text"></p>
        <!-- <span class="invisible">{{ step.text }}</span> -->
      </div>
      <img :src="step.image" class="mt-3 pt-3 img" />
      <hr />
    </div>

    <!-- Example Steps -->
    <div
      v-for="(step, index) in steps.examples"
      :key="'example' + index"
      class="fullpage"
    >
      <div class="pt-3 text-center w-100" :id="'example' + index">
        <ReviewModal
          :widgetPointer="step.pointer"
          :userInfo="userInfo"
          :userData="userData"
          :levels="levels"
          :currentLevel="currentLevel"
          :config="config"
          :db="db"
        ></ReviewModal>

        <p v-html="step.text"></p>
        <!-- <span class="invisible">{{ step.text }}</span> -->
        <div v-if="step.pointer" class="mt-3">
          <WidgetSelector
            :widgetType="widgetType"
            :widgetPointer="step.pointer"
            :widgetProperties="widgetProperties"
            :widgetSummary="widgetSummary"
            :playMode="'tutorial'"
            :userSettings="userSettings"
            :tutorialStep="step.tutorialStep"
            ref="widget"
          />
        </div>
        <div v-if="step.tutorialCompleted">
          <b-button @click="tutorialComplete" class="mt-3">Play now</b-button>
        </div>
        <hr />
      </div>
    </div>

    <div v-if="bins.length - 1 != currentBin.bin" v-scroll-to="nextStep">
      <Arrow />
    </div>

    <p></p>
  </div>
</template>

<style>
.img {
  max-height: 80vh;
  width: 100%;
  max-width: 500px;
}

.tutorial {
  /* height: 500vh; */
}

.fullpage {
  height: 100%;
}

.invisible {
  opacity: 0;
  white-space: pre-wrap;
}

.pbar {
  position: -webkit-sticky; /* Safari */
  position: sticky;
  background: white;
  top: 0;
  z-index: 2;
}
</style>

<script>
/**
 * TODO: fill this in.
 */
import _ from "lodash";
import Vue from "vue";
import Arrow from "./Animations/ArrowDown";
import Bubbles from "./Animations/Bubbles";
import WidgetSelector from "./WidgetSelector";
import ReviewModal from "./ReviewModal";

Vue.component("ReviewModal", ReviewModal);
const VueScrollTo = require("vue-scrollto");

// You can also pass in the default options
Vue.use(VueScrollTo, {
  container: "body",
  duration: 500,
  easing: "ease",
  offset: 0,
  force: true,
  cancelable: true,
  onStart: false,
  onDone: false,
  onCancel: false,
  x: false,
  y: true
});

export default {
  name: "tutorial",
  components: {
    Arrow,
    Bubbles,
    WidgetSelector
  },
  data() {
    return {
      /**
       * The current scroll position
       */
      progressPosition: 0,
      scrollY: 0,
      /**
       * The sample IDs summary (not implemented yet)
       */
      widgetSummary: {}, // TODO: fill this properly
      /**
       * User settings from firebase (not implemented yet)
       */
      userSettings: {}, // TODO: fill this properly
      bins: []
    };
  },
  props: {
    /**
     * the various levels, the points need to reach the levels,
     * and the badges (colored and greyed out) to display
     */
    levels: {
      type: Object,
      required: true
    },
    /**
     * The config object that is loaded from src/config.js.
     * It defines how the app is configured, including
     * any content that needs to be displayed (app title, images, etc)
     * and also the type of widget and where to update pointers to data
     */
    config: {
      type: Object,
      required: true
    },
    /**
     * the user's current level
     */
    currentLevel: {
      type: Object,
      required: true
    },
    /**
     * the authenticated user object from firebase
     */
    userInfo: {
      type: Object,
      required: true
    },
    /**
     * the computed user data object based on userInfo
     */
    userData: {
      type: Object,
      required: true
    },
    /**
     * the intialized firebase database
     */
    db: {
      type: Object,
      required: true
    }
  },
  watch: {},
  mounted() {
    this.setBins();
  },
  computed: {
    /**
     * The widget type defined in config.
     */
    widgetType() {
      return this.config.widgetType;
    },
    /**
     * The widget properties defined in config.
     */
    widgetProperties() {
      return this.config.widgetProperties;
    },
    /**
     * The steps defined in config, with text and images to display.
     */
    steps() {
      return this.config.tutorial.steps;
    },
    /**
     * The type of background animation to show.
     */
    backgroundAnimation() {
      return this.config.tutorial.customBackgroundAnimation;
    },
    currentBin() {
      const that = this;
      const cBin = _.filter(
        this.bins,
        b => that.scrollY <= b.to && that.scrollY >= b.from
      );
      if (cBin.length) {
        return cBin[0];
      }

      return { bin: 0 };
    },
    /**
     * The next step that should be displayed.
     */
    nextStep() {
      if (this.currentBin.bin < this.steps.intro.length - 1) {
        return `#intro${this.currentBin.bin + 1}`;
      }
      return `#example${this.currentBin.bin - this.steps.intro.length + 1}`;
    }
  },
  methods: {
    setBins() {
      const Nsteps = this.steps.intro.length + this.steps.examples.length;
      this.bins = [];
      if (this.$el) {
        let startIdx = 1;
        if (this.$el.children[startIdx].id == "div-pbar") {
          startIdx += 1;
        }

        for (let i = startIdx; i <= Nsteps; i += 1) {
          const child = this.$el.children[i];
          let from = child.offsetTop;
          if (i == startIdx) {
            from = 0;
          }
          let to = child.offsetTop + child.offsetHeight;
          this.bins.push({ bin: i - startIdx, from: from, to: to });
        }
      } else {
        const binSize = 1 / Nsteps;
        for (let i = 0; i < Nsteps; i += 1) {
          this.bins.push({
            bin: i,
            from: i * binSize,
            to: (i + 1) * binSize
          });
        }
      }
    },
    /**
     * When this method is run, we tell the parent component that the
     * user has completed the tutorial.
     */
    tutorialComplete() {
      this.$emit("taken_tutorial", true);
    },
    /**
     * Keep track of the scroll position and save it to the progressPosition variable.
     */
    handleScroll() {
      const Nsteps = this.steps.intro.length + this.steps.examples.length;
      this.progressPosition = this.currentBin.bin / Nsteps;
      this.setBins();
      this.scrollY = Math.ceil(window.scrollY);
    }
  },
  /**
   * Add a scroll listener when the component is created.
   */
  created() {
    window.addEventListener("scroll", this.handleScroll);
  },
  /**
   * Remove the scroll listener when the component is destroyed.
   */
  destroyed() {
    window.removeEventListener("scroll", this.handleScroll);
  }
};
</script>
