<script lang="ts">
    import { answerStore } from "../stores";

    $: correctCount = Object.values($answerStore).filter(v => v == 0).length;
</script>

<p>Your score is {correctCount}</p>
