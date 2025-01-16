<script lang="ts">
  import { onMount } from 'svelte';

  export let repo: string;
  export let issueTerm: string = 'url';
  export let label: string = 'post comments';
  export let theme: string = 'github-light';

  onMount(() => {
    const script = document.createElement('script');
    const container = document.getElementById('utterances-container');

    Object.entries({
      src: 'https://utteranc.es/client.js',
      repo,
      'issue-term': issueTerm,
      label,
      theme,
      crossorigin: 'anonymous',
    }).forEach(([key, value]) => {
      script.setAttribute(key, value as string);
    });

    container?.appendChild(script);
  });
</script>

<div id="utterances-container"></div>
