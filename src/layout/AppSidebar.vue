<script setup>
import AppMenu from './AppMenu.vue';

const emit=defineEmits(['openMenu1','toggleJalan','togglePelabuhan','togglePolygon','toggleWaisai','toggleKantorPemerintah','toggleTempatPendidikan', 'toggleBandara', 'toggleHutan', 'toggleKesehatan', 'togglePekarangan', 'togglePelabuhan', 'togglePemukiman', 'toggleSungai', 'toggleTambang', 'toggleTPA', 'toggleLapangan'])
function Menu1(isOpen) {
    emit('openMenu1',isOpen)
}

function toggleJalan(toggled) {
    emit('toggleJalan', toggled)
}

function togglePelabuhan(toggled) {
    emit('togglePelabuhan', toggled)
}
function togglePolygon(toggled) {
    emit('togglePolygon', toggled)
}

function toggleWaisai(toggled) {
    emit('toggleWaisai', toggled)
}

function toggleKantorPemerintah(toggled) {
    emit('toggleKantorPemerintah', toggled)
}

function toggleTempatPendidikan(toggled) {
    emit('toggleTempatPendidikan', toggled)
}

function toggleBandara(toggled) {
    emit('toggleBandara', toggled)
}

function toggleHutan(toggled) {
    emit('toggleHutan', toggled)
}

function toggleKesehatan(toggled) {
    emit('toggleKesehatan', toggled)
}

function togglePekarangan(toggled) {
    emit('togglePekarangan', toggled)
}

function togglePelabuhan(toggled) {
    emit('togglePelabuhan', toggled)
}

function togglePemukiman(toggled) {
    emit('togglePemukiman', toggled)
}

function toggleSungai(toggled) {
    emit('toggleSungai', toggled)
}

function toggleTambang(toggled) {
    emit('toggleBandara', toggled)
}

function toggleTPA(toggled) {
    emit('toggleTPA', toggled)
}

function toggleLapangan(toggled) {
    emit('toggleLapangan', toggled)
}


</script> 

<template>
    <app-menu 
        @open-menu1="(openMenu1)=>Menu1(openMenu1)" 
        @toggle-jalan="(res)=>toggleJalan(res)" 
        @toggle-pelabuhan="(res)=>togglePelabuhan(res)"
        @toggle-polygon="(res)=>togglePolygon(res)"
        @toggle-waisai="(res)=>toggleWaisai(res)"
        @toggle-kantor-pemerintah="(res)=>toggleKantorPemerintah(res)"
        @toggle-tempat-pendidikan="(res)=>toggleTempatPendidikan(res)"
        @toggle-bandara="(res)=>toggleBandara(res)"
        @toggle-hutan="(res)=>toggleHutan(res)"
        @toggle-kesehatan="(res)=>toggleKesehatan(res)"
        @toggle-pekarangan="(res)=>togglePekarangan(res)"
        @toggle-pelabuhan="(res)=>togglePelabuhan(res)"
        @toggle-pemukiman="(res)=>togglePemukiman(res)"
        @toggle-sungai="(res)=>toggleSungai(res)"
        @toggle-tambang="(res)=>toggleTambang(res)"
        @toggle-tpa="(res)=>toggleTPA(res)"
        @toggle-lapangan="(res)=>toggleLapangan(res)"


    >
    </app-menu>
</template>

<style lang="scss" scoped></style>
