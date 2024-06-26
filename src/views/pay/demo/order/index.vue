<script lang="ts" setup>
import DemoModal from './DemoModal.vue'
import { columns, searchFormSchema } from './demo.data'
import { useGo } from '@/hooks/web/usePage'
import { useI18n } from '@/hooks/web/useI18n'
import { useMessage } from '@/hooks/web/useMessage'
import { useModal } from '@/components/Modal'
import { IconEnum } from '@/enums/appEnum'
import { BasicTable, TableAction, useTable } from '@/components/Table'
import { getDemoOrderPage, refundDemoOrder } from '@/api/pay/demo'
import { DocAlert } from '@/components/DocAlert'

defineOptions({ name: 'PayDemo' })

const go = useGo()
const { t } = useI18n()
const { createConfirm, createMessage } = useMessage()
const [registerModal, { openModal }] = useModal()

const [registerTable, { reload }] = useTable({
  title: '接入示例列表',
  api: getDemoOrderPage,
  columns,
  formConfig: { labelWidth: 120, schemas: searchFormSchema },
  useSearchForm: true,
  showTableSetting: true,
  showIndexColumn: false,
  actionColumn: {
    width: 180,
    title: t('common.action'),
    dataIndex: 'action',
    fixed: 'right',
  },
})

function handleAdd() {
  openModal(true)
}

/** 支付按钮操作 */
function handlePay(record: Recordable) {
  go(`/pay/cashier?id=${record.payOrderId}&&returnUrl=${encodeURIComponent(`/pay/demo-order?id=${record.id}`)}`)
}

/** 退款按钮操作 */
async function handleRefund(record: Recordable) {
  createConfirm({
    title: '退款',
    iconType: 'warning',
    content: `是否确认退款编号为"${record.id}"的示例订单?`,
    async onOk() {
      await refundDemoOrder(record.id)
      createMessage.success(t('common.successText'))
      reload()
    },
  })
}
</script>

<template>
  <div>
    <DocAlert title="支付宝支付接入" url="https://doc.iocoder.cn/pay/alipay-pay-demo/" />
    <DocAlert title="支付宝、微信退款接入" url="https://doc.iocoder.cn/pay/refund-demo/" />
    <DocAlert title="微信公众号支付接入" url="https://doc.iocoder.cn/pay/wx-pub-pay-demo/" />
    <DocAlert title="微信小程序支付接入" url="https://doc.iocoder.cn/pay/wx-lite-pay-demo/" />

    <BasicTable @register="registerTable">
      <template #toolbar>
        <a-button v-auth="['pay:app:create']" type="primary" :pre-icon="IconEnum.ADD" @click="handleAdd">
          发起订单
        </a-button>
      </template>
      <template #bodyCell="{ column, record }">
        <template v-if="column.key === 'action'">
          <TableAction
            :actions="[
              {
                icon: IconEnum.EDIT,
                label: '前往支付',
                ifShow: () => {
                  return !record.payed
                },
                onClick: handlePay.bind(null, record),
              },
              {
                icon: IconEnum.DELETE,
                danger: true,
                label: '发起退款',
                ifShow: () => {
                  return record.payed && !record.payRefundId
                },
                onClick: handleRefund.bind(null, record),
              },
            ]"
          />
        </template>
      </template>
    </BasicTable>
    <DemoModal @register="registerModal" @success="reload()" />
  </div>
</template>
