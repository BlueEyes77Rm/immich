<script>
  export let item;
  export let highlighted;
</script>

<li class="autocomplete-items" class:autocomplete-active={highlighted} on:click>{@html item.display_name}</li>

<style>
  li.autocomplete-items {
    list-style: none;
    border-bottom: 1px solid #d4d4d4;
    color: black;
    z-index: 99;
    /*position the autocomplete items to be the same width as the container:*/
    top: 100%;
    left: 0;
    right: 0;
    padding: 10px;
    cursor: pointer;
    background-color: #fff;
    border-radius: 10px;
  }

  li.autocomplete-items:hover {
    /*when hovering an item:*/
    /*background-color:DodgerBlue !important;
    color: white;*/
    --tw-bg-opacity: 1;
    background-color: rgb(var(--immich-dark-primary) / var(--tw-bg-opacity));
    --tw-text-opacity: 1;
    color: rgb(var(--immich-dark-gray) / var(--tw-text-opacity));
  }

  li.autocomplete-items:active {
    /*when navigating through the items using the arrow keys:*/
    --tw-bg-opacity: 1;
    background-color: rgb(var(--immich-dark-primary) / var(--tw-bg-opacity));
    --tw-text-opacity: 1;
    color: rgb(var(--immich-dark-gray) / var(--tw-text-opacity));
  }

  .autocomplete-active {
    /*when navigating through the items using the arrow keys:*/
    --tw-bg-opacity: 1;
    background-color: rgb(var(--immich-dark-primary) / var(--tw-bg-opacity));
    --tw-text-opacity: 1;
    color: rgb(var(--immich-dark-gray) / var(--tw-text-opacity));
  }
</style>
