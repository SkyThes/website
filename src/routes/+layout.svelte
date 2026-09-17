<script lang="ts">
  import '../app.css';
  import { page } from '$app/state';
  import { ModeWatcher } from 'mode-watcher';
  import Header from '$lib/components/header.svelte';
  import AlertTriangle from '@lucide/svelte/icons/alert-triangle';
  import ArrowRight from '@lucide/svelte/icons/arrow-right';
  import Code from '@lucide/svelte/icons/code';

  let { children } = $props();

  let showBanner = $state(false);
  let isDev = $state(false);
  let version: string | undefined = $state();

  const announcement = {
    enabled: true,
    path: '/',
    label: 'Update',
    message: 'Protect your MCP Server with Pocket ID in just a few minutes.',
    href: '/#mcp-agents',
  } as const;

  async function readVersionFile(): Promise<string> {
    try {
      const res = await fetch('https://raw.githubusercontent.com/pocket-id/pocket-id/refs/heads/main/.version');
      return await res.text();
    } catch {
      return '';
    }
  }

  if (typeof window !== 'undefined') {
    const host = window.location.hostname;
    const isProd = host === 'pocket-id.org';
    if (!isProd) {
      showBanner = true;
      isDev = host === 'localhost' || host === '127.0.0.1';
      readVersionFile().then((v) => (version = v?.trim() || undefined));
    }
  }
</script>

<ModeWatcher disableTransitions={false} />

{#if showBanner}
  <div
    class="sticky top-0 z-[60] border-b border-blue-500 text-blue-500 bg-blue-500/10 dark:bg-blue-500/15 backdrop-blur-sm">
    <div class="container-wrapper px-6 py-2">
      <div class="text-[12px] font-medium flex items-center justify-center gap-2 text-center">
        {#if isDev}
          <Code class="size-4" />
          <span>Development environment — documentation may not reflect the production version</span>
        {:else}
          <AlertTriangle class="size-4" />
          <span>
            This documentation is for an unreleased version of Pocket ID. See the
            <a href="https://pocket-id.org/docs" class="underline font-semibold text-blue-500 hover:text-blue-400"
              >latest version</a>
            {#if version}
              (v{version}){/if}
          </span>
        {/if}
      </div>
    </div>
  </div>
{/if}

{#if announcement.enabled && page.url.pathname === announcement.path}
  <a
    href={announcement.href}
    class="group block border-b border-violet-400/30 bg-gradient-to-r from-violet-600 via-indigo-600 to-blue-600 px-4 py-2.5 text-white shadow-sm transition-[filter] hover:brightness-110 dark:from-violet-700 dark:via-indigo-700 dark:to-blue-700"
  >
    <span class="mx-auto flex max-w-3xl items-center justify-center gap-2 text-center text-sm font-medium">
      <span class="rounded-full bg-white/15 px-2 py-0.5 text-xs font-semibold uppercase tracking-wide ring-1 ring-white/25">
        {announcement.label}
      </span>
      <span>{announcement.message}</span>
      <ArrowRight class="hidden size-4 shrink-0 transition-transform group-hover:translate-x-0.5 sm:block" />
    </span>
  </a>
{/if}

<div class="bg-background text-foreground flex min-h-screen flex-col">
  <Header />
  <main class="flex-1">
    {@render children()}
  </main>
</div>
