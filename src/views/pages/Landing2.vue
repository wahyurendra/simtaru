<template>
    <!-- <div class="bg-cover md:bg-contain bg-center h-64 w-64" style="background-image: url('/layout/images/bg.png');"> -->
      
  <!-- Content goes here -->
<!-- </div> -->
<!-- <div class="absolute"> -->
<!-- </div> -->

<div class="">
  <div class="absolute middle w-6" style="margin-top: 10%; margin-left: 28%;">
    <div class="grid">
      <div class="col-2">
        <img src='/layout/images/raj.png' class="w-10">
      </div>
      <div class="col-10">
        <h3 style="margin-bottom: -0.5em;"><b class="text-orange-400">SISTEM INFORMASI TATA RUANG</b></h3>
        <h3 class="text-white">Pemerintah Kabupaten Raja Ampat</h3>
        <p class="text-white">Jalan Raya Waisai, Waisai, Distrik Kota Waisai, Kabupaten Raja Ampat, Papua Bar. 98482</p>
      </div>
      <div class="col-12" style="margin-top: -2em;">
        <hr class="border-b-1 border-gray-300 my-2 block">
      </div>
      <div class="col-12">
        <span class="typed-text text-white text-7xl">{{ typeValue }}</span>
        <span class="text-white text-7xl blinking-cursor">|</span>
        <span class="cursor" :class="{ typing: typeStatus }">&nbsp;</span>
      </div>
      <div class="col-3"></div>
      <div class="col-6">
        <Button size="large" @click="goMain" rounded severity="warning">Jelajahi Sekarang</Button>
      </div>
      <div class="col-3"></div>
      
    </div>
  </div>
</div>

  
  
</template>

<script>
export default{
  data: () => {
    return {
      typeValue: "",
      typeStatus: false,
      displayTextArray: ["Jelajahi Kab. Raja Ampat", "Jelajahi Tata Ruang"],
      typingSpeed: 10,
      erasingSpeed: 10,
      newTextDelay: 2000,
      displayTextArrayIndex: 0,
      charIndex: 0,
    }
  },
  methods:{
    goMain() {
      this.$router.push({path:'/map'})
    },
    typeText() {
      if (this.charIndex < this.displayTextArray[this.displayTextArrayIndex].length) {
        if (!this.typeStatus) this.typeStatus = true;
        this.typeValue += this.displayTextArray[this.displayTextArrayIndex].charAt(
          this.charIndex
        );
        this.charIndex += 1;
        setTimeout(this.typeText, this.typingSpeed);
      } else {
        this.typeStatus = false;
        setTimeout(this.eraseText, this.newTextDelay);
      }
    },
    eraseText() {
      if (this.charIndex > 0) {
        if (!this.typeStatus) this.typeStatus = true;
        this.typeValue = this.displayTextArray[this.displayTextArrayIndex].substring(
          0,
          this.charIndex - 1
        );
        this.charIndex -= 1;
        setTimeout(this.eraseText, this.erasingSpeed);
      } else {
        this.typeStatus = false;
        this.displayTextArrayIndex += 1;
        if (this.displayTextArrayIndex >= this.displayTextArray.length)
          this.displayTextArrayIndex = 0;
        setTimeout(this.typeText, this.typingSpeed + 1000);
      }
    },
  },
  created() {
    setTimeout(this.typeText, this.newTextDelay + 200);
  },
}
// import { useRouter } from 'vue-router';
// const router=useRouter()

// async function goMain() {
//   router.push({path:'/map'})
// }
</script>

<style lang="scss" module>


html{
  
  background-image: url('/layout/images/bg-min.JPG');
  background-repeat: no-repeat;
  background-attachment: fixed;
  background-size: 100% 100%;
}
body {
  background-color: rgba(0, 0, 0, 0.22);
  backdrop-filter: blur(7px);
}
// Cursor blinking CSS Starts...
.blinking-cursor {
  font-size: 6rem;
  color: #2c3e50;
  -webkit-animation: 1s blink step-end infinite;
  -moz-animation: 1s blink step-end infinite;
  -ms-animation: 1s blink step-end infinite;
  -o-animation: 1s blink step-end infinite;
  animation: 1s blink step-end infinite;
}
@keyframes blink {
  from,
  to {
    color: transparent;
  }
  50% {
    color: #2c3e50;
  }
}
@-moz-keyframes blink {
  from,
  to {
    color: transparent;
  }
  50% {
    color: #2c3e50;
  }
}
@-webkit-keyframes blink {
  from,
  to {
    color: transparent;
  }
  50% {
    color: #2c3e50;
  }
}
@-ms-keyframes blink {
  from,
  to {
    color: transparent;
  }
  50% {
    color: #2c3e50;
  }
}
@-o-keyframes blink {
  from,
  to {
    color: transparent;
  }
  50% {
    color: #2c3e50;
  }
}
// Cursor blinking CSS Ends...
</style>