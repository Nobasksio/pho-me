<template>
  <div class="garden-side background">
    <logo></logo>
    <div class="container">
      <div class="full-width row justify-start items-baseline">
        <h1 class="title">КОНТАКТЫ</h1>
      </div>
      <div class="row justify-between">
        <div class="col-auto">
          <div class="under-title">АДРЕС</div>
          <div class="description">
            г. Иркутск, ул. 3 июля, 25<br />ТРК «Модный Квартал», 2 этаж
          </div>
        </div>
        <div class="col-auto">
          <div class="under-title">РЕЖИМ РАБОТЫ</div>
          <div class="description">ВС - ЧТ с 12:00 до 22:00
            <br />ПТ - СБ с 12:00 до 23:00</div>
        </div>
        <div class="col-auto">
          <div class="under-title">КОНТАКТЫ</div>
          <div class="description">
            <u
              ><a href="tel:83952000000">{{ this.contacts.phone }}</a><br />
              <a href="mailto:contact@pho-me.ru">hello@pho-me.ru</a></u
            >
          </div>
        </div>
      </div>
      <div class="cardBox">
        <div class="row descriptionZones justify-start" v-if="!showMap">
          <div class="row items-center q-pr-xs q-pl-xs">
            <div class="yellow-zone"></div>
            <div class="delivery-text">от 800 руб</div>
          </div>
          <div class="row items-center q-pr-xs q-pl-xs">
            <div class="blue-zone pl-3"></div>
            <div class="delivery-text">от 1100 руб</div>
          </div>
          <div class="row items-center q-pr-xs q-pl-xs">
            <div class="purple-zone pl-3"></div>
            <div class="delivery-text">от 1400 руб</div>
          </div>
          <div class="row items-center q-pr-xs q-pl-xs">
            <div class="green-zone pl-3"></div>
            <div class="delivery-text">от 1700 руб</div>
          </div>
        </div>
        <div class="relative-box">
          <q-btn
            flat
            size="md"
            class="pho-btn-med"
            no-caps
            unelevated
            v-if="showMap"
          >
            <div @click="showMaps">Показать зоны доставки</div>
          </q-btn>
          <q-btn
            flat
            size="md"
            class="pho-btn-med"
            no-caps
            unelevated
            v-if="!showMap"
          >
            <div @click="showMaps">Показать карту</div>
          </q-btn>
          <yandex-map
            class="yandex-map-user"
            :class="{active: showMap === false, noActive: showMap === true}"
            :controls="['zoomControl']"
            :zoom="12"
            :coords="[52.273108, 104.291269]"
            style="width: 100%; height: 438px"
            v-if="isMounted"
          >
          <template  v-if="!showMap">
            <ymap-marker
              :balloon="{
                body: item.properties.description,
              }"
              :coords="getPriceZoneCoords(item)"
              :key="index"
              :marker-fill="{
                color: item.properties.fill,
                opacity: item.properties['fill-opacity'],
              }"
              :marker-stroke="{
                color: item.properties.stroke,
                width: item.properties['stroke-width'],
                opacity: item.properties['stroke-opacity'],
              }"
              :markerId="'m' + index"
              marker-type="Polygon"
              v-for="(item, index) in dsZonesPriced.features"
            ></ymap-marker>
            <ymap-marker
              :coords="[ourDepartment[0], ourDepartment[1]]"
              markerId="ourMap"
              hint-content="Pho Me"
              marker-type="Placemark"
              :properties="departmentProperty"
            ></ymap-marker>
          </template>
<template v-else>
            <ymap-marker
              :coords="[coords[0], coords[1]]"
              markerId="42m"
              marker-type="Placemark"
            ></ymap-marker>
            <ymap-marker
              :coords="[ourDepartment[0], ourDepartment[1]]"
              markerId="ourMap"
              hint-content="Pho Me"
              marker-type="Placemark"
              :properties="departmentProperty"
            ></ymap-marker>
            </template>
          </yandex-map>
        </div>
      </div>
      <div class="follow-container row justify-start items-center">
        <div class="under-title">FOLLOW YOUR LOVE:</div>
        <a :href="contacts.instagram" target="_blank"
          ><img class="icon-img" src="../assets/icon/inst-icon.png"
        /></a>
<!--        <a href="https://www.facebook.com/phome.irk/" target="_blank"
          ><img class="icon-img" src="../assets/icon/fb-icon.png"
        /></a> -->
      </div>
    </div>
    <bottom-navigation></bottom-navigation>
  </div>
</template >

<script >
import { mapState } from 'vuex';
import { loadYmap } from 'vue-yandex-maps';
// import YmapConstructor from '../boot/yandex-map-constructor.json';
import BottomNavigation from '../components/navigation/BottomNavigation.vue';
import logo from '../components/navigation/logo.vue';

export default {
  components: { logo, BottomNavigation },
  name: 'ContactsPage',
  data() {
    return {
      // dsZonesPriced: YmapConstructor,
      coords: [],
      centerMap: [52.286191, 104.297709],
      ourDepartment: [52.27333480057664, 104.29042273754133],
      loading: false,
      departmentProperty: {
        description: 'Наш ресторан',
        iconCaption: 'Фо Ми',
        'marker-color': '#f371d1',
      },
      showMap: true,
      makeActive: '',
      showZones: 0,
      isMounted: false,
    };
  },
  computed: {
    ...mapState('contacts', ['contacts', 'dsZonesPriced']),
  },
  methods: {
    getPriceZoneCoords(item) {
      const result = item.geometry.coordinates[0];
      return [result];
    },
    showMaps() {
      this.showMap = !this.showMap;
    },
  },
  async mounted() {
    await this.$store.dispatch('contacts/getContacts');
    this.isMounted = true;
    const settings = {
      apiKey: '7df138bc-f837-4e1b-b1c3-9790e63279b0',
      lang: 'ru_RU',
      coordorder: 'latlong',
      version: '2.1',
    };
    await loadYmap(settings);
    // eslint-disable-next-line
    this.ymapsObj = ymaps;
  },
};
</script >

<style scoped lang='scss'>
.garden-side {
  background: radial-gradient(#f8c200, #5b9f58);
  min-height: 100%;
  overflow: hidden;
  padding: 0 120px 178px 120px;
  position: relative;
}

.container {
  padding: 0;
}

.under-title {
  font-size: 36px;
  font-family: lb;
  line-height: 48.6px;
  font-weight: 900;
  color: #fff;
}

.description {
  font-family: tr;
  font-size: 24px;
  line-height: 36px;
  color: #fff;
  margin-right: 10px;
}

a {
  color: #fff;
}

.relative-box {
  position: relative;
  margin: 0 auto;
  max-width: 1400px;
}

.yandex-map-user {
  width: 100%;
  height: 310px;
  border-radius: 15px;
  margin: 38px 0;
  overflow: hidden;

  &.active {
    animation: active-animation 0.2s;
  }
  &.noActive {
    animation: noactive-animation 0.2s;
  }
}

@keyframes active-animation {
  from {
   margin-top: -68px;
  }
  25% {
    margin-top: -51px;
  }
  50% {
    margin-top: -34px;
  }
  75% {
    margin-top: -17px;
  }
  to {
    margin-top: 0;
  }
}

@keyframes noactive-animation {
  from {
   margin-top: 136px;
  }
  25% {
    margin-top: 111px;
  }
  50% {
    margin-top: 86px;
  }
  75% {
    margin-top: 61px;
  }
  to {
    margin-top: 36px;
  }
}

.descriptionZones {
  margin: 20px 0 20px 0;
}

.follow-container {
  margin-top: 42px;
}

.icon-img {
  margin-left: 50px;
}

@media only screen and (max-width: 1023px) {
  .garden-side {
    padding: 25.51px 14.83px  48px 14.83px;
  }

  .title {
    margin: 0 0 18px 0;
  }
  .logo {
    display: none;
  }

  .under-title {
    font-size: 20px;
    line-height: 27px;
    margin-right: 10px;
  }

  .description {
    font-size: 16px;
    line-height: 22px;
    padding-bottom: 31px;
  }

  .icon-img {
    margin-left: 26.04px;
    height: 22.18px;
  }
  .relative-box {
    margin: 0 -14.83px 0 -14.83px;
  }
  .yandex-map-user {
    border-radius: 0;
  }
}

.cardBox {
}

.pho-btn-med {
  margin-top: 10px;
  font-family: lcb;
  border-radius: 10px;
  background: #fcd000;
  border: 2px solid #fcd000;
  color: #4f4f4f;
  font-size: 24px;
  text-transform: none;
  position: absolute;
  z-index: 2;
  left: 10px;
}

.delivery-text {
  color: white;
  font-size: 20px;
}

.yellow-zone {
  width: 50px;
  height: 30px;
  border-radius: 20px;
  background: #ffd21e;
  margin: 20px 10px 20px 0;
}
.blue-zone {
  width: 50px;
  height: 30px;
  border-radius: 20px;
  background: #82cdff;
  margin: 20px 10px 20px 0px;
}
.purple-zone {
  width: 50px;
  height: 30px;
  border-radius: 20px;
  background: #f371d1;
  margin: 20px 10px 20px 0px;
}
.green-zone {
  width: 50px;
  height: 30px;
  border-radius: 20px;
  background: #56db40;
  margin: 20px 10px 20px 0px;
}
</style>
