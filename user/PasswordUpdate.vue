<template>
<div class="my-4" v-if="mdpid === 0">
	<button class="btn btn-outline-info" @click.prevent="mdpid = 1 "> Mot de passe<i class="bi bi-key-fill" style="padding:5px;"></i></button>
	<div class="alert alert-info mt-4" role="alert" v-if="success==true">Mot de passe modifié</div>
</div>

<article v-if="mdpid === 1">
	<div class="input-group py-2 ">
		<input type="password" class="form-control" style="letter-spacing: 1px;" placeholder="Saisissez votre mot de passe actuel" v-model="oldPassword" @keydown.enter.prevent="verifOldPassword()">
		<button class="btn btn-outline-secondary bi bi-check" type="button" @click="verifOldPassword()"></button>
		<button class="btn btn-outline-secondary bi bi-x" type="button" @click="resetDisplay()"></button>
	</div>
	<div class="alert alert-warning italic" role="alert" v-if="oldPasswordId"> {{oldPasswordError}}</div>
</article>

<!-- ////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////// -->

<section>
	<div class="py-2" v-if="mdpid === 2">
		<div class="password d-flex flex-column align-items-center" style="letter-spacing: 1px;">
			<input class="form-control" type="password" placeholder="Nouveau mot de passe" autocomplete="off" v-model="newPassword">
			<div>
					
				<transition class="p-1 m-1 mt-2 p-2 alert " v-bind:class="{ 'alert-dark': warningId, 'alert-danger': !warningId }" role="alert" >
					<div v-if="passwordValidation.errors.length > 0 && !submitted" class="hints">
						<div v-for="error in passwordValidation.errors" :key="error">{{error}}</div>
					</div>
				</transition>
			</div>
			<input class="mt-4" type="password" placeholder="Confirmation mot de passe" v-model.lazy='checkPassword' autocomplete="off" @keydown.enter.prevent="upPassword()">
			</div>
	
			<button class="btn btn-outline-secondary bi bi-check mt-4 m-2 " type="button" @click="verifNewPassword()"> Valider</button>
			<button class="btn btn-outline-secondary bi bi-x mt-4 m-2 " type="button" @click="resetDisplay()"> Annuler </button>
	</div>	
			
			<div class="alert alert-warning italic" role="alert" v-if='newPasswordId==true'>  {{newPasswordError}}</div>
</section>

</template>


<script>
import {getAuth, reauthenticateWithCredential, EmailAuthProvider, updatePassword } from '@firebase/auth';

export default {

	data () {
		return {
			rules: [
				{ message:'Une lettre minuscule.', regex:/[a-z]+/ },
				{ message:"Une lettre majuscule.",  regex:/[A-Z]+/ },
				{ message:"8 characteres minimum.", regex:/.{8,}/ },
				{ message:"Un chiffre minimum.", regex:/[0-9]+/ }
			],
			submitted:false,
			mdpid : 0,

            oldPassword:"",
			oldPasswordId:false,
			oldPasswordError:"",

			newPassword:"",
			newPasswordError:"",
			newPasswordId:false,
			warningId:true,
			checkAcceptId:true,

			checkPassword:"",
			pending: {
				updatePassword: false,
			},
			success: false
		}
	},
	methods: {
		/**
		 * Verifie si le mot de passe est bien le mot de passe du login de firebase
		 */
		reauth() {
			let cred = EmailAuthProvider.credential(getAuth().currentUser.email, this.oldPassword);

			return reauthenticateWithCredential(getAuth().currentUser, cred)
			.then((userCred) => {
				return userCred;
			});
		},

		/**
		 * Verifie si le mot de passe entrer est bon, sinon renvoie des messages d'erreur sous forme de pop-up (grâce à oldPasswordId)
		 */
		verifOldPassword() {
			if(this.oldPassword==""){
				this.oldPasswordError="Veuillez entrer votre mot de passe";
				this.oldPasswordId=true;
				
			/* }else if(this.oldPassword != "Pebble35"){ // Si le mot de passe n'est pas celui de la connexion firebase
				this.oldPasswordId=true; 
				this.oldPassword="";
				this.oldPasswordError="Mot de passe incorrect"; */
			}/* else if(this.oldPassword === "2"){
				console.log(this.reauth());
			} */
			else {
				this.reauth()
				.then(() => {
					console.log("j'ai gagné !");
					this.mdpid=2
				})
				.catch((error) => {
					console.error("j'ai perdu :-(", error);
				});
			}
		},

		/**
		 * Reinitialise toutes les valeurs afin que aucune pop-up de default sois visible et que chaque displayPassword sois remis àzero à la saisie
		 */
		resetDisplay() {
			this.oldPassword="";
			this.newPassword="";
			this.checkPassword="";
			this.mdpid = 0;
			this.oldPasswordId=false;
			this.newPasswordId=false;
		},

		/**
		 * Determine si le mot de passe taper est accepter en terme de complexité grâce au regex determionés dans les rules
		 */
		passValid() {
			let errors = []
			for (let condition of this.rules) {
				if (!condition.regex.test(this.newPassword)) {
					return true;
				}
			}
			if (errors.length === 0) {
				return true
			} else {
				return false
			}
		},

		/**
		 * Verifie si le nouveau mot de passe entrer est accepter : 
		 * - Selon la complexite,
		 * - Si ce n'est pas une chaine vide
		 * - Si le nouveau mot de passe et le 'checkPassword' sont identique
		 * --> sinon renvoie des messages d'erreur sous forme de pop-up (grâce à newPasswordId)
		 */
		verifNewPassword(){
			if(this.passwordsFilled == false){
				this.newPasswordError="Champs vide, veuillez entrer une valeur correspondante";
				this.newPasswordId=true;
			}else if(this.passValid() == false){
				this.newPasswordError="Toutes les conditions ne sont pas respectées";
				this.newPasswordId=true;
				this.warningId=false;
			}else if(this.notSamePasswords){
				this.newPasswordError="Les mots de passe sont différents, veuillez réessayer";
				this.newPasswordId=true;
			}else{
				this.upPassword();
			}
		},

		/**
		 * Modifie le mot de passe 
		 */
		upPassword() {
			
				this.pending.updatePassword = true;

				let auth = getAuth();
				let user = auth.currentUser;
				const cred = EmailAuthProvider.credential(user.email, this.oldPassword);
				//user.reauthenticateWithCredential(cred)

				reauthenticateWithCredential(user,cred)
				.then(() => {
					updatePassword(user, this.newPassword);
				})
				.then(() => {
					this.success = true;
					this.resetDisplay();
				})
				.catch(this.$app.catchError)
				.finally(() => {
					this.pending.updatePassword = false;
				});
		}
	},
	computed: {
		/**
		 * Teste si le nouveau mot de passe et le checkP mot de passe sont des contenus vide  
		 */
		passwordsFilled () {
			return (this.newPassword !== '' && this.checkPassword !== '')
		},

		/**
		 * Teste si les deux nouveaux mots de passe sont different
		*/
		notSamePasswords() {
			if (this.passwordsFilled) {
				return (this.newPassword !== this.checkPassword)
			} else {
				return false
			}
		},
		/**
		 * Teste si le nouveau mot de passe correspond bien au complexité requise avec les rules.regex 
		 *  --> Affiche un message d'erreur sinon
		 */
		passwordValidation () {
			let errors = []
			for (let condition of this.rules) {
				if (!condition.regex.test(this.newPassword)) {
					errors.push("> "+ condition.message);
				}
			}
			if (errors.length === 0) {
				return { valid:true, errors }
			} else {
				return { valid:false, errors }
			}
		},
	},
	unmounted(){
		this.success=false;
	}
}

</script>