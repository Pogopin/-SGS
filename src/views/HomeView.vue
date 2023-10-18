<script setup>
import { ref, watch, onUnmounted, computed } from 'vue'
import { towns } from '../config/config.js'
import { useCookies } from "vue3-cookies"

const town = ref('')
const shop = ref('')
const worker = ref('')
const shift = ref('')
const brigade = ref('')
const formVisible = ref(false)

const { cookies } = useCookies()

function selectCountry(value) {
  town.value = value
}  
function selectShop(value) {
  shop.value = value
}

const selectShopsOptions = computed(() => {
  const temp = []
  towns.forEach((el)=> {
    if(el.town === town.value) { el.shop.forEach((el) => temp.push(el.name)) }
  })
  return temp
})
const selectWorkerOptions = computed(() => {
  const temp = []
  towns.forEach((el)=> {
    if(el.town === town.value) {
      el.shop.forEach((item) => {             
        if(item.name === shop.value) { temp.push(...item.worker)  }        
      })      
    }
  })
  return temp
})

function selectWorker(value) {
  worker.value = value
}
function selectShift(value) {
  shift.value = value
}
function selectBrigade(value) {
  brigade.value = value
}
watch(town, () => {
  shop.value = ''
  worker.value = ''  
})
watch(shop, () => {
  worker.value = null  
})
function showForm() {
  formVisible.value = true
}
// cookies.set("town", town.value);
// onUnmounted(()=>{
//   cookies.set('');
// })

function saveToCookie() {
  document.cookie = `town=${town.value}; expires=Thu, 18 Dec 2023 12:00:00 UTC; path=/`;
  document.cookie = `shop=${shop.value}; expires=Thu, 18 Dec 2023 12:00:00 UTC; path=/`;
  document.cookie = `worker=${worker.value}; expires=Thu, 18 Dec 2023 12:00:00 UTC; path=/`;
  document.cookie = `shift=${shift.value}; expires=Thu, 18 Dec 2023 12:00:00 UTC; path=/`;
  document.cookie = `brigade=${brigade.value}; expires=Thu, 18 Dec 2023 12:00:00 UTC; path=/`;
}
watch([town, shop, worker, shift, brigade], () => {
  saveToCookie();
});
</script>

<template>
  <h1 class="title">Первые 3 поля зависят друг от друга, данные формы сохраняются в cookie</h1>

  <form class="form">
    <p class="form-label">Выбери город</p>  
    <el-select v-model="town" class="m-2 input-block" placeholder="Select" size="large" @change="(value)=> selectCountry(value)" >
      <el-option
        v-for="item in towns"
        :key="item.town"
        :label="item.label"
        :value="item.town"
      />
    </el-select>

    <p class="form-label">Выбери цех</p>
    <el-select v-model="shop" class="m-2 input-block" placeholder="Select" size="large" @change="(value) => selectShop(value)">
      <el-option
        v-for="(item) in selectShopsOptions"
        :key="item"
        :label="item.label"
        :value="item"      
      />
    </el-select>

    <p class="form-label">Выбери сотрудника</p>
    <el-select v-model="worker" class="m-2 input-block" placeholder="Select" size="large" @change="(value) => selectWorker(value)">
      <el-option
        v-for="(item, index) in selectWorkerOptions"
        :key="index"
        :label="item.label"
        :value="item"      
      />
    </el-select>

    <p class="form-label">Бригада</p>
    <el-select v-model="brigade" class="m-2 input-block" placeholder="Select" size="large" @change="(value) => selectBrigade(value)">
      <el-option
        v-for="(item, index) in ['непонятно что здесь писать']"
        :key="index"
        :label="item.label"
        :value="item"      
      />
    </el-select>

    <p class="form-label">Смена</p>
    <el-select v-model="shift" class="m-2 input-block" placeholder="Select" size="large" @change="(value) => selectShift(value)">
      <el-option
        v-for="(item, index) in ['первая с 8 до 20:00','вторая с 20:00 до 8']"
        :key="index"
        :label="item.label"
        :value="item"      
      />
    </el-select>
    <el-row class="mb-4">    
      <el-button type="primary" @click="showForm">submit</el-button>    
    </el-row>
  </form>  

  <div class="form-data" v-if="formVisible">
    <p>{{town}}</p>
    <p>{{shop}}</p>
    <p>{{worker}}</p>
    <p>{{brigade}}</p>
    <p>{{shift}}</p>
  </div>
  
</template>
<style scoped>
.title {
  margin-bottom: 30px;
}
.input-block {
  display: block;
  max-width: 500px;
  margin-bottom: 20px;
}
.form-label {
  color: rgb(13, 145, 79)
}
.form-data {
  margin-top: 50px;
}
</style>