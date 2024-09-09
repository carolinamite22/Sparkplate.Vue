<template>
  <svg
    width="24"
    height="24"
    viewBox="0 0 80 80"
    fill="none"
    xmlns="http://www.w3.org/2000/svg"
  >
    <path
      fill-rule="evenodd"
      clip-rule="evenodd"
      d="M20 20H32V32H20V20ZM16 16V36H36V16H16Z"
      :fill="color"
    />
    <path
      fill-rule="evenodd"
      clip-rule="evenodd"
      d="M48 20H60V32H48V20ZM44 16V36H64V16H44Z"
      :fill="color"
    />
    <path
      fill-rule="evenodd"
      clip-rule="evenodd"
      d="M20 48H32V60H20V48ZM16 44V64H36V44H16Z"
      :fill="color"
    />
    <rect x="44" y="44" width="8" height="8" :fill="color" />
    <rect x="56" y="44" width="8" height="8" :fill="color" />
    <rect x="44" y="56" width="8" height="8" :fill="color" />
    <rect x="56" y="56" width="8" height="8" :fill="color" />
    <rect x="24" y="24" width="4" height="4" :fill="color" />
    <rect x="52" y="24" width="4" height="4" :fill="color" />
    <rect x="24" y="52" width="4" height="4" :fill="color" />
  </svg>
</template>

<script>
export default {
  name: 'QrCodeIcon',
  props: {
    color: {
      type: String,
      default: 'currentColor'
    }
  }
}
</script>
