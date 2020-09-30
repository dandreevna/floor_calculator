<template>
	<div class="kalk7">
		<p class="h1-kalk-poly">Калькулятор стяжки пола</p>
		<hr style="margin-bottom: 20px;">
		<div class="row">
			<div class="col">
				<label>Длина (м):</label><br>
				<input type="number" step="0.1" v-model="length" min="0">
			</div>
			<div class="col">
				<label>Ширина (м):</label><br>
				<input type="number" step="0.1" v-model="width" min="0">
			</div>
		</div>
		<div class="col">
			<label>Площадь (кв м):</label><br>
			<input type="number" step="0.1" v-model="space" min="0">
		</div>
		<div class="col">
			<label>Толщина стяжки (мм):</label><br>
			<input type="number" v-model="thickness" min="0">
		</div>
		<div class="col">
			<input type="radio" v-model="readyMix" value="a"> - Рассчитать смесь готовую в мешках <br>
			<input type="radio" v-model="readyMix" value="b"> - Будем готовить смесь сами
		</div>
		<hr>
		<div v-if="readyMix == 'a'">
			<h2>Готовая смесь:</h2>
			<div class="row">
				<div class="col">
					<label>Цена готовой смеси за мешок (руб):</label><br>
					<input type="number" v-model="priceReadyMix" min="0">
				</div>
				<div class="col">
					<label>Вес готовой смеси (кг):</label><br>
					<input type="number" v-model="weightReadyMix" min="0">
				</div>
			</div>
		</div>
		<div v-else>
			<h2>Готовим смесь сами:</h2>
			<div class="row">
				<div class="col">
					<label>Цена песка за мешок 30 кг (руб):</label><br>
					<input type="number" v-model="priceBag30" min="0">
				</div>
				<div class="col">
					<label>Цена цемента за мешок 50 кг (руб):</label><br>
					<input type="number" v-model="priceBag50" min="0">
				</div>
			</div>
			<div class="row">
				<div class="col">
					<label>Марка цемента:</label><br>
					<select v-model="cementGrade">
						<option>M400</option>
						<option>M500</option>
					</select>
				</div>
				<div class="col">
					<label>Используем пластификатор С3:</label><br>
					<select v-model="C3">
						<option>Да</option>
						<option>Нет</option>
					</select>
				</div>
			</div>
		</div>

		<hr>

		<div class="col">
			<label>Чем укрепляем:</label><br>
			<select v-model="hardener">
				<option>Сетка</option>
				<option>Фибра</option>
				<option>Сетка и фибра</option>
				<option>Ничего</option>
			</select>
		</div>

		<hr>

		<!--<center><button class="sent" @click="clickSent">Расчитать</button></center>-->

		<div v-if="(readyMix == 'b')">
			<p>На стяжку потребуется:</p>
			<p><b>Вода:</b> {{water}} л</p>
			<p><b>Цемент:</b> {{cement}} кг ({{cementBags}} мешков по 50 кг), стоимостью {{cementPrice}} руб</p>
			<p><b>Песок:</b> {{sand}} кг ({{sandBags}} мешков по 30 кг), стоимостью {{sandPrice}} руб</p>
			<p v-if="(C3 == 'Да')"><b>Пластификатор</b></p>

			<p v-if="(hardener == 'Сетка') || (hardener == 'Сетка и фибра')"><b>Сетка:</b> {{ grid }} кв м</p>
		</div>

		<div v-if="(readyMix == 'a')">
			<p><b>Итого вес стяжки:</b> {{ screedWeight }} кг</p>
			<p><b>Необходимо мешков:</b> {{ numberBags }} шт</p>
			<p><b>Стоимость готовой смеси:</b> {{ priceReadyMixBags }} руб</p>
			<p v-if="(hardener == 'Сетка') || (hardener == 'Сетка и фибра')"><b>Сетка:</b> {{ grid }} кв м</p>
		</div>
	</div>
</template>
<script>
	export default {
		watch: {
			length(){
				this.space = this.length*this.width;
			},
			width(){
				this.space = this.length*this.width;
			}
		},
		data(){
			return {
				length: 2,
				width: 2,
				space: 4,
				thickness: 30,
				readyMix: 'a',
				priceReadyMix: 130,
				weightReadyMix: 25,
				priceBag30: 100,
				priceBag50: 240,
				cementGrade: 'M400',
				C3: 'Да',
				hardener:'Сетка',
				//sent: false,
			}
		},
		computed:{
			screedWeight(){
				return this.space * this.thickness * 1.8;
			},
			numberBags(){
				return Math.ceil(this.screedWeight/this.weightReadyMix);
			},
			priceReadyMixBags(){
				return this.numberBags*this.priceReadyMix;
			},
			grid(){
				return +this.space + 1;
			},
			v(){
				return Math.ceil(+this.space*this.thickness/1000);
			},
			mass(){
				return this.v*1300;
			},
			cement(){
				return Math.ceil(this.mass/3);
			},
			sand(){
				return this.mass - this.cement;
			},
			water(){
				return Math.ceil(this.mass*0.4);
			},
			cementBags(){
				return Math.ceil(this.cement/50);
			},
			cementPrice(){
				return this.cementBags*this.priceBag50;
			},
			sandBags(){
				return Math.ceil(this.sand/30);
			},
			sandPrice(){
				return this.sandBags*this.priceBag30;
			}

		},
		methods: {
			clickSent(){
				this.sent = true;
			}
		}
	}
</script>
<style lang="less">
	.kalk7{
		background-color: #3F3F3F;
		box-shadow: 0px 0px 5px 2px rgba(0,0,0,0.6);
		max-width: 1160px;
		margin-left: auto;
		margin-right: auto;
		font-family: Geneva, Arial, Helvetica, sans-serif;
		font-size: 17px;
		box-sizing: border-box;
		border: 2px solid #3F3F3F;
		border-radius: 5px;
		padding: 15px;
		color: white;
		width: 100%;
		h2{
			font-size: 20px;
			font-weight: bold;
			margin-bottom: 20px;
		}

		input[type=number], select{
			width: 80%;
			border-radius: 5px;
			font-family: Geneva, Arial, Helvetica, sans-serif;
			font-size: 17px;
			padding: 12px;
			margin-top: 5px;
			margin-bottom: 5px;
			background-color: white;
		}
		.sent{
			width: 228px;
			background-color: #FF9D3D;
			border: 1px solid #f88a19;
			cursor: pointer;
			font-family: Geneva, Arial, Helvetica, sans-serif;
			font-size: 20px;
			color: #303030;
			box-shadow: inset 0px 0px 5px 2px rgba(0,0,0,0.1);
			transition:all 0.2s ease;
			margin: 15px;
		}
		.sent:hover{
			transform: scale(1.03);
		}
        .h1-kalk-poly{
                    font-size: 20px;
                    font-weight: bold;
                    outline: none;
                    margin-left: 20px;
                    color: #FF9D3D;
        }
		h1{
			font-size: 20px;
			font-weight: bold;
			outline: none;
			margin-left: 20px;
			color: #FF9D3D;
		}
		hr{
			border: 1px solid #FF9D3D;
		}
		.row{
			display: flex;
			flex-wrap: wrap;
			justify-content: space-around;
		}
		.col{
			width: calc(100% / 12 * 6 - 20px);
			margin: 10px;
			box-sizing: border-box;
		}
	}
	@media screen and (max-width: 800px){
		.kalk7{
			.col{
				width: calc(100% / 12 * 12 - 20px);
			}
		}
	}
</style>