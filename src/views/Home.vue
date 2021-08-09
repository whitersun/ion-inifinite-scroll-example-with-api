<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>Blank</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Blank</ion-title>
        </ion-toolbar>
      </ion-header>

      <ion-list v-if="items.length">
        <ion-card v-for="(item, i) in items" :key="i">
          {{ i }}
          <img :src="item.image" />
          <ion-item>
            <ion-card-header>
              <ion-card-subtitle>{{ item.category }}</ion-card-subtitle>
              <ion-card-title>{{ item.title }}</ion-card-title>
            </ion-card-header>

            <ion-card-content>
              {{ item.description }}
            </ion-card-content>
          </ion-item>
        </ion-card>
      </ion-list>

      <ion-skeleton-text
        v-else
        animated
        style="width: 100%; height: 12rem"
      ></ion-skeleton-text>

      <ion-infinite-scroll
        @ionInfinite="loadData($event)"
        threshold="100px"
        id="infinite-scroll"
        :disabled="isDisabled"
      >
        <ion-infinite-scroll-content
          loading-spinner="bubbles"
          loading-text="Loading more data..."
        >
        </ion-infinite-scroll-content>
      </ion-infinite-scroll>
    </ion-content>
  </ion-page>
</template>

<script>
import {
  IonContent,
  IonHeader,
  IonPage,
  IonTitle,
  IonItem,
  IonList,
  IonCard,
  IonCardContent,
  IonCardTitle,
  IonCardSubtitle,
  IonToolbar,
  IonInfiniteScroll,
  IonInfiniteScrollContent,
  IonSkeletonText,
  IonCardHeader,
} from "@ionic/vue";
import { onBeforeMount, ref } from "vue";
import axios from "axios";

export default {
  name: "Home",
  components: {
    IonContent,
    IonHeader,
    IonPage,
    IonTitle,
    IonItem,
    IonList,
    IonCard,
    IonCardContent,
    IonCardTitle,
    IonCardSubtitle,
    IonCardHeader,
    IonToolbar,

    IonInfiniteScroll,
    IonInfiniteScrollContent,
    IonSkeletonText,
  },

  setup() {
    const posts = ref(null);

    const options = {
      method: "GET",
      url: "https://fakestoreapi.com/products",
    };

    const exportApi = async () => {
      const response = await axios.request(options);

      posts.value = response.data;
      console.log(posts.value);
    };

    onBeforeMount(async () => {
      await exportApi();

      pushData();
    });

    const isDisabled = ref(false);
    const toggleInfiniteScroll = () => {
      isDisabled.value = !isDisabled.value;
    };
    const items = ref([]);
    const pushData = () => {
      const max = items.value.length + 5;
      const min = max - 5;
      for (let i = min; i < max; i++) {
        items.value.push(posts.value[i]);
      }
    };
    const loadData = (ev) => {
      setTimeout(() => {
        pushData();
        console.log("Loaded data");
        ev.target.complete();

        // App logic to determine if all data is loaded
        // and disable the infinite scroll
        if (items.value.length == posts.value.length) {
          ev.target.disabled = true;
        }
      }, 500);
    };

    return {
      isDisabled,
      toggleInfiniteScroll,
      loadData,
      items,
    };
  },
};
</script>

<style scoped>
#container {
  text-align: center;

  position: absolute;
  left: 0;
  right: 0;
  top: 50%;
  transform: translateY(-50%);
}

#container strong {
  font-size: 20px;
  line-height: 26px;
}

#container p {
  font-size: 16px;
  line-height: 22px;

  color: #8c8c8c;

  margin: 0;
}

#container a {
  text-decoration: none;
}
</style>
