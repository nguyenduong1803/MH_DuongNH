<template>
  <div>
    <div class="test1__search-wrap" :class="[isShowSearch ? 'active' : '']">
      <div class="overlay" @click="isShowSearch = false"></div>
      <div class="border">
        <div class="wrap_input">
          <div class="search">
            <img class="test1__search-icon" :src="searchIcon" alt="" />
          </div>
          <div class="selected__wrap" ref="selected">
            <SelectedItem
              v-for="(item, index) in listSelect"
              :key="index"
              :selectItem="item"
              @onDelete="handleSelect"
            />
            
          </div>
          <input
            type="text"
            class="tes1__search-provinces"
            placeholder="Nhập tên thành phố để tìm kiếm..."
            @focus="isShowSearch = true"
            v-model="debounceSearch"
          />
          <ResultSearch
            v-if="debounceSearch!==''&& list.length>0"
            @onSelect="handleSelect"
            :list="list"
            :listSelect="listSelect"
          />
          <div class="search__output" v-else-if="keySearch!==''&& list.length===0 "><p class="notify">Không tìm thấy</p> </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import searchIcon from "../../assets/svg/search-svgrepo-com.svg";
import ResultSearch from "./ResultSearch.vue";
import SelectedItem from "./SelectedItem.vue";
import axios from "axios";
export default {
  data() {
    return {
      searchIcon,
      isShowSearch: false,
      list: [],
      listSelect: [],
      keySearch: "",
      debounceSearch: "",
      timeoutID: null,
    };
  },
  components: {
    ResultSearch,
    SelectedItem,
  },
  methods: {
    handleSelect(payload) {
      switch (payload.type) {
        case "add":
          if (!this.listSelect.some((item) => item === payload.value)) {
            this.listSelect.push(payload.value);
          }
          break;
        case "delete":
          this.listSelect.splice(payload.index, 1);
          break;
        default:
          break;
      }
    },
    debounce(value, delay) {
      clearTimeout(this.timeoutID);
      if(value){
        this.timeoutID = setTimeout(() => {
        this.keySearch = value;
      }, delay);
      }
    },
  },
  computed: {},
  watch: {
    keySearch(newValue) {
      axios
        .get(`https://provinces.open-api.vn/api/d/search/`, {
          params: {
            q: newValue,
          },
        })
        .then((response) => {
          this.list = response.data.map((provinces) => provinces.name);
        })
        .catch(function (err) {
          this.list = [];
        });
    },
    debounceSearch(newValue) {
      this.debounce(newValue, 500);
    },
  },
};
</script>
    

<style scoped>
.test1__search-wrap {
  margin: 0 auto;
  position: relative;
  width: 400px;
  height: auto;
}
.border {
  width: 100%;
  border-radius: 4px;
  border: 1px solid #dbdbdb;
  background-color: rgba(230, 249, 255, 0.2);
}
.tes1__search-provinces {
  position: relative;
  top: -1px;
  min-width: 200px;
  flex: 1;
  height: 49px;
  padding: 8px 10px 8px 10px;
  border: none;
  border-top: 1px solid #dbdbdb;
  border-collapse: separate;
  background-color: rgba(230, 249, 255, 0.2);
  overflow-y: auto;
  z-index: 10;
}
.tes1__search-provinces:focus {
  outline: none !important;
  border-radius: 4px;
}
.tes1__search-provinces::placeholder {
  font-weight: 400;
  font-size: 14px;
  line-height: 20px;
  color: #bfbfbf;
}
.test1__search-icon {
  width: 18px;
  height: 18px;
  fill: #dbdbdb;
  z-index: 10;
}
.search {
  padding: 8px;
  background-color: rgba(230, 249, 255, 0.2);
}
.tes1__search-provinces:focus {
  outline: 1px solid #1991d2;
}

.tes1__search-provinces:focus ~ .search__output {
  opacity: 1;
  visibility: visible;
}
.selected__wrap {
  display: block;

  gap: 8px;
  z-index: 12;
}
.selected__wrap::-webkit-scrollbar {
  display: none;
}
.wrap_input {
  display: flex;
  align-items: center;
  flex-wrap: wrap;
  position: relative;
}
.overlay {
  display: none;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 1;
}
.active .overlay {
  display: block;
}
.search__output {
  position: absolute;
  top: 100%;
  width: 400px;
  max-height: 128px;
  background-color: #f1f5f8;
  box-shadow: 0px 1px 8px rgb(102 102 102 / 25%);
  border-radius: 4px;
  z-index: 2;
  overflow: hidden;
  overflow-y: auto;
  -ms-overflow-style: none; /* IE and Edge */
  scrollbar-width: none; /* Firefox */
  margin: 0;
  transition: 0.2s;
}
.notify{
  padding: 12px;
}
</style>