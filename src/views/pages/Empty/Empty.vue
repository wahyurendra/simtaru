<template>
    <div class="body">
      
      <div ref="map" id="map"></div>
      <!-- <Button @click="isMenu1Open=!isMenu1Open" class="btn">Tombol</Button> -->
    </div>
    
    <Menu1 :propsVisible="openMenu1" @closeModal="(closeModal)=>closeMenu1(closeModal)"></Menu1>
    <!-- <Dialog v-model:visible="visible" maximizable header="Header" :style="{ width: '50vw' }">
        <p>
            Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.
            Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
        </p>
    </Dialog> -->
  </template>
  
  <script>
  // import * as mapboxgl from 'mapbox-gl';
  import * as maplibregl from 'maplibre-gl';
  import 'maplibre-gl/dist/maplibre-gl.css'
  import Menu1 from './Menu1/Menu1.vue';
  import { jalan } from './geojson';
  import { ibadah } from './geojson';
  import { perkantoran } from './geojson';
  import { pendidikan } from './geojson';
  import { bandara } from './geojson';
  import { hutan } from './geojson';
  import { kesehatan } from './geojson';
  import { pekarangan } from './geojson';
  import { pelabuhan } from './geojson';
  import { pemukiman } from './geojson';
  import { sungai } from './geojson';
  import { tambang } from './geojson';
  import { tpa } from './geojson';
  import { lapangan } from './geojson';


  export default {
    data() {
      return {
        map: null,
        mapku:null,
        marker:{}
        // visible:true
      };
    },
    emits: ['closeMenu1'],
    props:{
      openMenu1:Boolean,
      toggleJalan:Boolean,
      togglePelabuhan:Boolean,
      togglePolygon:Boolean,
      toggleWaisai:Boolean,
      toggleKantorPemerintah:Boolean,
      toggleTempatPendidikan:Boolean,
      toggleBandara:Boolean,
      toggleHutan:Boolean,
      toggleKesehatan:Boolean,
      togglePekarangan:Boolean,
      togglePelabuhan:Boolean,
      togglePemukiman:Boolean,
      toggleSungai:Boolean,
      toggleTambang:Boolean,
      toggleTPA:Boolean,
      toggleLapangan:Boolean
    },
    components:{
      Menu1
    },
    mounted(){
      this.mapku = new maplibregl.Map({
            container: this.$refs.map, // container id
            style: {
                'version': 8,
                'sources': {
                    'raster-tiles': {
                        'type': 'raster',
                        'tiles': [
                            // 'https://api.maptiler.com/maps/satellite/256/{z}/{x}/{y}.jpg?key=I2yXUrRywAILTorOi6nw'
                            'https://api.maptiler.com/maps/basic-v2/256/{z}/{x}/{y}@2x.png?key=I2yXUrRywAILTorOi6nw'
                        ],
                        'tileSize': 256,
                        
                    }
                },
                'layers': [
                    {
                        'id': 'simple-tiles',
                        'type': 'raster',
                        'source': 'raster-tiles',
                        'minzoom': 1,
                        'maxzoom': 21
                    }
                ]
            },
            maxZoom: 20,
            minZoom: 13,
            center: [130.814963, -0.423930], // starting position
            zoom: 15 // starting zoom
        });

        this.mapku.on('load', () => {
            
            this.mapku.on('click', 'jalan-lines',(e)=>{
              // console.log(e.lngLat.lat)
              // console.log(e.lngLat.lng)

              // console.log(e.features[0].properties['NAMA_JALAN'])
              // console.log(e.features[0].properties['JENIS_JALA'])
              var coordinates = [e.lngLat.lng,e.lngLat.lat]

              const popup=new maplibregl.Popup()
                .setLngLat(coordinates)
              const ob=Object.entries(e.features[0].properties)
              // console.log(e.features[0].properties)
              console.log(ob)
              var htmlnya=``
              ob.forEach(element => {
                // console.log(element)
                htmlnya=`${htmlnya}
                <tr>
                  <td>
                    ${element[0]}
                  </td>
                  <td>
                    ${element[1]}
                  </td>
                </tr>
                `
              });
              htmlnya=`<style>
              table {
                font-family: arial, sans-serif;
                border-collapse: collapse;
                width: 100%;
              }

              td, th {
                border: 1px solid #dddddd;
                text-align: left;
                padding: 8px;
              }

              tr:nth-child(even) {
                background-color: #dddddd;
              }
              </style><table>
                <tr>
                  <th>Nama</th>
                  <th>Keterangan</th>
                </tr>

              ${htmlnya}</table>`
              popup.setHTML(htmlnya)
                
              popup.addTo(this.mapku);

            });

            this.mapku.on('click', 'sarana-ibadah',(e)=>{
              // console.log(e)
              console.log(e.features[0].properties['NAMA_JALAN'])
              console.log(e.features[0].properties['JENIS_JALA'])
              var coordinates = [e.lngLat.lng,e.lngLat.lat]
              const popup=new maplibregl.Popup()
                .setLngLat(coordinates)
              const ob=Object.entries(e.features[0].properties)
              // console.log(e.features[0].properties)
              console.log(ob)
              var htmlnya=``
              ob.forEach(element => {
                // console.log(element)
                htmlnya=`${htmlnya}
                <tr>
                  <td>
                    ${element[0]}
                  </td>
                  <td>
                    ${element[1]}
                  </td>
                </tr>
                `
              });
              htmlnya=`<style>
              table {
                font-family: arial, sans-serif;
                border-collapse: collapse;
                width: 100%;
              }

              td, th {
                border: 1px solid #dddddd;
                text-align: left;
                padding: 8px;
              }

              tr:nth-child(even) {
                background-color: #dddddd;
              }
              </style><table>
                <tr>
                  <th>Nama</th>
                  <th>Keterangan</th>
                </tr>

              ${htmlnya}</table>`
              popup.setHTML(htmlnya)
                
              popup.addTo(this.mapku);
            });

            this.mapku.on('click', 'perkantoran',(e)=>{
              // console.log(e)
              console.log(e.features[0].properties['NAMA_JALAN'])
              console.log(e.features[0].properties['JENIS_JALA'])
              var coordinates = [e.lngLat.lng,e.lngLat.lat]
              const popup=new maplibregl.Popup()
                .setLngLat(coordinates)
              const ob=Object.entries(e.features[0].properties)
              // console.log(e.features[0].properties)
              console.log(ob)
              var htmlnya=``
              ob.forEach(element => {
                // console.log(element)
                htmlnya=`${htmlnya}
                <tr>
                  <td>
                    ${element[0]}
                  </td>
                  <td>
                    ${element[1]}
                  </td>
                </tr>
                `
              });
              htmlnya=`<style>
              table {
                font-family: arial, sans-serif;
                border-collapse: collapse;
                width: 100%;
              }

              td, th {
                border: 1px solid #dddddd;
                text-align: left;
                padding: 8px;
              }

              tr:nth-child(even) {
                background-color: #dddddd;
              }
              </style><table>
                <tr>
                  <th>Nama</th>
                  <th>Keterangan</th>
                </tr>

              ${htmlnya}</table>`
              popup.setHTML(htmlnya)
                
              popup.addTo(this.mapku);
            });

            this.mapku.on('click', 'pendidikan',(e)=>{
              // console.log(e)
              console.log(e.features[0].properties['NAMA_JALAN'])
              console.log(e.features[0].properties['JENIS_JALA'])
              var coordinates = [e.lngLat.lng,e.lngLat.lat]
              const popup=new maplibregl.Popup()
                .setLngLat(coordinates)
              const ob=Object.entries(e.features[0].properties)
              // console.log(e.features[0].properties)
              console.log(ob)
              var htmlnya=``
              ob.forEach(element => {
                // console.log(element)
                htmlnya=`${htmlnya}
                <tr>
                  <td>
                    ${element[0]}
                  </td>
                  <td>
                    ${element[1]}
                  </td>
                </tr>
                `
              });
              htmlnya=`<style>
              table {
                font-family: arial, sans-serif;
                border-collapse: collapse;
                width: 100%;
              }

              td, th {
                border: 1px solid #dddddd;
                text-align: left;
                padding: 8px;
              }

              tr:nth-child(even) {
                background-color: #dddddd;
              }
              </style><table>
                <tr>
                  <th>Nama</th>
                  <th>Keterangan</th>
                </tr>

              ${htmlnya}</table>`
              popup.setHTML(htmlnya)
                
              popup.addTo(this.mapku);
            });

            // Change the cursor to a pointer when the mouse is over the places layer.
            this.mapku.on('mouseenter', 'places', () => {
              this.mapku.getCanvas().style.cursor = 'pointer';
            });

            // Change it back to a pointer when it leaves.
            this.mapku.on('mouseleave', 'places', () => {
              this.mapku.getCanvas().style.cursor = '';
            });

            // this.addMarker([130.804953, -0.431823],"tanda1")
            // End On load
        });
        
        // map.scrollZoom.disable();
        this.mapku.addControl(new maplibregl.NavigationControl());
        this.mapku.addControl(new maplibregl.FullscreenControl());
    },
    methods: {
      closeMenu1(close){
        this.$emit('closeMenu1',close)
      },

      drawLine(){
        // Jalan
        this.mapku.addSource('jalan-lines', {
        'type': 'geojson',
        'data': jalan
        });
        // End Jalan
        this.mapku.addLayer({
              'id': 'jalan-lines',
              'type': 'line',
              'source': 'jalan-lines',
              'paint': {
                  'line-width': 3.5,
                  // 'line-color': ['get', 'NAMA_JALAN']
                  'line-color': "#33C9EB"
              }
          });
      },

      removeLine(id){
          this.mapku.removeLayer(id);
          this.mapku.removeSource(id);
      },
      

      addFotoUdara(){
                  // source raster waisai
          this.mapku.addSource('waisai-raster', {
            'type': 'raster',
            'tiles': ['https://apisimtaru.kartabhumi.co.id/services/fotoudara/tiles/{z}/{x}/{y}.png'], 
            'tileSize': 256 
            
          });
          


            this.mapku.addLayer({
            'id': 'waisai-raster', 
            'type': 'raster', 
            'source': 'waisai-raster',
            'paint': {}
          });
      },

      removeFotoUdara(){
        const id='waisai-raster'
        this.mapku.removeLayer(id);
        this.mapku.removeSource(id);
        
      },

      addMarker(coordinates,id){
        this.marker[id] = new maplibregl.Marker()
            .setLngLat(coordinates)
            .addTo(this.mapku);
      },

      removeMarker(id){
        this.marker[id].remove();
        delete this.marker[id]
      },

      addPolygon(nama,data,warna){
        console.log(nama)
        console.log(data)
        // Area Sarana Ibadah
        this.mapku.addSource(nama, {
            'type': 'geojson',
            'data': data
        });
        // End Sarana Ibadah
        
        this.mapku.addLayer({
              'id': nama,
              'type': 'fill',
              'source': nama,
              'layout': {},
              'paint': {
                  'fill-color': warna,
                  'fill-opacity': 0.8
              }
          });
      },
      removePolygon(id){
          this.mapku.removeLayer(id);
          this.mapku.removeSource(id);
      },
    },
    watch:{
      toggleJalan: function(news,old){
        news?this.drawLine():this.removeLine('jalan-lines')
      },
      togglePelabuhan: function(news,old){
        news?this.addMarker([130.804953, -0.431823],'pelabuhan'):this.removeMarker('pelabuhan')
      },
      
      toggleWaisai: function(news,old){
        news?this.addFotoUdara():this.removeFotoUdara()
      },
      togglePolygon: function(news,old){
        news?this.addPolygon('sarana-ibadah',ibadah,'#2b0575'):this.removePolygon('sarana-ibadah')
      },
      toggleKantorPemerintah: function(news,old){
        news?this.addPolygon('perkantoran',perkantoran,'#0bbed8'):this.removePolygon('perkantoran')
      },
      toggleTempatPendidikan: function(news,old){
        news?this.addPolygon('pendidikan',pendidikan,'#009c6b'):this.removePolygon('pendidikan')
      },
      toggleBandara: function(news,old){
        news?this.addPolygon('bandara',bandara,'#009c6b'):this.removePolygon('bandara')
      },
      toggleHutan: function(news,old){
        news?this.addPolygon('hutan',hutan,'#009c6b'):this.removePolygon('hutan')
      },
      toggleKesehatan: function(news,old){
        news?this.addPolygon('kesehatan',kesehatan,'#009c6b'):this.removePolygon('kesehatan')
      },
      togglePekarangan: function(news,old){
        news?this.addPolygon('pekarangan',pekarangan,'#009c6b'):this.removePolygon('pekarangan')
      },
      togglePelabuhan: function(news,old){
        news?this.addPolygon('pelabuhan',pelabuhan,'#009c6b'):this.removePolygon('pelabuhan')
      },
      togglePemukiman: function(news,old){
        news?this.addPolygon('pemukiman',pemukiman,'#009c6b'):this.removePolygon('pemukiman')
      },
      toggleSungai: function(news,old){
        news?this.addPolygon('sungai',sungai,'#009c6b'):this.removePolygon('sungai')
      },
      toggleTambang: function(news,old){
        news?this.addPolygon('tambang',tambang,'#009c6b'):this.removePolygon('tambang')
      },
      toggleTPA: function(news,old){
        news?this.addPolygon('TPA',tpa,'#009c6b'):this.removePolygon('TPA')
      },
      toggleLapangan: function(news,old){
        news?this.addPolygon('lapangan',lapangan,'#009c6b'):this.removePolygon('lapangan')
      }

    },
    beforeDestroy() {
      if (this.map) {
        this.map.remove();
      }
    }
  };
  </script>
  
<style>
    .body { margin: 0; padding: 0; }
    #map { position: absolute; left:0; top: 10%; bottom: 0; width: 100%; height:90%; }
</style>
