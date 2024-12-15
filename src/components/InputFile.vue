<template>
  <div class="flex flex-col w-full h-full items-center justify-around">
    <div
      class="w-[80%] rounded-xl h-96 shadow-md bg-[#F9F2ED] mt-10 flex-col items-center justify-around"
    >
      <div class="w-full h-[80%] flex items-center justify-around">
        <div class="h-full w-[40%] flex flex-col justify-evenly">
          <div class="text-[#019ed8] font-semibold text-2xl font-mono text-center">
            Chọn mô hình muốn sử dụng
          </div>
          <div class="flex w-full justify-around">
            <div
              class="min-w-32 py-4 font-mono px-2 text-center font-semibold cursor-pointer rounded-[30px] text-xl text-white"
              :class="resNet18 ? 'bg-[#003153]' : 'bg-[#b5c0cb]'"
              @click="resNet18 = !resNet18"
            >
              ResNet18
            </div>
            <div
              class="min-w-32 py-4 font-mono px-2 text-center font-semibold cursor-pointer rounded-[30px] text-xl text-white"
              :class="cdil ? 'bg-[#003153]' : 'bg-[#b5c0cb]'"
              @click="cdil = !cdil"
            >
              CDIL
            </div>
            <div
              class="min-w-32 py-4 font-mono px-2 text-center font-semibold cursor-pointer rounded-[30px] text-xl text-white"
              :class="efficientnet ? 'bg-[#003153]' : 'bg-[#b5c0cb]'"
              @click="efficientnet = !efficientnet"
            >
              EfficientNet
            </div>
          </div>
          <div class="flex w-full justify-evenly">
            <div
              class="min-w-32 py-4 font-mono px-2 text-center font-semibold cursor-pointer rounded-[30px] text-xl text-white"
              :class="lateFusion ? 'bg-[#003153]' : 'bg-[#b5c0cb]'"
              @click="lateFusion = !lateFusion"
            >
              Late-Fusion
            </div>
            <div
              class="min-w-32 py-4 font-mono px-2 text-center font-semibold cursor-pointer rounded-[30px] text-xl text-white"
              :class="attFusion ? 'bg-[#003153]' : 'bg-[#b5c0cb]'"
              @click="attFusion = !attFusion"
            >
              Att-Late-Fusion
            </div>
          </div>
        </div>
        <div class="h-full w-[40%] flex flex-col justify-evenly">
          <div class="text-[#019ed8] font-semibold text-2xl font-mono text-center">
            Tải dữ liệu cần dự đoán
          </div>
          <div
            class="w-full h-40 flex flex-col justify-around items-center border-dashed border-2 border-[#cccccc]"
          >
            <div class="flex w-full h-[50px] justify-evenly">
              <div
                v-for="(file, index) in files"
                :key="index"
                class="border-2 border-green-400 min-w-32 h-[50px] rounded-xl flex items-center p-2"
              >
                <img src="../assets/images/csv_icon.png" alt="Image" class="w-[35px] h-[35px]" />
                <div class="text-zinc-700 font-mono font-semibold">
                  {{ file.name }}
                </div>
              </div>
            </div>
            <div
              @click="triggerFileUpload"
              class="w-32 h-[30%] py-3 bg-[#1170e6] font-mono px-2 text-center font-semibold cursor-pointer rounded-[30px] text-base text-white content-center"
            >
              Tải lên
            </div>
            <input type="file" ref="fileInput" class="hidden" @change="handleFileUpload" multiple />
          </div>
        </div>
      </div>
      <div class="h-[20%] w-full flex justify-center">
        <div
          @click="analyzeFiles"
          class="w-32 py-4 max-h-16 bg-[#1170e6] font-mono px-2 text-center font-semibold cursor-pointer rounded-[30px] text-xl text-white"
        >
          Phân tích
        </div>
      </div>
    </div>
    <div class="w-[80%] mt-6 py-4 bg-[#F9F2ED] rounded-3xl shadow-md" v-if="processedData.length">
      <div class="flex flex-col w-full h-full">
        <div class="flex px-10 items-center" v-if="dataTrue.length">
          <div class="text-[#019ed8] font-semibold text-2xl font-mono text-center pt-6 pb-4">
            BẤT THƯỜNG CỦA ECG LÀ:
          </div>
          <div
            v-for="(y_true, index) in dataTrue"
            :key="index"
            v-show="y_true"
            class="py-2 h-14 px-2 mx-2 min-w-24 rounded-full flex items-center justify-center bg-[#003153] text-white font-semibold text-xl font-mono text-center"
          >
            {{ getLabel(index) }}
          </div>
        </div>
        <div class="text-[#019ed8] font-semibold text-2xl font-mono text-center pt-6 pb-4">
          KẾT QUẢ PHÂN TÍCH
        </div>
        <div class="w-full flex flex-wrap items-center justify-around">
          <div
            v-for="result in processedData"
            :key="result.type"
            :class="`h-[17rem] w-80 flex flex-col items-center rounded-3xl m-2 shadow-2xl ${!result.y_trues.length || (result.y_trues.length && result.isTrue) ? 'bg-[#24a865]' : 'bg-[#ed3535]'}`"
          >
            <div
              class="min-w-32 py-2 font-mono px-2 text-center m-2 font-semibold cursor-pointer rounded-[30px] text-xl text-white"
            >
              {{ getTitle(result.type) }}
            </div>
            <div class="flex flex-wrap justify-around">
              <div
                v-for="(pred, index) in result.y_preds"
                :key="index"
                :class="`p-3 m-2 min-w-20 rounded-full font-semibold text-xl font-mono text-center ${pred === 1 ? 'bg-[#1170e6] text-white' : 'bg-white text-black'}`"
              >
                {{ getLabel(index) }}
              </div>
            </div>
          </div>
          <!-- <div class="h-80 w-80 bg-[#24a865] flex flex-col items-center rounded-3xl m-2 shadow-lg">
            <div
              class="min-w-32 max-w-40 py-4 font-mono px-2 text-center m-4 font-semibold cursor-pointer bg-[#003153] rounded-[30px] text-xl text-white"
            >
              ResNet18
            </div>
            <div class="flex flex-wrap justify-around">
              <div
                class="py-3 m-3 w-20 rounded-full bg-white text-black font-semibold text-xl font-mono text-center"
              >
                1dAVb
              </div>
              <div
                class="py-3 m-3 w-20 rounded-full bg-white text-black font-semibold text-xl font-mono text-center"
              >
                RBBB
              </div>
              <div
                class="py-3 m-3 w-20 rounded-full bg-white text-black font-semibold text-xl font-mono text-center"
              >
                LBBB
              </div>
              <div
                class="py-3 m-3 w-20 bg-[#1170e6] rounded-full text-white font-semibold text-xl font-mono text-center"
              >
                SB
              </div>
              <div
                class="py-3 m-3 w-20 rounded-full bg-white text-black font-semibold text-xl font-mono text-center"
              >
                ST
              </div>
              <div
                class="py-3 m-3 w-20 rounded-full bg-[#1170e6] text-white font-semibold text-xl font-mono text-center"
              >
                AF
              </div>
              <div
                class="py-3 px-3 m-3 rounded-full bg-white text-black font-semibold text-xl font-mono text-center"
              >
                normal_ecg
              </div>
            </div>
          </div> -->
        </div>
      </div>
    </div>
    <div
      class="w-[80%] mt-6 p-4 bg-[#F9F2ED] shadow-md rounded-3xl flex flex-col items-center"
      v-if="images.length"
    >
      <div class="text-[#019ed8] font-semibold text-2xl font-mono text-center pt-6 pb-4">
        BIỂN DIỄN ECG 12-LEAD
      </div>
      <div v-for="image in images" :key="image.name" class="image-container">
        <img :src="image.data" :alt="image.name" class="image" />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import axios from 'axios'

const resNet18 = ref(false)
const cdil = ref(false)
const efficientnet = ref(false)
const lateFusion = ref(false)
const attFusion = ref(false)

const fileInput = ref(null)
const files = ref([])
const images = ref([])

const dataTrue = ref([])
const processedData = ref([])

interface AnalysisResult {
  y_preds: number[]
  y_trues: number[]
}

interface AnalysisData {
  [key: string]: AnalysisResult
}

const triggerFileUpload = () => {
  fileInput.value.click()
}

const handleFileUpload = (event) => {
  files.value = Array.from(event.target.files)
}

const getImages = async () => {
  const response = await axios.get(`${import.meta.env.VITE_API_URL}/list-images`)
  images.value = response.data
}

const analyzeFiles = async () => {
  const formData = new FormData()
  files.value.forEach((file) => {
    formData.append('files', file)
  })

  const types = []
  if (resNet18.value) types.push('resnet18')
  if (cdil.value) types.push('cdil')
  if (efficientnet.value) types.push('efficientnet')
  if (lateFusion.value) types.push('lateFusion')
  if (attFusion.value) types.push('attFusion')

  types.forEach((type) => {
    formData.append('types', type)
  })

  try {
    const response = await axios.post(`${import.meta.env.VITE_API_URL}/predict-ecg`, formData)
    await processResponseData(response.data)
    await getImages()
  } catch (error) {
    console.error('Error:', error)
    alert('Lỗi khi phân tích!')
  }
}

const processResponseData = async (data: AnalysisData) => {
  processedData.value = Object.entries(data).map(([type, { y_preds, y_trues }]) => {
    const isTrue = y_preds.every((pred, index) => pred === y_trues[index])
    if (y_trues) {
      dataTrue.value = y_trues
    }
    return {
      type,
      y_preds,
      y_trues,
      isTrue
    }
  })
}

function getTitle(type) {
  const titles = {
    resnet18: 'ResNet18',
    cdil: 'CDIL',
    efficientnet: 'EfficientNet',
    lateFusion: 'Late-Fusion',
    attFusion: 'Att-Late-Fusion'
  }
  return titles[type] || type
}

function getLabel(index) {
  const labels = ['1dAVb', 'RBBB', 'LBBB', 'SB', 'ST', 'AF', 'normal_ecg']
  return labels[index]
}
</script>
