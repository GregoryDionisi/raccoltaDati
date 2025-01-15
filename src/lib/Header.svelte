<script>
    let { nome } = $props();
</script>

<div class="text-center my-8">
    <h1 class="text-4xl font-bold text-primary">
        {nome ? nome : "User"}'s Form
    </h1>
</div>
