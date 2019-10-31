<script>
  // labelWithStyle {
  // labelText
  // labelClass
  //}
  export let labelWithStyle;
  export let handler;
</script>

<span on:click={() => handler(labelWithStyle)} class={labelWithStyle.className}>
  {labelWithStyle.labelText}
</span>
&nbsp;
