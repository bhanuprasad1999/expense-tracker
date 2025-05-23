<script lang="ts">

    interface NavbarItem {
        label: string;
        href: string;
    }

    export let leftItems: NavbarItem[] = [];
    export let centerItems:NavbarItem[] = [];
    export let rightItems:NavbarItem[] = [];
    
</script>

<nav class="navbar">
    <div class="navbar-section left">
        {#each leftItems as item}
            <a href={item.href} class="navbar-link">{item.label}</a>
        {/each}
    </div>
    <div class="navbar-section center">
        {#each centerItems as item}
            <a href={item.href} class="navbar-link">{item.label}</a>
        {/each}
    </div>
    <div class="navbar-section right">
        {#each rightItems as item}
            <a href={item.href} class="navbar-link">{item.label}</a>
        {/each}
    </div>
</nav>

<style>
    .navbar {
        display:flex;
        align-items: center;
        justify-content: space-between;
        height: 5rem;
        background-color:unset;
        padding: 0 2rem;
    }
    .navbar-section {
        display:flex;
        gap: 1rem;
    }
    .center {
        flex: 1;
        justify-content: center;
        display: flex;
    }
    .left {
        justify-content: flex-start;
    }
    .right {
        justify-content: flex-end;
    }
    .navbar-link {
        text-decoration: none;
        font-weight: 500;
        transition: color 0.2s;
    }
    .navbar-link:hover {
        color: #0070f3;
    }
</style>