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
        <div class="attr_container">
          <div class="attr_name">{{ attrColorList.name }} &nbsp;&nbsp;&nbsp;&nbsp;:</div>
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
          <div class="attr_name">{{ attrSizeList.name }} &nbsp;&nbsp;:</div>
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
            <div class="attr_name">Toptan Fiyat (Adet):</div>
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
            <p>Toplam :</p>
            <h1 id="total"></h1>
          </div>
        </div>
        <div class="attr_container add_btn">
          <div style="margin-top: -1.5rem">
            <img src="../assets/shipping.png" class="shipping" />
            <span style="margin: auto; font-size: 18px">Kargo ücreti: Ücretsiz</span>
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
    console.log(this.baremScale);

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
      const b = this.myData.productVariants.filter(
        (item) =>
          item.attributes[1].value === this.selectedColor &&
          item.attributes[0].value === e.target.value
      );
      this.colorsOfImg = [];
      console.log(b);
      for (let i = 0; i < b.length; i++) {
        const element = b[i];
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
        this.count = this.baremValue * 9.5;
        totalID.innerHTML = this.count + " TL";
        this.writeBarem = this.baremScale[0];
        document.getElementById("9.5").style.backgroundColor = "#3EB595";
        document.getElementById("7.13").style.backgroundColor = "#f7f7f7";
        document.getElementById("8.46").style.backgroundColor = "#f7f7f7";
      } else if (600 <= this.baremValue && this.baremValue <= 799) {
        this.count = this.baremValue * 8.46;
        totalID.innerHTML = this.count + " TL";
        this.writeBarem = this.baremScale[1];
        document.getElementById("9.5").style.backgroundColor = "#f7f7f7";
        document.getElementById("7.13").style.backgroundColor = "#f7f7f7";
        document.getElementById("8.46").style.backgroundColor = "#3EB595";
      } else if (800 <= this.baremValue && this.baremValue <= 2147483647) {
        this.count = this.baremValue * 7.13;
        totalID.innerHTML = this.count + " TL";
        this.writeBarem = this.baremScale[2];
        document.getElementById("9.5").style.backgroundColor = "#f7f7f7";
        document.getElementById("8.46").style.backgroundColor = "#f7f7f7";
        document.getElementById("7.13").style.backgroundColor = "#3EB595";
      } else {
        this.count = this.baremValue;
        totalID.innerHTML = this.count + " TL";
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
          this.writeBarem.minimumQuantity + " " + this.writeBarem.maximumQuantity
        );
      } else {
        console.log(this.writeBarem);
      }
      console.log(this.sizeId);
    },
  },
};
</script>

<style scoped>
.container {
  width: calc(78%);
  height: calc(800px);
  display: flex;
  align-content: center;
  justify-content: center;
  align-items: flex-start;
  margin: auto;
}
.row {
  width: calc(100%);
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-content: center;
}
.column {
  width: calc(50%);
  padding: 10px;
  display: flex;
  flex-direction: column;
}
.column:first-child {
  align-items: center;
}
.column:nth-child(2) {
  text-align: left;
}
.thumbnail {
  margin-top: 30px;
  height: 170px;
}
.star_area {
  margin-bottom: 1rem;
}
.star {
  transform: scale(1) !important;
  width: 1rem;
  height: 1rem;
}
img {
  width: 30rem;
  height: 30rem;
  transform: scale(0.9);
}
img:hover {
  transform: scale(1.1);
}
.thumbnail img {
  width: 5rem;
  height: 5rem;
}
.title {
  font-size: 40px;
  line-height: normal;
}
.attr_container {
  display: flex;
  flex-direction: row;
  align-items: center;
  font-size: 22px;
  margin-top: 10px;
}
.attr_values {
  margin-left: 2rem;
}
.attr_values button {
  padding: 1rem;
  margin-right: 1rem;
  border: none;
  border-radius: 3px;
  cursor: pointer;
  outline: none;
  width: 5rem;
}
.attr_values button:active {
  background-color: rgba(128, 128, 128, 0.123);
}
.barem_area {
  background-color: #f7f7f7;
  padding-top: 1rem;
  padding-right: 1rem;
  border: none;
  border-radius: 20px 20px 0 0;
}
.count_area {
  background-color: #f7f7f7;
  padding-top: 1rem;
  padding-right: 1rem;
  border: none;
  border-radius: 0 0 20px 20px;
  margin-top: 0rem;
}
.count_area span {
  margin-left: 2rem;
}
.barem_area .attr_name {
  margin: 2rem;
}
.barem_list {
  display: flex;
  flex-direction: row;
  justify-content: flex-start;
  width: 100%;
}
.barem_container {
  text-align: center;
  padding: 4px;
  margin-bottom: 1rem;
  margin-left: 1rem;
  box-shadow: 0 3px 10px rgb(0 0 0 / 0.2);
  border-radius: 20px;
  padding-top: 1rem;
}
.barem_container p {
  margin-top: 10px;
  text-align: center;
  font-size: 18px;
}
.barem_container span {
  width: 8rem;
  font-size: 22px;
}
.total_area {
  margin: 2rem;
}
.total_area p {
  margin: auto 0;
  margin-left: 0;
  font-size: 28px;
  font-weight: 700;
}
.total_area h1 {
  margin-left: 2rem;
}
.add_btn {
  margin: 2rem;
  margin-left: -6rem;
  margin-top: -1rem;
  display: flex;
  flex-direction: column;
}
.shipping {
  width: 2rem;
  height: 2rem;
  margin-bottom: -0.5rem;
}
#add_btn {
  background-color: #3eb595;
  border: none;
  color: white;
  padding: 15px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 22px;
  cursor: pointer;
  border-radius: 8px;
}
button {
  transform: scale(0.95);
}
button:focus,
button:hover {
  transform: scale(1.05);
}
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

/* Firefox */
input[type="number"] {
  -moz-appearance: textfield;
}

input[type="number"] {
  width: 15%;
  padding: 12px 20px;
  margin: 8px 0;
  box-sizing: border-box;
  border: 2px solid #e9f2ec;
  border-radius: 4px;
  transform: scale(0.98);
  border-radius: 20px;
  margin-left: 10px;
}
input[type="number"]:focus {
  border: 2px solid #e9f2ec;
  outline: none;
  transform: scale(1.02);
}
</style>
