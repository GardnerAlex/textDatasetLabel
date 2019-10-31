<script>
  // labelWithStyle {
  // labelText
  // labelClass
  //}
  export let labelsWithStyles;
  export let selectedOption;
  // индекс элемента в массиве размеченных словосочетаний
  export let selectedLabelsGroupIndex;
  export let handler;
  let selected;
  console.log("selectedOption", selectedOption);
</script>

<select
  bind:value={selectedOption}
  selected={selectedOption}
  on:change={() => handler(selectedLabelsGroupIndex, selectedOption)}>
  {#each labelsWithStyles as selectOption}
    <option value={selectOption.labelText}>{selectOption.labelText}</option>
  {/each}
</select>
