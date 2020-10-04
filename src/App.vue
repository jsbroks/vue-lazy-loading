<template>
  <div class="container mx-auto mt-4 text-center">
    <h1 class="mb-2 font-bold">Login to Vue the Secret Image</h1>

    <button
      class="bg-green-500 hover:bg-green-700 text-white font-bold py-2 px-4 m-4 rounded"
      @click="toggleLogin"
    >
      {{ isLoggedIn ? 'Sign Out' : 'Log in' }}
    </button>

    <div class="bg-white shadow-md rounded">
      <Suspense v-if="isLoggedIn">
        <template #default>
          <SecretImage />
        </template>
        <template #fallback>
          <Loading />
        </template>
      </Suspense>
    </div>
  </div>
</template>

<script>
import { ref, defineAsyncComponent } from 'vue'
import Loading from './components/Loading'

const SecretImage = defineAsyncComponent(async () => {
  // Added a short delay for show loading componet more clearly.
  await new Promise(r => setTimeout(r, 1000))
  return import('./components/SecretImage.vue')
})

export default {
  name: 'App',
  components: { SecretImage, Loading },
  setup() {
    const isLoggedIn = ref(false)
    const toggleLogin = () => (isLoggedIn.value = !isLoggedIn.value)
    return { isLoggedIn, toggleLogin }
  }
}
</script>
