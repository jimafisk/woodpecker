<template>
  <FluidContainer class="space-y-4 flex flex-col my-0">
    <Button class="ml-auto" text="Logout" :to="`${address}/logout`" />

    <div>
      <h2 class="text-lg text-gray-500">Your Personal Token</h2>
      <pre class="cli-box">{{ token }}</pre>
    </div>

    <div>
      <h2 class="text-lg text-gray-500">Shell setup</h2>
      <pre class="cli-box">{{ usageWithShell }}</pre>
    </div>

    <div>
      <h2 class="text-lg text-gray-500">Example API Usage</h2>
      <pre class="cli-box">{{ usageWithCurl }}</pre>
    </div>

    <div>
      <div class="flex items-center">
        <h2 class="text-lg text-gray-500">Example CLI Usage</h2>
        <a :href="cliDownload" target="_blank" class="ml-4 text-link">Download CLI</a>
      </div>
      <pre class="cli-box">{{ usageWithCli }}</pre>
    </div>
  </FluidContainer>
</template>

<script lang="ts">
import { computed, defineComponent, onMounted, ref } from 'vue';

import Button from '~/components/atomic/Button.vue';
import FluidContainer from '~/components/layout/FluidContainer.vue';
import useApiClient from '~/compositions/useApiClient';

export default defineComponent({
  name: 'User',

  components: {
    FluidContainer,
    Button,
  },

  setup() {
    const apiClient = useApiClient();
    const token = ref<string | undefined>();

    onMounted(async () => {
      token.value = await apiClient.getToken();
    });

    // eslint-disable-next-line no-restricted-globals
    const address = `${location.protocol}//${location.host}`;

    const usageWithShell = computed(() => {
      let usage = `export WOODPECKER_SERVER="${address}"\n`;
      usage += `export WOODPECKER_TOKEN="${token.value}"\n`;
      return usage;
    });

    const usageWithCurl =
      // eslint-disable-next-line no-template-curly-in-string
      '# do shell setup steps before\ncurl -i ${WOODPECKER_SERVER}/api/user -H "Authorization: Bearer ${WOODPECKER_TOKEN}"';

    const usageWithCli = '# do shell setup steps before\nwoodpecker info';

    const cliDownload = 'https://github.com/woodpecker-ci/woodpecker/releases';

    return { token, usageWithShell, usageWithCurl, usageWithCli, cliDownload, address };
  },
});
</script>

<style scoped>
.cli-box {
  @apply bg-gray-400 p-2 rounded-md text-white break-words dark:bg-dark-300 dark:text-gray-500;
  white-space: pre-wrap;
}
</style>
