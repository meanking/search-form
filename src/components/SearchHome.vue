<template>
  <div>
    <div>
      <label for="type_aller">
        <input type="radio" v-model="type" value="aller" id="type_aller" /> ALLER
      </label>
      <label for="type_retour" style="margin-left: 20px;">
        <input type="radio" v-model="type" value="retour" id="type_retour" checked /> RETOUR
      </label>
    </div>
    <div style="display: flex; margin-top: 20px;">
      <div>
        <div>
          <select id="city" v-model="local" style="width: 450px; height: 40px;">
            <option v-for="(city, i) in cities" :key="i">{{city.city}} - {{city.local}}</option>
          </select>
        </div>
        <div v-if="showDispature" style="margin-top: 20px;">
          <select id="changedLocal" disabled style="width: 450px; height: 40px;">
            <option>{{changedLocal}}</option>
          </select>
        </div>
      </div>
      <div style="margin-left: 30px;">
        <div>
          <input
            type="text"
            id="date1"
            v-model="travel_date1"
            style="width: 150px; padding: 10px 10px;"
            placeholder="Travel date"
          />
        </div>
        <div style="margin-top: 20px;">
          <input
            type="text"
            id="date2"
            v-model="travel_date2"
            style="width: 150px; padding: 10px 10px;"
            placeholder="Travel date"
          />
        </div>
      </div>
      <div style="margin-left: 30px;">
        <div>
          <button
            style="width: 450px; height: 40px; text-align: left;"
            @click="openPassager"
          >{{ passagers }} PASSAGERS</button>
          <div
            v-if="showPassagers"
            style="margin-top: 5px; border: 1px solid #f5f5f5; padding: 10px; position: absolute; z-index: 1; background-color: white;"
          >
            <div style="display: flex;">
              <p>Combien de passagers?</p>
              <select v-model="passagers" style="margin-left: 30px; width: 225px; height: 40px;">
                <option v-for="passager in passagersRange" :key="passager">{{passager}}</option>
              </select>
            </div>
            <div>
              <p>Âge des passagers au moment du voyage</p>
            </div>
            <div style="display: flex;" v-for="idx in parseInt(passagers)" :key="idx">
              <p>Passager {{idx}}</p>
              <select style="margin-left: 60px; width: 130px; height: 40px;">
                <option v-for="a in 18" :key="a">{{ (19 - a) == 18? '18+': 19 - a }}</option>
              </select>
              <select style="margin-left: 30px; width: 130px; height: 40px;">
                <option selected>HOMME</option>
                <option>FEMME</option>
              </select>
            </div>
            <div style="margin-top: 20px;">
              <span>Résident</span>
              <input type="checkbox" style="margin-left: 100px;" />
            </div>
            <div style="margin-top: 20px; text-align: right;">
              <button
                style="background-color: #FB6340; color: white; border: none; padding: 7px 10px; border-radius: 3px; cursor: pointer;"
                @click="confirmPassager"
              >CONFIRMER</button>
            </div>
          </div>
        </div>
        <div style="margin-top: 20px;">
          <button
            style="width: 450px; height: 40px; text-align: left;"
            @click="openVehicule"
          >1 VEHICULE : {{ vehicule }} {{ vehicule_type }}</button>
          <div
            v-if="showVehicle"
            style="margin-top: 5px; border: 1px solid #f5f5f5; padding: 10px; position: absolute; z-index: 1; background-color: white;"
          >
            <div>
              <div>
                <select v-model="vehicule" style="width: 430px; height: 40px;">
                  <option v-for="vehicle in vehicles" :key="vehicle">{{vehicle}}</option>
                </select>
              </div>
              <div>
                <select v-model="vehicule_type" style="width: 430px; height: 40px;">
                  <option v-for="vehicle_type in vehicle_types" :key="vehicle_type">{{vehicle_type}}</option>
                </select>
              </div>
              <p style="text-align: center;">{{vehicule}} {{vehicule_type}}</p>
            </div>
            <div style="margin-top: 20px;">
              <label for="vous">
                <input id="vous" type="checkbox" />
                <span style="margin-left: 15px;">Avez vous un porte bagage ?</span>
              </label>
            </div>
            <div style="margin-top: 20px;">
              <span>Hauteur</span>
              <input
                type="number"
                max="150"
                min="0"
                style="width: 200px; height: 30px; margin-left: 140px;"
              /> cm
            </div>
            <div style="margin-top: 20px;">
              <label for="caravane">
                <input id="caravane" type="checkbox" />
                <span style="margin-left: 15px;">Avez vous une caravane ou remorque ?</span>
              </label>
            </div>
            <div style="margin-top: 20px;">
              <span>Remorque Hauteur</span>
              <input
                type="number"
                max="4"
                min="0"
                style="width: 200px; height: 30px; margin-left: 65px;"
              /> m
            </div>
            <div style="margin-top: 20px;">
              <span>Remorque longueur</span>
              <input
                type="number"
                max="7"
                min="0"
                style="width: 200px; height: 30px; margin-left: 60px;"
              /> m
            </div>
            <div style="margin-top: 20px; text-align: right;">
              <button
                style="background-color: #FB6340; color: white; border: none; padding: 7px 10px; border-radius: 3px; cursor: pointer;"
                @click="confirmVehicle"
              >CONFIRMER</button>
            </div>
          </div>
        </div>
        <div style="text-align: center; margin-top: 60px;">
          <button
            style="width: 150px; height: 40px; background-color: #5E72E4; color: white; border: none; border-radius: 3px;"
          >CONTINUE</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, watchEffect } from "vue";
import data from "../mock/cities";

export default {
  name: "SearchHome",
  props: {},
  setup() {
    const cities = ref(data.cities);
    const type = ref("retour");
    const local = ref();
    let changedLocal = ref();
    let showDispature = ref(true);
    let passagers = ref(3);
    let passagersRange = [1, 2, 3, 4, 5, 6, 7, 8, 9];
    let showPassagers = ref(false);
    let showVehicle = ref(false);
    let vehicles = ["Ford", "Mercedes", "Maybah", "Benz", "Nissan"];
    let vehicle_types = ["Class A", "Class B", "Class C", "Class D", "Class E"];
    let vehicule = ref();
    let vehicule_type = ref();

    function openPassager() {
      showPassagers.value = !showPassagers.value;
    }

    function openVehicule() {
      showVehicle.value = !showVehicle.value;
    }

    function confirmPassager() {
      showPassagers.value = false;
    }

    function confirmVehicle() {
      showVehicle.value = false;
    }

    watchEffect(() => {
      if (type.value == "aller") {
        showDispature.value = false;
      } else {
        showDispature.value = true;
      }

      if (local.value != null) {
        let val = local.value;
        if (val.includes("-")) {
          let arr = val.split("-");
          changedLocal.value = arr[1] + " - " + arr[0];
        }
      }
    });

    return {
      cities,
      type,
      local,
      showDispature,
      changedLocal,
      passagers,
      passagersRange,
      openPassager,
      showPassagers,
      showVehicle,
      openVehicule,
      vehicles,
      vehicle_types,
      vehicule,
      vehicule_type,
      confirmPassager,
      confirmVehicle,
    };
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
