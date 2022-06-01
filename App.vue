<template>
  <div id="Actividad_Main">
    <div
      class="pantalla"
      id="pantallaActividad"
      v-if="pantalla_actual == 'pantallaActividad'"
    >
    <!-- <div class="tiempo_bar">
         <div class="progress progresotime" id="bt">
           <div class="progress-bar" role="progressbar" v-bind:style="'width:'+llenado_bar+'%'" aria-valuemin="0" aria-valuemax="100"></div>
         </div>
    </div> -->
    <div class="pantalla_v" id="pantalla_v">
    </div>
    <div class="pantalla_r" id="pantalla_r">
    </div>
    <nivel1 class="trazos_pantalla" @respuestasalumno="respuestasdadas" @tiemporespuestas="respuestatiempo" v-bind:plantilla_nombre="plantilla_actual" />
    </div>
  </div>
</template>

<script>
/* eslint-disable */
/* global plantilla, _, elementos_palabras, jQuery, ConsolaUtilidad, _kod */
/* eslint-enable */
import nivel1 from '@/components/N1.vue'
export default {
  name: "App",
  components: {
    nivel1
    // ...
  },
  data: function () {
    return {
      // valorparar:null,
      // tiempo_mm: 1000/180000,
      // llenado_bar:0,
      // milisegundos:0,
      // segundos:0,
      /**
       * La plantilla con la que inicial el ejercicio.
       * Almacenar en la instancia para evitar que se modifique durante la ejecucion.
       */
      plantilla_actual: plantilla,
      /**
       * Configuracion de la iteración
       */
      iteracion_config: {
        /**
         * La iteracion actual
         */
        iteracion_actual: 0,
        /**
         * El maximo de iteraciones
         */
        iteracion_max: 1,
        /**
         * El total de respuestas correctas durante el ejercicio
         */
        juego_respuestas_correctas: 0,
        /**
         * El total de respuestas incorrectas durante el ejercicio
         */
        juego_respuestas_incorrectas: 0,
        /**
         * Para modalidad 1 y 3, necesito saber si es mayor, menor o random
         */
      },
      /**
       * La pantalla actual que debe activarse y mostrarse
       */
      pantalla_actual: null, //"pantallaActividad",
      /**
       * Tiempo para esperar antes de cambiar de iteración
       */
      pausa_siguiente_iteracion: 2 * 1000,
      /**
       * Bloquear o no el click
       */
      permitir_click: false,
      /**
       * El tiempo en ms cuando el usuario hace click en el boton iniciar de las instrucciones
       */
      tiempo_inicio: 0,
      /**
       * Progressbar para cuando se muestra la pantalla actividad
       * @const
       * @type {String} - `component_id` para ProgressBar
       */
      barra_pantalla_actividad: "barra_pantalla_actividad",
      /**
       * Referencia a setTimeout en metodo responder()
       */
      clock_pausa_siguiente_iteracion: 0,
      tiempo_responder_iteracion: 1000/(180* 1000),
    }; //fin return data
  },
  computed: {
    /**
     * El contenido del ejercicio usando elementos_palabras
     */
    contenido: function () {
      return "todo";
    },
  },
  methods: {
  respuestatiempo(valor){
if(valor){
  document.getElementById('bt').style.display='block';
  this.iniciandoT();
}else{
  this.parar_tiempo();
}
    },

    respuestasdadas(valor){
      
      if(valor){
      this.iteracion_config.juego_respuestas_correctas+=1;
      this.iteracion_config.juego_respuestas_incorrectas+=0;
      document.getElementById("pantalla_v").style.display='block';
      }else{
      this.iteracion_config.juego_respuestas_correctas+=0;
      this.iteracion_config.juego_respuestas_incorrectas+=1;
      document.getElementById("pantalla_r").style.display='block';
      }
       console.log("gane o perdi:"+valor);
       this.responder();
    },
    /**
     * Envia el resultado del ejercicio a la ConsolaEjercicios, se llama en `siguienteIteracion()`
     */
    finEjercicio: function () {
      const maxElementosCorrectos =
        this.iteracion_config.juego_respuestas_correctas +
        this.iteracion_config.juego_respuestas_incorrectas;
      const en_blanco = Math.max(
        0,
        maxElementosCorrectos -
          (this.iteracion_config.juego_respuestas_correctas +
            this.iteracion_config.juego_respuestas_incorrectas)
      );
      //var d = new Date();
      jQuery("#Actividad_Main").stop(true, true);
      return jQuery("body").trigger({
        type: "Consola.Ejercicio.Terminado",
        resultado: {
          correcto: this.iteracion_config.juego_respuestas_correctas,
          incorrecto: this.iteracion_config.juego_respuestas_incorrectas,
          blanco: en_blanco,
          tiempo: Math.round(
            (new Date().getTime() - this.tiempo_inicio) / 1000
          ),
          total: maxElementosCorrectos,
          nivel: Math.min(
            this.iteracion_config.nivel_actual,
            this.iteracion_config.nivel_max
          ),
        },
      });
    },
    /**
     * Maneja el estado de las iteraciones y llama a `finEjercicio`
     * cuando se han usado todas las iteraciones
     */
    siguiente_iteracion: function () {
      this.iteracion_config.iteracion_actual++;
      clearTimeout(this.clock_pausa_siguiente_iteracion);
      if (
        this.iteracion_config.iteracion_actual >
        this.iteracion_config.iteracion_max
      ) {
        return this.finEjercicio();
      }
      // this.armar_contenido();
      this.pantalla_actual = "";
      return setTimeout(this.pantalla_actividad, 1000);
    },
    /**
     * Muestra una lectura o preguntas. Segun el ejercicio.
     */
    pantalla_actividad: function () {
      this.pantalla_actual = "pantallaActividad";
      //<% if(ejercicio_debug) { %>
      // this.$root.$emit('progress-bar-duration', this.barra_pantalla_actividad, 90 * 1000);
      //<% } else { %>
      // this.$root.$emit('progress-bar-start', this.barra_pantalla_actividad);
      //<% } %>
      this.permitir_click = true;
    },
    /**
     * Evalua las respuestas del usuario
     */
    responder: function () {
      if (!this.permitir_click) {
        return;
      }
      // this.$root.$emit('progress-bar-stop', this.barra_pantalla_actividad);
      this.permitir_click = false;
      this.clock_pausa_siguiente_iteracion = setTimeout(
        this.siguiente_iteracion,
        this.pausa_siguiente_iteracion
      );
    },
    armar_contenido(){
      this.iniciando();
    },
  //   iniciando(){ 
      
  //  this.valorparar = setInterval(
  //  this.iniciar_tiempo,10);
  //   },
  //   iniciar_tiempo(){
    
  //   this.milisegundos++;

  // //  console.log(this.llenado_bar);
  //   if(this.milisegundos==100){
  //     this.milisegundos=0;
  //     this.segundos++;
  //     // console.log(this.segundos)
  //   }
  //   if(this.segundos===181){
  //       this.parar_tiempo();
  //       this.respuestasdadas(false);
  //        document.getElementById("pantallaActividad").style.backgroundColor='#F6535DCC';

  //   } 
  //   this.llenado_bar=this.llenado_bar+ this.tiempo_responder_iteracion;        
  //   },
  //   parar_tiempo(){ 
  //   clearInterval(this.valorparar);
  //   },
  },
  mounted: function () {
    jQuery(document).ready(() => {
      jQuery("body").bind("Consola.Ejercicio.Iniciado", () => {
        this.tiempo_inicio = new Date().getTime();
        setTimeout(this.siguiente_iteracion, 1000);
      });
      //<% if(ejercicio_debug) { %>
      window.app = this;
      if (jQuery('#btnEmpezarEjercicio').length !== 0) {
        jQuery('#btnEmpezarEjercicio').click();
      } else {
        ConsolaUtilidad.funciones.SaltarInstrucciones();
      }
      //<% } %>
    });
  },
};
</script>

<style lang="scss" scoped>
//@import "./css/style.scss";
#Actividad_Main {
  .pantalla {
    display: block;
  }
}
.trazos_pantalla{
  position: relative;
}
.pantalla_v{
  border-radius: 10px;
  display: none;
  position: absolute;
  margin: 0 auto;
  background-color: #3FE495CC;
  width: 95%;
  height: 90%;
}
.pantalla_r{
  border-radius: 10px;
  display: none;
  position: absolute;
  margin: 0 auto;
  background-color: #F6535DCC;
  width: 95%;
  height: 90%;
}
  // .tiempo_bar{
  //   display: flex;
  //   justify-content: center;
  // }
  // .progresotime{
  //   // margin-left: calc(50% - 25%);
  //   margin: 5px auto;
  //   margin-top:10px ;
  //   width: 78%;
  //   height: 14px;
  //    border-radius: 20px;
  //    background: #ffd6ad;; 
  // }
  // .progress-bar{
  // background-color:orange;
  // }
</style>
