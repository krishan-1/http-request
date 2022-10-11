<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperiences"
          >Load Submitted Experiences</base-button
        >
      </div>
      <div v-if="isLoading" class="progress-bar">
        <span class="bar">
          <span class="progress"></span>
        </span>
      </div>
      <p v-else-if="!isLoading && error">{{ error }}</p>
      <p v-else-if="!isLoading && (!results || results.length === 0)">
        No stored experience found.start adding some survey result
      </p>
      <ul v-else>
        <survey-result
          v-for="result in results"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        ></survey-result>
      </ul>
    </base-card>
  </section>
</template>

<script>
import SurveyResult from "./SurveyResult.vue";

export default {
  components: {
    SurveyResult,
  },
  data() {
    return {
      results: [],
      isLoading: false,
      error: null,
    };
  },

  methods: {
    loadExperiences() {
      this.isLoading = true;
      this.error = null;
      fetch(
        "https://vue-http-req-8017f-default-rtdb.firebaseio.com//survey.json"
      )
        .then((response) => {
          if (response.ok) {
            return response.json();
          }
        })
        .then(
          (data) => {
            this.isLoading = false;
            // console.log(data)
            // this.results = data; this is not valid here
            //so============
            const result = []; //temp variable to store data
            for (let id in data) {
              result.push({
                id: id,
                name: data[id].name,
                rating: data[id].rating,
              });
            }
            this.results = result;
          }
          // method: 'GET', // ================> you can remove it bcz it's default
        )
        .catch((error) => {
          //it will only work if any error occured try removing .json from url
          console.log(error);
          this.isLoading = false;
          this.error = "failed to fetch data - please try again";
        });
    },
  },

  mounted() {
    this.loadExperiences();
  },
};
</script>

<style lang="scss" scoped>
// Color Variables ======================================
$green: #75b800;
$gray-light: #eef1f3;

// Bar Variables ========================================
$bar-size: 5px;
$bar-radius: 60px;
$bar-bg: rgba(0, 0, 0, 0.075);

@keyframes loader {
  0% {
    width: 0;
  }

  20% {
    width: 10%;
  }

  25% {
    width: 24%;
  }

  43% {
    width: 41%;
  }

  56% {
    width: 50%;
  }

  66% {
    width: 52%;
  }

  71% {
    width: 60%;
  }

  75% {
    width: 76%;
  }

  94% {
    width: 86%;
  }

  100% {
    width: 100%;
  }
}

// Bar ============================================
.progress-bar {
  border-radius: $bar-radius;
  overflow: hidden;
  margin: 1rem auto;
  width: 100%;

  span {
    display: block;
  }
}

.bar {
  background: $bar-bg;
}

.progress {
  animation: loader 8s ease forwards;
  // Change the animation fill mode 'infinite' to 'forwards' to stop the animation from repeating.
  background: $green;
  color: #fff;
  padding: $bar-size;
  width: 0;
}

ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>
