<template>
  <div class="container">
    <h1>Gerador de Link WhatsApp</h1>

    <form id="whatsappForm">
      <label for="phone">Telefone</label>
      <input type="text" name="phone" v-model="phone" placeholder="(11) 99999-9999" required>

      <label for="message">Mensagem</label>
      <input type="text" name="message" v-model="message" minlength="3" placeholder="Oi, quero falar com você">
      <button @click="generateLink">Gerar link</button>
    </form>

    <div id="generatedLinkContainer" class="link-container hidden">
      <p id="generatedLinkText"></p>
      <button id="copyLinkButton">Copiar link</button>
    </div>

    <div id="notification" class="notification hidden">
      Link copiado para a área de transferência!
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {

  data() {
    return {
      phone: '',
      message: ''
    }
  },

  watch: {
    phone() {
      var x = this.phone.replace(/\D/g, '').match(/(\d{0,2})(\d{0,5})(\d{0,4})/);
      this.phone = !x[2] ? x[1] : '(' + x[1] + ') ' + x[2] + (x[3] ? '-' + x[3] : '');
    }
  },

  methods: {
    generateLink(event) {
      event.preventDefault();

      this.phone = this.phone.replace(/\D/g, '');
      const url = `https://api.whatsapp.com/send?phone=55${this.phone}&text=${encodeURIComponent(this.message)}`;

      const generatedLinkText = document.getElementById('generatedLinkText');
      generatedLinkText.textContent = url;

      this.sendApiForm();

      const generatedLinkContainer = document.getElementById('generatedLinkContainer');
      generatedLinkContainer.classList.remove('hidden');

      const copyLinkButton = document.getElementById('copyLinkButton');
      copyLinkButton.addEventListener('click', () => {
        navigator.clipboard.writeText(url);
        this.showNotification();
      });
    },

    showNotification() {
      const notification = document.getElementById('notification');
      notification.classList.remove('hidden');

      setTimeout(() => {
        notification.classList.add('hidden');
      }, 3000);
    },

    sendApiForm() {
      console.log(process.env)
      let url = process.env.VUE_APP_API_URL
      let secret_code = process.env.VUE_APP_NOT_SECRET_CODE

      axios({
        method: 'post',
        url: `${url}/form-contact`,
        headers: {
          'x-api-code': secret_code
        },
        data: {
          phone: this.phone,
          message: this.message || "nao identificado.",
          name: "nao identificado.",
          email: "nao identificado.",
          gender: "nao identificado.",
          application: "whats-link"
        }
      }).then(response => {
        console.log(response.data)
      }).catch(error => {
        console.log(error)
      })
    }
  },
}
</script>

<style>
/* Resetando estilos padrão */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Arial', sans-serif;
}

/* Estilo para o container principal */
.container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: #f5f5f5;
  padding: 20px;
}

/* Estilo do título */
h1 {
  font-size: 2em;
  color: #333;
  margin-bottom: 20px;
}

/* Estilo do formulário */
form {
  background-color: #fff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  width: 100%;
  max-width: 400px;
  display: flex;
  flex-direction: column;
}

/* Estilo dos rótulos */
form label {
  font-size: 1em;
  color: #666;
  margin-bottom: 5px;
}

/* Estilo dos campos de input */
form input {
  padding: 10px;
  margin-bottom: 15px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 1em;
  transition: border-color 0.3s ease;
}

/* Estilo do input em foco */
form input:focus {
  border-color: #007bff;
  outline: none;
}

/* Estilo do botão de submit */
form button {
  padding: 10px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 4px;
  font-size: 1em;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

/* Estilo do botão ao passar o mouse */
form button:hover {
  background-color: #0056b3;
}

/* Estilo para o container do link gerado */
.link-container {
  margin-top: 20px;
  padding: 10px;
  background-color: #e9ecef;
  border-radius: 4px;
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
  max-width: 400px;
}

/* Esconde o container inicialmente */
.hidden {
  display: none;
}

/* Estilo do texto do link gerado */
#generatedLinkText {
  margin-bottom: 10px;
  font-size: 1em;
  color: #333;
  word-break: break-all;
  /* Permite quebra de linha para links longos */
}

/* Estilo do botão de copiar */
#copyLinkButton {
  padding: 10px;
  background-color: #28a745;
  color: #fff;
  border: none;
  border-radius: 4px;
  font-size: 1em;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

/* Estilo do botão de copiar ao passar o mouse */
#copyLinkButton:hover {
  background-color: #218838;
}

/* Estilo da notificação */
.notification {
  position: fixed;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  background-color: #28a745;
  color: #fff;
  padding: 10px 20px;
  border-radius: 4px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
  transition: opacity 0.3s ease;
  opacity: 0;
  z-index: 1000;
}

/* Mostra a notificação */
.notification:not(.hidden) {
  opacity: 1;
}

/* Estilo responsivo para dispositivos móveis */
@media (max-width: 600px) {
  form {
    width: 100%;
    padding: 15px;
  }

  h1 {
    font-size: 1.5em;
  }

  .link-container {
    width: 100%;
  }
}
</style>