<script setup lang="ts">
import { Button } from '@/components/ui/button'
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from '@/components/ui/card'
import { useAdminAuthStore } from '../admin-auth-store'

const adminAuthStore = useAdminAuthStore()
</script>

<template>
  <section class="mx-auto flex min-h-svh w-full max-w-5xl items-center px-5 py-8">
    <Card class="w-full">
      <CardHeader>
        <CardTitle>Admin dashboard</CardTitle>
        <CardDescription>
          Signed in as {{ adminAuthStore.user?.name || adminAuthStore.user?.email }}
        </CardDescription>
      </CardHeader>
      <CardContent class="flex items-center justify-between gap-4">
        <p class="text-sm text-zinc-600">Your admin session is active.</p>
        <Button
          size="sm"
          type="button"
          :disabled="adminAuthStore.loading"
          @click="adminAuthStore.logout"
        >
          {{ adminAuthStore.loading ? 'Logging out...' : 'Logout' }}
        </Button>
      </CardContent>
    </Card>
  </section>
</template>
