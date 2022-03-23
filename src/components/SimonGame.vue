<template>
	<div class="wrapper">
		<div class="simon">
			<ul>
				<li
                    v-for="sector in sectors"
                    :key="sector.id"
                    :class="sector.class+' '+sector.active"
                    @click="handleSectorClick(sector.id)">
                </li>
			</ul>
		</div>
		<GameOptions 
            :roundNumber="roundNumber"
            :levels="levels"
            :isPlayed="isPlayed"
            :isGameLoss="isGameLoss"
            :changeLevel="changeLevel"
            :clickStartGame="clickStartGame"
        />
	</div>
</template>

<script>

import '../assets/simonGame.css';
import audio0 from '../assets/sounds/0.ogg';
import audio1 from '../assets/sounds/1.ogg';
import audio2 from '../assets/sounds/2.ogg';
import audio3 from '../assets/sounds/3.ogg';
import GameOptions from './GameOptions/GameOptions.vue';

export default {
	name: 'SimonGame',
	components: {
		GameOptions
	},
	data() {
		return {
			isPlayed: false,
			isGameLoss: false,
			roundNumber: 0,
			stages: [],
			audios:[
				audio0,
				audio1,
				audio2,
				audio3
			],
			sectors:[
                { id: 0, class: 'red', active: '' },
                { id: 1, class: 'blue', active: '' },
                { id: 2, class: 'yellow', active: '' },
                { id: 3, class: 'green', active: '' },
            ],
			levels:{
				selected: 1.5,
				list: [
					{ value: 1.5, name: 'easy' },
					{ value: 1, name: 'medium' },
					{ value: 0.4, name: 'hard' },
				]
			},
		}
	},
	methods: {
		playAudio(index) {
			const audio = new Audio(this.audios[index]);
			audio.play();
		},
		handleSectorClick(sectorId) {
			if(this.isPlayed){
				return;
			}
			this.sectors[sectorId].active = 'active';
			this.playAudio(sectorId);
			setTimeout(()=>{
				this.sectors[sectorId].active = '';
			}, 100);

			if(this.roundNumber && !this.isGameLoss){
				if(this.stages[0] === sectorId){
					this.stages.shift();
					if(!this.stages.length){
						this.isPlayed = true;
						setTimeout(()=>{
							this.roundNumber++;
							this.fillGameSectors();
							this.playGameSectors();
						}, 1000);
					}
				}else{
					this.isGameLoss = true;
				}
			}
		},
		changeLevel(value){
			this.levels.selected = value;
		},
		clickStartGame(){
			this.isPlayed = true;
			this.isGameLoss = false;
			this.roundNumber = 1;
			this.stages = [];
			this.fillGameSectors();
			this.playGameSectors();
		},
		fillGameSectors(){
			for(let i=0;i<this.roundNumber;i++){
				this.stages.push(Math.floor(Math.random() * this.sectors.length));
			}
		},
		async playGameSectors(){
			for(let inx in this.stages){
				await new Promise(resolve => setTimeout(resolve, this.levels.selected * 1000 / 2)).then(()=>{
					this.playAudio(this.stages[inx]);
					this.sectors[this.stages[inx]].active = 'active';
				});
				await new Promise(resolve => setTimeout(resolve, this.levels.selected * 1000 / 2)).then(()=>{
					this.sectors[this.stages[inx]].active = '';
				});
			}
			this.isPlayed = false;
			console.log(this.stages);
		},
	}
}
</script>
