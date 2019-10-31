<script>
  export let text;
  export let handler;
  export let wordId;
  export let className;
  console.log("ProcessedTextComponent input:", text, wordId);
</script>

<span on:click={() => handler(wordId)} class={className}>{text.text}</span>
&nbsp;
