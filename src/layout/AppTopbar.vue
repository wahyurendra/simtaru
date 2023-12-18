<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from 'vue';
import { useLayout } from '@/layout/composables/layout';
import { useRouter } from 'vue-router';
import Dialog from 'primevue/dialog';
import axios from 'axios';
import { useToast } from 'primevue/usetoast';
import { useField, useForm } from 'vee-validate';
import Swal from 'sweetalert2'
const { handleSubmit, resetForm } = useForm();
const { value, errorMessage } = useField('value', validateField);
const toast = useToast();
const inusername =ref("");
const inpassword =ref("");
const isLogin=ref(false);




const { layoutConfig, onMenuToggle } = useLayout();

const outsideClickListener = ref(null);
const topbarMenuActive = ref(false);
const router = useRouter();

const emit=defineEmits(['isLogin'])

onMounted(() => {
    bindOutsideClickListener();
});

onBeforeUnmount(() => {
    unbindOutsideClickListener();
});

const logoUrl = computed(() => {
    // return `layout/images/${layoutConfig.darkTheme.value ? 'logo-white' : 'logo-dark'}.svg`;
    return `layout/images/raj.png`;
});

const onTopBarMenuButton = () => {
    topbarMenuActive.value = !topbarMenuActive.value;
};
const onSettingsClick = () => {
    topbarMenuActive.value = false;
    router.push('/documentation');
};
const topbarMenuClasses = computed(() => {
    return {
        'layout-topbar-menu-mobile-active': topbarMenuActive.value
    };
});

const bindOutsideClickListener = () => {
    if (!outsideClickListener.value) {
        outsideClickListener.value = (event) => {
            if (isOutsideClicked(event)) {
                topbarMenuActive.value = false;
            }
        };
        document.addEventListener('click', outsideClickListener.value);
    }
};
const unbindOutsideClickListener = () => {
    if (outsideClickListener.value) {
        document.removeEventListener('click', outsideClickListener);
        outsideClickListener.value = null;
    }
};
const isOutsideClicked = (event) => {
    if (!topbarMenuActive.value) return;

    const sidebarEl = document.querySelector('.layout-topbar-menu');
    const topbarEl = document.querySelector('.layout-topbar-menu-button');

    return !(sidebarEl.isSameNode(event.target) || sidebarEl.contains(event.target) || topbarEl.isSameNode(event.target) || topbarEl.contains(event.target));
};

function buttonLogin() {
    visible.value=!visible.value
}

const visible = ref(false);

function doLogin() {
    axios.post("https://apigeojson.kartabhumi.co.id/login", {username:inusername.value,password:inpassword.value})
        .then((response) =>{
            localStorage.setItem("accessToken",response.data['access_token'])
            cekLogin()
            buttonLogin()
            Swal.fire({
                title: "Login Berhasil",
                icon: "success"
                });

        })
        .catch((err) =>{
          return new Error(err.message);
        });
};

function validateField(value) {
    if (!value) {
        return 'Name - Surname is required.';
    }

    return true;
};
function logout() {
    localStorage.removeItem("accessToken")
    cekLogin()
    Swal.fire({
        title: "Logout",
        icon: "success"
        });
}
function cekLogin() {
    isLogin.value=localStorage.getItem("accessToken")!=null
    emit('isLogin',isLogin.value)
}
cekLogin()
</script>

<template>
    <div class="layout-topbar">
        <router-link to="/" class="layout-topbar-logo">
            <img :src="logoUrl" style="width: 2em;height: 2em;" alt="logo" />
            <span>SIMTARU - RAJA AMPAT</span>
        </router-link>
<!-- 
        <button class="p-link layout-menu-button layout-topbar-button" @click="onMenuToggle()">
            <i class="pi pi-bars"></i>
        </button> -->

        <button class="p-link layout-topbar-menu-button layout-topbar-button" @click="onTopBarMenuButton()">
            <i class="pi pi-ellipsis-v"></i>
        </button>

        <div class="layout-topbar-menu" :class="topbarMenuClasses">
            <Button v-if="!isLogin" label="Login" icon="pi pi-user" @click="buttonLogin" />
            <Button v-else label="Logout" icon="pi pi-user" @click="logout" />


            <Dialog
                v-model:visible="visible"
                modal
                :pt="{
                    mask: {
                        style: 'backdrop-filter: blur(2px)'
                    }
                }"
            >
            <!-- <template #container=""> -->
                <div class="flex flex-column px-8 py-5 gap-4" style="border-radius: 12px; background-image: radial-gradient(circle at left top, var(--primary-400), var(--primary-700))">
                    <div class="inline-flex flex-column gap-2">
                        <label for="username" class="text-primary-50 font-semibold">Username</label>
                        <!-- <InputText id="username" class="bg-white-alpha-20 border-none p-3 text-primary-50"></InputText> -->
                        <InputText id="username" v-model="inusername" type="text" :class="{ 'p-invalid': errorMessage }" class="bg-white-alpha-20 border-none p-3 text-primary-50" aria-describedby="text-error" />
                    </div>
                    <div class="inline-flex flex-column gap-2">
                        <label for="password" class="text-primary-50 font-semibold">Password</label>
                        <InputText id="password" v-model="inpassword" type="password" :class="{ 'p-invalid': errorMessage }" class="bg-white-alpha-20 border-none p-3 text-primary-50" aria-describedby="text-error" />

                    </div>
                    <div class="flex align-items-center gap-2">
                        <Button type="submit" @click="doLogin()" label="Sign-In" text class="p-3 w-full text-primary-50 border-1 border-white-alpha-30 hover:bg-white-alpha-10"/>
                    </div>
                </div>
            <!-- </template> -->
            </Dialog>
        </div>
    </div>
</template>

<style lang="scss" scoped>
.layout-topbar {
    background-color: #ffa701 !important;
}

</style>
