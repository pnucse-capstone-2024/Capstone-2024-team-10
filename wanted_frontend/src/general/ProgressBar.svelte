<!-- ProgressBar.svelte -->
<script>
    export let series = 0;
</script>

<style>
    .progress-container {
        width: 100%;
        background-color: #f3f3f3;
        border-radius: 10px;
        overflow: hidden;
    }

    .progress-bar {
        height: 24px;
        background-color: #4caf50;
        transition: width 0.3s ease;
        border-radius: 10px 0 0 10px;
    }
</style>

<div class="progress-container">
    <div class="progress-bar" style="width: {series}%;"></div>
</div>
