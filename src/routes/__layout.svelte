<script lang="ts">
  import '../app.css'
  import Head from '$lib/components/Head.svelte'
  // import Kofi from '$lib/components/Kofi.svelte'
  import Menu from '$lib/components/Menu.svelte'
  import Social from '$lib/components/Social.svelte'
  import ThemeSelector from '$lib/components/ThemeSelector.svelte'
  import Modal from '$lib/components/Modal.svelte'
  import { darkTheme, modalViewed } from '$lib/store'

  const url = 'https://cable-temp-calc.vercel.app'
  const title = 'Cable Operating Temperature Calculator'
  const description = 'Cable Operating Temperature Calculator cr.Sansirih'

  // const menuItems = [{ name: 'Made by bossoq', url: 'https://github.com/bossoq/cable-temp-calc' }]
  const imageUrl = `${url}/screenshot.jpg`
  const gtagId = null
  const themeIcons = ['🌞 Toggle Light mode', '🌙 Toggle Dark mode']
</script>

<Head {title} {description} {url} {imageUrl} {gtagId} />
<!-- <Kofi name="bossoq" label="Buy me Coffee" /> -->
<!-- <Social {url} {title} /> -->
<main class={$darkTheme && 'dark'}>
  <ThemeSelector {themeIcons} />
  <Menu items={[]} />
  {#if $modalViewed}
    <Modal />
  {/if}
  <slot />
</main>
