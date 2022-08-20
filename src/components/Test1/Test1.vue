<template>
  <div>
    <div class="test1__search-wrap" :class="[isShowSearch ? 'active' : '']">
      <div class="overlay" @click="isShowSearch=false"></div>
      <input
        type="text"
        class="tes1__search-provinces"
        placeholder="Nhập tên thành phố để tìm kiếm..."
        @focus="isShowSearch=true"
        :style="styleWrapSelect"
        v-model="keySearch"
      />
      <img class="test1__search-icon" :src="search" alt="" />
      <div class="selected__wrap" ref="selected">
        <SelectedItem
          v-for="(item, index) in listSelect"
          :key="index"
          :selectItem="item"
          @onDelete="handleSelect"
        />
      </div>
      <ResultSearch @onSelect="handleSelect" :list="list" />
    </div>
  </div>
</template>

<script>
import search from "../../assets/svg/search-svgrepo-com.svg";
import ResultSearch from "./ResultSearch.vue";
import SelectedItem from "./SelectedItem.vue";
import axios from "axios";
export default {
  data() {
    return {
      search,
      isShowSearch: false,
      list: [],
      listSelect: [],
      offset: 0,
      keySearch: "",
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
          this.listSelect.push(payload.value);
          break;
        case "delete":
          this.listSelect.splice(payload.index, 1);
          break;

        default:
          break;
      }
    },
    getJobs: function () {
      axios
        .get(this.jobsListURL + "?" + this.AxiosParams)
        .then((response) => (this.jobsList = response.data.data))

        .catch((error) => {
          console.log(error);
          this.errored = true;
        })

        .finally(() => (this.loading = false));
    },
  },
  computed: {
    styleWrapSelect() {
      if (this.listSelect.length > 0) {
        return { paddingLeft: this.$refs.selected.offsetWidth + 40 + "px" };
      }
    },
  },
  watch: {
    keySearch(newValue) {
       axios
          .get(`https://provinces.open-api.vn/api/d/search/`, {
            params: {
              q: newValue,
            },
          })
          .then((response) => {
            this.list=response.data.map(provinces=>provinces.name)
          })
          .catch(function (err) {
            this.list=[]
          });
     

    //  this.list=listProvices.map(provinces=>provinces.name)
    },
  },
};
</script>
    

<style scoped>
.test1__search-wrap {
  margin: 0 auto;
  position: relative;
  width: 400px;
  height: 49px;
}
.tes1__search-provinces {
  position: relative;
  width: 100%;
  height: 49px;
  border-radius: 4px;
  padding: 8px 10px 8px 40px;
  border: 1px solid #dbdbdb;
  background-color: rgba(230, 249, 255, 0.2);
  overflow-y: auto;
  z-index: 10;
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
  position: absolute;
  left: 10px;
  top: 50%;
  transform: translateY(-50%);
  z-index: 10;
}
.tes1__search-provinces:focus {
  outline: 1px solid #1991d2;
}

.tes1__search-provinces:focus ~ .search__output {
  opacity: 1;
  visibility: visible;
}
.selected__wrap {
  position: absolute;
  display: flex;
  width: 245px;
  height: auto;
  overflow-y: auto;
  gap: 8px;
  top: 50%;
  left: 34px;
  transform: translateY(-50%);
  z-index: 12;
}
.selected__wrap::-webkit-scrollbar {
  display: none;
}
.overlay{
  display: none;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 1;
}
.active .overlay{
  display: block;
}
</style>