<script lang="ts">
  export let type: any = 'button';
  export let variant:string = 'primary';
  export let disabled:boolean = false;
  export let onClick = () => {};

  function getVariantClass(variant:string) {
    switch (variant) {
      case 'primary':
        return 'btn-primary';
      case 'success':
        return 'btn-success';
      case 'info':
        return 'btn-info';
      case 'warning':
        return 'btn-warning';
      case 'danger':
        return 'btn-danger';
      default:
        return 'btn-primary';
    }
  }
</script>

<button
  class={`btn ${getVariantClass(variant)}`}
  type={type}
  disabled={disabled}
  on:click={onClick}
>
  <slot />
</button>

<style>
  /* No additional styles needed, using global CSS */
  button{
    margin: 2px;
  }
</style>