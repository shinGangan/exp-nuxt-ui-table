<script lang="ts">
import type { InferOutput } from 'valibot';
import { pipe, object, string, regex, maxLength, union, literal } from 'valibot';
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
export type UserTableRow = InferOutput<typeof UserSchema>;

export interface UserTableColumn extends TableColumn {
  key: keyof UserTable;
  label: string;
}
</script>

<script setup lang="ts" generic="R extends UserTableRow, C extends UserTableColumn">
const { t } = useI18n();
const data = defineModel<R[]>('data');

// TODO: UserTableColumn.key配列を生成し、それをループで回せたら嬉しいね
const columns: C[] = [
  { key: 'id', label: t('table.id') },
  { key: 'name', label: t('table.name') },
  { key: 'phone', label: t('table.phone') },
  { key: 'createdDate', label: t('table.createdDate') },
  { key: 'status', label: t('table.status') }
];

/**
 * テーブルの各行を押下した際, アラートを表示する
 * @param row
 */
const selectRow = (row: R): void => {
  alert(`${row.name}は${t(`status.${row.status}`)}ユーザーです.`);
};
</script>

<template>
  <UTable
    :rows="data"
    :columns
    :ui="{
      th: { base: 'text-center' },
      tr: { base: 'text-center hover:cursor-pointer' }
    }"
    @select="selectRow"
  >
    <template #status-data="{ row }">
      <UBadge
        :label="t(`status.${row.status}`)"
        :color="row.status === 'available' ? 'primary' : 'gray'"
      />
    </template>
  </UTable>
</template>

<i18n lang="yaml">
ja:
  table:
    id: 顧客ID
    name: 顧客名
    phone: 電話番号
    createdDate: 登録日
    status: ステータス
  status:
    available: 利用可能
    unavailable: 利用不可
</i18n>
