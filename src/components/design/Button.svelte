<script lang="ts">
  export let type: 'button' | 'submit' | 'reset' | null | undefined = 'button';
  export let variant: string = 'primary';
  export let size: string = 'md';
  export let disabled: boolean = false;
  export let loading: boolean = false;
  export let icon: boolean = false;
  export let wide: boolean = false;
  export let block: boolean = false;
  export let onClick: (event?: Event) => void = () => {};
  export let className: string = '';

  function getVariantClass(variant: string): string {
    switch (variant) {
      case 'primary':
        return '';
      case 'secondary':
        return 'btn-secondary';
      case 'outline':
        return 'btn-outline';
      case 'success':
        return 'btn-success';
      case 'info':
        return 'btn-info';
      case 'warning':
        return 'btn-warning';
      case 'danger':
        return 'btn-danger';
      default:
        return '';
    }
  }

  function getSizeClass(size: string, icon: boolean): string {
    const prefix = icon ? 'btn-icon-' : 'btn-';
    switch (size) {
      case 'xs':
        return `${prefix}xs`;
      case 'sm':
        return `${prefix}sm`;
      case 'md':
        return `${prefix}md`;
      case 'lg':
        return `${prefix}lg`;
      case 'xl':
        return `${prefix}xl`;
      default:
        return `${prefix}md`;
    }
  }

  function getModifierClasses(): string {
    const modifiers = [];
    if (wide) modifiers.push('btn-wide');
    if (block) modifiers.push('btn-block');
    return modifiers.join(' ');
  }

  $: buttonClass = [
    getSizeClass(size, icon),
    getVariantClass(variant),
    getModifierClasses(),
    className
  ].filter(Boolean).join(' ');

  function handleClick(event: Event) {
    if (!disabled && !loading) {
      onClick(event);
    }
  }
</script>

<button
  class={buttonClass}
  type={type}
  disabled={disabled || loading}
  on:click={handleClick}
  aria-busy={loading}
>
  {#if loading}
    <span class="btn-spinner" aria-hidden="true">
      <svg viewBox="0 0 24 24" width="16" height="16">
        <circle cx="12" cy="12" r="10" stroke="currentColor" stroke-width="2" fill="none" opacity="0.25"/>
        <path d="M12 2a10 10 0 0 1 10 10" stroke="currentColor" stroke-width="2" fill="none" opacity="0.75"/>
      </svg>
    </span>
  {/if}
  <slot />
</button>

<style>
  .btn-spinner {
    display: inline-flex;
    animation: spin 1s linear infinite;
  }
  
  .btn-spinner svg {
    width: 1em;
    height: 1em;
  }

  @keyframes spin {
    from {
      transform: rotate(0deg);
    }
    to {
      transform: rotate(360deg);
    }
  }

  /* Success variant - needs to be added to global CSS */
  :global(.btn-success) {
    background-color: var(--color-success);
    border-color: var(--color-success);
    color: white;
  }

  :global(.btn-success:hover) {
    background-color: var(--color-success-hover);
    border-color: var(--color-success-hover);
  }

  /* Info variant - needs to be added to global CSS */
  :global(.btn-info) {
    background-color: var(--color-info);
    border-color: var(--color-info);
    color: white;
  }

  :global(.btn-info:hover) {
    background-color: var(--color-info-hover);
    border-color: var(--color-info-hover);
  }

  /* Warning variant - needs to be added to global CSS */
  :global(.btn-warning) {
    background-color: var(--color-warning);
    border-color: var(--color-warning);
    color: var(--color-text-inverse);
  }

  :global(.btn-warning:hover) {
    background-color: var(--color-warning-hover);
    border-color: var(--color-warning-hover);
  }
</style>