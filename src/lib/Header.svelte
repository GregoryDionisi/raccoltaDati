<script>
    let { nome } = $props();
</script>

<div class="text-center my-8">
    <h1 class="text-4xl font-bold text-primary">
        Form di {nome ? nome : "User"}
    </h1>
</div>
