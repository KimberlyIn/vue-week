<template>
	<div>
		<h1>Jemma 的產品頁面</h1>
		<Loading :active="isLoading"></Loading>
		<!-- align-middle 查 -->
		<table class="table align-middle">
			<thead>
				<tr>
					<th>圖片</th>
					<th>商品名稱</th>
					<th>價格</th>
					<th></th>
				</tr>
			</thead>
			<tbody>
				<!-- products 多筆渲染 -->
				<tr v-for="item in products" :key="item.id">
					<td style="width: 200px">
						<div style="width:100px; background-size: cover; background-position: center"
						:style="{ backgroundImage:`url(${item.imageUrl})` }">
						<!-- 加入背景圖片，前面是屬性名稱，後面是值 -->
						</div>
					</td>
					<td>
						{{ item.title }}
					</td>
					<td>
						<!-- 如果金額不等於 item.price 就渲染 item.origin_price -->
						<!-- 當有輸入售價時，就把原價畫上刪除線、並顯示售價。如果沒有售價的話，就直接顯示原價 -->
						<div class="h5" v-if="!item.price">{{ item.origin_price }} 元</div>
						<div class="h6" v-if="item.price">原價 {{ item.origin_price }} 元</div>
						<div class="h5" v-if="item.price">現在只要 {{ item.price }} 元</div>
					</td>
					<td>
						<div class="btn-group btn-group-sm">
							<!-- @click="getProduct(item.id)" v-on 點擊觸發函式 -->
							<!-- :disabled="loadingStatus.loadingItem === item.id" 
							如果 loadingStatus.loadingItem 等於 item.id 就啟動 disabled -->
							<button
								type="button"
								class="btn btn-outline-secondary"
								@click="getProduct(item.id)"
								:disabled="loadingStatus.loadingItem === item.id">
								<!-- v-if="lodingStatus.lodingItem === item.id" 
								v-if 為條件判斷 這裡為 disabled 的判斷式 -->
								<i class="fas fa-spinner fa-pulse"
									v-if="lodingStatus.lodingItem === item.id"
								></i>
									查看更多
							</button>
							<button
								type="button"
								class="btn btn-outline-danger"
								@click="addToCart(item.id)"
								:disabled="loadingStatus.loadingItem === item.id">
								<i class="fas fa-spinner fa-pulse"
									v-if="loadingStatus.loadingItem === item.id"
								></i>
								加到購物車
							</button>
						</div>
					</td>
				</tr>
			</tbody>
		</table>
		<UserProductModal
		ref="userProductModal"
		:product="product"
		@add-to-cart="addToCart">
		</UserProductModal>
	</div>
</template>

<script>
// @ = src + 絕對路徑
import UserProductModal from '@/components/UserProductModal.vue';

export default {
	name: 'Products',
	data() {
		return {
			// 產品列表
			products: [],
			// disabled 判斷
			loadingStatus: {
				loadingItem: '',
			},
			// 問
			isLoading: false,
			product: {},
		};
	},
	// 問
	components: {
		UserProductModal,
	},
	created() {
		// 研究
		this.getProducts();
	},
	methods: {
		// 問
		// 加入購物車
		addToCart(id,qty = 1) {
			// 問
			this.isLoading = true;
			// 問
			const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart`;
			// 阻止用戶一直去點擊 loadingStatus.loadingItem 如果等於 id 就會變成 disabled 的狀態
			this.loadingStatus.loadingItem = id;
			// 購物車結構
			const cart = {
				product_id: id,
				qty,
			};
			// [參數]: { "data": { "product_id":"-L9tH8jxVb2Ka_DYPwng","qty":1 } }
			this.$http.post(url, { data: cart})
			.then((response) => {
				// console.log(response);
				if(response.data.success) {
					alert(response.data.message);
					// 資料取得完成以後 disabled 狀態才會消失
					this.loadingStatus.loadingItem = '';
					this.$refs.UserProductModal.hideMod();
					this.isLoading = false;
				}	else {
					alert(response.data.message);
				}
			});
		},
		// 問 取得所有產品細節？
		getProucts() {
			this.isLoading = true;
			const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/products`
			// 問 $http 是什麼 這裡得 this 會直接指向 axios ?
			this.$http.get(url)
			.then((response) => {
				if(response.data.success) {
					// 回傳 response.data 裡面的 products
					this.products = response.data.products;
					this.isLoading = false;
				} else {
					alert(response.data.message);
				}
			});
		},
		// 取得單一商品細節
		getProuct(id) {
			this.isLoading = true;
			const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/product/${id}`;
			this.loadingStatus.loadingItem = id;
			this.$http.get(url)
			.then((response) => {
				if(response.data.success) {
					this.loadingStatus.loadingItem = '';
					this.product = response.data.product;
					this.isLoading = false;
					this.$refs.UserProductModal.openModal();
				} else {
					alert(response.data.message);
				}
			});
		},
	}
}
</script>