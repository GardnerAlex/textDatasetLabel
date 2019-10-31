<script>
  import TopSelectorComponent from "./TopSelectorComponent.svelte";
  import ProcessedTextComponent from "./ProcessedTextComponent.svelte";
  import SelectedTextGroupComponent from "./SelectedTextGroupComponent.svelte";
  import DeleteLabelGroup from "./DeleteLabelGroupComponent.svelte";
  import LabelSelectorComponent from "./LabelSelectorComponent.svelte";
  let groupId = 0;
  let labelsWithStyles = [];
  let labels = "option1, option2, option3";
  let cssClasses = ["class1", "class2", "class3", "selCandidate"];
  let textForProcessing = "Some interesting fucking text";
  let processedText = {};
  let selectCandidatesList = [];
  let selectedList = [];
  let defaultWordProps = {
    selectCandidate: false,
    selected: false,
    className: "",
    labelText: "",
    groupId: undefined
  };

  function prepareTextForProcessing(text) {
    for (let [index, word] of text.split(" ").entries()) {
      processedText[index] = { id: index, text: word, ...defaultWordProps };
    }
    console.log("prepareTextForProcessing: result: ", processedText);
  }

  function prepareLabelsWithStyles(labels) {
    for (let [index, text] of labels.split(", ").entries()) {
      labelsWithStyles.push({
        labelText: text.trim(),
        className: cssClasses[index]
      });
    }
    console.log("preparedLabelsWithStyles: result: ", labelsWithStyles);
  }

  function topSelectorHandler(labelObject) {
    // в рамках одной сессии до обновления окна - уникальный id размеченной группы слов
    groupId++;
    let tempSelectionGroup = [];
    // если есть 1 или больше кандидатов на разметку, записывается в tempSelectionGroup
    // это нужно для группового выделения нескольких слов в размечаемом тексте
    for (let id of Object.keys(processedText)) {
      if (processedText[id].selectCandidate) {
        processedText[id] = {
          ...processedText[id],
          selectCandidate: false,
          selected: true,
          groupId,
          ...labelObject
        };
        tempSelectionGroup.push(processedText[id]);
      }
    }
    // Костыль: хэндлер срабатывает всегда, но если нет объектов selectCandidate
    // таким образом никаких действий не выполняется
    if (tempSelectionGroup.length > 0) {
      selectedList.push(tempSelectionGroup);
      selectedList = [...selectedList];
    }
    console.log("topSelectorHandler selectedList:", selectedList);
  }

  function resetWordProps(wordId) {
    console.log("reset", wordId);
    processedText[wordId] = {
      text: processedText[wordId].text,
      id: processedText[wordId].id,
      ...defaultWordProps
    };
    console.log("resetWordProps", processedText[wordId]);
  }

  function deleteLabelsGroupHandler(groupIndex) {
    console.log("deleteLabelsGroupHandler", groupIndex);
    if (groupIndex !== undefined) {
      for (let element of selectedList[groupIndex]) {
        console.log("deleteLabelsGroupHandler", element);
        resetWordProps(element.id);
      }
      selectedList.splice(groupIndex, 1);
      selectedList = selectedList;
    }
  }

  function selectLabelsGroupHandler(groupIndex, labelTextSelected) {
    console.log("selectLabelsGroupHandler", groupIndex, labelTextSelected);
    const labelWithStyleObject = labelsWithStyles.filter(obj => {
      return obj.labelText === labelTextSelected;
    })[0];
    console.log("selectLabelsGroupHandler filter", labelWithStyleObject);
    for (let word of selectedList[groupIndex]) {
      processedText[word.id].labelText = labelWithStyleObject.labelText;
      processedText[word.id].className = labelWithStyleObject.className;
      console.log(processedText);
      // word = { word, ...labelWithStyleObject };
    }
  }

  function processedTextCLickHandler(id) {
    // id слова размеченной фразы
    if (!processedText[id].selected) {
      if (!processedText[id].selectCandidate) {
        console.log(
          "processedTextCLickHandler, добавили: ",
          processedText[id].text
        );
        processedText[id].selectCandidate = true;
        processedText[id].className = "selCandidate";
      } else {
        console.log(
          "processedTextCLickHandler, удалили: ",
          processedText[id].text
        );
        processedText[id].selectCandidate = false;
        processedText[id].className = "";
      }
    }
    processedText = { ...processedText };
    console.log(processedText);
  }
  prepareTextForProcessing(textForProcessing);
  prepareLabelsWithStyles(labels);
</script>

<style>
  h1 {
    color: purple;
  }
</style>

<h1>Hello!11</h1>
<div>
  {#each labelsWithStyles as labelWithStyle}
    <TopSelectorComponent handler={topSelectorHandler} {labelWithStyle} />
  {/each}
</div>
<div>
  {#each Object.keys(processedText) as id}
    <ProcessedTextComponent
      handler={processedTextCLickHandler}
      text={processedText[id]}
      className={processedText[id].className}
      wordId={id} />
  {/each}
</div>
<div>
  {#each selectedList as selectedLabelsGroup, i}
    <div>
      {#each selectedLabelsGroup as text}
        <SelectedTextGroupComponent {text} groupIndex={i} />
      {/each}
      <LabelSelectorComponent
        selectedLabelsGroupIndex={i}
        selectedOption={selectedLabelsGroup[0].labelText}
        handler={selectLabelsGroupHandler}
        {labelsWithStyles} />
      <DeleteLabelGroup groupId={i} handler={deleteLabelsGroupHandler} />
    </div>
  {/each}
</div>
