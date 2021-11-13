<template>
  <el-container>
    <el-main>
      <div class="box">
        <div>
          <h1>Pomodoro TADS</h1>
        </div>
        <div>
          <h1>{{ mostrarMinutos }}:{{ mostrarSegundos }}</h1>
        </div>
        <div>
          <el-button @click="comecar" type="primary" :disabled="tempoReduzindo">
            {{ textoBotao }}
            <img
              src="https://img.icons8.com/ios-filled/15/f8f8ff/circled-play.png"
              class="icon"
            />
          </el-button>
          <el-button @click="parar" type="danger">
            Pausar
            <img
              src="https://img.icons8.com/ios-filled/15/f8f8ff/circled-pause.png"
              class="icon"
            />
          </el-button>
          <el-button @click="resetar" :disabled="tempoReduzindo" type="warning">
            Resetar
            <img
              src="https://img.icons8.com/ios-filled/15/f8f8ff/recurring-appointment.png"
              class="icon"
            />
          </el-button>
        </div>
        <div class="btn-intervalo">
          <el-button
            @click="ativarIntervalo"
            type="success"
            :disabled="tempoReduzindo"
            >Intervalo
            <img
              src="https://img.icons8.com/ios-filled/15/f8f8ff/time-span.png"
              class="icon"
          /></el-button>
        </div>
        <div>
          <h1>{{ contador }}</h1>
          <el-button @click="resetarContador" type="danger"
            >Zerar Contador
            <img
              src="https://img.icons8.com/ios-filled/15/f8f8ff/delete--v1.png"
              class="icon"
          /></el-button>
          <el-button
            v-show="mostrarIntervalo"
            @click="ativarIntervaloLongo()"
            type="success"
            :disabled="tempoReduzindo"
            >Intervalo Longo
            <img
              src="https://img.icons8.com/ios-filled/15/f8f8ff/time-span.png"
              class="icon"
          /></el-button>
        </div>
      </div>
    </el-main>
  </el-container>
</template>
<script>
import alert from "../assets/metalgear.mp3"

export default {
  name: "Home",
  data() {
    return {
      intervaloAtivo: false,
      mostrarIntervalo: false,
      tempoReduzindo: false,
      tempoAtual: null,
      totalSegundos: 0.1 * 60,
      textoBotao: "Começar",
      contador: 3,
      alertAudio: new Audio(alert)
    };
  },
  methods: {
    formatarTempo(tempo) {
      //adiciona 0 quando o tempo restante for menos que 10.
      if (tempo < 10) {
        return "0" + tempo;
      }
      return tempo.toString();
    },
    comecar() {
      this.tempoReduzindo = true;
      this.textoBotao = "Continuar";
      //setInterval funcao JS, executa uma funcao selecionada por tempo determinado em mlsegundos
      //tempoAtual deve ser utilizado para poder pausar e resetar o tempo
      this.tempoAtual = setInterval(() => {
        if (this.totalSegundos <= 0) {
          if (this.intervaloAtivo == false) {
            this.contador += 1;
          } else {
            this.intervaloAtivo = false;
          }
          this.totalSegundos = 0.1 * 60;
          this.textoBotao = "Começar";
          this.parar();
          this.alertAudio.play();
          this.habilitarIntervaloLongo();
          return;
        }
        this.totalSegundos -= 1;
      }, 1000);
    },
    parar() {
      this.tempoReduzindo = false;
      //clearInterval funcao JS, pausa a execução de um setInterval. Não reseta
      clearInterval(this.tempoAtual);
    },
    resetar() {
      this.parar();
      this.textoBotao = "Começar";
      this.totalSegundos = 25 * 60;
    },
    ativarIntervalo() {
      this.intervaloAtivo = true;
      this.totalSegundos = 0.1 * 60;
      this.comecar();
    },
    ativarIntervaloLongo() {
      this.intervaloAtivo = true;
      this.totalSegundos = 10 * 60;
      this.comecar();
    },
    resetarContador() {
      this.mostrarIntervalo = false;
      this.contador = 0;    
    },
  },
  computed: {
    mostrarMinutos() {
      const minutos = Math.floor(this.totalSegundos / 60);
      return this.formatarTempo(minutos);
    },
    mostrarSegundos() {
      //Utilizado o % pois pega o RESTO de segundos da operacao do mostrarMinutos
      const segundos = this.totalSegundos % 60;
      return this.formatarTempo(segundos);
    },
    habilitarIntervaloLongo() {
      if (this.contador >= 4) {
        this.mostrarIntervalo = true;
      }
    },
  },
};
</script>
<style>
.icon {
  padding-left: 5px;
  vertical-align: middle;
}
.box {
  border: 2px solid #409eff;
  width: 480px;
  height: 360px;
  margin: auto;
  right: 0;
  left: 0;
}
.btn-intervalo {
  padding-top: 25px;
}
</style>