<template>
  <div class="porject">
    <div class="ironSpecies">
      <select class="product" name="product" id="product" v-model="product">
        <option value="角鐵" selected>角鐵</option>
        <option value="鐵板">鐵板</option>
        <option value="圓鐵">圓鐵</option>
      </select>
      <input type="text" class="size" placeholder="請輸入單位長度(mm)" v-model.number="lang">
      <input type="text" class="quantity" placeholder="請輸入總數量" v-model.number="quan">
      <button class="calcu" @click="caculate">計算</button>
    </div>
    <table class="calculation" border="1">
      <caption>
        <h2>鐵材計算</h2>
      </caption>
      <thead>
        <tr>
          <th>編號</th>
          <th>種類</th>
          <th>尺寸長度</th>
          <th>使用數量</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, id) in useProduct" :key="id">
          <td>{{ item.index }}</td>
          <td>{{ item.product }}</td>
          <td>{{ (item.long) / 1000 }}M</td>
          <td>{{ item.userquan }}</td>
        </tr>
      </tbody>
    </table>
    <table class="Remaining" border="1">
      <caption>
        <h2>剩餘鐵材</h2>
      </caption>
      <thead>
        <tr>
          <th>編號</th>
          <th>種類</th>
          <th>剩餘長度</th>
          <th>數量</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, id) in currentRemain" :key="id">
          <td>{{ item.index }}</td>
          <td>{{ item.product }}</td>
          <td>{{ item.remainLang }}</td>
          <td>{{ item.num }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: "IronSpecies",
  data(){
    return{
      product: '',
      size: [],
      lang: null,
      useLang: 0,
      allquan: 0,
      quan: null,
      useProduct: [],
      remain: [],
      number: 0,
      currentRemain: [],
    };
  },
  methods: {
    caculate(){
      let vm = this;
      let remainLang = 0;
      let firstQuan = 0;
      vm.allquan = 0;
      this.size.some((item) => {
        remainLang = item;
        if(item >= vm.lang){
          while(vm.quan > 0){
            if(vm.quan - firstQuan >= 0){
              firstQuan = parseInt(item / vm.lang);
              if(item % vm.lang !== 0){
                vm.remain.push({
                  index: vm.number,
                  product: this.product,
                  remainLang: (item % vm.lang),
                  num: 1
                });
              }
              vm.allquan++;
              vm.quan-=firstQuan;
            }else{
              remainLang-=vm.lang;
              vm.quan--;
              if(vm.quan === 0){
                vm.allquan++;
                vm.remain.push({
                  index: vm.number,
                  product: this.product,
                  remainLang: remainLang,
                  num: 1
                });
              }
            }
            
          }
          vm.useProduct.push({
            index: vm.number,
            product: vm.product,
            long: item,
            userquan: vm.allquan
          });

          vm.currentRemain = Object.values(vm.remain.reduce((c, {index,product,remainLang,num}) => {
            c[remainLang] = c[remainLang] || {index, product, remainLang, num: 0};
            c[remainLang].product = product;
            c[remainLang].remainLang = remainLang;
            c[remainLang].num+=num;
            return c;
          }, {}));

          vm.number++;
          return true;
        }else if(item < vm.lang){
          return false;
        }
      })
    },
  },
  watch: {
    product(pro){
      if(pro === '角鐵'){
        this.size = [6000, 9000, 10000];
      }else if(pro === '鐵板' || pro === '圓鐵'){
        this.size = [6000];
      }
    }
  }
};
</script>

<style scoped lang="scss">
.porject{
  width: 100%;
  height: 100vh;
  background:#f1f094;
  padding: 3rem 15%;
  display: flex;
  flex-direction: column;
  .ironSpecies{
    padding: 2rem 0;
    display: flex;
    justify-content: center;
    align-items: center;
    .product, .size, .quantity, .calcu{
      border-radius: 0.2rem;
      border: 1px solid gray;
      margin: 0 2rem;
      padding: 0.5rem;
      font-size: 1rem;
      text-align: center;
    }
  }
  .calculation, .Remaining{
    margin: 2rem 0;
    border: 2px solid gray;
    border-collapse: collapse;
    caption{
      margin: 0.5rem 0;
    }
    th, td{
      padding: 0.5rem 0.25rem;
      font-size: 1rem;
    }
  }
}
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
