<template>
  <div>
    <b-link :to="url(territory)">
      {{territory.name}} ({{territory.city}})
    </b-link>
    <span v-if="territory.status">
      on {{territory.status && checkoutDate(territory.status.date)}}
    </span>
    <div v-if="territory.lastVisited">
      {{formattedLastVisited}}
    </div>
    <div class="last-activity font-weight-normal" v-if="territory.status">
      Last activity: {{this.lastTimestamp}}
    </div>
  </div>
</template>
<script>
import { mapActions, mapGetters } from 'vuex';
import format from 'date-fns/format';
import formatDistance from 'date-fns/formatDistance';

export default {
  name: 'MyTerritory',
  props: ['territory'],
  methods: {
    ...mapActions({
      setTerritory: 'territory/setTerritory',
    }),
    checkoutDate(date) {
      return format(new Date(date), 'MM/dd/yyyy');
    },
    url(terr) {
      return `/territories/${terr.group_code}/${terr.id}`;
    },
  },
  computed: {
    ...mapGetters({
      lastActivity: 'territory/lastActivity',
    }),
    lastTimestamp() {
      if (this.lastActivity) {
        const timestamp = Number(this.lastActivity.timestamp);
        if (!Number.isNaN(timestamp)) {
          return format(new Date(timestamp), 'E M/d p');
        }
      }
      return null;
    },
    formattedLastVisited() {
      return formatDistance(new Date(this.territory.lastVisited), new Date(), { addSuffix: true });
    },
  },
  mounted() {
    this.setTerritory(this.territory);
  },
};
</script>
<style scoped>
  .last-activity {
    font-size: 16px;
  }
</style>
