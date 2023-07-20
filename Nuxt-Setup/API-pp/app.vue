<template>
  <div>
    <p v-if="loading">Chargement...</p>
    <div v-else>
      <ul v-if="jobs.length > 0">
        <div class="container">
          <div v-for="job in jobs" :key="job.id" class="jobs">
            <div
              class="title"
              v-text="job.title"
              :style="{ lineHeight: job.title.length < 35 ? '45px' : null }"
            ></div>
            <div class="content">
              <div class="salarayRange">
                <div v-if="!job.salaryMin">
                  Pas de salaire fournit pour cette offre d'emploi
                </div>
                <div
                  v-else-if="
                    job.salaryMax === job.salaryMin && job.salaryMin > 0
                  "
                >
                  Salaire : {{ job.salaryMax }} € annuel
                </div>
                <div v-else>
                  Salaire à partir de {{ job.salaryMin }} € annuel
                </div>
              </div>
              <div v-if="job.applicationEmail" class="email">
                Pour postuler, envoyez votre CV ainsi que votre lettre de
                motivation à {{ job.applicationEmail }}
                <div
                  class="description"
                  v-html="getFirstWords(job.answer1, 50)"
                ></div>
              </div>
              <div v-else>
                <div
                  class="description"
                  v-html="getFirstWords(job.answer1, 60)"
                ></div>
              </div>
              <button v-if="job.answer1.length > 400" class="button">
                Voir plus
              </button>
            </div>
          </div>
        </div>
      </ul>
      <p v-else>No jobs found.</p>
    </div>
  </div>
</template>
<style>
*,
body {
  padding: 0;
  font-family: Gravur Condensed, ui-sans-serif, system-ui, -apple-system,
    BlinkMacSystemFont, Segoe UI, Roboto, Helvetica Neue, Arial, Noto Sans,
    sans-serif, Apple Color Emoji, Segoe UI Emoji, Segoe UI Symbol,
    Noto Color Emoji;
}

body {
  background-color: #dbddde;
}

button {
  cursor: pointer;
}

button:hover {
  opacity: 0.7;
}

.container {
  margin: 0 auto;
  justify-content: center;
  flex-wrap: wrap;
  display: flex;
}

.jobs {
  background-color: #174c62;
  margin: 20px;
  width: 280px;

  border-radius: 20px;
}

.jobs:hover {
  box-shadow: 0px 0px 20px 0px rgba(0, 0, 0, 0.8);
}

.title {
  border-radius: 18px 18px 0px 0px;
  font-weight: 600;
  background-color: #58d9db;
  padding-block: 10px;
  padding-inline: 5px;
  height: 45px;
  text-align: center;
}

.content {
  height: auto;
  margin: 10px;
}

.email {
  color: #a6a6a6;
}

.description {
  color: #fff;
  margin-top: 10px;
}

.button {
  margin: 20px;
  line-height: 20px;
  height: 20px;
  margin-inline: auto;
  display: flex;
  justify-content: center;
  background-color: #58d9db;
  border: none;
  width: 35%;
  border-radius: 10px;
}
</style>
<script>
import axios from "axios";

export default {
  data() {
    return {
      loading: true,
      jobs: [],
      longestDescription: 0,
    };
  },
  head: {
    link: [{ rel: "icon", type: "image/x-icon", href: "/favicon.ico" }],
  },
  mounted() {
    axios
      .get("https://app.staging.profilpublic.fr/api/jobs")
      .then((response) => {
        this.jobs = response.data.data;
        this.loading = false;
        // this.longestDescription = [...this.jobs.map((obj) => obj.answer1)].sort(
        //   (a, b) => b.length - a.length
        // )[0];
      })
      .catch((error) => {
        console.error("error : ", error);
        this.loading = false;
      });
  },
  methods: {
    getFirstWords(htmlContent, numberofWords) {
      const textOnly = htmlContent.replace(/<[^>]+>/g, "");
      const wordsArray = textOnly.split(" ");
      const firstWords = wordsArray.slice(0, numberofWords).join(" ");
      return firstWords;
    },
  },
};
</script>
