<!-- Header content -->

<div class="header">
    <slot name="header" />
</div>
<div class="content">
    <slot name="content" />
</div>

<style>
    .header {
        position: sticky;
        top: 0;
        left: 0;
        right: 0;
        height: 10%;
        background: linear-gradient(
            0deg,
            rgba(21, 32, 55, 0.603) 0%,
            rgba(8, 56, 160, 0.692) 100%
        );
        
        box-shadow: 0 4px 12px 0 rgba(139, 139, 139, 0.26);
        backdrop-filter: blur(20px) saturate(54%);
        z-index: 1;
    }

    .content {
        position: absolute;
        top: 10%;
        border: 1px solid #f0f0f0;
        padding: 20px;
        height: 100%;
        margin: 5% 10%;
        background-color: rgba(21, 32, 55, 0.32);
        border: 1px solid rgba(255, 255, 255, 0.125);
        width: 80%;
        border-radius: 12px;
        backdrop-filter: blur(4px) saturate(54%);
        -webkit-backdrop-filter: blur(4px) saturate(54%);
    }
</style>
