<template>
    <div class="flex">
        <div class="h-screen w-[20vw]">
            <div class="flex w-full justify-between border-b-[1px] border-gray-200 pb-5">
                <button v-for="(country, index) in countries"
                        :key="index"
                        :class="[country.country === currentCountry.country? 'bg-[#ff9d00]' : '']" class="w-1/2"
                        @click="currentCountry = country"
                >
                    {{ country.country }}
                </button>
            </div>

            <div class="p-4">
                <div v-for="(city, index) in currentCountry?.cities" :key="index">
                    <div class="flex justify-between text-[#ff9d00] cursor-pointer md:hover:bg-black/5"
                         @click="city.isOpen = !city.isOpen">
                        <p class="font-semibold">{{ city?.name }}</p>
                        <img :class="{'rotate-180' : city.isOpen}" alt=""
                             class="duration-500" src="../assets/arrow-down-icon.svg"
                        >
                    </div>
                    <div v-if="city.isOpen" class="flex flex-col gap-4">
                        <div v-for="(location, locationIndex) in city.locations" :key="locationIndex"
                             @click="changeLocation(location)"
                        >
                            <p>{{ location.name }}</p>
                            <p>{{ location.mobile }}</p>
                            <a :href="location.websiteUrl" class="text-blue-400"
                               target="_blank">{{ location.websiteUrl }}</a>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <yandex-map
                :settings="mapSettings"
                cursor-grab
                style="background: white"
                height="100vh"
                width="80vw"
        >
            <yandex-map-default-scheme-layer :settings="{ theme: 'light' }"/>
            <yandex-map-default-features-layer/>
            <yandex-map-clusterer
                zoom-on-cluster-click
                v-model="clusterer"
                :grid-size="1"

            >
            <yandex-map-marker
                v-for="(marker, index) in markers"
                :key="index"
                :settings="marker"
            >
                <div class="marker" @click.stop.prevent="changeLocation(marker)">
                    <div class="absolute bottom-7 -left-4" v-if="selectedLocation === marker.coordinates">
                        <div
                            class="w-64 p-2 flex flex-col items-start gap-2 relative bg-[#1d355d] text-white z-[9999]"
                        >
                            <p class="text-[#ff9d00]">{{marker.name}}</p>
                            <p >{{marker.mobile}}</p>
                            <a :href="marker.websiteUrl" class="text-blue-400"
                               target="_blank">{{ marker.websiteUrl }}</a>
                            <div @click.stop.prevent="selectedLocation = null"  class="absolute top-2 right-4 cursor-pointer">
                                X
                            </div>
                        </div>
                    </div>

                </div>
            </yandex-map-marker>
                <template #cluster="{ length }">
                    <div class="cluster">
                        {{ length }}
                    </div>
                </template>
            </yandex-map-clusterer>
        </yandex-map>
    </div>
</template>

<script lang="ts" setup>
import {computed, ref, shallowRef, watch} from 'vue'
import data from '../countriesData.json'
import {
    YandexMap,
    YandexMapClusterer,
    YandexMapDefaultFeaturesLayer,
    YandexMapDefaultSchemeLayer,
    YandexMapMarker,
} from "vue-yandex-maps";

const selectedLocation = ref()
const trueBounds = ref([[0, 0], [0, 0]]);

const clusterer = shallowRef()


const changeLocation = (location) => {
    selectedLocation.value = location.coordinates
};

const mapSettings = ref({
    location: {
        center: [37.64, 55.74],
        zoom: 5,
        duration: 500,
    },
    theme: 'light',
});


const countries = data

const currentCountry = ref(countries[0])

const markers = computed(() => {
    return currentCountry.value.cities.map((city) => {
        return city.locations
    }).flat()
})
</script>
<style scoped>
.marker {
    position: relative;
    width: 24px;
    height: 24px;
    background: #1d355d;
    border-radius: 50%;
    box-shadow: 0 0 5px rgba(0, 0, 0, 0.5);
    text-align: center;
    color: #fff;
    font-weight: bold;
    line-height: 20px;
    cursor: pointer;
}

.cluster {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 38px;
    height: 38px;
    background: #1d355d;
    color: #fff;
    border-radius: 100%;
    cursor: pointer;
}
</style>