<template>
	<div class="gameOptions">
        <div class="level">
            <div>Difficulty level</div>
            <label
                v-for="(item, index) in levels.list"
                :key="index"
                :for="item.name"
            >
                {{item.name}}
                <input
                    type="radio"
                    :key="index"
                    :id="item.name"
                    :value="item.value"
                    :disabled="isPlayed"
                    :checked="levels.selected === item.value"
                    name="level"
                    @change="changeLevel(item.value)"
                />
            </label>
        </div>
        <div class="startGame">
            <div><button :disabled="isPlayed" @click="clickStartGame()">Start</button></div>
            <div>Round: {{roundNumber}}</div>
        </div>
        <div class="message" v-if="isGameLoss">Sorry, you lost after {{roundNumber}} rounds!</div>
    </div>
</template>


<script>
import '../../assets/gameOptions.css';
export default {
	name: 'GameOptions',
	props: {
		roundNumber: Number,
		levels: Object,
		isPlayed: Boolean,
		isGameLoss: Boolean,
        changeLevel: Function,
		clickStartGame: Function
    },
}
</script>