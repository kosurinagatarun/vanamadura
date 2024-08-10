<template>
    <div :class="theme === 'frontend' ? 'frontend-theme' : 'backend-theme'">
        <div v-if="theme === 'frontend'">
            <FrontendNavbarComponent />
            <FrontendCartComponent />
            <router-view></router-view>
            <FrontendMobileSideBarComponent />
            <FrontendMobileNavBarComponent />
            <FrontendMobileCategoryComponent />
            <FrontendMobileAccountComponent />
            <FrontendCookiesComponent />
            <FrontendFooterComponent />
        </div>

        <div v-if="theme === 'backend'">
            <main class="db-main" v-if="logged">
                <BackendNavbarComponent />
                <BackendMenuComponent />
                <router-view></router-view>
            </main>
            <div v-if="!logged">
                <router-view></router-view>
            </div>
        </div>
    </div>
</template>

<script>
import BackendNavbarComponent from "./layouts/backend/BackendNavbarComponent";
import BackendMenuComponent from "./layouts/backend/BackendMenuComponent";
import FrontendNavbarComponent from "./layouts/frontend/FrontendNavBarComponent";
import FrontendFooterComponent from "./layouts/frontend/FrontendFooterComponent";
import FrontendCartComponent from "./layouts/frontend/FrontendCartComponent";
import FrontendMobileNavBarComponent from "./layouts/frontend/FrontendMobileNavBarComponent";
import FrontendMobileCategoryComponent from "./layouts/frontend/FrontendMobileCategoryComponent";
import FrontendMobileAccountComponent from "./layouts/frontend/FrontendMobileAccountComponent";
import FrontendMobileSideBarComponent from "./layouts/frontend/FrontendMobileSideBarComponent";
import FrontendCookiesComponent from "./layouts/frontend/FrontendCookiesComponent";
import DisplayModeEnum from "../enums/modules/displayModeEnum";
import env from "../config/env";

export default {
    name: "DefaultComponent",
    components: {
        FrontendMobileSideBarComponent,
        FrontendMobileAccountComponent,
        FrontendMobileCategoryComponent,
        FrontendMobileNavBarComponent,
        FrontendCartComponent,
        FrontendNavbarComponent,
        FrontendFooterComponent,
        BackendNavbarComponent,
        BackendMenuComponent,
        FrontendCookiesComponent,
    },
    data() {
        return {
            theme: "frontend",
        };
    },
    beforeMount() {
        this.displayModeDefine();
        this.$store
            .dispatch("frontendSetting/lists")
            .then((res) => {
                this.$store.dispatch("globalState/init", {
                    language_id: res.data.data.site_default_language,
                    search_restaurant: "",
                    location: null,
                    latitude: null,
                    longitude: null,
                });
            })
            .catch((error) => {
                console.error("Failed to load settings:", error);
                // Optional: Add fallback or notify user
            });

        if (
            env.DEMO === "true" ||
            env.DEMO === true ||
            env.DEMO === "1" ||
            env.DEMO === 1
        ) {
            this.$store
                .dispatch("authcheck")
                .then((res) => {
                    if (res.data.status === false) {
                        this.$router.push({ name: "frontend.home" });
                    }
                })
                .catch();
        }
    },
    computed: {
        logged: function () {
            return this.$store.getters.authStatus;
        },
        displayMode: function () {
            return this.$store.getters["globalState/lists"].display_mode;
        },
    },
    methods: {
        displayModeDefine: function () {
            let dir = "ltr";
            if (
                this.$store.getters["globalState/lists"].display_mode ===
                DisplayModeEnum.LTR
            ) {
                dir = "ltr";
            } else {
                dir = "rtl";
            }
            document.documentElement.setAttribute("dir", dir);
        },
    },

    watch: {
        $route(e) {
            if (e.meta.isFrontend === true) {
                this.theme = "frontend";
            } else {
                this.theme = "backend";
            }
        },
        displayMode() {
            this.displayModeDefine();
        },
    },
};
</script>

<style>
/* Example global styles for frontend and backend themes */
.frontend-theme body {
    background-color: #001e0d !important; /* Dark green background for frontend */
    color: whitesmoke;
}

.frontend-theme .header-nav-list,
.frontend-theme .header-nav-item {
    background-color: #228531 !important; /* Green background for frontend header */
}

.frontend-theme .footer-part {
    background-color: #003e1e !important; /* Darker green background for frontend footer */
}

.backend-theme body {
    background-color: #f0f0f0; /* Example background for backend */
}
</style>
