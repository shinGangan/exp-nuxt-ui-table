<script setup lang="ts">
import type { InferOutput } from 'valibot';
import { pipe, object, string, regex, maxLength, union, literal } from 'valibot';
import { useDateFormat, useNow } from '@vueuse/core';
import type { TableColumn } from '#ui/types';

/**
 * @param id - ユーザーID
 * @param name - 顧客名
 * @param phone - 電話番号
 * @param createdDate - 登録日. YYYY/MM/DD 形式で表示する
 * @param status - 顧客ステータス. available: 利用可能, unavailable: 利用不可
 */
const UserSchema = object({
  id: string(),
  name: string(),
  phone: pipe(
    string(),
    maxLength(12, '電話番号を正しく入力してください'),
    regex(/^([0][0-9]{1,4})[-]?[0-9]{1,4}[-]?[0-9]{4}$/, '電話番号を正しく入力してください')
  ),
  createdDate: string(),
  status: union([literal('available'), literal('unavailable')])
});
type UserTable = InferOutput<typeof UserSchema>;

interface UserTableColumn extends TableColumn {
  key: keyof UserTable;
  label: string;
}

const columns: UserTableColumn[] = [
  { key: 'id', label: '顧客ID' },
  { key: 'name', label: '顧客名' },
  { key: 'phone', label: '電話番号' },
  { key: 'createdDate', label: '登録日' },
  { key: 'status', label: 'ステータス' }
];

const rows: UserTable[] = [...Array(15)].map((v, i) => {
  return {
    id: i + 1,
    name: `テストユーザー${i + 1}`,
    phone: `090-1234-56${String(i + 1).padStart(2, '0')}`,
    createdDate: useDateFormat(useNow(), 'YYYY-MM-DD'),
    status: i % 3 ? 'available' : 'unavailable'
  };
});
</script>

<template>
  <UContainer class="flex justify-center pt-16">
    <UTable :rows :columns />
  </UContainer>
</template>
