<script setup>
import {ref}  from 'vue';
import Header from '@/components/Header.vue';

//Получаем блок, чтобы потом использовать его innerHTML
const textarea = ref(null);

//Строка содержащая HTML код для отображения элементов
const htmlContent = ref('<h1>Hello World</h1> Lorem ipsum dolor sit amet consectetur adipisicing elit. Illum fugiat dolor ex laborum qui. Blanditiis, nostrum? Autem laborum nesciunt laudantium, sunt soluta et iste incidunt tenetur porro. Quod, tenetur inventore? <br><img src="https://upload.wikimedia.org/wikipedia/commons/thumb/e/ec/Mona_Lisa%2C_by_Leonardo_da_Vinci%2C_from_C2RMF_retouched.jpg/250px-Mona_Lisa%2C_by_Leonardo_da_Vinci%2C_from_C2RMF_retouched.jpg" alt=""><h1>Mona Lisa</h1>Lorem, ipsum dolor sit amet consectetur adipisicing elit. Aspernatur cum nobis dolore quasi perspiciatis dolorum?');

//Массив который будет хранить историю изменений
const htmlHistory = ref([]);
htmlHistory.value.push(htmlContent.value); //Сразу добавляем первую версию

//Индекс для перехода по истории
const currentHtmlIndex = ref(0);

//Если внутренний код изменился - сохраняем в историю и увеличиваем индекс 
function saveChanges(){
  if (htmlContent.value !== textarea.value.innerHTML){
    htmlContent.value = textarea.value.innerHTML;
    htmlHistory.value.push(htmlContent.value);
    currentHtmlIndex.value++;
  }
}

//Переходим на предыдущую версию
function previousHtml(){
  if (currentHtmlIndex.value > 0){
    currentHtmlIndex.value--;
    htmlContent.value = htmlHistory.value[currentHtmlIndex.value];
  }
}

//Переходим на следующую версию
function nextHtml(){
  if (currentHtmlIndex.value < htmlHistory.value.length - 1){
    currentHtmlIndex.value++;
    htmlContent.value = htmlHistory.value[currentHtmlIndex.value];
  }
}

//Получаем выделенный сегмент и окружаем его h1
function textToLabel(){
  const selection = window.getSelection();
  if (selection.rangeCount > 0) {
    const range = selection.getRangeAt(0);
    const h1 = document.createElement('h1');
    range.surroundContents(h1);
    selection.removeAllRanges();
    saveChanges();
  }
}

//Находим выделенный сегмент и убираем h1
function labelToText(){
  const selection = window.getSelection();
  if (selection.rangeCount > 0) {
    const range = selection.getRangeAt(0);
    const selectedText = range.toString();
    const updatedHtmlContent = htmlContent.value.replace(`<h1>${selectedText}</h1>`, selectedText);
    textarea.value.innerHTML = updatedHtmlContent;
    saveChanges();
  }
}

//Запрашиваем ссылку на картинку, если она валидна - создаём элемент и встраиваем его
function addImage(){
  const selection = window.getSelection();
  const imgUrl = prompt('Укажите ссылку на картинку');

  if (isImageURL(imgUrl) && imgUrl !== ''){
    if (selection.rangeCount > 0) {
      const range = selection.getRangeAt(0);

      const imageElement = document.createElement('img');
      imageElement.src = imgUrl;
      range.insertNode(imageElement);

      saveChanges();
    }
  }else{
    alert('Неверная ссылка. Доступные форматы: jpg, jpeg, png, gif, bmp, svg');
  }

  function isImageURL(url) {
    const allowedExtensions = ['.jpg', '.jpeg', '.png', '.gif', '.bmp', '.svg'];
    const urlLowercase = url.toLowerCase();
    return allowedExtensions.some(extension => urlLowercase.endsWith(extension));
  }
}

//Копируем HTML код в буфер обмена
function copyHhtml(){
  navigator.clipboard.writeText(htmlContent.value);
}
</script>

<template>
  <Header 
    @back="previousHtml" 
    @forward="nextHtml" 
    @label="textToLabel"
    @text="labelToText"
    @img="addImage"
    @copy="copyHhtml">
  </Header>
  <div class="container">
    <div 
      class="textarea"
      ref="textarea" 
      v-html="htmlContent" 
      contenteditable="true"
      @blur="saveChanges()">
    </div>
  </div>
</template>

<style scoped lang="sass">
.container
  width: 960px
  padding: 0px 107px
.textarea
  color: #EAEAEA
  font-family: Roboto
  font-size: 15px
  line-height: 23px  
</style>