<template>
    <div class="body">
      
      <div ref="map" id="map"></div>
      <pre id="distance" class="distance-container"></pre>
      <!-- <div class="calculation-box">
        <div id="calculated-area"></div>
    </div> -->
      <Button @click="toggle"  text size="small"><span class="layer" v-b-tooltip.hover title="Layer"></span></Button>
      <Button @click="toggle1" text size="small"><span class="raster" v-b-tooltip.hover title="Raster"></span></Button>
      <Button @click="toggle2" text size="small"><span class="ruler" v-b-tooltip.hover title="Pengukuran"></span></Button>
      <Button @click="toggle3" text size="small"><span class="mapstyle" v-b-tooltip.hover title="Jenis Maps"></span></Button>
      <Button @click="toggle4" text size="small"><span class="pin" v-b-tooltip.hover title="Fasilitas Umum"></span></Button>  
      <input @change="handleImage" id="fileUpload" type="file" hidden>
      <!-- <input id="fileUpload" type="file" hidden> -->
      <Toast/>
      <!-- <Button @click="chooseFiles()" text size="small" type="file"><span class="upload" v-b-tooltip.hover title="Upload File"></span></Button>   -->
      <Button @click="visible = true" v-if="isLogin" text size="small" type="file"><span class="upload" v-b-tooltip.hover title="Upload File"></span></Button>  
            
      <Dialog v-model:visible="visible" modal header="Upload SHP" :style="{ width: '40rem' }" :breakpoints="{ '1199px': '75vw', '575px': '90vw' }">
        <h6>Pilih Batas Wilayah</h6>
        <div class="card flex justify-content-center">
          <Dropdown @change="handleDropdown" v-model="selectedCity" :options="cities" optionLabel="name" placeholder="Pilih Wilayah" class="w-full md:w-14rem" />
        </div>
        <div class="card flex justify-content-center">
          <Button @click="chooseFiles()" label="Pilih File" raised />
          <!-- <Toast />
          <FileUpload 
          @select="convertoBase64($event, 'skPengangkatanPindah', 'file')"
           mode="basic" name="file" url="/api/upload" accept="image/*" :maxFileSize="1000000" @upload="onUpload" /> -->
        </div>
        <div class="flex justify-content-center">
          <Button @click="submitFile()" label="Upload" icon="pi pi-upload" />
        </div>
      </Dialog>



      <!-- <input @change="handleImage" type="file" accept="image/*" class="custom-input"> -->
      <!-- <Button @click="toggle5" text size="small"><span class="upload" v-b-tooltip.hover title="Upload"></span></Button> -->
      

   
      <OverlayPanel ref="op" appendTo="body" :showCloseIcon="false">
        <Tree 
          v-model:selectionKeys="selectedKey" 
          :value="nodes" 
          selectionMode="checkbox" 
          class="w-full md:w-30rem"
          @nodeSelect="onNodeSelect"
          @nodeUnselect="onNodeUnselect"
          />
      </OverlayPanel>
      <OverlayPanel ref="op1" appendTo="body" :showCloseIcon="false">
        <Tree 
          style="height: 350px; overflow: scroll;"
          v-model:selectionKeys="selectedKey1" 
          :value="nodesUdara" 
          selectionMode="checkbox" 
          :filter="true" 
          filterMode="lenient" 
          class="w-full md:w-30rem"
          @nodeSelect="onNodeUdaraSelect"
          @nodeUnselect="onNodeUdaraUnselect"
          />
      </OverlayPanel>
      <OverlayPanel ref="op2" appendTo="body" :showCloseIcon="false">
        <Button @click="subMeasureDistance" class="p-button p-button-secondary mr-2 mb-2"> Jarak</Button>
        <Button @click="buttonDrawPolygon" class="p-button p-button-secondary mr-2 mb-2"> Area</Button>
      </OverlayPanel>
      <OverlayPanel ref="op3" appendTo="body" :showCloseIcon="false">
        <Tree 
          v-model:selectionKeys="selectedKey2" 
          :value="menustyle" 
          selectionMode="single" 
          class="w-full md:w-30rem"
          @nodeSelect="setStyleMap"
          @nodeUnselect="removeFotoUdara"
          />
      </OverlayPanel>
      <pre id="info"></pre>
      <OverlayPanel ref="op4" appendTo="body" :showCloseIcon="false" style="height: 400px;">
        <Tree 
        style="height: 350px; overflow: scroll;"
        v-model:selectionKeys="selectedKey3" 
        :value="nodesFasilitas" 
        :filter="true" 
        selectionMode="checkbox"
        filterMode="lenient" 
        class="w-full md:w-30rem" 
        @nodeSelect="onNodeFasilitasSelect"
        @nodeUnselect="onNodeFasilitasUnselect"
        />

      </OverlayPanel>

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
  import * as mapboxgl from 'mapbox-gl';
  
  import 'mapbox-gl/dist/mapbox-gl.css';
  // import * as mapboxgl from 'maplibre-gl';
  // import 'maplibre-gl/dist/maplibre-gl.css'
  import Menu1 from './Menu1/Menu1.vue';
  import * as turf from '@turf/turf';
  import MapboxDraw from "@mapbox/mapbox-gl-draw";
  import MapboxGeocoder from '@mapbox/mapbox-gl-geocoder';
  import '@mapbox/mapbox-gl-geocoder/dist/mapbox-gl-geocoder.css';
  import { menuraster } from './menuoption';
  import { mapstylemapbox } from './menuoption';  
  import axios from 'axios';
  import { ref } from "vue";

  export default {
    data() {
      return {
        map: null,
        mapku:null,
        marker:{},
        nodes:null,
        nodesUdara:null,
        nodesFasilitas:null,
        selectedKey: null,
        selectedKey1: null,
        selectedKey2: null,
        selectedKey3: null,
        isMeasureDistance:false,
        koordinatPengukuranLuas:null,
        popUpPengukuranLuas:null,
        popUpFasilitas:{},
        menuraster:null,
        menustyle:null,
        image: '',
        file: '',
        batas: '',
        visible: false,
        selectedCity: null,
        cities: [
            { name: 'Kelurahan Warmasen', code: 'NY' },
            { name: 'Kelurahan Saporkren', code: 'RM' },
            { name: 'Kelurahan Sapordanco', code: 'LDN' },
            { name: 'Kelurahan Bonkawir', code: 'IST' },
            { name: 'Kelurahan Waisai Kota', code: 'PRS' },
            { name: 'Batas Wilayah', code: 'PRS' }

        ]
      };
    },
    emits: ['closeMenu1'],
    props:{
      openMenu1:Boolean,
      toggleJalan:Boolean,
      togglePelabuhan:Boolean,
      togglePolygon:Boolean,
      toggleWaisai:Boolean,
      isLogin:Boolean
    },
    components:{
      Menu1
    },
    mounted(){
      this.menuraster = menuraster;
      this.menustyle = mapstylemapbox;
      this.getTreeNodes().then((data)=>{
        this.nodes = data
      })
      this.getTreeNodesUdara().then((data)=>{
        this.nodesUdara = data
      })
      this.getTreeNodesFasilitas().then((data)=>{
        this.nodesFasilitas = data
      })
      mapboxgl.accessToken = 'pk.eyJ1IjoieGljZXc2OTU4MSIsImEiOiJjbG1qemprNW8wNzg0MnNxcXUxeXBvbTBpIn0.JTqLlI9LIoLKEGkoxBbiyg';
      this.mapku = new mapboxgl.Map({
        container: 'map',
        // Choose from Mapbox's core styles, or make your own style with Mapbox Studio
        style: 'mapbox://styles/mapbox/streets-v12',
        center: [130.814963, -0.423930],
        zoom: 15,
        maxZoom: 21,
        minZoom: 7.5,
        // projection: 'naturalEarth' // starting projection
        });

        this.draw = new MapboxDraw({
            displayControlsDefault: false,
        });
        this.mapku.addControl(this.draw);
        this.mapku.on('click', this.handleMouseClick);
        this.mapku.on('dblclick', this.handleMouseClick);
        this.mapku.on('mousemove', this.handleMouseMove);
        
        

        
        // measure distace
        this.distanceContainer = document.getElementById('distance');
        this.geojson = {
            'type': 'FeatureCollection',
            'features': []
        };
        this.linestring = {
            'type': 'Feature',
            'geometry': {
                'type': 'LineString',
                'coordinates': []
            }
        };

        // end distance
        
        // 
        this.coordinatesGeocoder = function (query) {
        // Match anything which looks like
        // decimal degrees coordinate pair.
        this.matches = query.match(
        /^[ ]*(?:Lat: )?(-?\d+\.?\d*)[, ]+(?:Lng: )?(-?\d+\.?\d*)[ ]*$/i
        );
        if (!this.matches) {
        return null;
        }
        
        function coordinateFeature(lng, lat) {
          return {
          center: [lng, lat],
          geometry: {
          type: 'Point',
          coordinates: [lng, lat]
          },
          place_name: 'Lat: ' + lat + ' Lng: ' + lng,
          place_type: ['coordinate'],
          properties: {},
          type: 'Feature'
          };
        }
        
        const coord1 = Number(this.matches[1]);
        const coord2 = Number(this.matches[2]);
        const geocodes = [];
        
        if (coord1 < -90 || coord1 > 90) {
        // must be lng, lat
        geocodes.push(coordinateFeature(coord1, coord2));
        }
        
        if (coord2 < -90 || coord2 > 90) {
        // must be lat, lng
        geocodes.push(coordinateFeature(coord2, coord1));
        }
        
        if (geocodes.length === 0) {
        // else could be either lng, lat or lat, lng
        geocodes.push(coordinateFeature(coord1, coord2));
        geocodes.push(coordinateFeature(coord2, coord1));
        }
        
        return geocodes;
        };
        
        // Add the control to the map.
        this.mapku.addControl(
          new MapboxGeocoder({
          accessToken: mapboxgl.accessToken,
          localGeocoder: this.coordinatesGeocoder,
          zoom: 4,
          placeholder: 'Pencarian',
          mapboxgl: mapboxgl,
          reverseGeocode: true
          })
        );
        // 
        this.mapku.addControl(new mapboxgl.ScaleControl());
        this.mapku.on('mousemove', (e) => {
          document.getElementById('info').innerHTML =
          `Posisi Mouse: ${e.lngLat.lng}, ${e.lngLat.lat}`
          });


        
        // map.scrollZoom.disable();
        this.mapku.addControl(new mapboxgl.NavigationControl());
        this.mapku.addControl(new mapboxgl.FullscreenControl());
    },
    methods: {
      showSuccess() {
            this.$toast.add({ severity: 'success', summary: 'Success Upload', detail: 'Upload SHP Berhasil!', life: 3000 });
        },
      submitFile(){
        this.createBase64Image(this.file)
      },
      handleImage(e) {
        this.file = e.target.files[0];  
      },
      handleDropdown(e) {
        // console.log(e.value['name'])
        this.batas = e.value['name'];  
      },
      createBase64Image(fileObject){
        const reader = new FileReader();
        reader.onload = (e) => {
          this.image = e.target.result;
          this.uploadImage(this.image);
        };
        reader.readAsDataURL(fileObject);
      },
      uploadImage(isi){
        // const {image} = this;
        axios.post("https://apigeojson.kartabhumi.co.id/api/upload", {file:isi,batas:this.batas})
        .then((response) =>{
          this.remoteUrl = response.data.url;
          this.showSuccess();
        })
        .catch((err) =>{
          return new Error(err.message);
        })
      },
      chooseFiles() {
          document.getElementById("fileUpload").click()
      },
      setStyleMap(selected){
        this.mapku.setStyle('mapbox://styles/mapbox/' + selected.data);
      },
      async setPopupFasilitas(selected){
        await this.getTreeNodesFasilitasDetail(selected.data).then((data)=>{
          var ob = Object.entries(data['features'][0]['properties'])
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
            </style>
            <img src="/layout/images/bg-min.JPG" width="100%" height="100%">
            <table>
              <tr>
                <th>Nama</th>
                <th>Keterangan</th>
              </tr>

            ${htmlnya}</table>`
          this.popUpFasilitas[selected.data]= new mapboxgl.Popup()
                    .setLngLat(selected.koordinat)
                    .setHTML(htmlnya)
                    // .addTo(this.mapku)

        })

        
      },
      async onNodeFasilitasSelect(selected){
          await this.setPopupFasilitas(selected)
          this.addPin(selected),
          this.mapku.flyTo({
            center:selected.koordinat,
            essential: true
          })
          this.popUpFasilitas[selected.data].addTo(this.mapku)


        },
      onNodeFasilitasUnselect(unselected){
          this.removeFotoFasilitasNodes(unselected)  
      },
      addPin(selected){
        this.marker[selected.data] = new mapboxgl.Marker()
          .setLngLat([selected.koordinat[0],selected.koordinat[1]])
          .setPopup(this.popUpFasilitas[selected.data])
          .addTo(this.mapku);
      },
      removeFotoFasilitasNodes(unselected){

        this.marker[unselected.data].remove()
        this.popUpFasilitas[unselected.data].remove()
        
      },
      // asdsa
      onNodeUdaraSelect(selected){
          this.addFotoUdaraNode(selected)
          this.mapku.flyTo({
            center:selected.koordinat,
            minzoom:selected.minzoom,
            zoom:(selected.minzoom+selected.maxzoom)/2,            
            essential: true
          })
        },
      onNodeUdaraUnselect(unselected){
          this.removeFotoUdaraNodes(unselected)  
      },
      addFotoUdaraNode(selected){
        this.mapku.addSource(selected.name,{
          type:'raster',
          tiles:selected.tiles,
          tilesize:selected.tilesize
        })

        this.mapku.addLayer({
          id:selected.name,
          type:'raster',
          source:selected.name,
          paint:{}
        })
      },
      removeFotoUdaraNodes(unselected){
        this.mapku.removeLayer(unselected.name);
        this.mapku.removeSource(unselected.name);
        
      },
      handleMouseMove(e) {
        if (this.draw.getMode() === 'draw_polygon') {
          this.whileUpdateArea(e);
        }
      },
      handleMouseClick(e) {
        if (this.draw.getMode() === 'draw_polygon') {
          this.clickUpdateArea(e);
        }
      },
      buttonDrawPolygon(){
        this.isDrawArea=!this.isDrawArea
        this.isDrawArea?this.enableDrawPolygon():this.deleteDrawnItems()
      },
      enableDrawPolygon() {
        this.draw.changeMode('draw_polygon');
      },
      deleteDrawnItems() {
        let ids = this.draw.getAll().features.map(feat => feat.id);
        this.draw.delete(ids);
      },
      getMidpoint(pointA, pointB) {
        var midpointLat = (pointA.lat + pointB.lat) / 2;
        var midpointLon = (pointA.lng + pointB.lng) / 2;
        return { lat: midpointLat, lng: midpointLon };
      },
      whileUpdateArea(e) {
            const data = this.draw.getAll();
            if (data.features.length > 0) {
                const area = turf.area(data);
                const roundedArea = Math.round(area * 100) / 100;
                if (this.koordinatPengukuranLuas) {
                  const currentCoordinate=this.getMidpoint(this.koordinatPengukuranLuas,e.lngLat)
                  this.popUpPengukuranLuas.setLngLat(currentCoordinate)
                  .setHTML(`<p><strong>${
                        roundedArea
                    }</strong></p><p>meter persegi</p>`)                  
                }
            }
      },
      clickUpdateArea(e) {
            const data = this.draw.getAll();
            if (data.features.length > 0) {
                const area = turf.area(data);
                // restrict to area to 2 decimal points
                const roundedArea = Math.round(area * 100) / 100;
                if (this.koordinatPengukuranLuas) {
                  this.koordinatPengukuranLuas!=e.lngLat?this.koordinatPengukuranLuas=this.getMidpoint(e.lngLat,this.koordinatPengukuranLuas):this.koordinatPengukuranLuas=e.lngLat  
                }
                else{
                  this.koordinatPengukuranLuas=e.lngLat
                }
                
                this.popUpPengukuranLuas= new mapboxgl.Popup()
                  .setLngLat(this.koordinatPengukuranLuas)
                  .setHTML(`<p><strong>${
                        roundedArea
                    }</strong></p><p>meter persegi</p>`)
                  .addTo(this.mapku);
            }
      },
      subMeasureDistance(){
        this.isMeasureDistance=!this.isMeasureDistance
        this.isMeasureDistance?this.measuredistance():this.closemeasuredistance()
      },
      drawDistance(e) {
            const features = this.mapku.queryRenderedFeatures(e.point, {
                layers: ['measure-points']
            });

            if (this.geojson.features.length > 1) this.geojson.features.pop();

            // Clear the Distance container to populate it with a new value
            this.distanceContainer.innerHTML = '';

            // If a feature was clicked, remove it from the map
            if (features.length) {
                const id = features[0].properties.id;
                this.geojson.features = this.geojson.features.filter((point) => {
                    return point.properties.id !== id;
                });
            } else {
                const point = {
                    'type': 'Feature',
                    'geometry': {
                        'type': 'Point',
                        'coordinates': [e.lngLat.lng, e.lngLat.lat]
                    },
                    'properties': {
                        'id': String(new Date().getTime())
                    }
                };

                this.geojson.features.push(point);
            }

            if (this.geojson.features.length > 1) {
                this.linestring.geometry.coordinates = this.geojson.features.map(
                    (point) => {
                        return point.geometry.coordinates;
                    }
                );

                this.geojson.features.push(this.linestring);

                // Populate the distanceContainer with total distance
                const value = document.createElement('pre');
                value.textContent =
                    `Total jarak: ${
                        turf.length(this.linestring).toLocaleString()
                    }km`;
                this.distanceContainer.appendChild(value);
            }

            this.mapku.getSource('geojson').setData(this.geojson);
      },
      measuredistance(){
        this.mapku.addSource('geojson', {
            'type': 'geojson',
            'data': this.geojson
        });
        this.mapku.addLayer({
            id: 'measure-points',
            type: 'circle',
            source: 'geojson',
            paint: {
                'circle-radius': 5,
                'circle-color': '#ffa701'
            },
            filter: ['in', '$type', 'Point']
        });
        this.mapku.addLayer({
            id: 'measure-lines',
            type: 'line',
            source: 'geojson',
            layout: {
                'line-cap': 'round',
                'line-join': 'round'
            },
            paint: {
                'line-color': '#ffa701',
                'line-width': 2.5
            },
            filter: ['in', '$type', 'LineString']
        });
        this.mapku.on('click',this.drawDistance );
        this.mapku.on('mousemove', (e) => {
          const features = this.mapku.queryRenderedFeatures(e.point, {
            layers: ['measure-points']
          });
          // UI indicator for clicking/hovering a point on the map
          this.mapku.getCanvas().style.cursor = features.length ?
              'pointer' :
              'crosshair';
          });
      },
      closemeasuredistance(){
        this.distanceContainer.innerHTML = '';
        this.mapku.on('mousemove', (e) => {
          const features = this.mapku.queryRenderedFeatures(e.point, {
            layers: ['measure-points']
          });
          // UI indicator for clicking/hovering a point on the map
          this.mapku.getCanvas().style.cursor = "grab"
          });


        this.mapku.off('click',this.drawDistance );
        this.mapku.removeLayer('measure-points');
        this.mapku.removeLayer('measure-lines');
        this.mapku.removeSource('geojson');
        this.geojson={
            'type': 'FeatureCollection',
            'features': []
        };
        
      },
      onNodeSelect(selected){
        var terpilih
        if(selected.children){
          selected.children.forEach(element => {
            this.addGeojson(element)
            terpilih=element
          });
        }
        else{
          this.addGeojson(selected)
          terpilih=selected         
        }
        this.mapku.flyTo({
            center:terpilih.koordinat,
            essential: true
          })
      },
      onNodeUnselect(unselected){
        if (unselected.children) {
          unselected.children.forEach(element => {
            this.removeGeojson(element)
          });
        }
        else{
          this.removeGeojson(unselected)
        } 
      },
      getTreeNodes() {
        return fetch('https://apigeojson.kartabhumi.co.id/api/simtaru')
            .then((res) => res.json())
            .then((d) => d.root);
      },
      getTreeNodesUdara() {
        return fetch('https://apigeojson.kartabhumi.co.id/api/fotoudara')
            .then((res) => res.json())
            .then((d) => d.root);
      },
      getTreeNodesFasilitas() {
        return fetch('https://apigeojson.kartabhumi.co.id/api/fasilitas')
            .then((res) => res.json())
            .then((d) => d.root);

      },
      getTreeNodesFasilitasDetail(id){
        return fetch(`https://apigeojson.kartabhumi.co.id/api/fasilitas/${id}`)
            .then((res) => res.json())
            // .then((d) => d.root);

      },
      toggle(event) {
        this.$refs.op.toggle(event);
      },
      toggle1(event) {
        this.$refs.op1.toggle(event);
      },
      toggle2(event) {
        this.$refs.op2.toggle(event);
      },
      toggle3(event) {
        this.$refs.op3.toggle(event);
      },
      toggle4(event) {
        this.$refs.op4.toggle(event);
      },
      closeMenu1(close){
        this.$emit('closeMenu1',close)
      },
      addGeojson(selected){
        this.mapku.addSource(selected.data, {
            'type': 'geojson',
            'data': `https://apigeojson.kartabhumi.co.id/api/simtaru/${selected.parent}/${selected.data}`
          });
          
          var paint={};
          `${selected.jenis}` === 'line'?paint['line-color']=`${selected.warna}`:`${selected.jenis}`==='fill'?paint['fill-color']=`${selected.warna}`:null;
          `${selected.jenis}` === 'line'?paint['line-width']=3.5:`${selected.jenis}`==='fill'?paint['fill-opacity']=0.8:null;
          this.mapku.addLayer({
              'id': `${selected.data}`,
              'type': `${selected.jenis}`,
              'source': `${selected.data}`,
              'paint': paint
              
          });
          this.mapku.on('click', selected.data,(e)=>{
              // console.log(e.lngLat.lat)
              // console.log(e.lngLat.lng)

              // console.log(e.features[0].properties['NAMA_JALAN'])
              // console.log(e.features[0].properties['JENIS_JALA'])
              var coordinates = [e.lngLat.lng,e.lngLat.lat]
              const popup=new mapboxgl.Popup()
                .setLngLat(coordinates)
              const ob=Object.entries(e.features[0].properties)
              // console.log(ob)
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
          // console.log(paint);
      },
      removeGeojson(unselected){
          this.mapku.removeLayer(unselected.data);
          this.mapku.removeSource(unselected.data);
      },
      drawLine(){

      },
      

      removeLine(id){
          this.mapku.removeLayer(id);
          this.mapku.removeSource(id);
      },
      

      addFotoUdara(url){
          this.mapku.addSource('waisai-raster', {
            'type': 'raster',
            'tiles': [`https://apisimtaru.kartabhumi.co.id/services/${url.data}/tiles/{z}/{x}/{y}.png`], 
            'tileSize': 256 
          });
          
            this.mapku.addLayer({
            'id': 'waisai-raster', 
            'type': 'raster', 
            'source': 'waisai-raster',
            'paint': {}
          });
      },

      removeFotoUdara(url){
        const id='waisai-raster'
        this.mapku.removeLayer(id);
        this.mapku.removeSource(id);
        
      },

      addMarker(coordinates,id){
        this.marker[id] = new mapboxgl.Marker()
            .setLngLat(coordinates)
            .addTo(this.mapku);
      },

      removeMarker(id){
        this.marker[id].remove();
        delete this.marker[id]
      },

      addPolygon(){
      },
      removePolygon(id){
      },
    },
    watch:{
      toggleJalan: function(news,old){
        news?this.drawLine():this.removeLine('lines')
      },
      togglePelabuhan: function(news,old){
        news?this.addMarker([130.804953, -0.431823],'pelabuhan'):this.removeMarker('pelabuhan')
      },
      togglePolygon: function(news,old){
        news?this.addPolygon():this.removePolygon('polygon')
      },
      toggleWaisai: function(news,old){
        news?this.addFotoUdara():this.removeFotoUdara()
      },
      visible: function(news,old){
        news?this.error=false:null
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
    #map { position: absolute; left:0; top: 9%; bottom: 0; width: 100%; height:88%; }

    #info {
      display: table;
      position: absolute;
      margin: 0px auto;
      word-wrap: anywhere;
      white-space: pre-wrap;
      margin-top: 36.5%;
      /* padding: 10px; */
      padding-bottom: 5px;
      border: none;
      border-radius: 3px;
      font-size: 12px;
      text-align: center;
      color: #222;
      background: #fff;
    }
    .custom-control-button {
      background-color: white;
      border: none;
      border-radius: 3px;
      box-shadow: 0 1px 2px rgba(0,0,0,0.1);
      cursor: pointer;
      font-size: 14px;
      padding: 10px;
    }

    .distance-container {
        position: absolute;
        top: 150px;
        left: 10px;
        z-index: 1;
    }

    .distance-container > * {
        background-color: rgba(0, 0, 0, 0.5);
        color: #fff;
        font-size: 11px;
        line-height: 18px;
        display: block;
        margin: 0;
        padding: 5px 10px;
        border-radius: 3px;
    }

    .calculation-box {
        height: 75px;
        width: 150px;
        position: absolute;
        bottom: 160px;
        left: 10px;
        background-color: rgba(255, 255, 255, 0.9);
        padding: 15px;
        text-align: center;
    }

    p {
        font-family: 'Open Sans';
        margin: 0;
        font-size: 13px;
    }

    span.layer {
      background: url(/layout/images/layer.svg) no-repeat top left;
      background-size: contain;
      cursor: pointer;
      display: inline-block;
      height: 30px;
      width: 30px;
    }
    span.raster {
      background: url(/layout/images/image.svg) no-repeat top left;
      background-size: contain;
      cursor: pointer;
      display: inline-block;
      height: 30px;
      width: 30px;
    }
    span.ruler {
      background: url(/layout/images/ruler.svg) no-repeat top left;
      background-size: contain;
      cursor: pointer;
      display: inline-block;
      height: 30px;
      width: 30px;
    }
    span.mapstyle {
      background: url(/layout/images/map.svg) no-repeat top left;
      background-size: contain;
      cursor: pointer;
      display: inline-block;
      height: 30px;
      width: 30px;
    }
    span.pin {
      background: url(/layout/images/pin.svg) no-repeat top left;
      background-size: contain;
      cursor: pointer;
      display: inline-block;
      height: 30px;
      width: 30px;
    }
    span.upload {
      background: url(/layout/images/upload.svg) no-repeat top left;
      background-size: contain;
      cursor: pointer;
      display: inline-block;
      height: 30px;
      width: 30px;
    }
    
</style>