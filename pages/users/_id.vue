<template>
  <v-container
    v-if="!!user && !loadingUser"
  >
    <v-card>
      <v-list-item>
        <v-list-item-avatar
          color="secondary"
          :size="100"
        >
          <img
            v-if="avatar"
            :src="avatar"
            alt="user avatar"
          >
          <span
            v-else
            class="white--text display-4"
          >
            {{ firstInitial }}
          </span>
        </v-list-item-avatar>
        <v-list-item-content>
          <v-list-item-title
            class="display-1"
          >
            {{ username }}
          </v-list-item-title>
          <v-list-item-subtitle>
            <v-chip
              v-for="role in roles"
              :key="role.id"
              class="mx-1"
              color="primary"
            >
              {{ role.name }}
            </v-chip>
            <div
              v-if="!roles"
            >
              No roles
            </div>
          </v-list-item-subtitle>
        </v-list-item-content>
      </v-list-item>

      <v-card-text>
        <div
          v-if="bio"
          class="ml-10"
          v-html="bio"
        />

        <div
          v-else
        >
          <i>No bio...</i>
        </div>

        <v-simple-table
          class="mt-6"
        >
          <tbody>
            <tr>
              <td>Registered</td>
              <td>{{ registeredAt }}</td>
            </tr>
            <tr>
              <td>Records Uploaded</td>
              <td>{{ uploadedRecordsTotal }}</td>
            </tr>
            <tr>
              <td>Records Favorited</td>
              <td>{{ uploadedRecordsTotal }}</td>
            </tr>
            <tr>
              <td>Folders</td>
              <td>{{ foldersTotal }}</td>
            </tr>
            <tr>
              <td>Friends</td>
              <td>{{ foldersTotal }}</td>
            </tr>
            <tr>
              <td>Threads</td>
              <td>{{ foldersTotal }}</td>
            </tr>
            <tr>
              <td>Posts</td>
              <td>{{ foldersTotal }}</td>
            </tr>
          </tbody>
        </v-simple-table>
      </v-card-text>

      <v-card-actions>
        <v-btn
          color="error"
        >
          <v-icon left>
            fa-flag
          </v-icon>
          Report
        </v-btn>

        <v-spacer />

        <v-btn
          color="primary"
        >
          <v-icon left>
            fa-envelope
          </v-icon>
          Send Message
        </v-btn>

        <v-btn
          color="accent"
        >
          <v-icon left>
            fa-heart
          </v-icon>
          Add Friend
        </v-btn>
      </v-card-actions>
    </v-card>

    <v-card
      class="mt-8"
    >
      <card-header
        title="Uploads"
        :count="uploadedRecordsTotal"
      />

      <v-card-text>
        <thumbnail-slider
          :records="uploadedRecords"
          :total="uploadedRecordsTotal"
        />
      </v-card-text>
    </v-card>

    <v-card
      class="mt-8"
    >
      <card-header
        title="Favorites"
        :count="uploadedRecordsTotal"
      />

      <v-card-text>
        <thumbnail-slider
          :records="uploadedRecords"
          :total="uploadedRecordsTotal"
        />
      </v-card-text>
    </v-card>
  </v-container>

  <v-container
    v-else-if="!loadingUser"
  >
    <v-layout
      justify-center
      align-center
    >
      <v-flex
        xs12
        sm8
        md6
      >
        <v-card>
          <card-header>
            <template v-slot:title>
              <v-icon
                color="secondary"
              >
                fa-question-circle
              </v-icon>

              <span
                class="ml-3"
              >
                User not found
              </span>
            </template>
          </card-header>

          <v-card-text
            class="subtitle-1"
          >
            The user you requested was not found. This could be because they never existed, they've been deactivated,
            they have their profile set to private, or it is an account reserved for system functions.
          </v-card-text>
        </v-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
import gql from 'graphql-tag'
import { mapMutations, mapGetters, mapActions } from 'vuex'
import CardHeader from '~/components/CardHeader'
import ThumbnailSlider from '~/components/Record/ThumbnailSlider.vue'

export default {
  components: {
    CardHeader,
    ThumbnailSlider,
  },

  data: () => ({
    user: null,
    loadingUser: true,
  }),

  computed: {
    ...mapGetters({

    }),

    userId () {
      return this.$route.params.id
    },

    bio () {
      if (!this.user) { return null }

      return this.user.bio
    },

    uploadedRecords () {
      if (!this.user) { return null }

      return this.user.records.data
    },

    uploadedRecordsTotal () {
      if (!this.user) { return 0 }

      return this.user.records.paginatorInfo.total
    },

    roles () {
      if (!this.user) { return null }

      return this.user.roles
    },

    username () {
      if (!this.user) { return null }

      return this.user.username
    },

    avatar () {
      if (!this.user) { return null }

      return this.user.avatar_url
    },

    registeredAt () {
      if (!this.user) { return null }

      return this.user.created_at
    },

    folders () {
      if (!this.user) { return null }

      return this.user.folders.data
    },

    foldersTotal () {
      if (!this.user) { return null }

      return this.user.folders.paginatorInfo.total
    },

    firstInitial () {
      if (!this.user) { return null }

      return this.user.username[0]
    },
  },

  created () {
    this.getUser()
  },

  methods: {
    ...mapMutations({

    }),

    ...mapActions({

    }),

    getUser () {
      this.loadingUser = true

      const query = gql`query($id: ID!){
        user(id: $id) {
          id
          username
          system_account
          avatar_url
          created_at
          email
          bio
          roles{
            id
            name
          }
          records(page: 1, first: 10){
            data{
              id
              thumbnail
            }
            paginatorInfo{
              total
            }
          }
          folders(page: 1, first: 10){
            data{
              id
              name
              cover_image
            }
            paginatorInfo{
              total
            }
          }
          favorites_folder{
            id
            name
            cover_image
          }
        }
      }`

      const variables = {
        id: this.userId
      }

      this.$apollo.query({
        query,
        variables
      }).then(({ data }) => {
        this.user = data.user
      }).finally(() => {
        this.loadingUser = false
      })
    },
  },

}
</script>

<style scoped>
.input-row{
  min-height:68px;
}
</style>
