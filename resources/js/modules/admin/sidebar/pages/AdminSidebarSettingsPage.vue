<script setup lang="ts">
import { Button } from '@/components/ui/button'
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from '@/components/ui/card'
import { useAdminSidebarStore } from '@/modules/admin/sidebar/sidebar-store'
import type { SidebarData } from '@/types/sidebar'
import { computed, onMounted, ref } from 'vue'

const sidebarStore = useAdminSidebarStore()
const editorValue = ref('')
const errorMessage = ref('')

const sidebar = computed(() => sidebarStore.sidebars[0] ?? null)

const loadSidebar = async () => {
  await sidebarStore.index()
  editorValue.value = sidebar.value ? JSON.stringify(sidebar.value.data, null, 2) : ''
}

const saveSidebar = async () => {
  if (!sidebar.value) return

  try {
    errorMessage.value = ''
    const parsed = JSON.parse(editorValue.value) as SidebarData

    await sidebarStore.update(sidebar.value.id, {
      name: sidebar.value.name,
      description: sidebar.value.description,
      data: parsed,
    })

    editorValue.value = sidebar.value
      ? JSON.stringify(sidebar.value.data, null, 2)
      : editorValue.value
  } catch (error) {
    errorMessage.value =
      error instanceof SyntaxError ? 'Sidebar JSON is invalid.' : 'Unable to save sidebar.'
  }
}

onMounted(loadSidebar)
</script>

<template>
  <section class="mx-auto w-full max-w-5xl px-5 py-8">
    <Card>
      <CardHeader>
        <CardTitle>Admin sidebar settings</CardTitle>
        <CardDescription
          >Update the platform admin navigation structure stored by the API.</CardDescription
        >
      </CardHeader>
      <CardContent class="space-y-4">
        <div v-if="sidebar" class="grid gap-2 text-sm text-muted-foreground sm:grid-cols-3">
          <div>Name: {{ sidebar.name }}</div>
          <div>Groups: {{ sidebar.stats?.nav_groups ?? 0 }}</div>
          <div>Links: {{ sidebar.stats?.links ?? 0 }}</div>
        </div>

        <textarea
          v-model="editorValue"
          class="min-h-[28rem] w-full resize-y rounded border bg-background p-4 font-mono text-sm outline-none focus:border-teal-600 focus:ring-2 focus:ring-teal-100"
          spellcheck="false"
        />

        <p v-if="errorMessage" class="text-sm font-medium text-destructive">{{ errorMessage }}</p>

        <div class="flex justify-end">
          <Button :disabled="sidebarStore.loading || !sidebar" @click="saveSidebar">
            {{ sidebarStore.loading ? 'Saving...' : 'Save sidebar' }}
          </Button>
        </div>
      </CardContent>
    </Card>
  </section>
</template>
