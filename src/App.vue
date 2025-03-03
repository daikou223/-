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
import ike from './assets/ike.png';
import Gcoty from './assets/gosinnbokucoty.png';
import gosinnboku from './assets/gosinnboku.png';
import fir from './assets/fir.png';
import wood from './assets/wood.png';
import mugi from './assets/mugi.png';
import stone from './assets/stone.png';

// 変数管理部分
let intervalId: number | null = null; // Interval ID を保存
const debug: boolean = false;

// ハンドにある場合の CSS クラス
const materialcolors: string[] = ["green", "yellow", "gray"];
const selectMaterialcolors: string[] = ["selectGreen", "selectYellow", "selectGray"];
const darkmaterialcolors: string[] = ["darkgreen", "darkyellow", "darkGray"];

// 画像リスト
const materialJpgs: string[] = [wood, mugi, stone];
const grandJpgs: string[] = [
  dirt, coty, tree, deadtree, winter, kusa, ine, isi,
  fire, stones, ike, Gcoty, gosinnboku, fir
];

// 状態管理
let handNum = ref<number[]>([1, 0, 0]);
let selection = ref<number>(0);
let time = ref<number>(0);
let mag = ref<number>(1);
let conditions = ref<number[]>([0, 0, 0, 0]);

// フィールドの栄養値
const fildNutorition: number = Math.floor(Math.random() * 5) + 3;

// 操作一覧: [前の状態][アイテムごとの挙動: [遷移先番号, 各素材の増減]]
const operate: [number, number[]][][] = [
  // dirt: 0
  [[0, [0, 0, 0]], [1, [-1, 0, 0]], [5, [0, -3, 0]], [9, [0, 0, -3]]],
  // coty: 1
  [[1, [0, 0, 0]], [1, [0, 0, 0]], [1, [0, 0, 0]], [1, [0, 0, 0]]],
  // tree: 2
  [[0, [fildNutorition, 0, 0]], [0, [fildNutorition, 0, 0]], [0, [fildNutorition, 0, 0]], [0, [fildNutorition, 0, 0]]],
  // deadtree: 3
  [[3, [0, 0, 0]], [3, [0, 0, 0]], [3, [0, 0, 0]], [8, [0, 0, -1]]],
  // winter: 4
  [[4, [0, 0, 0]], [4, [0, 0, 0]], [4, [0, 0, 0]], [4, [0, 0, 0]]],
  // kusa: 5
  [[5, [0, 0, 0]], [5, [0, 0, 0]], [5, [0, 0, 0]], [5, [0, 0, 0]]],
  // ine: 6
  [[0, [0, fildNutorition, 0]], [0, [0, fildNutorition, 0]], [0, [0, fildNutorition, 0]], [0, [0, fildNutorition, 0]]],
  // stone: 7
  [[0, [0, 0, 1]], [0, [0, 0, 1]], [0, [0, 0, 1]], [0, [0, 0, 1]]],
  // fire: 8
  [[8, [0, 0, 0]], [8, [0, 0, 0]], [8, [0, 0, 0]], [0, [0, 0, -1]]],
  // stones: 9
  [[9, [0, 0, 0]], [11, [-1, 0, 0]], [5, [0, -1, 0]], [9, [0, 0, 0]]],
  // ご神木の芽: 11
  [[11, [0, 0, 0]], [11, [0, 0, 0]], [11, [0, 0, 0]], [11, [0, 0, 0]]],
  // ご神木: 12
  [[12, [0, 0, 0]], [12, [0, 0, 0]], [12, [0, 0, 0]], [12, [0, 0, 1]]],
  // fir: 13
  [[13, [0, 0, 0]], [8, [-5, 0, 0]], [8, [0, -1, 0]], [12, [0, 0, 1]]],
];

// 操作可能フラグ
const operAble: boolean[] = [false, false, true];

// 時間経過による遷移（前の状態）
const growth: number[] = [7, 2, 2, 5, 0, 6, 6, 0, 13, 9, 0, 12, 12, 0];

// 冬に入ったタイミングでの遷移
const gowinter: number[] = [4, 4, 3, 4, 4, 4, 4, 4, 4, 9, 10, 4, 4, 4];

//関数*********************************************
//アイテムを選択したときの関数
function select(selectTo:number){
  if(selection.value !== selectTo){
    selection.value = selectTo
  }else if(selection.value == selectTo){
    selection.value = 0;
  }
}
//フィールドへの干渉を実装する関数
function plant(fildNo:number){
  let able = true;
  //すべての素材が足りているかどうかの判定
  for(let i = 0;i<materialJpgs.length;i++){
    if(handNum.value[i] + operate[conditions.value[fildNo]][selection.value][1][i] < 0){
      able = false
    }
  }
  if(able){
    //所持数を増減させる
    for(let i = 0;i<materialJpgs.length;i++){
      if(operate[conditions.value[fildNo]][selection.value][1][i] > 0){
        handNum.value[i] += operate[conditions.value[fildNo]][selection.value][1][i]*mag.value;
      }else{
        handNum.value[i] += operate[conditions.value[fildNo]][selection.value][1][i];
      }
    }
    conditions.value[fildNo]= operate[conditions.value[fildNo]][selection.value][0];
    //所持数が0ならば、選択を解除
    if(handNum.value[selection.value-1] <= 0){
        selection.value = 0;
    }
  }
}
//時間変化を実装
const fetchDataInInterval = () => {
  intervalId = setInterval(() => {
    if(debug){
      console.log("手持ち",handNum.value);
      console.log("フィールド",conditions.value);
      console.log("倍率",mag.value);
    }
    time.value++;
    //冬に入るタイミング
    if((time.value+3)%12+1 == 1 && !(conditions.value.includes(8))){
      for(let i = 0;i<4;i++){
        conditions.value[i] = gowinter[conditions.value[i]]
      }
    }else{
      for(let i = 0;i<4;i++){
        if(Math.random()<0.2 || debug){
          conditions.value[i] = growth[conditions.value[i]]
        }
      }
    }
    //ご神木の判定
    if(conditions.value.includes(12)){
      if(Math.random()<0.2 || debug){
         mag.value++
      }
    }
    //10年度になったら終了
    if(time.value == 120){
      clearInterval(intervalId);
      intervalId = null; // IDをリセット
      window.confirm(`${handNum.value[0]+handNum.value[1]+handNum.value[2]} pt`)
    }
    console.log(time.value);
  }, debug ? 10000:3000)
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
        <img v-bind:src = "grandJpgs[conditions[0]]" v-on:click = "plant(0)"/>
      </td>
      <td>
        <img v-bind:src = "grandJpgs[conditions[1]]" v-on:click = "plant(1)"/>
      </td>
    </tr>
    <tr>
      <td>
        <img v-bind:src ="grandJpgs[conditions[2]]" v-on:click = "plant(2)"/>
      </td>
      <td>
        <img v-bind:src = "grandJpgs[conditions[3]]" v-on:click = "plant(3)"/>
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