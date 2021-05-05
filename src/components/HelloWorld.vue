<template>
  <div class="character-list">
    <label for="search">Search a character: </label>
    <input type="search" name="search" id="" @input="getSearchResult" />
    <ul v-if="results">
      <li
        v-for="(item, index) in results"
        :key="index"
        @click="getDetails(item.url)"
      >
        {{ item.name }}
      </li>
    </ul>
    <section id="pagination">
      <button id="previous" v-show="previous" @click="refreshList">
        previous
      </button>
      <span>{{ currentPage }} out of {{ totalPages }}</span>
      <button id="next" v-show="next" @click="refreshList">next</button>
    </section>

    <CharacterDetails
      v-if="detailsStatus"
      :personObject="personObject"
      @clicked="closeDetails"
    />
  </div>
</template>

<script>
import { getCharacters, getSingleCharacter, getSearch } from "@/api/api.js";
import CharacterDetails from "@/components/CharacterDetails.vue";
export default {
  components: {
    CharacterDetails,
  },
  data() {
    return {
      results: [],
      next: "",
      previous: "",
      personObject: {},
      detailsStatus: false,
      currentPage: 1,
      totalPages: 0,
    };
  },
  created: async function () {
    const data = await getCharacters();
    this.results = data.results;
    this.next = data.next;
    this.previous = data.previous;
    this.totalPages = Math.ceil(data.count / 10);
  },
  methods: {
    closeDetails() {
      this.detailsStatus = false;
    },
    async refreshList(e) {
      try {
        if (e.target.id == "previous") {
          const data = await getCharacters(this.previous);
          this.results = data.results;
          this.next = data.next;
          this.previous = data.previous;
          this.currentPage--;
        } else {
          const data = await getCharacters(this.next);
          this.results = data.results;
          this.next = data.next;
          this.previous = data.previous;
          this.currentPage++;
        }
      } catch (error) {
        console.log(error.message);
      }
    },
    async getDetails(id) {
      const data = await getSingleCharacter(id);
      this.personObject = data;
      this.detailsStatus = true;
    },
    async getSearchResult(e) {
      const input = e.target.value;
      if (input) {
        const data = await getSearch(e.target.value);
        this.results = data.results;
      }
    },
  },
};
</script>

<style scoped lang="css">
.character-list {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
ul {
  list-style: none;
}
li {
  margin-bottom: 1rem;
}

#pagination {
  width: 200px;
  display: flex;
  justify-content: center space-between;
}
</style>
