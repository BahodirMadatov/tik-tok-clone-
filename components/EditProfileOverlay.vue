<template>
    <div
        id="EditProfileOverlay"
        class="fixed flex justify-center pt-14 md:pt-[105px] z-50 top-0 left-0 w-full h-full bg-black bg-opacity-50 overflow-auto"
    >
        <div
            class="relative bg-white w-full max-w-[700px] sm:h-[580px] h-[655px] mx-3 p-4 rounded-lg mb-10"
            :class="!uploadedImage ? 'h-[655px]': 'h-[580px]'"
        >
            <div class="absolute flex items-center justify-between w-full p-5 left-0 top-0 border-b border-b-gray-300">
                <div class="text-[22px] font-medium">
                    Edit Profile
                </div>
                <button @click="$event => $generalStore.isEditProfileOpen = false">
                    <Icon name="mdi:close" size="25"/>
                </button>
            </div>

            <div 
                :class="!uploadedImage ? 'mt-16' : 'nt-[58px]'"
                class="h-[calc(580px-200px)]">
                    <div class="" v-if="!uploadedImage">
                        <div id="ProfilePhotoSection" 
                            class="flex flex-col border-b sm:h-[118px] h-[145px] px-1.5 py-2 w-full"
                        >
                        <div class="font-semibold text-[15px] sm:mb-0 mb-1 text-gray-700 sm:w-[160px] sm:text-left text-center">
                            Profile Photo
                        </div>

                        <div class="flex items-center justify-center sm:-mt-6">
                            <label for="image" class="relative cursor-pointer">
                                <img 
                                    class="rounded-full"
                                    width="95"
                                    src="https://picsum.photos/id/8/300/320"
                                >
                                <div class="absolute bottom-0 right-0 rounded-full bg-white shadow-xl border p-1 border-gray-300 inline-block w-[32px]">
                                    <Icon name="ph:pencil-simple-line-bold" class="mt-1 ml-0.5" size="17"/>
                                </div>
                            </label>
                            <input type="file" class="hidden" id="image" @input="getUploadedImage" accept="image/png, image/jpeg, image/jpg">
                        </div>
                    </div>

                    <div class="flex flex-col border-b sm:h-[118px] px-1.5 py-2 mt-1.5 w-full" id="UsernameSection">
                        <div class="font-semibold text-[15px] sm:mb-0 mb-1 text-gray-700 sm:w[160px] sm:text-left text-center">
                            Username
                        </div>

                        <div class="flex items-center justify-center sm:-mt-6">
                            <div class="sm:w-[60%] w-full max-w-md">
                                <TextInput
                                    placeholder="Username"
                                    v-model:input="userName"
                                    inputType="text"
                                    max="30"
                                />
                                <div class="mt-4 text-[11px] text-gray-500">
                                    Usernames can only contain letters, numbers, underscores, and periods. 
                                    Changing your username will also change your profile link.
                                </div>
                            </div>
                        </div>
                    </div>

                    <div class="flex flex-col sm:h[120px] px-1.5 py-2 mt-2 w-full" id="BioSection">
                        <div class="font-semibold text-[15px] sm:mb-0 mb-1 text-gray-700 sm:w-[160px] sm:text-left text-center">
                            Bio
                        </div>
                        <div class="flex items-center justify-center sm:-mt-6">
                            <div class="w-full sm:w-[60%] max-w-md">
                                <textarea 
                                    cols="30" 
                                    rows="4"
                                    v-model="userBio"
                                    maxlength="80"
                                    class="resize-none w-full border border-gray-300 rounded-md py-2.5 px-3 text-gray-800 bg-[#f1f1f2] focus:outline-none"
                                >
                                </textarea>
                                <div class="text-gray-500 text-[11px]" v-if="userBio">
                                {{ userBio.length }}/80
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <div class="w-full h-[430px]" v-else>
                    <Cropper
                        class="h-[430px]"
                        ref="cropper"
                        :stencil-component="CircleStencil"
                        :src="uploadedImage"
                    />
                </div>
            </div>

            <div
                id="ButtonSection"
                class="absolute p-5 left-0 bottom-0 border-t border-t-gray-300 w-full"
            >
                <div
                    id="UpdateInfoButton"
                    v-if="!uploadedImage"
                    class="flex items-center justify-end"
                >
                    <button
                        @click="$event => $generalStore.isEditProfileOpen = false"
                        class="flex items-center border rounded-sm px-3 py-[6px] hover:bg-gray-100"
                    >
                        <span class="px-2 font-semibold text-[15px]">Cancel</span>
                    </button>

                    <button
                        @click="$event => cropAndUpdateImage()"
                        class="flex items-center bg-[#F02C56] text-white border rounded-md ml-3 px-3 py-[6px]"
                    >
                    <span class="mx-4 font-medium text-[15px]">Apply</span>
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { Cropper, CircleStencil } from 'vue-advanced-cropper'
import 'vue-advanced-cropper/dist/style.css';

import { storeToRefs } from 'pinia';
const { $generalStore, $userStore, $profileStore } = useNuxtApp()
const { name, bio, image } = storeToRefs($userStore)

onMounted(() => {
    userName.value = name.value
    userBio.value = bio.value
    userImage.value = image.value
})

let cropper = ref(null)
let uploadedImage = ref(null)
let file = ref(null)
let userName = ref(null)
let userImage = ref(null)
let userBio = ref(null)
let isUploaded = ref(false)

const getUploadedImage = (e) => {
    file.value = e.target.files[0]
    uploadedImage.value = URL.createObjectURL(file.value)
}

watch(() => userName.value, () => {
    if (!userName.value || userName.value === name.value) {
        isUpdated.value = false
    } else {
        isUpdated.value = true
    }
})
watch(() => userBio.value, () => {
    if (!userBio.value || userBio.value.length < 1) {
        isUpdated.value = false
    } else {
        isUpdated.value = true
    }
})
</script>
