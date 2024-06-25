<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Anime List</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-loading :is-open="loading" message="Loading..." :duration="4000" spinner="circles"></ion-loading>
    <ion-content v-if="!loading">
      <ion-grid>
        <ion-row>
          <anime-card v-for="anime in animes" :key="anime.mal_id" :anime="anime" />
        </ion-row>
      </ion-grid>
      <div class="pagination">
        <ion-button @click="prevPage" :disabled="page === 1">
          Previous
        </ion-button>
        <span>Page {{ page }}</span>
        <ion-button @click="nextPage">Next</ion-button>
      </div>
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent, IonGrid, IonRow, IonButton, IonLoading } from '@ionic/vue';
import AnimeService from '@/services/AnimeService';
import AnimeCard from '@/components/AnimeCard.vue';

export default {
  name: 'HomePage',
  components: {
    IonPage, IonHeader, IonToolbar, IonTitle, IonContent, IonGrid, IonRow, IonButton, AnimeCard, IonLoading
  },
  data() {
    return {
      animes: [],
      page: 1,
      limit: 12,
      loading: true
    };
  },
  async created() {
    await this.loadAnimes();
  },
  methods: {
    async loadAnimes() {
      this.loading = true;
      try {
        this.animes = await AnimeService.getAnimes(this.page, this.limit);
        this.loading = false;
      } catch (error) {
        console.error('Error fetching animes:', error);
        this.loading = false;
      } 
      
    },
    async nextPage() {
      this.page += 1;
      await this.loadAnimes();
    },
    async prevPage() {
      if (this.page > 1) {
        this.page -= 1;
        await this.loadAnimes();
      }
    }
  }
};
</script>

<style scoped>
.pagination {
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 20px 0;
}

.pagination span {
  margin: 0 10px;
}
</style>