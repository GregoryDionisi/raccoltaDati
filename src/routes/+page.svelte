<script>
    import Header from "$lib/Header.svelte";

    let formState = $state({ //$state variabile reattiva che reagisce ai cambiamenti della UI
        answers: {},
        step: 0,
        error: "",
    });

    const QUESTION = [
        {
            id: "nome",
            question: "Qual è il tuo nome?",
            type: "text"
        },
        {
            id: "eta",
            question: "Quanti anni hai?",
            type: "number"
        },
        {
            id: "presentazione",
            question: "Scrivi una breve presentazione su di te",
            type: "textarea"
        },
        {
            id: "nazione",
            question: "In quale paese vivi?",
            type: "select",
            options: ["Italia", "Francia", "Spagna", "Germania", "Altro"]
        },
        {
            id: "azienda",
            question: "In quali aziende hai lavorato?",
            type: "select",
            options: ["Google", "Microsoft", "Amazon", "Altro"]
        },
        {
            id: "disponibilita",
            question: "Sei disponibile a lavorare da remoto?",
            type: "radio",
            options: ["Vero", "Falso"]
        },
        {
            id: "esperienza",
            question: "Quanti anni di esperienza lavorativa hai?",
            type: "radio",
            options: ["Nessuna", "1-2 anni", "3-5 anni", "Più di 5 anni"]
        }
    ];

    function nextStep(id) {
        if (formState.answers[id]) {
            formState.step += 1;
            formState.error = "";
        } else {
            formState.error = `Per favore inserire ${id}`;
        }
    }

    function resetForm() {
        formState.answers = {};
        formState.step = 0;
        formState.error = "";
    }
</script>

<Header nome={formState.answers?.nome}/>

<div class="container mx-auto p-4">
    {#if formState.step >= QUESTION.length}
        <p class="text-center text-2xl font-semibold">Thank you for your {formState.answers.nome} answers!</p>
        <!-- Riepilogo risposte -->
        <div class="my-8 p-6 border border-gray-300 rounded-lg shadow-lg">
            <h2 class="text-2xl font-semibold mb-4">Riepilogo delle tue risposte</h2>
            <ul class="space-y-4">
                {#each QUESTION as {id, question}, index}
                    {#if formState.answers[id]}
                        <li class="flex justify-between">
                            <span class="font-medium">{question}</span>
                            <span>{formState.answers[id]}</span>
                        </li>
                    {/if}
                {/each}
            </ul>
            <div class="mt-6 text-center">
                <button 
                    class="btn btn-primary" 
                    onclick={resetForm}>
                    Torna al primo passo
                </button>
            </div>
        </div>
    {:else}
        <p class="text-center text-xl">Step {formState.step + 1} di {QUESTION.length}</p>
    {/if}
    
    {#each QUESTION as {id, question, type, options}, index}
        {#if formState.step === index} <!-- Per ogni domanda, verifica se formState.step corrisponde all'indice corrente. -->
            <div class="my-6 p-6 border border-gray-300 rounded-lg shadow-lg">
                <h2 class="text-lg font-semibold mb-4">{question}</h2>
                {@render formStep({id, question, type, options})}
                {#if formState.error}
                    <p class="text-red-500">{formState.error}</p>
                {/if}
                <div class="mt-4 text-center">
                    <button 
                        class="btn btn-primary"
                        onclick={() => nextStep(id)}>
                        Next
                    </button>
                </div>
            </div>
        {/if}
    {/each}
</div>


<!-- {JSON.stringify(formState)} --> <!--utilizzato per il debug-->

 
{#snippet formStep ({id, question, type, options})}
<div class="space-y-4">
    <label for={id} class="block text-md font-medium">{question}</label>
    {#if type === "textarea"}
        <textarea 
            id={id} 
            bind:value={formState.answers[id]} 
            rows="4" 
            class="textarea textarea-bordered w-full" 
            placeholder="Inserisci qui la tua risposta...">
        </textarea>
    {:else if type === "select"}
        <select 
            id={id} 
            bind:value={formState.answers[id]} 
            class="select select-bordered w-full">
            <option value="">-- Seleziona {id} esistente --</option>
            {#each options as option}
                <option value={option}>{option}</option>
            {/each}
        </select>
    {:else if type === "radio"}
        <div class="space-y-2">
            {#each options as option}
                <label class="flex items-center space-x-2">
                    <input
                        type="radio"
                        name={id}
                        value={option}
                        bind:group={formState.answers[id]}
                        class="radio"
                    />
                    <span>{option}</span> <!--tutti i radio buttons hanno lo stesso nome in modo che quando viene selezionato uno gli altri vengono deselezionati (comportamento di default dei radio buttons)-->
                </label> <!--bind:group utilizzato quando vuoi associare più input dello stesso gruppo a una singola variabile  e si usa solitamente con radio buttons e checkbox-->
            {/each}
        </div>
    {:else}
        <input 
            {type} 
            id={id} 
            bind:value={formState.answers[id]} 
            class="input input-bordered w-full"
        />
    {/if}
</div>
{/snippet}
