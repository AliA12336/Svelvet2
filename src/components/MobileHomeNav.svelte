<script lang="ts">
  import logo from '../assets/Logo 1.svg';
  import { slide } from 'svelte/transition';

  /* import store so that we can check if there's a user that is loggin in */
  import { userInfoStore } from '../authStoreTs';
  /* import supabase functionality for user login / logout */
  import { signInWithGithub, logout } from '../supabase-db';
  /* importing GitHub logo from assets */
  import github from '../assets/github-icon-white.svg';

  let { user, logged_in, user_avatar } = userInfoStore;

  let hidden = true;

  const toggleMenu = () => {
    hidden = !hidden;
  };

  let y: number;

  function clickOutside(node, { enabled: initialEnabled, cb }) {
    const handleOutsideClick = ({ target }) => {
      if (!node.contains(target)) {
        cb();
      }
    };

    function update({ enabled }) {
      if (enabled) {
        window.addEventListener('click', handleOutsideClick);
      } else {
        window.removeEventListener('click', handleOutsideClick);
      }
    }

    update({ enabled: initialEnabled });
    return {
      update,
      destroy() {
        window.removeEventListener('click', handleOutsideClick);
      }
    };
  }
</script>

<svelte:window bind:scrollY={y} />

<div
  class:shadow-lg={y > 5}
  class="md:hidden sticky top-0 z-50 flex justify-between px-8 py-3 w-screen border-b h-16 bg-white"
  use:clickOutside={{ enabled: !hidden, cb: () => (hidden = true) }}
>
  <div class="flex items-center">
    <img src={logo} alt="Logo" class="aspect-ratio-auto h-8" />
    <a
      id="home"
      href="/"
      class="text-3xl text-gray-700 font-nunito font-medium tracking-wide ml-2 mr-6">svelvet</a
    >
  </div>
  <button class="outline-none mobile-menu-button pl-8 " on:click={toggleMenu}>
    <div id="navMenu" class:active={!hidden}>
      <span /><span /><span />
    </div>
  </button>
</div>

{#if !hidden}
  <div
    transition:slide
    class="md:hidden absolute w-screen mobile-menu border px-8 bg-gray-100 text-gray-700"
  >
    <ul class="text-center">
      <li>
        <a on:click={toggleMenu} href="/" class="block py-6">Home</a>
      </li>
      <li>
        <a on:click={toggleMenu} href="/docs/installation" class="block py-6">Documentation</a>
      </li>
      <!-- //TODO create button for create page -->
      <li>
        <a on:click={toggleMenu} href="/playground" class="block py-6">REPL</a>
      </li>
      <li>
        <a
          on:click={toggleMenu}
          target="_blank"
          href="https://medium.com/@alexander.zambrano/simplify-application-diagramming-with-svelvet-a8f664731243"
          class="block py-6">Blog</a
        >
      </li>
      <li>
        <a
          on:click={toggleMenu}
          href="https://github.com/open-source-labs/Svelvet"
          target="_blank"
          class="block py-6">Github</a
        >
      </li>
      <!-- added login / logout button link! -->

      {#if $user}
      <button on:click={logout}>
        <div class="login-container rounded-full px-4 py-1 bg-rose-100 text-red-400 tracking-wider hover:text-rose-500 hover:bg-white">Logout 
        <img src={$user_avatar} alt="user pic"/>
      </div> 
    </button>
    {:else}
      <button on:click={signInWithGithub}><div class="login-container px-6 py-3 btn-primary">
        Log In
        <img src={github} alt="github-logo" />
      </div></button>
    {/if}
    </ul>
  </div>
{/if}



<style>
  #navMenu > span {
    display: block;
    width: 28px;
    height: 2px;
    border-radius: 9999px;
    background-color: rgb(104, 104, 104);
  }

  #navMenu > span:not(:last-child) {
    margin-bottom: 7px;
  }

  #navMenu,
  #navMenu > span {
    transition: all 0.2s ease-in-out;
  }

  #navMenu.active {
    transition-delay: 0.4s;
    transform: rotate(45deg);
  }
  #navMenu.active > span:nth-child(2) {
    width: 0;
  }

  #navMenu.active > span:nth-child(1),
  #navMenu.active > span:nth-child(3) {
    transition-delay: 0.2s;
  }

  #navMenu.active > span:nth-child(1) {
    transform: translateY(9px);
  }

  #navMenu.active > span:nth-child(3) {
    transform: translateY(-9px) rotate(90deg);
  }

  .login-container{
    display: flex;
    width: 8em;
    justify-content: space-between;
    border-style: solid;
    border-width: 1.25px;
    border-color: #ff4561;
    border-radius: 2em;
    align-items: center;
    flex: 1;
    float: left;
    padding: .25em 1em .25em;
  }

  .login-container img {
    display: inline-block;
    margin-left: 5px;
    height: 32px;
    width: 32px;
    border-radius: 50%;
  }

</style>
