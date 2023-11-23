<template>
    <UCard v-if="!success">
        <template #header>
            Sign In to Fintrack
        </template>

        <form @submit="handleLogin">
            <UFormGroup label="Email" name="email" class="mb-4" :required="true"
                help="You will receive an email with the confirmation link">
                <UInput type="email" placeholder="Email" required v-model="email" />
            </UFormGroup>

            <UButton type="submit" variant="solid" color="black" :loading="pending" :disabled="pending">Sign In</UButton>
        </form>
    </UCard>
    <UCard v-else>
        <template #header>
            Email has been sent
        </template>
        <div class="text-center">
            <p>We have setn an email to <strong>{{ email }}</strong> with a link to sign in.</p>
            <p>
                <strong>Important:</strong> The link will expire in 5 minutes.
            </p>
        </div>
    </UCard>
</template>

<script setup>

const success = ref(false)
const pending = ref(false)
const email = ref()
const { toastError } = useAppToast()
const supabase = useSupabaseClient()
const redirectURL = useRuntimeConfig().public.baseUrl

useRedirectIfAuthenticated()

const handleLogin = async () => {
    pending.value = true

    try {
        const { error } = await supabase.auth.signInWithOtp({
            email: email.value,
            options: {
                emailRedirectTo: `${redirectURL}/confirm`
            }
        })

        if (error) {
            toastError({
                title: 'Error authenticating',
                description: error.message,
            })
        } else {
            success.value = true
        }
    } catch (error) {

    } finally {
        pending.value = false
    }
}
</script>