<script setup>
// const { title } = useCourse()

const supabase = useSupabaseClient()

const doThing = () => {
  console.log('thing')
}

const login = async () => {
  const { error } = await supabase.auth.signInWithOAuth({
    provider: 'github',
  })

  if (error) {
    console.error('Error logging in:', error.message)
  }
}
</script>


<template>
  <div class="prose w-full max-w-2xl h-9">
<!--    <h1>Log in to {{ title }}</h1>-->
    <button @click="doThing">
      Bringo
    </button>
    <button
      class="bg-blue-500 text-white font-bold py-2 px-4 rounded"
      @click="login"
    >
      Log in with Github
    </button>
  </div>
</template>
