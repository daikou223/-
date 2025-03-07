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
import nenndo from './assets/nenndo.png';
import rennga from './assets/rennga.png';
//import rofire from './assets/rofire.png';
//import rofir from './assets/rofir.png';


import wood from './assets/wood.png';
import mugi from './assets/mugi.png';
import stone from './assets/stone.png';
import renga from './assets/renga.png';

// 変数管理部分*********************************
let intervalId: number | null = null; // Interval ID を保存
const debug: boolean = false;

class Fild{
  image: string;
  constructor(image:string){
    this.image = image;
  }
  doClick(matel:number){
    switch(matel){
      default: return this.nodoing();
    }
  }
  gowinter(){
    return new Winter();
  }
  goTime():Fild{
    return this;
  }
  nodoing(){
    console.log("操作未割り当て")
    return this;
  }
}

class Winter extends Fild{
  constructor(){
    super(winter);
  }
  goTime(): Fild{
    if(Math.random()<0.3){
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
  goTime(){
    if(Math.random() < 0.3){
      return new Stone();
    }else{
      return this;
    }
  }
  doClick(matel){
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
  plant(){
    if(handNum.value[0]>0){
      handNum.value[0]--;
      return new Coty();
    }
    return new Dirt();
  }
  set(){
    if(handNum.value[2]>5){
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
  goTime(){
    if(Math.random()<0.3){
      return new Tree();
    }
    return this;
  }
}

class Tree extends Fild{
  constructor(){
    super(tree);
  }
  doClick(matel){
    switch(matel){
      default:
        return this.harvest();
    }
  }
  gowinter(){
    return new Deadtree()
  }
  harvest(){
    handNum.value[0] += (Math.floor(Math.random()*5)+3)*mag.value;
    return new Dirt()
  }
}

class Deadtree extends Fild{
  constructor(){
    super(deadtree);
  }
  doClick(matel){
    switch(matel){
      case 3:
        return this.ignition();
      default:
        return this;
    }
  }
  goTime(){
    if(Math.random() < 0.5){
      return new Kusa()
    }
    return this;
  }
  ignition(){
    console.log(handNum.value[2],"発火")
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
  goTime(){
    return new Dirt();
  }
  doClick(matel){
    handNum.value[2] += mag.value;
    return new Dirt();
  }
}

class Kusa extends Fild{
  constructor(){
    super(kusa)
  }
  goTime(){
    if(Math.random() <0.3){
      return new Ine()
    }
    return this;
  }
  doClick(matel){
    switch(matel){
      case 3:
        return this.crush();
        break;
      default:
        return this;
    }
  }
  crush(){
    if(handNum.value[2] > 0 ){
      handNum.value[2]--
      return new Nenndo();
    }
  }
}
class Ine extends Fild{
  constructor(){
    super(ine)
  }
  doClick(matel){
    handNum.value[1] += (Math.floor(Math.random()*5)+3)*mag.value
    return new Dirt()
  }
}

class Fire extends Fild{
  constructor(){
    super(fire);
  }
  goTime(){
    if(Math.floor(Math.random()<0.3)){
      return new Fir();
    }
    return this;
  }
}

class Fir extends Fild{
  constructor(){
    super(fir);
  }
  goTime(){
    if(Math.floor(Math.random()<0.3)){
      return new Dirt();
    }
    return this;
  }
  doClick(matel){
    switch(matel){
      case 2:
        return this.stoke()
      default:
        return this
    }
  }
  stoke(){
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
  goTime(){
    if(Math.floor(Math.random()<0.3)){
      return new Gosinnboku();
    }
    return this;
  }
}

class Gosinnboku extends Fild{
  constructor(){
    super(gosinnboku);
  }
  goTime(){
    if(Math.floor(Math.random()<0.3)){
      mag.value++;
    }
    return this;
  }
}

class Stones extends Fild{
  constructor(){
    super(stones);
  }
  doClick(matel){
    switch(matel){
      case 1:
        return this.plant()
      default:
        return this
    }
  }
  plant(){
    if(handNum.value[0] > 0){
      handNum.value[0]--;
      return new Gcoty(); 
    }
    return this;
  }
}

class Nenndo extends Fild{
  constructor(){
    super(nenndo)
  }
  goTime(){
    if(Math.random() < 0.3){
      return new Rennga();
    }
    return this;
  }
}

class Rennga extends Fild{
  constructor(){
    super(rennga)
  }
  doClick(){
    handNum.value[3] += mag.value;
    return new Dirt();
  }
}
// ハンドにある場合の CSS クラス
const materialcolors: string[] = ["green", "yellow", "gray","orange"];
const selectMaterialcolors: string[] = ["selectGreen", "selectYellow", "selectGray","selectOrange"];
const darkmaterialcolors: string[] = ["darkgreen", "darkyellow", "darkGray","darkOrange"];

// 画像リスト
const materialJpgs: string[] = [wood, mugi, stone,renga];

// 状態管理
let handNum = ref<number[]>([1, 0, 0, 0]);
let selection = ref<number>(0);
let time = ref<number>(0);
let mag = ref<number>(1);
let fild_condition: Fild[] = ref<Fild[]>([new Dirt(),new Dirt(),new Dirt(),new Dirt()]);

//関数*********************************************
//アイテムを選択したときの関数
function select(selectTo:number){
  if(selection.value !== selectTo){
    selection.value = selectTo
  }else if(selection.value == selectTo){
    selection.value = 0;
  }
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
    //冬の実装
    //火が存在するなら、冬を迎えない
    let winterFlag = true;
    for(let i = 0;i<4;i++){
      if(fild_condition.value[i] instanceof Fire){
        winterFlag = false
      }
    }
    console.log(winterFlag);
    if((time.value+3)%12+1 == 1 && winterFlag){
      for(let i = 0;i<4;i++){
        fild_condition.value[i] = fild_condition.value[i].gowinter();
      }
    }else{
      //時間経過
      console.log(fild_condition.value);
      for(let i = 0;i<4;i++){
        fild_condition.value[i] = fild_condition.value[i].goTime();
      }
    }
  }, debug ? 10000:3000)
}

function click(fildNum:number){
  fild_condition.value[fildNum] = fild_condition.value[fildNum].doClick(selection.value);
  if(handNum.value[selection.value-1] <= 0){
    selection.value = 0;
  }
  console.log(fild_condition.value);
}
// コンポーネントがマウントされたら定期実行を開始
onMounted(fetchDataInInterval)
</script>

<template>
  {{ Math.floor(time / 12)+1 }}年度{{ (time+3)%12+1 }}月
  <!-- フィールドの状態を描画 -->
  <table class = "rey">
    <tr>
      <td>
        <img v-bind:src = "fild_condition[0].image" v-on:click = "click(0)"/>
      </td>
      <td>
        <img v-bind:src = "fild_condition[1].image" v-on:click = "click(1)"/>
      </td>
    </tr>
    <tr>
      <td>
        <img v-bind:src ="fild_condition[2].image" v-on:click = "click(2)"/>
      </td>
      <td>
        <img v-bind:src = "fild_condition[3].image" v-on:click = "click(3)"/>
      </td>
    </tr>
  </table>
  <!-- 手札を描画 -->
  <table class = "rey">
    <tr>
      <td v-for = "matel in materialJpgs.length" >

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
  <p>倍率:x{{ mag }}</p>
</template>