<template>
  <div id="about">
    <h1>About fibr</h1>

    <b-container>
      <p class="mt-3">
        Fibr is an app for quality control of diffusion MRI images from the
        <a href="https://healthybrainnetwork.org/">Healthy Brain Network</a>.
      </p>
      <h1 class="mt-3">How can you help?</h1>
      <p class="mt-3">
        You will see static images or moving gifs of brain MRIs. Your objective
        is to classify these images as "Pass" or "Fail" by swiping right or
        left. See the <b-link to="tutorial">tutorial</b-link> for further
        instructions on how to rate images.
      </p>
      <h1 class="mt-3">Why are we doing this?</h1>
      <p class="mt-3">
        Innovations in medical imaging are helping researchers understand the
        developing human brain. There is growing hope that these technologies
        will lead to clinical tools that can help diagnose and treat diseases,
        mental illness and learning disorders. But to achieve this dream, we
        need massive MRI datasets to capture the broad spectrum of brain
        development. So the Child Mind Institute has launched the Healthy Brain
        Network, a landmark mental health study that is collecting MRI images
        and other assessment data from 10,000 New York City area children. We
        have developed algorithms that analyze these images to measure the
        integrity of connections between different parts of the brain. But they
        aren't perfect and sometimes do a bad job, especially if the child moved
        a lot in the scanner (kids have a hard time staying still sometimes). We
        need humans to visually inspect the quality of the output. Computers can
        struggle with this sort of thing but humans are great at it! That's why
        we need you. We need you to help researchers learn about pediatric
        mental health and learning disorders.
      </p>
      <h1 class="mt-3">
        You get points for each sample you annotate.
      </h1>
      <p class="mt-3">
        You can unlock the following animals based on the number of points you
        have. Go unlock them all so you can brag about being a unicorn
        &#128526;!
      </p>

      <b-container fluid class="p-4">
        <b-row>
          <b-col v-for="lev in levels" :key="lev.level">
            <div>
              <b-img
                fluid
                class="pokemon"
                :src="lev.img_grey"
                alt="Thumbnail"
              />
              <br />
              Level {{ lev.level }}
              <br />
              <span>{{ lev.min }} points</span>
            </div>
          </b-col>
        </b-row>
      </b-container>

      <h1>Acknowledgements</h1>

      <p class="mt-3">
        Fibr is inspired by the previous success of the
        <a href="https://braindr.us/">braindr</a> app for structural MRI images
        and was built using a modified version of the
        <a href="https://swipesforscience.org/">Swipes for Science</a> template.
      </p>
      <p class="mt-3">
        Fibr development is supported through a grant from the
        <a href="https://www.moore.org/">Gordon and Betty Moore Foundation</a>
        and from the
        <a href="https://sloan.org/">Alfred P. Sloan Foundation</a> to the
        <a href="http://escience.washington.edu/"
          >University of Washington eScience Institute</a
        >, as well as
        <a
          href="https://projectreporter.nih.gov/project_info_details.cfm?aid=9886761&icde=46874320&ddparam=&ddvalue=&ddsub=&cr=2&csb=default&cs=ASC&pball="
          >NIMH BRAIN Initiative grant 1RF1MH121868-01</a
        >
        to Ariel Rokem (University of Washington).
      </p>
      <p class="mt-3">
        To learn more about this research, please visit
        <a href="https://autofq.org">https://autofq.org</a>
      </p></b-container
    >
  </div>
</template>

<style>
.pokemon {
  min-width: 25px;
  min-height: 25px;
  width: 100px;
  height: 100px;
}

#about {
  min-height: 100vh;
}
</style>

<script>
/**
 * This is the about page for the route /about.
 * Right now this page only shows the various badges you can win if you play
 * the game. Eventually, we should fill the content with text that the researcher
 * provides, in markdown.
 */
export default {
  name: "about",
  data() {
    return {};
  },
  props: {
    /**
     * the various levels, the points need to reach the levels,
     * and the badges (colored and greyed out) to display
     */
    levels: {
      type: Object,
      required: false
    }
  }
};
</script>
