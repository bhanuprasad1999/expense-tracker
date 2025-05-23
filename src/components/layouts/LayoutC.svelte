<!-- Center content -->

<div class="content glassmorphism">
    <slot name="content"/>
</div>

<style>
    .content {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        border: 1px solid #f0f0f0;
        padding: 20px;
        height: 40rem;
        max-width: 40rem;
        margin: 2% 0;
        width: 100%;
    }

    .glassmorphism {
        backdrop-filter: blur(4px) saturate(54%);
        -webkit-backdrop-filter: blur(4px) saturate(54%);
        background-color: rgba(21, 32, 55, 0.32);
        border-radius: 12px;
        border: 1px solid rgba(255, 255, 255, 0.125);
    }
</style>
