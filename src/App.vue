<script setup lang="ts">
//地面のテクスチャはクレジット表示必須 https://jp.freepik.com/free-vector/game-ground-textures_13050184.html
//画像の入れ込みを行う
import { ref, onMounted } from 'vue';

// 地面のテクスチャ（クレジット表示必須）
import dirt from './assets/dirt.jpg';
import coty from './assets/coty.png';
import tree from './assets/tree.png';
import deadtree from './assets/deadtree.png';
import winter from './assets/winter.png';
import kusa from './assets/kusa.png';
import ine from './assets/ine.png';
import isi from './assets/isi.png';
import fire from './assets/fire.png';
import stones from './assets/stones.png';
import gcoty from './assets/gosinnbokucoty.png';
import gosinnboku from './assets/gosinnboku.png';
import fir from './assets/fir.png';


import wood from './assets/wood.png';
import mugi from './assets/mugi.png';
import stone from './assets/stone.png';

// 変数管理部分*********************************
let intervalId: number | null = null; // Interval ID を保存
const debug: boolean = false;
let frag = ref<number>(0);
let dateClass = ref<string>("dateblack");
let scores = ref<number[]>([]); 
const updateTime = 1000;
const monthTime = 3000;
const defparsent =  0.1/(monthTime/updateTime)*5;
const middleParsent =  0.1/(monthTime/updateTime)*10;
let endmonth = ref<number>(69);
let extentNeeds = ref<number[]>([10,10]);
let gosinnbokuLv = ref<number>(1);
//クラス管理************************************
class Fild{
  image: string;
  constructor(image:string){
    this.image = image;
  }
  doClick(matel:number):Fild{
    switch(matel){
      default: return this.nodoing();
    }
  }
  gowinter():Fild{
    return new Winter();
  }
  gotime():Fild{
    return this;
  }
  nodoing():Fild{
    console.log("操作未割り当て")
    return this;
  }
}

class Winter extends Fild{
  constructor(){
    super(winter);
  }
  gotime():Fild{
    if(Math.random()<defparsent || (time.value/(monthTime/updateTime))%10 >= 6){
      return new Dirt();
    }else{
      return this;
    }
  }
}

class Dirt extends Fild{
  constructor(){
    super(dirt);
  }
  gotime():Fild{
    if(Math.random() < defparsent){
      return new Stone();
    }else{
      return this;
    }
  }
  doClick(matel:number):Fild{
    switch(matel){
      case 1:
        return this.plant();
        break;
      case 3:
        return this.set();
        break;
      default:
        return this.nodoing();
        break;
    }
  }
  plant():Fild{
    if(handNum.value[0]>0){
      handNum.value[0]--;
      return new Coty();
    }
    return new Dirt();
  }
  set():Fild{
    if(handNum.value[2]>=5){
      handNum.value[2] -= 5;
      return new Stones();
    }
    return this
  }
}

class Coty extends Fild{
  constructor(){
    super(coty);
  }
  gotime():Fild{
    if(Math.random()<defparsent){
      return new Tree();
    }
    return this;
  }
}

class Tree extends Fild{
  constructor(){
    super(tree);
  }
  doClick(matel:number):Fild{
    switch(matel){
      default:
        return this.harvest();
    }
  }
  gowinter():Fild{
    return new Deadtree()
  }
  harvest():Fild{
    handNum.value[0] += (Math.floor(Math.random()*5)+3)*mag.value;
    return new Dirt()
  }
}

class Deadtree extends Fild{
  constructor(){
    super(deadtree);
  }
  doClick(matel:number):Fild{
    switch(matel){
      case 3:
        return this.ignition();
      default:
        return this;
    }
  }
  gotime():Fild{
    if(Math.random() < defparsent){
      return new Kusa()
    }
    return this;
  }
  ignition():Fild{
    if(handNum.value[2] >= 1){
      handNum.value[2]--;
      return new Fire()
    }
    return this;
  }
}

class Stone extends Fild{
  constructor(){
    super(isi);
  }
  gotime():Fild{
    if(Math.random()<defparsent){
      return new Dirt();
    }
    return this
  }
  doClick(matel:number):Fild{
    handNum.value[2] += mag.value;
    return new Dirt();
  }
}

class Kusa extends Fild{
  constructor(){
    super(kusa)
  }
  gotime():Fild{
    if(Math.random() <defparsent){
      return new Ine()
    }
    return this;
  }
  doClick(matel:number):Fild{
    switch(matel){
      default:
        return this;
    }
  }
}

class Ine extends Fild{
  constructor(){
    super(ine)
  }
  doClick(matel:number):Fild{
    handNum.value[1] += (Math.floor(Math.random()*5)+3)*mag.value
    return new Dirt()
  }
}

class Fire extends Fild{
  level:number;
  constructor(){
    super(fire);
    this.level = 3;
  }
  gotime():Fild{
    if(Math.random()<defparsent){
      this.level-=1;
      if(this.level == 0){
        return new Fir();
      }
      return this
    }
    return this;
  }
}

class Fir extends Fild{
  level:number;
  constructor(){
    super(fir);
    this.level = 3
  }
  gotime():Fild{
    if(Math.random()<defparsent){
      this.level-=1;
      if(this.level == 0){
        return new Fir();
      }
      return this
    }
    return this;
  }
  doClick(matel:number):Fild{
    switch(matel){
      case 2:
        return this.stoke()
      default:
        return this
    }
  }
  stoke():Fild{
    if(handNum.value[1] > 0){
      handNum.value[1]--;
      return new Fire(); 
    }
    return this;
  }
}

class Gcoty extends Fild{
  constructor(){
    super(gcoty);
  }
  gotime():Fild{
    if(Math.random()<defparsent){
      return new Gosinnboku();
    }
    return this;
  }
}

class Gosinnboku extends Fild{
  constructor(){
    super(gosinnboku);
  }
  gotime():Fild{
    if(Math.random()<1-(1-defparsent)**gosinnbokuLv.value){
      mag.value += 1;
    }
    return this;
  }
  doClick(matel:number):Fild{
    handNum.value[0]+= handNum.value[0] += (Math.floor(Math.random()*5)+3)*mag.value;
    return new Dirt()
  }
}

class Stones extends Fild{
  constructor(){
    super(stones);
  }
  doClick(matel:number):Fild{
    switch(matel){
      case 1:
        return this.plant()
      default:
        return this
    }
  }
  plant():Fild{
    if(handNum.value[0] > 0){
      handNum.value[0]--;
      return new Gcoty(); 
    }
    return this;
  }
}


// ハンドにある場合の CSS クラス
const materialcolors: string[] = ["green", "yellow", "gray","orange"];
const selectMaterialcolors: string[] = ["selectGreen", "selectYellow", "selectGray","selectOrange"];
const darkmaterialcolors: string[] = ["darkgreen", "darkyellow", "darkGray","darkOrange"];

// 画像リスト
const materialJpgs: string[] = [wood, mugi, stone];

// 状態管理
let handNum = ref<number[]>([1, 0, 0]);
let selection = ref<number>(0);
let time = ref<number>(0);
let mag = ref<number>(1);
let fild_condition = ref<Fild[]>([new Dirt(),new Dirt(),new Dirt(),new Dirt()]);

//関数*********************************************
//アイテムを選択したときの関数
function select(selectTo:number){
  if(handNum.value[selectTo-1] != 0){
    if(selection.value !== selectTo){
      selection.value = selectTo
    }else if(selection.value == selectTo){
      selection.value = 0;
    }
  }
}
//フィールドクリック時
function click(fildNum:number){
  fild_condition.value[fildNum] = fild_condition.value[fildNum].doClick(selection.value);
  if(handNum.value[selection.value-1] <= 0){
    selection.value = 0;
  }
}
//延長クリック時
function extent(){
  let extendfrag = 1
  if(handNum.value[1] < extentNeeds.value[1]){
    extendfrag = 0
  }
  if(handNum.value[0] < extentNeeds.value[0]){
    extendfrag = 0
  }
  if(extendfrag == 1){
    handNum.value[1]-= extentNeeds.value[1];
    handNum.value[0]-= extentNeeds.value[0];
    extentNeeds.value[1] = extentNeeds.value[1]*2+Math.floor(Math.random()*5);
    extentNeeds.value[0] = extentNeeds.value[0]*3+Math.floor(Math.random()*5);
    endmonth.value = endmonth.value + 10;
    dateClass.value = "dateblack";
  }
}
function gacha(){
  if(handNum.value[2] >= gosinnbokuLv.value**3){
    handNum.value[2] -= gosinnbokuLv.value**3;
    gosinnbokuLv.value += 1;
  }
}
//初期化関数
const startgame = ()=>{
  let scoresString:string[] = []
  if(localStorage.getItem("score")){
    const storedScore = localStorage.getItem("score");
    if(storedScore !== null){
      scoresString = storedScore.split(" ");
    }
  }
  scores.value = []
  for(let i = 0;i<scoresString.length;i++){
    let scoreNumber = Number(scoresString[i])
    let p = 0
    for(let j = 0;j<scores.value.length;j++){
      if(scores.value[j] > scoreNumber){
        p++
      }
    }
    scores.value.splice(p,0,scoreNumber)
  }
  dateClass.value = "dateblack";
  handNum.value =[1, 0, 0];
  selection.value = 0;
  time.value = 1;
  mag.value = 1;
  fild_condition.value = [new Dirt(),new Dirt(),new Dirt(),new Dirt()];
  frag.value = 1
  fetchDataInInterval();
}
// コンポーネントがマウントされたら定期実行を開始
let scoresString:string[] = []
if(localStorage.getItem("score")){
  const storedScore = localStorage.getItem("score");
  if(storedScore !== null){
    scoresString = storedScore.split(" ");
  }
}
scores.value = []
for(let i = 0;i<scoresString.length;i++){
  let scoreNumber = Number(scoresString[i])
  let p = 0
  for(let j = 0;j<scores.value.length;j++){
    if(scores.value[j] > scoreNumber){
      p++
    }
  }
  scores.value.splice(p,0,scoreNumber)
}
//時間変化を実装
const fetchDataInInterval = () => {
  intervalId = setInterval(() => {
    if(debug){
      console.log("手持ち",handNum.value);
      console.log("フィールド",fild_condition.value);
      console.log("倍率",mag.value);
    }
    time.value++;
    if(time.value/(monthTime/updateTime) > endmonth.value){
      frag.value = 0
      if (intervalId) {
        clearInterval(intervalId);
        intervalId = null;
      }
      scores.value.push(handNum.value[0]+handNum.value[1]+handNum.value[2])
      localStorage.setItem("score",scores.value.join(" "))
      window.confirm(`最終得点は${handNum.value[0]+handNum.value[1]+handNum.value[2]}点でした`)
      let scoresString:string[] = []
      if(localStorage.getItem("score")){
        const storedScore = localStorage.getItem("score");
        if(storedScore !== null){
          scoresString = storedScore.split(" ");
        }
      }
  scores.value = []
  for(let i = 0;i<scoresString.length;i++){
    let scoreNumber = Number(scoresString[i])
    let p = 0
    for(let j = 0;j<scores.value.length;j++){
      if(scores.value[j] > scoreNumber){
        p++
      }
    }
    scores.value.splice(p,0,scoreNumber)
  }
    }
    //最後が近くなったら赤くする
    if(time.value/(monthTime/updateTime) >= endmonth.value-9){
      dateClass.value = "red"
    }
    //冬の実装
    //火が存在するなら、冬を迎えない
    let winterFlag = true;
    for(let i = 0;i<4;i++){
      if(fild_condition.value[i] instanceof Fire){
        winterFlag = false
      }
    }
    if((time.value/(monthTime/updateTime))%10 == 0 && winterFlag){
      for(let i = 0;i<4;i++){
        fild_condition.value[i] = fild_condition.value[i].gowinter();
      }
    }else{
      //時間経過
      for(let i = 0;i<4;i++){
        fild_condition.value[i] = fild_condition.value[i].gotime();
      }
    }
  }, updateTime)
}
//キーボード走査
function eventLit(){
  document.addEventListener('keyup', (event) => {
  if(frag.value ==1){
    if (event.key === 'o') {
      click(1);
    } 
    if (event.key === 'i') {
      click(0);
    } 
    if(event.key === 'l'){
      click(3)
    }
    if (event.key === 'k') {
      click(2);
    } 
    if (event.key === '0') {
      select(0);
    } 
    if (event.key === '1') {
      select(1);
    } 
    if (event.key === '2') {
      select(2);
    } 
    if (event.key === '3') {
      select(3);
    } 
    if (event.key === 'q') {
      extent();
    } 
    if (event.key === 'w') {
      gacha();
    } 
  }
  if(event.key === ' '){
    if(frag.value == 0){
      startgame();
     }
  }
  })};
onMounted(()=>{
  eventLit()
})
</script>

<template>
  <div v-if = "frag == 0">
    <button @click = "startgame" class = "startButton">開始</button>
    <table>
    <tbody>
      <tr v-for = "s in Math.min(scores.length,5)">
        <td>{{s}}位:</td><td>{{ scores[s-1] }}</td>
      </tr>
    </tbody>
  </table>
    </div>
    <div v-else>
      <div v-bind:class = "dateClass">
  <div className = "month" >{{ Math.floor(time/(monthTime/updateTime)) }} 月 <br>&nbsp;/ {{endmonth}} 月<br>
    <p>倍率:x{{ mag }}</p> </div>
  <div :style = "{ backgroundColor:`rgb(256,${0.90**((endmonth-70)/100)*250},0)`,border:`2px solid black`,width:`100px`,height:`80px`,margin:`10px`}" v-on:click="extent">
    延長 {{endmonth}}&rarr;{{endmonth+10}}<br>
    <img className = "magn" v-bind:src = "wood"/>
    x{{extentNeeds[0]}}<br>
    <img className = "magn" v-bind:src = "mugi"/>
    x{{extentNeeds[1]}}
  </div>
  <div :style = "{ backgroundColor:`rgb(200,200,200)`,border:`2px solid black`,width:`100px`,height:`80px`,margin:`10px`}" v-on:click="gacha">
    ご神木ランク {{gosinnbokuLv}}&rarr;{{gosinnbokuLv+1}}<br>
    <img className = "magn" v-bind:src = "stone"/>
    x{{gosinnbokuLv**3}}<br>
  </div>
  </div>
  <!-- フィールドの状態を描画 -->
  <table class = "rey">
    <tr>
      <td className = "fild">
        <img v-bind:src = "fild_condition[0].image" v-on:click = "click(0)"/>
      </td>
      <td className = "fild">
        <img v-bind:src = "fild_condition[1].image" v-on:click = "click(1)"/>
      </td>
    </tr>
    <tr >
      <td className = "fild">
        <img v-bind:src ="fild_condition[2].image" v-on:click = "click(2)"/>
      </td>
      <td className = "fild">
        <img v-bind:src = "fild_condition[3].image" v-on:click = "click(3)"/>
      </td>
    </tr>
  </table>
  <!-- 手札を描画 -->
  <table class = "rey">
    <tr>
      <td v-for = "matel in materialJpgs.length">
        <div v-if="matel== selection" v-on:click = "select(matel)">
        x{{ handNum[matel-1] }}
        <div v-bind:class = "selectMaterialcolors[matel-1]">
          <img v-bind:src = "materialJpgs[matel-1]"/>
        </div>
      </div>

        <div v-else-if="handNum[matel-1]>0" v-on:click = "select(matel)">
        x{{ handNum[matel-1] }}
        <div v-bind:class = "materialcolors[matel-1]">
          <img v-bind:src = "materialJpgs[matel-1]"/>
        </div>
      </div>

      <div v-if="handNum[matel-1]==0">
        x{{ handNum[matel-1] }}
        <div v-bind:class = "darkmaterialcolors[matel-1]">
          <img v-bind:src = "materialJpgs[matel-1]"/>
        </div>
      </div>
      </td>
    </tr>
  </table>
  <table>
    <tbody>
      <tr v-for = "s in Math.min(scores.length,5)">
        <td>{{ s }}位 : </td><td>{{ scores[s-1] }}</td>
      </tr>
    </tbody>
  </table>
  </div>
</template>