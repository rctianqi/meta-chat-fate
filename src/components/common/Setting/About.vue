<script setup lang='ts'>
import { computed, onMounted, ref } from 'vue'
import { NSpin } from 'naive-ui'
import { fetchChatConfig } from '@/api'
import pkg from '@/../package.json'
import { useAuthStore } from '@/store'

interface ConfigState {
  timeoutMs?: number
  reverseProxy?: string
  apiModel?: string
  socksProxy?: string
  httpsProxy?: string
  balance?: string
}

const authStore = useAuthStore()

const loading = ref(false)

const config = ref<ConfigState>()

const isChatGPTAPI = computed<boolean>(() => !!authStore.token)

async function fetchConfig() {
  try {
    loading.value = true
    const { data } = await fetchChatConfig<ConfigState>()
    config.value = data
  }
  finally {
    loading.value = false
  }
}

onMounted(() => {
  fetchConfig()
})
</script>

<template>
  <NSpin :show="loading">
    <div class="p-4 space-y-4">
      <h2 class="text-xl font-bold">
        Version - {{ pkg.version }}
      </h2>
      <div class="p-2 space-y-2 rounded-md bg-neutral-100 dark:bg-neutral-700">
        <p>
				<div class="p-4 space-y-4"><h2 class="text-xl font-bold"> 趣答Ai-禁止提问任何违法违规话题 </h2>
				<div class="p-2 space-y-2 rounded-md bg-neutral-100 dark:bg-neutral-700">
				<p> 使用此程序请遵守 <a class="text-blue-600 dark:text-blue-500" href="https://oss1.qdymys.cn/assets/disclaimer_ai_edu.html" target="_blank"> 使用协议 </a> ，
				 </p><p> 如果你觉得此程序对你有帮助，请加入我们交流群，一起探讨Ai智能！ </p></div>
				 <p>警告：严禁用此程序问答任何违法违规话题，违者记录IP保存日志！</p>
				 <p>如有任何问题请联系客服， 微信:Limerick_PH</p></div>
          
        </p>
      </div>
      <p>{{ $t("setting.api") }}：{{ config?.apiModel ?? '-' }}</p>
      <p v-if="isChatGPTAPI">
        {{ $t("setting.balance") }}：{{ config?.balance ?? '-' }}
        <span class="text-xs text-neutral-400">({{ $t('setting.monthlyUsage') }})</span>
      </p>
    </div>
  </NSpin>
</template>
