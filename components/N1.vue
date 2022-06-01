<template>
  <div class="hello">
    <div class="contenedor">
      <svg class="figura1" width="700" height="300" viewBox="0 -10 700 300" fill="none">
     
      <line v-for="cord in objCoordenadas.lineas" :key="cord.length" v-bind:id="cord.p" v-bind:x1="cord.x1" v-bind:y1="cord.y1" v-bind:x2="cord.x2" v-bind:y2="cord.y2"  stroke-width="5" stroke="#C8DFED" stroke-dasharray="5 5" ></line>
      <circle v-for="ptsA in objCoordenadas.puntos" :key="ptsA.length" v-bind:id="ptsA.p+'1'" v-bind:cx="ptsA.x" v-bind:cy="ptsA.y" r="13" fill="black"/>
      <circle v-for="ptsB in objCoordenadas.puntos" :key="ptsB.length" v-bind:id="ptsB.p+'2'" v-bind:cx="ptsB.x" v-bind:cy="ptsB.y+215" r="13" fill="red"/>
      </svg>
      <canvas ref="pizarra"  class="cva" width="700px" height="300px" v-hammer:panstart="captureOn" v-hammer:pan="mo" v-hammer:panend="captureOff"></canvas>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
   data: function(){
     return{
       mostrar:false,
       objCoordenadas:{},
        llave_ordentrazo:true,
        llave_punto:[],
        retroalimentacion:null,
        paso_mostrar:0,
        pa:false,
        pb:false,
        //variables corectas que no debo borrar
        puntoinicioA:0,
        puntoinicioB:0,
        llave_dibujar:false,
        llave_inicioOK:false,
        punto_siguiente:0,
        llave_stop:false

     }
   },
  props: {
      plantilla_nombre:String
  },computed:{
    
   }
   ,mounted(){
    console.log(this.plantilla_nombre)
    if(this.plantilla_nombre=="preTrazosUnirLinea1"){
        this.objCoordenadas={
          alineacion:"verticales",
          cantidad:5,
          puntos:[{x:150,y:25,p:"B"},{x:250,y:25,p:"C"},{x:350,y:25,p:"D"},{x:450,y:25,p:"E"},{x:550,y:25,p:"F"}],
          lineas:[{x1:150,y1:33,x2:150,y2:250,p:"LB"},
                  {x1:250,y1:33,x2:250,y2:250,p:"LC"},
                  {x1:350,y1:33,x2:350,y2:250,p:"LD"},
                  {x1:450,y1:33,x2:450,y2:250,p:"LE"},
                  {x1:550,y1:33,x2:550,y2:250,p:"LF"}]
                  }
      }
    //  if(this.plantilla_nombre=="preTrazosUnirLinea2"){
    //     this.objCoordenadas={
    //       alineacion:"verticales",
    //       cantidad:3,
    //       puntos:[{x:250,y:25,p:"C"},{x:350,y:25,p:"D"},{x:450,y:25,p:"E"}],
    //        lineas:[
    //               {x1:250,y1:25,x2:250,y2:240,p:"LC"},
    //               {x1:350,y1:25,x2:350,y2:240,p:"LD"},
    //               {x1:450,y1:25,x2:450,y2:240,p:"LE"}]
    //               }
    //   }
    //   if(this.plantilla_nombre=="preTrazosUnirLinea3"){
    //     this.objCoordenadas={
    //       alineacion:"verticales",
    //       cantidad:7,
    //       puntos:[{x:50,y:25,p:"A"},{x:150,y:25,p:"B"},{x:250,y:25,p:"C"},{x:350,y:25,p:"D"},{x:450,y:25,p:"E"},{x:550,y:25,p:"F"},{x:650,y:25,p:"G"}],
    //       lineas:[{x1:50,y1:25,x2:50,y2:240,p:"LA"},
    //               {x1:150,y1:25,x2:150,y2:240,p:"LB"},
    //               {x1:250,y1:25,x2:250,y2:240,p:"LC"},
    //               {x1:350,y1:25,x2:350,y2:240,p:"LD"},
    //               {x1:450,y1:25,x2:450,y2:240,p:"LE"},
    //               {x1:550,y1:25,x2:550,y2:240,p:"LF"},
    //               {x1:650,y1:25,x2:650,y2:240,p:"LG"}]
    //               }
    //   }
    //   if(this.plantilla_nombre=="preTrazosUnirLinea4"){
    //     this.objCoordenadas={
    //       alineacion:"verticales",
    //       cantidad:7,
    //       puntos:[{x:50,y:25,p:"A"},{x:150,y:25,p:"B"},{x:250,y:25,p:"C"},{x:350,y:25,p:"D"},{x:450,y:25,p:"E"},{x:550,y:25,p:"F"},{x:650,y:25,p:"G"}],
    //       lineas:[{x1:50,y1:25,x2:50,y2:240,p:"LA"},
    //               {x1:150,y1:25,x2:150,y2:240,p:"LB"},
    //               {x1:250,y1:25,x2:250,y2:240,p:"LC"},
    //               {x1:350,y1:25,x2:350,y2:240,p:"LD"},
    //               {x1:450,y1:25,x2:450,y2:240,p:"LE"},
    //               {x1:550,y1:25,x2:550,y2:240,p:"LF"},
    //               {x1:650,y1:25,x2:650,y2:240,p:"LG"}]
    //               }
    //   }
    //   if(this.plantilla_nombre=="preTrazosUnirLinea5"){
    //     this.objCoordenadas={
    //       alineacion:"verticales",
    //       cantidad:1,
    //       puntos:[{x:350,y:25,p:"D"}],
    //       lineas:[{x1:350,y1:25,x2:350,y2:240,p:"LD"}]
    //               }
    //   }
     
  },
  methods:{
    // metodos de trazado 
   
     mo: function(e) {

      if (this.captureToggle==true){
      var x= e.center.x;
      var y= e.center.y;
      // console.log(x,y);
      this.trazar(x,y)

      }

     },
     captureOn: function() {
       
      this.captureToggle = true
      this.isDrawing=true
      this.llave_stop=false;

    },
     captureOff: function() {
      this.captureToggle = false;
       this.isDrawing=false;
       
    },
    puntos_dibuja(){
        var xcir= 0
        var ycir= 0
        var x2cir=0
        var y2cir=0
        // var L=""
        this.context.strokeStyle = "red";
        this.context.fillStyle = "#6ab150";
        for (let k = 0; k < this.objCoordenadas.lineas.length; k++) { 
          xcir=this.objCoordenadas.lineas[k].x1;
          ycir=this.objCoordenadas.lineas[k].y1;
          x2cir=this.objCoordenadas.lineas[k].x2;
          y2cir=this.objCoordenadas.lineas[k].y2;
          this.context.beginPath();
          this.context.arc(xcir, ycir, 15, 0, 9 * Math.PI);
          this.context.stroke();
          this.context.beginPath();
          this.context.arc(x2cir, y2cir, 15, 0, 9 * Math.PI);
          this.context.stroke();
        }
    },
    ordentrazo(x,xvisitada,indice){
     if(xvisitada<x+20 & x-20<xvisitada){
       if(this.llave_punto[this.llave_punto.length-1]!=indice){
           this.llave_punto.push(indice)
           console.log(indice);
          //  this.punto_siguiente+=1;
         }
     }       
    },
    puntos_valida_inicio(x,y,x1,y1,x2,y2,indice){
      
      if(x<x1+15 & x1-15<x & y<y1+15 & y1-15<y){
       if(this.objCoordenadas.lineas[this.punto_siguiente].p===indice & this.puntoinicioA===0){
        this.puntoinicioA=1
        console.log("inicie en el puntoAok") 
       }if(this.objCoordenadas.lineas[this.punto_siguiente].p!=indice){
         this.puntoinicioA=2
       }
       this.llave_dibujar=true
     }
     if(x<x2+15 & x2-15<x & y<y2+15 & y2-15<y){
       if(this.objCoordenadas.lineas[this.punto_siguiente].p===indice & this.puntoinicioB===0){
         this.puntoinicioB=1
        console.log("inicie en el puntoBok") 
       }if(this.objCoordenadas.lineas[this.punto_siguiente].p!=indice){
         this.puntoinicioB=2
       }
       this.llave_dibujar=true
     }
    },
    reiniciarparametros(){
      this.puntoinicioA=0
      this.puntoinicioB=0
    },
    limpiar_trazo(){
        this.canvas =this.$refs.pizarra
        var ctx = this.canvas.getContext("2d");
        ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
     },
    trazar(clientX,clientY){
      this.canvas =this.$refs.pizarra
      this.context=this.canvas.getContext("2d")
      var rect = this.canvas.getBoundingClientRect();
      var x=clientX-rect.left;
      var y=clientY-rect.top;
       if(this.llave_punto.length==this.objCoordenadas.cantidad){
            this.responder();
          }
    //  console.log(parseInt(x),parseInt(y))
     for (let i = 0; i < this.objCoordenadas.lineas.length; i++) {
       if(this.llave_stop==false){
       this.puntos_valida_inicio(x,y,this.objCoordenadas.lineas[i].x1,this.objCoordenadas.lineas[i].y1,this.objCoordenadas.lineas[i].x2,this.objCoordenadas.lineas[i].y2,this.objCoordenadas.lineas[i].p)
       }
     }
     //si es punto ok p1 iz
    //  if(!this.llave_inicioOK){
     if(this.puntoinicioA==1 & this.puntoinicioB==1){
      this.llave_dibujar=false
      this.llave_inicioOK=true
      this.llave_stop=true
      this.reiniciarparametros();
      if(this.llave_punto[this.llave_punto.length-1]!=this.objCoordenadas.lineas[this.punto_siguiente].p){
          this.llave_punto.push(this.objCoordenadas.lineas[this.punto_siguiente].p)
          console.log(this.llave_punto);
      }
      this.punto_siguiente+=1
     }
     if(this.puntoinicioA==2 & this.puntoinicioB==2){
       this.llave_dibujar=false
       this.llave_inicioOK=false
       this.llave_stop=true
       this.punto_siguiente=0
       document.getElementById("pantallaActividad").style.backgroundColor='#F6535DCC';
        setTimeout(() => {
          document.getElementById("pantallaActividad").style.backgroundColor='white'
          this.limpiar_trazo();
          this.reiniciarparametros();
        },1000);
       
     }
    //  }
        if(this.isDrawing==true & this.llave_dibujar==true){ 
         

          if(this.mostrar==false){
            this.puntos_dibuja()
            this.mostrar=true
          }
        
        this.context.beginPath();
        this.context.moveTo(this.startX,this.startY);
        this.context.lineTo(x,y);
        this.context.lineWidth =10;
        this.context.lineCap ='round';
        this.context.strokeStyle ="#4978D6";
        this.context.stroke();
        // this.startX=x;
        // this.startY=y;    
      }
      // }    
      
      
    },
    responder(){
      var aciertos=0;
       if(this.llave_punto.length==this.objCoordenadas.lineas.length){
          for (let v = 0; v < this.llave_punto.length; v++) {
           if(this.llave_punto[v]==this.objCoordenadas.lineas[v].p){
             aciertos+=1;
           }
          }
         }
         if(aciertos==this.objCoordenadas.lineas.length){
            this.$emit("respuestasalumno",true);
         }else{
            this.$emit("respuestasalumno",false); 
            document.getElementById("pantallaActividad").style.backgroundColor='#F6535DCC'
         }
    },
    mostra_pasos(){
       this.retroalimentacion = setInterval(
       this.rutacorrecta,2000);
    },
    rutacorrecta(){
     if(this.paso_mostrar===7){
       console.log("estoy dentro")
     }else{
        clearInterval(this.retroalimentacion)
        console.log("perdi")
     }      
      console.log(this.paso_mostrar);
      this.paso_mostrar+=1;
        
    }
  }
  
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.contenedor{
  width: 78%;
  margin: 0 auto;
  border-top: 3px solid #FFCC0D ;
  border-bottom: 3px solid #FFCC0D;
}
.hello{
  padding-top: 50px;
}
.contenedor{
  display: flex ;
  justify-content: center;
}
.figura1{
  // outline: 2px solid green;
  position: absolute;
  margin: 0 auto;
}
.cva{
  // outline: 2px solid magenta;
  margin: 0 auto;
  position: relative;
}
</style>
