<template>
  <v-container fluid>
	<v-layout row wrap>
		<v-flex xs12>
			<div class="display-1 text-xs-center"> Direct Messages from {{recipient.username}}</div>
		</v-flex>
		<v-flex xs12>
			<v-text-field
			  disabled
			  v-model="messageDisplay"
	          name="theirMessages"
	          textarea
	          :dark="darkness"
	          :rows="textAreaRows"
	        ></v-text-field>
		</v-flex>
		<v-flex xs12>
			<v-divider />
		</v-flex>
		<v-flex xs9 md10>
			<v-text-field
			  v-model="value"
	          name="yourMessage"
	          label="write message here"
	          single-line
	        ></v-text-field>
		</v-flex>
		<v-flex xs3 md2>
			<v-btn @click="submitMessage">
				Submit Message
			</v-btn>
		</v-flex>
	</v-layout>
</v-container>
</template>

<script>
import {mapGetters, mapState} from 'vuex'	
export default {
	props: {	
		recipient:{	
			type: Object,	
			required: true	
	
		}	
	},	
	data: function(){
		return {
        	theirMessages: [],
        	value: '',
		}
	},
	methods: {
		submitMessage: function(){
			if (this.$socket.connected) {
			  this.$socket.emit('directMessage', {value: this.value, recipient: this.recipient})
			}
		},
  	},
	computed: {
		...mapState([	
			'allUsers',	
			'myUsername',	
			'mySocketID',	
			'myDMs'	
		]),	
    	...mapGetters([	
    		'usersWhoAreMe',	
    		'usersWhoAreNotMe',	
    		'usersWhoAreNotMeByUserName',	
    		'darkness'	
    	]),	
    	textAreaRows(){
    		if (this.myDMs.length < 5) {
    			return 5
    		} else {
    			 return this.myDMs.length + 1
    		}
    	},
    	messageDisplay(){
    		let bigOldString = ''
    		let relevantDMs = this.myDMs.filter(item=>item.username === this.recipient.username)[0].messages

    		relevantDMs.forEach(item=>{
    			bigOldString += item.from.username + ': ' + item.value + '\n'
    		})
    		return bigOldString 
    	},

    }

}


</script>

<style>
.theme--dark.input-group textarea:disabled, .theme--dark.input-group__details {
	color: white!important;
}

.input-group textarea:disabled, .input-group__details{
  color: black!important;

}
</style>