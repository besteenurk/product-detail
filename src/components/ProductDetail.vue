<template>
  <div class="container">
    <div class="row">
      <div class="column">
        <img :v-model="selectedImg" :src="selectedImg" />
        <div class="thumbnail" id="allImg">
          <img
            v-for="item in allImg"
            :key="item.id"
            @click="changeImg($event)"
            :v-model="selectedImg"
            :src="item"
          />
        </div>
        <div class="thumbnail" id="selImg">
          <img
            v-for="item in colorsOfImg"
            :key="item.id"
            @click="changeImg($event)"
            :v-model="selectedImg"
            :src="item"
          />
        </div>
      </div>
      <div class="column">
        <h2 class="title">{{ myData.productTitle }}</h2>
        <div class="star_area">
          <img src="../assets/star.png" class="star" />
          <img src="../assets/star.png" class="star" />
          <img src="../assets/star.png" class="star" />
          <img src="../assets/star.png" class="star" />
          <img src="../assets/star.png" class="star" />

          <span> &nbsp;&nbsp; 26 Yorum</span>
        </div>
        <div class="star_area">
          <span>10 TL / Birim Fiyatı</span>
        </div>
        <div class="attr_container">
          <div class="attr_name">{{ attrColorList.name }} :</div>
          <div class="attr_values">
            <button
              @click="checkVariant(item)"
              v-for="item in attrColorList.values"
              :key="item.title"
              id="btn_color"
            >
              {{ item }}
            </button>
          </div>
        </div>
        <div class="attr_container">
          <div class="attr_name">{{ attrSizeList.name }} :</div>
          <div class="attr_values">
            <!-- <button v-for="item in attrSizeList.values" :key="item.title">
                {{ item }}
              </button> -->
            <button
              @click="
                checkSize($event);
                getImgFromColor($event);
              "
              id="1"
              type="button"
              value="S"
            >
              S
            </button>
            <button
              @click="
                checkSize($event);
                getImgFromColor($event);
              "
              id="2"
              type="button"
              value="M"
            >
              M
            </button>
            <button
              @click="
                checkSize($event);
                getImgFromColor($event);
              "
              id="3"
              type="button"
              value="L"
            >
              L
            </button>
            <button
              @click="
                checkSize($event);
                getImgFromColor($event);
              "
              id="4"
              type="button"
              value="XL"
            >
              XL
            </button>
          </div>
        </div>
        <div class="attr_container barem_area">
          <div class="barem_list">
            <div class="attr_name">
              Toptan Fiyat <br />
              (Adet):
            </div>
            <div
              class="barem_container"
              v-for="item in baremScale"
              :key="item.price"
              :id="item.price"
            >
              <span>{{ item.minimumQuantity }} - {{ item.maximumQuantity }}</span>
              <p>{{ item.price }} TL</p>
            </div>
          </div>
        </div>
        <div class="attr_container count_area">
          <div class="barem_list" style="margin-top: 1rem">
            <span><p>Adet :</p></span>
            <input
              @keydown.enter="calculate($event)"
              type="number"
              ref="groupId"
              v-model="baremCount"
            />
          </div>
        </div>
        <div class="attr_container">
          <div class="barem_list total_area">
            <p>Toplam:</p>
            <h1 id="total"></h1>
          </div>
        </div>
        <div class="attr_container add_btn">
          <div style="margin-top: -1.5rem">
            <img src="../assets/shipping.png" class="shipping" />
            <span class="shipping_inf">Kargo ücreti: Ücretsiz</span>
          </div>
          <button @click="addBasket()" id="add_btn">SEPETE EKLE</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import dataL from "../data/data.json";

export default {
  name: "HelloWorld",
  data() {
    return {
      myData: dataL,
      attr: [],
      attrColorList: { name: "", values: [] },
      attrSizeList: { name: "", values: [] },
      variant: [],
      selected: [],
      selectedColor: "",
      selectedSize: "",
      selectedImg: "",
      allImg: [],
      colorsOfImg: [],
      sizeId: "",
      baremCount: "",
      baremValue: "",
      count: "",
      baremScale: "",
      writeBarem: [],
    };
  },
  mounted() {
    this.attr.push(dataL.selectableAttributes);
    this.attrColorList.name = this.attr[0][0].name;
    this.attrColorList.values = this.attr[0][0].values;
    this.attrSizeList.name = this.attr[0][1].name;
    this.attrSizeList.values = this.attr[0][1].values;
    this.baremScale = dataL.baremList;

    this.selectedImg = "https://n11scdn.akamaized.net/a1/500/03/59/50/58/43001440.jpg";

    for (let i = 0; i < this.myData.productVariants.length; i++) {
      const element = this.myData.productVariants[i];
      element.images.forEach((element) => {
        this.allImg.push(element);
      });
    }

    if (this.count === "") {
      document.getElementById("add_btn").disabled = true;
    }

    document.getElementById("selImg").style.display = "none";
  },
  methods: {
    checkVariant(val) {
      this.selectedColor = val;

      var sSize = document.getElementById("1");
      var mSize = document.getElementById("2");
      var lSize = document.getElementById("3");
      var xlSize = document.getElementById("4");

      const b = this.myData.productVariants.filter(
        (item) => item.attributes[1].value === val
      );
      // get attributes value size
      this.selected = [];

      for (let i = 0; i < b.length; i++) {
        const element = b[i];
        this.selected.push(element.attributes[0].value);
      }

      this.selected.forEach((element) => {
        sSize.disabled = true;
        // eslint-disable-next-line no-constant-condition
        if (!(element.indexOf(mSize.value) === 0 || 1)) {
          mSize.disabled = true;
        } else {
          mSize.disabled = false;
        }
        // eslint-disable-next-line no-constant-condition
        if (!(element.indexOf(lSize.value) === 0 || 1)) {
          lSize.disabled = true;
        } else {
          lSize.disabled = false;
        }
        // eslint-disable-next-line no-constant-condition
        if (!(element.indexOf(xlSize.value) === 0 || 1)) {
          xlSize.disabled = true;
        } else {
          xlSize.disabled = false;
        }

        if (val === "Siyah") {
          mSize.disabled = true;
          xlSize.disabled = true;
        }
      });
    },
    checkSize(e) {
      this.selectedSize = e.target.value;
    },
    changeImg(e) {
      this.selectedImg = e.target.currentSrc;
    },
    getImgFromColor(e) {
      document.getElementById("allImg").style.display = "none";
      document.getElementById("selImg").style.display = "block";
      const product = this.myData.productVariants.filter(
        (item) =>
          item.attributes[1].value === this.selectedColor &&
          item.attributes[0].value === e.target.value
      );
      this.colorsOfImg = [];
      for (let i = 0; i < product.length; i++) {
        const element = product[i];
        this.sizeId = element.id;
        element.images.forEach((element) => {
          this.colorsOfImg.push(element);
        });
      }
    },
    calculate(e) {
      e.preventDefault();
      this.baremValue = this.$refs.groupId.value;
      var totalID = document.getElementById("total");

      if (120 <= this.baremValue && this.baremValue <= 599) {
        this.count = parseFloat(this.baremValue * 9.5).toFixed(2);
        totalID.innerHTML = this.count + " TL";
        this.writeBarem = this.baremScale[0];
        document.getElementById("9.5").style.backgroundColor = "#3EB595";
        document.getElementById("7.13").style.backgroundColor = "#f7f7f7";
        document.getElementById("8.46").style.backgroundColor = "#f7f7f7";
      } else if (600 <= this.baremValue && this.baremValue <= 799) {
        this.count = parseFloat(this.baremValue * 8.46).toFixed(2);
        totalID.innerHTML = this.count + " TL";
        this.writeBarem = this.baremScale[1];
        document.getElementById("9.5").style.backgroundColor = "#f7f7f7";
        document.getElementById("7.13").style.backgroundColor = "#f7f7f7";
        document.getElementById("8.46").style.backgroundColor = "#3EB595";
      } else if (800 <= this.baremValue && this.baremValue <= 2147483647) {
        this.count = parseFloat(this.baremValue * 7.13).toFixed(2);
        totalID.innerHTML = this.count + " TL";
        this.writeBarem = this.baremScale[2];
        document.getElementById("9.5").style.backgroundColor = "#f7f7f7";
        document.getElementById("8.46").style.backgroundColor = "#f7f7f7";
        document.getElementById("7.13").style.backgroundColor = "#3EB595";
      } else {
        this.count = this.baremValue;
        totalID.innerHTML = this.count * 10 + " TL";
        this.writeBarem = "No Barem";
        document.getElementById("9.5").style.backgroundColor = "#f7f7f7";
        document.getElementById("8.46").style.backgroundColor = "#f7f7f7";
        document.getElementById("7.13").style.backgroundColor = "#f7f7f7";
      }
      document.getElementById("add_btn").disabled = false;
    },
    addBasket() {
      if (this.writeBarem != "No Barem") {
        console.log(
          "Barem Area: ",
          this.writeBarem.minimumQuantity + " - " + this.writeBarem.maximumQuantity
        );
      } else {
        console.log("Barem Area: ", this.writeBarem);
      }
      console.log("Product ID: ", this.sizeId);
    },
  },
};
</script>

<style>
@import "./../styles/style.css";
</style>
