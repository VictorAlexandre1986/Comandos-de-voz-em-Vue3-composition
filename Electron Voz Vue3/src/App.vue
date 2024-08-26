<template>
  <div class="container">
    <div class="nav">
      <h2>Fale alguma coisa</h2>
    </div>
    <div id="content">
      {{ transcript }}
    </div>
  </div>
</template>

<script>
import { ref, onMounted, onUnmounted } from 'vue';

export default {
  setup() {
    const transcript = ref('');

    const downloadTxtFile = () => {
    const element = document.createElement('a');
    const file = new Blob([transcript.value], { type: 'text/plain' });
    element.href = URL.createObjectURL(file);
    element.download = 'transcript.txt';
    document.body.appendChild(element);
    element.click();
  };

    const commands = [
      {
        command: 'reset',
        callback: () => (transcript.value = ''),
      },
      {
        command: 'limpar',
        callback: () => (transcript.value = ''),
      },
      {
        command: 'abrir',
        callback: (site) => window.open('http://' + site),
      },
      {
        command: 'aumentar fonte',
        callback: () => {
          document.getElementById('content').style.fontSize = '22px';
        },
      },
      {
        command: 'diminuir fonte',
        callback: () => {
          document.getElementById('content').style.fontSize = '16px';
        },
      },
      {
        command: 'mudar cor',
        callback: (color) => {
          document.getElementById('content').style.color = color;
        },
      },
      {
        command: 'salvar texto',
        callback: downloadTxtFile,
      },
      {
        command: 'Salvar texto',
        callback: downloadTxtFile,
      },
      {
        command: 'sair',
        callback: () => {
          window.close();
        },
      },
    ];

    onMounted(() => {
      if (!('webkitSpeechRecognition' in window) && !('SpeechRecognition' in window)) {
        console.log("Browser doesn't support speech recognition.");
      } else {
        const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
        const recognition = new SpeechRecognition();

        recognition.continuous = true; // Mantém o microfone aberto
        recognition.lang = 'pt-BR';

        recognition.onresult = function (event) {
          let interimTranscript = '';
          for (let i = event.resultIndex; i < event.results.length; i++) {
            interimTranscript += event.results[i][0].transcript;
         } 
          if (interimTranscript == 'salvar texto'){
              transcript.value += '';
          }else if (interimTranscript == 'Salvar texto'){
              transcript.value += '';
          }else if (interimTranscript == 'ponto de interrogação'){
              transcript.value += '?';
          }else{
            transcript.value += ' ' + interimTranscript;    
          }
        
          
          

          commands.forEach(({ command, callback }) => {
            if (interimTranscript.includes(command)) {
              callback();
            }
          });

          
        };

        recognition.start();

        onUnmounted(() => {
          recognition.stop();
        });
      }
    });

    return {
      transcript,
    };
  },
};
</script>

<style>

.container{
  width: 95vw;
  padding: 20px;
  height: 100vh;
  overflow-x: hidden;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.nav h2{
  font-size: 22px;
  font-weight: 500;
  letter-spacing: 1px;
  text-align: center;
}

#content {
  width: 80%;
  overflow-x: hidden;
  border: none;
  border-radius: 12px;
  box-shadow: 0 0 8px #a5a094;
  height: 200px;
  font-size: 16px;
  padding: 12px;
  font-family: cursive;
  font-weight: 400;
  align-items: flex-start;
  justify-content: center;
  margin: auto;
  background: #eee;
  color: #666;
}


body {
  background-color: rgb(18, 148, 199);
  margin: auto;
  width: 100vw;
  justify-content: center;
  align-items: center;
  -webkit-animation: changecolor 10s;
  animation: changecolor 10s;
  -webkit-animation-iteration-count: infinite;
  overflow: hidden;
}
iframe{
  border-radius: 50%;
}
@-webkit-keyframes changecolor {
  from {
    background: red;
    color: white;
  }

  0% {
    background: yellow;
    color: red;
  }

  25% {
    background: blue;
    color: white;
  }

  50% {
    background: green;
    color: white;
  }

  75% {
    background: rgb(231, 50, 207);
    color: white;
  }

  100% {
    background: orange;
    color: rgb(255, 255, 255);
  }
}

@keyframes changecolor {
  from {
    background: rgb(241, 47, 47);
    color: white;
  }

  0% {
    background: rgb(233, 233, 34);
    color: rgb(37, 37, 37);
  }

  25% {
    background: rgb(65, 65, 248);
    color: white;
  }

  50% {
    background: rgb(47, 233, 47);
    color: white;
  }

  75% {
    background: rgb(0, 255, 221);
    color: white;
  }

  100% {
    background: orange;
    color: rgb(255, 255, 255);
  }
}
</style>
