<script setup>
import { inject } from "vue"
import avatarNoneUrl from '@/assets/avatar-none.png'
import { useUserStore } from "../../stores/user.js"

const serverBaseUrl = inject("serverBaseUrl")
const userStore = useUserStore()

const props = defineProps({
  users: {
    type: Array,
    default: () => [],
  },
  showId: {
    type: Boolean,
    default: true,
  },
  showEmail: {
    type: Boolean,
    default: true,
  },
  showType: {
    type: Boolean,
    default: true,
  },
  showPhoto: {
    type: Boolean,
    default: true,
  },
  showEditButton: {
    type: Boolean,
    default: true,
  },
  showDeleteButton: {
    type: Boolean,
    default: true,
  }
})

const emit = defineEmits(["edit", "delete"])

const photoFullUrl = (user) => {
  return user.photo_url
    ? serverBaseUrl + "/storage/fotos/" + user.photo_url
    : avatarNoneUrl
}

const editClick = (user) => {
  emit("edit", user)
}

const deleteClick = (user) => {
  emit("delete", user)
}

const canViewUserDetail = (userId) => {
  if (!userStore.user) {
    return false
  }
  return userStore.user.type == 'EM' || userStore.user.id == userId
}

const canDeleteUser = (userId) => {
  if (!userStore.user) {
    return false
  }
  return userStore.user.type == 'EM'
}

</script>

<template>
  <table class="table">
    <thead>
      <tr>
        <th v-if="showId" class="align-middle" style="color: black">#</th>
        <th v-if="showPhoto" class="align-middle" style="color: black">Photo</th>
        <th class="align-middle" style="color: black">Name</th>
        <th v-if="showEmail" class="align-middle" style="color: black">Email</th>
        <th v-if="showType" class="align-middle" style="color: black">Type</th>
        <th></th>
        <th></th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="user in users" :key="user.id">
        <td v-if="showId" class="align-middle">{{ user.id }}</td>
        <td v-if="showPhoto" class="align-middle">
          <img :src="photoFullUrl(user)" class="rounded-circle img_photo" />
        </td>
        <td class="align-middle">{{ user.name }}</td>
        <td v-if="showEmail" class="align-middle">{{ user.email }}</td>
        <td v-if="showType" class="align-middle">{{ user.type }}</td>
        <td class="text-end align-middle" v-if="showEditButton">
          <div class="d-flex justify-content-end" v-if="canViewUserDetail(user.id)">
            <button class="btn btn-xs btn-warning" @click="editClick(user)" v-if="showEditButton">
              <i class="bi bi-xs bi-pencil"></i>
              Edit
            </button>
          </div>
        </td>
        <td class="text-end align-middle" v-if="showDeleteButton">
          <div class="d-flex justify-content-end" v-if="canDeleteUser(user.id)">
            <button class="btn btn-xs btn-danger" @click="deleteClick(user)" v-if="showDeleteButton">
              <i class="bi bi-xs bi-trash"></i>
              Delete
            </button>
          </div>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<style scoped>
button {
  margin-left: 3px;
  margin-right: 3px;
}

.img_photo {
  width: 3.2rem;
  height: 3.2rem;
}
</style>
