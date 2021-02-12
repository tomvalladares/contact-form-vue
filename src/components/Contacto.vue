<template>
  <div class="contacto">
    <form 
      id="form-contact"
      class="form-contact"
      @submit="checkForm"
      action="https://api.staticforms.xyz/submit"
      method="post"
      novalidate="true"
    >
      <!-- NOMBRE -->
      <div class="form-field">
        <label for="name">Nombre *</label>
        <input id="name" v-model="data.name" class="form-field-input" type="text" placeholder="Ingresa tu nombre" name="name" required>
      </div>
      <!-- EMAIL -->
      <div class="form-field">
        <label for="email">Email *</label>
        <input id="email" v-model="data.email" class="form-field-input" type="email" placeholder="Ingresa tu Email" name="email" required> 
      </div>
      <!-- TELEFONO -->
      <div class="form-field">
        <label for="phone">Telefono</label>
        <input id="phone" v-model="data.phone" class="form-field-input" type="text" placeholder="Ingresa tu telefono" name="phone" > 
      </div>
      <!-- MENSAJE -->
      <div class="form-field">
        <label  for="message">Mensaje *</label>
        <textarea id="message" v-model="data.message" class="form-field-txt" name="message" rows="6" placeholder="Déjanos tu mensaje" required></textarea>
      </div>

      <!-- honeypot-->
      <div class="form-field" style="display:none">
        <label for="honeypot" >Honeypot</label>
        <input type="text" name="honeypot" v-model="data.honeypot" placeholder="honeypot" >
      </div>
      <!-- mensaje -->
      <div v-if="errors.length" class="info-message">
          
            <p v-for="error in errors" :key="error.id">{{error}}</p>
          
      </div>

      <!-- BOTON  -->
      <div class="form-field">
        <input type="submit" value="Enviar" class="form-button"> 
      </div>

    </form>
  </div>
</template>

<script>
import axios from 'axios';

export default {
data() {
  return {
    errors: [],
    data: {
      name:null,
      email:null,
      phone:null,
      subject: 'Mensaje de contacto desde formulario',
      honeypot:'',
      message:null,
      replyTo:'@',
      accessKey: '' // acá va el acceskey generado para el email desde staticforms.xyz
    },
    response:{
      type:'',
      message:''
    }
  }
},
methods: {
    checkForm: function(e) {
      this.errors= [];
      if(!this.data.name) {
        this.errors.push('El nombre es obligatorio')
      }
      if(isNaN(this.data.phone)){
        this.errors.push('Ingresa un número de telefono válido')
      }
      if(!this.data.email) {
        this.errors.push('El email es obligatorio')
      }else if (!this.validEmail(this.data.email)) {
        this.errors.push('El email debe ser válido.')
      }
      if(!this.data.message) {
          this.errors.push('Por favor, escribe un mensaje')
      }

      if(!this.errors.length){
        return this.submitHandle(e);
      }
      e.preventDefault();
      
    },
    validEmail: function(email){
      var re= /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
      return re.test(email)
    },

    // funcion para comunicarse con la API
    async submitHandle(e) {
      e.preventDefault();
      let data = this.data
      try {
        let response = await axios.post("https://api.staticforms.xyz/submit", data, {
          method: 'POST',
          body: JSON.stringify(data),
          headers: { 'Content-Type': 'application/json' }
        })
        if(response.data.success){
          this.errors.push('mensaje enviado con éxito, te contactaremos a la brevedad!')
          this.resetForm();

        }else{
          this.errors.push('Tuvimos un problema al enviar tu mensaje, por favor contáctanos directamente')
        }
        
      } catch (error) {
        this.errors.push['Tuvimos un problema al enviar tu mensaje']
      }
      this.cleanErrors(5000)
    },

    cleanErrors: function(t) {
        setTimeout(() => {
        this.errors = []
      },t)
    },

    resetForm: function() {
      this.data.name= null
      this.data.email= null
      this.data.phone=null
      this.data.message=null
      this.data.honeypot=''
    }
}
  
}
</script>

<style>
.info-message{
  list-style: none;
  margin: 10px;
  line-height: 0.1rem;
  
}
.form-contact{
  display: inline-block;
  margin: auto;
  background-color: lightcyan;
  padding: 20px;
}
.form-field{
  display: flex;
  flex-direction: column;
  margin: 2px 10px;
}
.form-field-input{
  background-color: #fff;
  border: 1px solid grey;
  height: 23px;
  padding: 5px;
}
.form-field-txt{
  border: 1px solid grey;
  padding: 5px;
  resize: none;
}
.form-field-input:focus, .form-field-txt:focus{
  outline: 1px solid orange;
  border: 1px solid orange;
}

.form-button {
  background-color: orange;
  border: none;
  padding: 5px;

}

.form-button:hover {
  background-color: rgb(255, 187, 0);
}


</style>