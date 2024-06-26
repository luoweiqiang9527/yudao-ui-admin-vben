<script lang="ts" setup>
import OrderModal from './OrderModal.vue'
import { columns, searchFormSchema } from './order.data'
import { useI18n } from '@/hooks/web/useI18n'
import { useMessage } from '@/hooks/web/useMessage'
import { useModal } from '@/components/Modal'
import { IconEnum } from '@/enums/appEnum'
import { BasicTable, TableAction, useTable } from '@/components/Table'
import type { OrderExportReqVO } from '@/api/pay/order'
import { exportOrder, getOrderPage } from '@/api/pay/order'
import { DocAlert } from '@/components/DocAlert'

defineOptions({ name: 'PayOrder' })

const { t } = useI18n()
const { createConfirm, createMessage } = useMessage()
const [registerModal, { openModal }] = useModal()

const [registerTable, { getForm }] = useTable({
  title: '订单列表',
  api: getOrderPage,
  columns,
  formConfig: { labelWidth: 120, schemas: searchFormSchema },
  useSearchForm: true,
  showTableSetting: true,
  actionColumn: {
    width: 140,
    title: t('common.action'),
    dataIndex: 'action',
    fixed: 'right',
  },
})

function handleQueryDetails(record: Recordable) {
  openModal(true, { record, isUpdate: true })
}

async function handleExport() {
  createConfirm({
    title: t('common.exportTitle'),
    iconType: 'warning',
    content: t('common.exportMessage'),
    async onOk() {
      await exportOrder(getForm().getFieldsValue() as OrderExportReqVO)
      createMessage.success(t('common.exportSuccessText'))
    },
  })
}
</script>

<template>
  <div>
    <DocAlert title="支付宝支付接入" url="https://doc.iocoder.cn/pay/alipay-pay-demo/" />
    <DocAlert title="微信公众号支付接入" url="https://doc.iocoder.cn/pay/wx-pub-pay-demo/" />
    <DocAlert title="微信小程序支付接入" url="https://doc.iocoder.cn/pay/wx-lite-pay-demo/" />

    <BasicTable @register="registerTable">
      <template #toolbar>
        <a-button v-auth="['pay:order:export']" :pre-icon="IconEnum.EXPORT" @click="handleExport">
          {{ t('action.export') }}
        </a-button>
      </template>
      <template #bodyCell="{ column, record }">
        <template v-if="column.key === 'action'">
          <TableAction
            :actions="[
              { icon: IconEnum.VIEW, label: t('action.detail'), auth: 'pay:order:query', onClick: handleQueryDetails.bind(null, record) },
            ]"
          />
        </template>
      </template>
    </BasicTable>
    <OrderModal @register="registerModal" />
  </div>
</template>
