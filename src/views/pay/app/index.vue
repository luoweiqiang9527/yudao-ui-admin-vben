<script lang="ts" setup>
import AppModal from './AppModal.vue'
import ChannelModal from './ChannelModal.vue'
import { columns, searchFormSchema } from './app.data'
import { useI18n } from '@/hooks/web/useI18n'
import { useMessage } from '@/hooks/web/useMessage'
import { useModal } from '@/components/Modal'
import { PayChannelEnum, PayType } from '@/enums/systemEnum'
import { IconEnum } from '@/enums/appEnum'
import { Icon } from '@/components/Icon'
import { BasicTable, TableAction, useTable } from '@/components/Table'
import { deleteApp, getAppPage } from '@/api/pay/app'
import { DocAlert } from '@/components/DocAlert'

defineOptions({ name: 'PayApp' })

const { t } = useI18n()
const { createMessage } = useMessage()
const [registerModal, { openModal }] = useModal()
const [registerChannelModal, { openModal: openChannelModal }] = useModal()

const [registerTable, { reload }] = useTable({
  title: '应用列表',
  api: getAppPage,
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

function handleCreate() {
  openModal(true, { isUpdate: false })
}

function handleEdit(record: Recordable) {
  openModal(true, { record, isUpdate: true })
}

async function handleDelete(record: Recordable) {
  await deleteApp(record.id)
  createMessage.success(t('common.delSuccessText'))
  reload()
}

function openChannel(record: Recordable, payCode: string, type: string, isUpdate: boolean) {
  openChannelModal(true, { record, payCode, type, isUpdate })
}

/**
 * 根据渠道编码判断渠道列表中是否存在
 *
 * @param channels 渠道列表
 * @param channelCode 渠道编码
 */
function isChannelExists(channels, channelCode) {
  if (!channels)
    return false

  return channels.includes(channelCode)
}
</script>

<template>
  <div>
    <DocAlert title="支付功能开启" url="https://doc.iocoder.cn/pay/build/" />

    <BasicTable @register="registerTable">
      <template #toolbar>
        <a-button v-auth="['pay:app:create']" type="primary" :pre-icon="IconEnum.ADD" @click="handleCreate">
          {{ t('action.create') }}
        </a-button>
      </template>
      <template #bodyCell="{ column, record }">
        <template v-if="column.key === PayChannelEnum.ALIPAY_APP.code">
          <a-button
            v-if="isChannelExists(record.channelCodes, PayChannelEnum.ALIPAY_APP.code)" type="primary"
            shape="circle" @click="openChannel(record, PayChannelEnum.ALIPAY_APP.code, PayType.ALIPAY, true)"
          >
            <Icon icon="ant-design:check-outlined" />
          </a-button>
          <a-button
            v-else type="primary" shape="circle" danger
            @click="openChannel(record, PayChannelEnum.ALIPAY_APP.code, PayType.ALIPAY, false)"
          >
            <Icon icon="ant-design:close-outlined" />
          </a-button>
        </template>
        <template v-if="column.key === PayChannelEnum.ALIPAY_PC.code">
          <a-button
            v-if="isChannelExists(record.channelCodes, PayChannelEnum.ALIPAY_PC.code)" type="primary"
            shape="circle" @click="openChannel(record, PayChannelEnum.ALIPAY_PC.code, PayType.ALIPAY, true)"
          >
            <Icon icon="ant-design:check-outlined" />
          </a-button>
          <a-button
            v-else type="primary" shape="circle" danger
            @click="openChannel(record, PayChannelEnum.ALIPAY_PC.code, PayType.ALIPAY, false)"
          >
            <Icon icon="ant-design:close-outlined" />
          </a-button>
        </template>
        <template v-if="column.key === PayChannelEnum.ALIPAY_WAP.code">
          <a-button
            v-if="isChannelExists(record.channelCodes, PayChannelEnum.ALIPAY_WAP.code)" type="primary"
            shape="circle" @click="openChannel(record, PayChannelEnum.ALIPAY_WAP.code, PayType.ALIPAY, true)"
          >
            <Icon icon="ant-design:check-outlined" />
          </a-button>
          <a-button
            v-else type="primary" shape="circle" danger
            @click="openChannel(record, PayChannelEnum.ALIPAY_WAP.code, PayType.ALIPAY, false)"
          >
            <Icon icon="ant-design:close-outlined" />
          </a-button>
        </template>
        <template v-if="column.key === PayChannelEnum.ALIPAY_QR.code">
          <a-button
            v-if="isChannelExists(record.channelCodes, PayChannelEnum.ALIPAY_QR.code)" type="primary"
            shape="circle" @click="openChannel(record, PayChannelEnum.ALIPAY_QR.code, PayType.ALIPAY, true)"
          >
            <Icon icon="ant-design:check-outlined" />
          </a-button>
          <a-button
            v-else type="primary" shape="circle" danger
            @click="openChannel(record, PayChannelEnum.ALIPAY_QR.code, PayType.ALIPAY, false)"
          >
            <Icon icon="ant-design:close-outlined" />
          </a-button>
        </template>
        <template v-if="column.key === PayChannelEnum.ALIPAY_BAR.code">
          <a-button
            v-if="isChannelExists(record.channelCodes, PayChannelEnum.ALIPAY_BAR.code)" type="primary"
            shape="circle" @click="openChannel(record, PayChannelEnum.ALIPAY_BAR.code, PayType.ALIPAY, true)"
          >
            <Icon icon="ant-design:check-outlined" />
          </a-button>
          <a-button
            v-else type="primary" shape="circle" danger
            @click="openChannel(record, PayChannelEnum.ALIPAY_BAR.code, PayType.ALIPAY, false)"
          >
            <Icon icon="ant-design:close-outlined" />
          </a-button>
        </template>
        <template v-if="column.key === PayChannelEnum.WX_LITE.code">
          <a-button
            v-if="isChannelExists(record.channelCodes, PayChannelEnum.WX_LITE.code)" type="primary" shape="circle"
            @click="openChannel(record, PayChannelEnum.WX_LITE.code, PayType.WECHAT, true)"
          >
            <Icon icon="ant-design:check-outlined" />
          </a-button>
          <a-button
            v-else type="primary" shape="circle" danger
            @click="openChannel(record, PayChannelEnum.WX_LITE.code, PayType.WECHAT, false)"
          >
            <Icon icon="ant-design:close-outlined" />
          </a-button>
        </template>
        <template v-if="column.key === PayChannelEnum.WX_PUB.code">
          <a-button
            v-if="isChannelExists(record.channelCodes, PayChannelEnum.WX_PUB.code)" type="primary" shape="circle"
            @click="openChannel(record, PayChannelEnum.WX_PUB.code, PayType.WECHAT, true)"
          >
            <Icon icon="ant-design:check-outlined" />
          </a-button>
          <a-button
            v-else type="primary" shape="circle" danger
            @click="openChannel(record, PayChannelEnum.WX_PUB.code, PayType.WECHAT, false)"
          >
            <Icon icon="ant-design:close-outlined" />
          </a-button>
        </template>
        <template v-if="column.key === PayChannelEnum.WX_APP.code">
          <a-button
            v-if="isChannelExists(record.channelCodes, PayChannelEnum.WX_APP.code)" type="primary" shape="circle"
            @click="openChannel(record, PayChannelEnum.WX_APP.code, PayType.WECHAT, true)"
          >
            <Icon icon="ant-design:check-outlined" />
          </a-button>
          <a-button
            v-else type="primary" shape="circle" danger
            @click="openChannel(record, PayChannelEnum.WX_APP.code, PayType.WECHAT, false)"
          >
            <Icon icon="ant-design:close-outlined" />
          </a-button>
        </template>
        <template v-if="column.key === PayChannelEnum.MOCK.code">
          <a-button
            v-if="isChannelExists(record.channelCodes, PayChannelEnum.MOCK.code)" type="primary" shape="circle"
            @click="openChannel(record, PayChannelEnum.MOCK.code, PayType.MOCK, true)"
          >
            <Icon icon="ant-design:check-outlined" />
          </a-button>
          <a-button
            v-else type="primary" shape="circle" danger
            @click="openChannel(record, PayChannelEnum.MOCK.code, PayType.MOCK, false)"
          >
            <Icon icon="ant-design:close-outlined" />
          </a-button>
        </template>
        <template v-if="column.key === 'action'">
          <TableAction
            :actions="[
              { icon: IconEnum.EDIT, label: t('action.edit'), auth: 'pay:app:update', onClick: handleEdit.bind(null, record) },
              {
                icon: IconEnum.DELETE,
                danger: true,
                label: t('action.delete'),
                auth: 'pay:app:delete',
                popConfirm: {
                  title: t('common.delMessage'),
                  placement: 'left',
                  confirm: handleDelete.bind(null, record),
                },
              },
            ]"
          />
        </template>
      </template>
    </BasicTable>
    <AppModal @register="registerModal" @success="reload()" />
    <ChannelModal @register="registerChannelModal" @success="reload()" />
  </div>
</template>
