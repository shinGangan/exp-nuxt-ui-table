<script setup lang="ts">
import { useNow } from '@vueuse/core';
import type { UserTableRow } from '~/componets/feature/user/userTable';

const table: UserTableRow[] = computed(() => {
  return [...Array(15)].map((_, i) => {
    const id = i + 1;
    return {
      id,
      name: `テストユーザー${id}`,
      phone: `090-1234-56${String(id).padStart(2, '0')}`,
      createdDate: useDateFormat(useNow(), 'YYYY-MM-DD'),
      status: i % 3 ? 'available' : 'unavailable'
    };
  });
});
</script>

<template>
  <UContainer class="pt-16">
    <UserTable v-model:data="table" />
  </UContainer>
</template>
