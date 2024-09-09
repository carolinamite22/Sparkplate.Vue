<template>
  <div class="view">
    <h1 class="view-name">QR Code Generator</h1>
    <div class="flex flex-col items-center p-4 bg-gray-100">
      <div class="w-full max-w-4xl flex flex-col items-center space-y-6">
        <div class="w-full flex flex-col lg:flex-row gap-4">
          <!-- Input field for QR code content -->
          <div class="w-full lg:w-1/2">
            <input
              v-model="inputValue"
              type="text"
              class="p-3 rounded-lg border-2 border-blue-300 w-full focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent transition duration-200 ease-in-out shadow-sm"
              placeholder="Enter a word to generate QR code"
            />
          </div>
          <!-- QR code display and save options -->
          <div class="w-full lg:w-1/2 flex flex-col items-center">
            <!-- QR code image or placeholder -->
            <div
              :class="[
                'rounded-lg bg-white flex items-center justify-center shadow-md overflow-hidden w-full transition-all duration-300 ease-in-out',
                inputValue
                  ? 'aspect-square p-4'
                  : 'p-3 border-2 border-gray-300'
              ]"
            >
              <img
                v-if="qrCodeSrc"
                :src="qrCodeSrc"
                alt="QR Code"
                ref="qrCodeImage"
                class="max-w-full max-h-full"
              />
              <span v-else class="text-gray-400">QR code will appear here</span>
            </div>
            <!-- Format selection dropdown and save button -->
            <div v-if="qrCodeSrc" class="relative mt-4 w-full flex gap-2">
              <div class="relative flex-grow">
                <!-- Dropdown button -->
                <button
                  @click="toggleDropdown"
                  class="w-full px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600 transition duration-200 ease-in-out shadow-sm flex items-center justify-between"
                >
                  <span>{{ selectedFormat || 'Select format' }}</span>
                  <svg
                    class="w-5 h-5 ml-2"
                    fill="none"
                    stroke="currentColor"
                    viewBox="0 0 24 24"
                    xmlns="http://www.w3.org/2000/svg"
                  >
                    <path
                      stroke-linecap="round"
                      stroke-linejoin="round"
                      stroke-width="2"
                      d="M19 9l-7 7-7-7"
                    ></path>
                  </svg>
                </button>
                <!-- Dropdown menu -->
                <div
                  v-if="showDropdown"
                  class="absolute left-0 right-0 mt-2 rounded-md shadow-lg bg-white ring-1 ring-black ring-opacity-5 z-10"
                >
                  <div
                    class="py-1"
                    role="menu"
                    aria-orientation="vertical"
                    aria-labelledby="options-menu"
                  >
                    <span
                      class="block px-4 py-1 text-sm text-gray-400 cursor-default"
                      role="menuitem"
                      >Select format</span
                    >
                    <a
                      @click="selectFormat('JPG')"
                      class="block px-4 py-1 text-sm text-gray-700 hover:bg-gray-100 hover:text-gray-900 cursor-pointer transition duration-150 ease-in-out"
                      role="menuitem"
                      >JPG</a
                    >
                    <a
                      @click="selectFormat('PNG')"
                      class="block px-4 py-1 text-sm text-gray-700 hover:bg-gray-100 hover:text-gray-900 cursor-pointer transition duration-150 ease-in-out"
                      role="menuitem"
                      >PNG</a
                    >
                    <a
                      @click="selectFormat('SVG')"
                      class="block px-4 py-1 text-sm text-gray-700 hover:bg-gray-100 hover:text-gray-900 cursor-pointer transition duration-150 ease-in-out"
                      role="menuitem"
                      >SVG</a
                    >
                  </div>
                </div>
              </div>
              <!-- Save button -->
              <button
                @click="saveImage"
                :disabled="!selectedFormat"
                class="px-4 py-2 bg-green-500 text-white rounded-lg hover:bg-green-600 transition duration-200 ease-in-out shadow-sm disabled:opacity-50 disabled:cursor-not-allowed"
              >
                Save
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, watch } from 'vue'

export default {
  name: 'QrGenerator',
  setup() {
    // Reactive variables
    const inputValue = ref('') // Stores the input for QR code generation
    const qrCodeSrc = ref('') // Stores the URL of the generated QR code image
    const qrCodeImage = ref(null) // Reference to the QR code image element
    const showDropdown = ref(false) // Controls the visibility of the format dropdown
    const selectedFormat = ref('') // Stores the selected image format

    // Watch for changes in the input value
    watch(inputValue, (newValue) => {
      if (newValue) {
        // Generate QR code URL when input is not empty
        const qrCodeUrl = `https://api.qrserver.com/v1/create-qr-code/?size=200x200&data=${encodeURIComponent(
          newValue
        )}`
        qrCodeSrc.value = qrCodeUrl
      } else {
        // Clear QR code when input is empty
        qrCodeSrc.value = ''
      }
      // Reset the dropdown menu when input changes
      selectedFormat.value = ''
    })

    // Function to handle format selection
    const selectFormat = (format) => {
      selectedFormat.value = format
      showDropdown.value = false
    }

    // Function to save the QR code image
    const saveImage = async () => {
      if (!qrCodeSrc.value || !selectedFormat.value) return

      // Fetch the QR code image in the selected format
      const response = await fetch(
        `https://api.qrserver.com/v1/create-qr-code/?size=200x200&data=${encodeURIComponent(
          inputValue.value
        )}&format=${selectedFormat.value.toLowerCase()}`
      )
      const blob = await response.blob()
      const url = window.URL.createObjectURL(blob)

      // Create a temporary link element to trigger the download
      const a = document.createElement('a')
      a.style.display = 'none'
      a.href = url
      a.download = `qrcode.${selectedFormat.value.toLowerCase()}`
      document.body.appendChild(a)
      a.click()
      window.URL.revokeObjectURL(url)
    }

    // Function to toggle the dropdown visibility
    const toggleDropdown = () => {
      showDropdown.value = !showDropdown.value
    }

    // Return reactive variables and functions for use in the template
    return {
      inputValue,
      qrCodeSrc,
      qrCodeImage,
      saveImage,
      showDropdown,
      toggleDropdown,
      selectedFormat,
      selectFormat
    }
  }
}
</script>
