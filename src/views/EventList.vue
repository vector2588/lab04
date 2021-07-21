<template>
  <h1>Events For Good</h1>
  <div class="events">
    <EventCard v-for="event in events" :key="event.id" :event="event" />
    <div class="pagination">
      <router-link
        id="page-prev"
        :to="{ name: 'EventList', query: { perPage,page: page - 1 } }"
        rel="prev"
        v-if="page != 1">
        Prev Page
      </router-link>
      <router-link
        id="page-next"
        :to="{ name: 'EventList', query: { perPage,page: page + 1 } }"
        rel="next"
        v-if="hasNextPage">
        Next Page
      </router-link>
    </div>
    <div class="showcard">
      <router-link id="del-card" :to="{ name: 'EventList', query: { perPage: perPage - 1,page }}" >
        Show less card
      </router-link>
      <router-link id="add-card" :to="{ name: 'EventList', query: { perPage: perPage + 1,page } }" >
        Show more card
      </router-link>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import EventCard from '@/components/EventCard.vue'
import EventService from '@/services/EventService.js'
import { watchEffect } from '@vue/runtime-core'
// import axios from 'axios'
export default {
  name: 'EventList',
  props: {
    page: {
      type: Number,
      required: true
    },
    perPage: {
      type: Number,
      required: true
    }
  },
  components: {
    EventCard // register it as a child component
  },
  data() {
    return {
      events: null,
      totalEvents: 0,
      totalCard: 0,
    }
  },
  created() {
    watchEffect(() => {
      EventService.getEvents(this.perPage, this.page)
        .then((response) => {
          this.events = response.data
          this.totalEvents = response.headers['x-total-count']
        })
        .catch((error) => {
          console.log(error)
        })
    })
  },
  computed: {
    hasNextPage() {
      let totalPages = Math.ceil(this.totalEvents / this.perPage)
      return this.page < totalPages
    }
  }
}
</script>
<style scoped>
.events {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.pagination {
  display: flex;
  width: 290px;
}

.pagination a {
  flex: 1;
  text-decoration: none;
  color: #2c3e50;
}
#page-prev {
  text-align: left;
}
#page-next {
  text-align: right;
}

.showcard {
  display: flex;
  width: 290px;
}

.showcard a {
  flex: 1;
  text-decoration: none;
  color: #2c3e50;
}
#del-card {
  text-align: left;
}
#add-card {
  text-align: right;
}
</style>
