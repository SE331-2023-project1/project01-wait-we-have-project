<script setup lang="ts">
import { computed, ref, type PropType } from "vue";
import type { AdviserItem } from "@/type";
import StudentService from "@/services/StudentService";
import { useMessageStore } from "@/stores/message";
import { storeToRefs } from 'pinia'
const professer = ref<AdviserItem | null>(null);
const props = defineProps({
  id: String,
  professer: {
    type: Object as PropType<AdviserItem>,
    require: true,
  },
});

StudentService.getAdviserById(Number(props.id))
  .then((response) => {
    professer.value = response.data;
  })
  .catch((error) => {
    console.log(error);
  });

const store = useMessageStore();
const detail = computed(() => store.getDetail(String(props.professer?.id)));
const newDetail = ref("");
function addDetail() {
  store.addDetail(String(props.professer?.id), newDetail.value);
  newDetail.value = "";
}
const { flashMessage } = storeToRefs(store)
</script>

<template>
  <div class="my-10">
    <div class="bg-red-700 transition duration-3000 my-2">
      <h4 class="text-center font-mono text-white">{{ flashMessage }}</h4>
    </div>
    <h1 class="text-center text-3xl font-mono">
      Professer ID: {{ professer?.professorID }}
    </h1>
    <div class="my-5">
      <div class="justify-center my-auto grid gap-3">
        <img :src="professer?.profileImage" alt="Student Image" class=" border-2 border-black h-48 w-36 mx-auto" />
        <div class="font-mono grid grid-cols-2 gap-2">
          <p class="text-center font-semibold">Name: </p>
          <p> {{ professer?.name }} {{ professer?.surname }}</p>
          <p class="text-center font-semibold">Email: </p>
          <p> {{ professer?.email }}</p>
          <p class="text-center font-semibold">Phone: </p>
          <p> {{ professer?.telephone }}</p>
        </div>
        <div v-if="detail && detail.length">
          <h1 class="text-center font-mono font-extrabold">professer Detail</h1>
          <p v-for="(details, index) in detail" :key="index" class="font-mono text-center">" {{ details }} "</p>
        </div>
        <div class="text-center font-mono border-t-2 border-black">
          <input type="text" v-model="newDetail" placeholder="Add Professer Detail.." class="border-2 border-black h-16 w-80 p-2 break-words mt-4 text-center" />
          <div>
            <button @click="addDetail" class="my-2 hover:text-red-800 font-extrabold underline">Add Detail</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
