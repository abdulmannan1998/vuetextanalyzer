<template>
  <header class="d-flex flex-column flex-md-row align-items-center p-3 px-md-4 mb-3 bg-body border-bottom shadow-sm">
    <p class="h5 my-0 me-md-auto fw-normal">Text Analyzer</p>
  </header>
  <section class="container" >
    <div  class="wrapper">
      <div class="txt-cont">
        <textarea v-model="newText" placeholder="Enter text here..." @focus="flagchange" :class="{ animate : flag} "/>
        <button class="btn-grad" @click="result">Submit</button>
        <div class="display-result">
          <div class="result-cont" v-for="option in filteredlist" :key="option.id">
            <p class="res-opt"> {{option.optionName}}</p>
            <p class="res-res">{{option.result}}</p>
          </div>
        </div>
      </div>
      <div class="rd-cont" >
        <label class="text" style="font-weight:bold; font-size: 1.1vw;">Choose Your Options</label>
        <div class="rd-wrapper" v-for="option in options" :key="option.id">
          <input class="state" type="checkbox" name="app" :checked="option.checked" v-on:change="markComplete(option)" :id="option.id" :value="option.id">
          <label class="label" :for="option.id">
            <div class="indicator"></div>
            <span class="text">{{option.optionName}}</span>
          </label>
        </div>
      </div>
    </div>
    <footer class="pt-4 my-md-5 pt-md-5 border-top">Text Analyzer |   Developed by Mannan Abdul   | Vue + Bootstrap</footer>
  </section>
</template>

<script>
import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'
import { ref } from 'vue';

export default {
  setup() {
    let buttonFlag = false;
    let flag = ref(false);
    const newText = ref('');
    const filteredlist = ref([]);

    

    const options = [
      {
        id: 1,
        optionName: 'Word Count',
        checked: false,
        result: ''
      },
      {
        id: 2,
        optionName: 'No of Letters',
        checked: false,
        result: ''
      },
      {
        id: 3,
        optionName: 'Longest Word',
        checked: false,
        result: ''
      },
      {
        id: 4,
        optionName: 'Average Word Length',
        checked: false,
        result: ''
      },
      {
        id: 5,
        optionName: 'Reading Time (in secs)',
        checked: false,
        result: ''
      },
      {
        id: 6,
        optionName: 'Median Word Length',
        checked: false,
        result: ''
      },
      {
        id: 7,
        optionName: 'Median Word (Sorted by Length)',
        checked: false,
        result: ''
      },
      {
        id: 8,
        optionName: 'Top 5 Most Common Words',
        checked: false,
        result: ''
      },
      {
        id: 9,
        optionName: 'Text Language',
        checked: false,
        result: ''
      },

    ]

    function refreshList(){
      if(buttonFlag){
        filteredlist.value.splice(0, filteredlist.value.length);
        options.filter(option => option.checked).forEach(x => filteredlist.value.push(x));
      }
      // console.log(filteredlist);  
    }

    function flagchange(){
      flag.value = true;
    }

    function markComplete(option){
      option.checked = !option.checked;
      refreshList();
      // console.log(option);
    }

    let readSpeed; 
    let wordCount;
    let noOfLetters;
    let longest = '';
    let avg = 0;
    let lenMedian;
    let median;
    let topFive;
    let txtlang = 'en';

    function result(){
      const avgReadSpeed = 4; //taken from the internet, units words/sec
      
      readSpeed = 0; 
      wordCount = 0;
      noOfLetters = 0;
      longest = '';
      avg = 0;
      lenMedian = 0;
      median = '';
      topFive = [];
      txtlang = 'en';
      
      let words = newText.value.replace(/[^A-Za-z0-9_ğüşıöç]/g, " ").split(" ")
      words = words.filter(word => word != " " && word != "");
      wordCount = words.length;

      if(wordCount != 0){
        let letters = words.reduce(function(accumulator, currentValue){return accumulator + currentValue});
        noOfLetters = letters.length;

        words.forEach((word)=>{
          if(word.length >= longest.length)
          {
            longest = word;
          }
        });

        avg = 0;
        words.forEach((word)=>{
          avg = avg + word.length;
        });
        // avg = 0;
        avg = avg / wordCount;

        readSpeed = wordCount/avgReadSpeed;

        let wordlen = [];
        words.forEach((word, index)=>{
          wordlen[index] = word.length;
        });
        wordlen.sort((a, b) => a - b);
        const lenMid = Math.ceil(wordCount / 2);
        lenMedian = wordCount % 2 === 0 ? (wordlen[lenMid] + wordlen[lenMid - 1]) / 2 : wordlen[lenMid - 1];      

        let sortedWords = words.sort((a, b) => a.length - b.length);
        const mid = Math.ceil(wordCount / 2);
        median = sortedWords[mid - 1];

        let countfreq =[];
        words.forEach((word)=>{
          const item = countfreq.find(x => x.name === word);
          if(item != undefined)
          {
            item.freq += 1;
          }
          else {
            countfreq.push({
              name: word,
              freq: 1
            });
          }
        });

        countfreq.sort((a, b) => {
          return b.freq - a.freq
        });
        
        topFive = [];
        for(let i = 0; i < Math.min(5, countfreq.length); i++)
        {
          topFive.push(countfreq[i].name);
        }

        const letterArray = letters.split("");
        let turkArray = letterArray.filter((x) => x === 'ç' || x === 'ğ' || x === 'ş' || x === 'ö' || x === 'ı' || x === 'ü');
        if (turkArray.length != 0) {
          txtlang ='tur';
        }

        // console.log(wordCount);
        // console.log(words);
        // console.log(noOfLetters);
        // console.log(longest);
        // console.log(avg);
        // console.log(readSpeed);
        // console.log(lenMedian);
        // console.log(sortedWords);
        // console.log(median);
        // console.log(countfreq);
        // console.log(topFive);
        // console.log(txtlang);
      }
      else {
        noOfLetters = 0;
        longest = 'No Input Yet'
        avg = 0;
        readSpeed = '0 secs';
        lenMedian = 0;
        median = 'No Input Yet'
        topFive = 'No Input Yet'
        txtlang = 'No Input Yet'

        // console.log(wordCount);
        // console.log(noOfLetters);
        // console.log(longest);
        // console.log(avg);
        // console.log(readSpeed);
        // console.log(lenMedian);
        // console.log(median);
        // console.log(topFive);
        // console.log(txtlang);
      }
      refreshList();
      options[0].result = wordCount;
      options[1].result = noOfLetters;
      options[2].result = longest;
      options[3].result = avg;
      options[4].result = readSpeed;
      options[5].result = lenMedian;
      options[6].result = median;
      options[7].result = topFive.reduce(function(accumulator, currentValue){return accumulator + ", " + currentValue});
      options[8].result = txtlang;
      buttonFlag = true;
      
      
    }

    return{
      flag,
      flagchange,
      newText, 
      options,
      markComplete,
      result,
      filteredlist,
      refreshList,
      // buttonFlag,
      // readSpeed, 
      // wordCount,
      // noOfLetters,
      // longest,
      // avg,
      // lenMedian,
      // median,
      // topFive,
      // txtlang,
    };
  }
}
</script>

<style>
  @import "./css/style.css";
</style>

         