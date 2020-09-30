<template>
  <div>
    <div class="zoneCompteur">
      <span class="compteur" v-for="element in tableau" :key="element.digit">
        <img :src=element.image width="134" :class=element.digit />
      </span>
    </div>
    <div class="zonehaut">
      <img class="imgFond" src="../assets/commun.png">
    </div>
    <div class="zoneBas">
      <img :src=imageBasSrc>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import axios from 'axios'
// import imgvide from "../assets/vide.png"
import img1 from "../assets/1.png"
import img2 from "../assets/2.png"
import img3 from "../assets/3.png"
import img4 from "../assets/4.png"
import img5 from "../assets/5.png"
import img6 from "../assets/6.png"
import img7 from "../assets/7.png"
import img8 from "../assets/8.png"
import img9 from "../assets/9.png"
import img0 from "../assets/0.png"
import imgDefaut from "../assets/smartNews.png"

//var prefixeimage="http://" + window.location.hostname + ":3003/image/"

export default {
  data(){
    return{
      prefixeimage: process.env.VUE_APP_PREFIXE_IMAGE+ this.getMagasin() +"/",
      nombre: 0, 
      magasin: '',
      ccu: '',
      imagesbas: [{src: imgDefaut, delai: 60}],
      indeximage: 0,
      imageBasSrc: imgDefaut,
      tableau: [ {image: '', digit: "digit1"},
                 {image: '', digit: "digit2"},
                 {image: '', digit: "digit3"},
                 {image: '', digit: "digit4"},
                 {image: '', digit: "digit5"},
                 {image: '', digit: "digit6"},
                 {image: '', digit: "digit7"}
                 ],
      images: [img0,img1,img2,img3,img4,img5,img6,img7,img8,img9]
    }
  },
  name: 'Home',
  mounted(){
    // Compteur
    console.log("init compteur")
    this.magasin = this.$_GET('magasin')
    this.ccu = this.$_GET('ccu')
    axios.get(process.env.VUE_APP_URL_API+"/compteur").then(resp => {
      console.log(resp)
      this.nombre = resp.data
      this.miseEnTab(resp.data)
      setInterval(this.miseAJour, 60000);   // TODO : refresh toutes les minutes 
    })
    // Image basse
    console.log("init image")
      axios.get(process.env.VUE_APP_PREFIXE_IMAGE + this.magasin +"/"+ this.ccu +".json").then(resp => {
      console.log(resp);
      this.imagesbas = resp.data
      this.nextImage()
      setInterval(this.refreshListeImage, 60000);
    })
    
  },
  methods:{
    miseEnTab(data){
      //console.log(data)
      var nbdigit=data.toString().length
      console.log("nb digits : " + nbdigit)
      for(let i=0; i<7; i++){
        
        if(i<7-nbdigit) {
            // on est sur du blanc
            this.tableau[i].image= ''
        } else {
            this.tableau[i].image= this.images[data.toString()[nbdigit-7+i]]
        }
      }
    },
    nextImage (){
       var nbElement = this.imagesbas.length;
      if(nbElement!=0) {
        if(this.indeximage>=nbElement-1) {
          this.indeximage=0;
          this.imageBasSrc=this.prefixeimage+this.imagesbas[this.indeximage].src
          setTimeout(this.nextImage, this.imagesbas[this.indeximage].delai*1000);
        } else {
          this.indeximage=this.indeximage+1;
          this.imageBasSrc=this.prefixeimage+this.imagesbas[this.indeximage].src
          setTimeout(this.nextImage, this.imagesbas[this.indeximage].delai*1000);
         }
      } else {
        console.log("tableau d'image vide" )
        setTimeout(this.nextImage, 300000);
      }
      
    },
    refreshListeImage (){
        console.log("refresh image list")
      //axios.get("http://" + window.location.hostname + ":3003/liste/24278").then(resp => {
      axios.get(process.env.VUE_APP_PREFIXE_IMAGE + this.magasin +"/"+ this.ccu +".json").then(resp => {
      this.imagesbas = resp.data
      console.log("taille tableau " + this.imagesbas.length)
      })
    },
    miseAJour(){
        console.log("refresh compteur")
      axios.get(process.env.VUE_APP_URL_API + "/compteur"
      ).then(resp => {
      console.log(resp)
      if(this.nombre != resp.data){ // on a eu un passage en plus (mini)
        // pour eviter les effets flash :  on ne met à jour les images que si le nombre à changé
        this.nombre = resp.data
        this.miseEnTab(resp.data)
      }
    })
    },
    getMagasin(){
      return window.location.hash.replace('#/home?magasin=', '')
    },
    $_GET(param) {
      const params = "?"+window.location.hash.split("?")[1]
      const urlParams = new URLSearchParams(params)
      const magasins = urlParams.get(param)
      return magasins
    }
  }

}
</script>
<style>
.zoneCompteur{
  border: none !important;
  position: absolute;
  top: 100px;
  left: 700px;
}
.zoneHaut{
  border: none !important;
  position: absolute;
  height: 540px !important;
  top: 0;
  left: 0;
}
.zoneBas{
  border: none !important;
  position: absolute;
  height: 540px !important;
  top: 540px;
  left: 0;
}
.imgFond{
  border: none !important;
}
.digit1{
  border: none !important;
  position: absolute;
  top: 0px;
  left: 0px;
}
.digit2{
  border: none !important;
  position: absolute;
  top: 0px;
  left: 150px;
}
.digit3{
  border: none !important;
  position: absolute;
  top: 0px;
  left: 300px;
}
.digit4{
  border: none !important;
  position: absolute;
  top: 0px;
  left: 450px;
}
.digit5{
  border: none !important;
  position: absolute;
  top: 0px;
  left: 600px;
}
.digit6{
  border: none !important;
  position: absolute;
  top: 0px;
  left: 750px;
}
.digit7{
  border: none !important;
  position: absolute;
  top: 0px;
  left: 900px;
}
.v-application{
  overflow: hidden;
  background-size: 100% 100% !important;
}

body::-webkit-scrollbar {
    display: none;
}
</style>
