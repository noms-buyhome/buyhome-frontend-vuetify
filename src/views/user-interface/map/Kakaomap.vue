<template>
  <VCard>
    <VCardText>
      <VRow>
        <!-- 화면 왼쪽 지도 출력 start -->
        <VCol cols="8">
          <div>
            <div class="btn-group">
              <button type="button" @click="currentLocationSearch()" class="w-btn w-btn-search">
                {{ address }} 검색
              </button>
              <button type="button" @click="displayMarker([])" class="w-btn w-btn-delete">X</button>
              <button
                type="button"
                @click="eduToggle()"
                class="w-btn w-btn-green"
                :class="{ 'w-btn w-btn-selected': !eduSwitch }"
              >
                학교
              </button>
              <button
                type="button"
                @click="transportToggle()"
                class="w-btn w-btn-green"
                :class="{ 'w-btn w-btn-selected': !transportSwitch }"
              >
                교통
              </button>
              <button
                type="button"
                @click="mediToggle()"
                class="w-btn w-btn-green"
                :class="{ 'w-btn w-btn-selected': !mediSwitch }"
              >
                병원
              </button>
              <button
                type="button"
                @click="foodToggle()"
                class="w-btn w-btn-green"
                :class="{ 'w-btn w-btn-selected': !foodSwitch }"
              >
                식당
              </button>
            </div>
            <div id="map" @mouseup="updateLatLng()"></div>
          </div>
        </VCol>
        <!-- 화면 왼쪽 지도 출력 end -->

        <!-- 화면 오른쪽 매물 리스트 출력 start -->
        <VCol cols="4">
          <v-table fixed-header height="580px">
            <thead>
              <tr>
                <th class="text-left">아파트이름</th>
                <th class="text-left">지번</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in homeTables" :key="item.name">
                <td>
                  <a v-on:click.prevent="setCenterInMap(item.lat, item.lng)">&nbsp;&nbsp;{{ item.aptName }}</a>
                </td>
                <td>{{ item.jibun }}</td>
              </tr>
            </tbody>
          </v-table>
          <!-- <div v-if="homeTables.length > 0">
            <v-data-table :headers="headers" :items="homeTables" :items-per-page="5" class="elevation-1"></v-data-table>
          </div>
          <div v-else>
            <img src="./images/icon-medi.png" alt="img태그다잉?" />
          </div> -->
        </VCol>
        <!-- 화면 오른쪽 매물 리스트 출력 end -->
      </VRow>
    </VCardText>
    <v-dialog v-model="dialog">
      <v-card class="modal">
        <p>아파트 상세 정보</p>
        <h1>🏠 일성 빌라트</h1>
        주소 : {{ this.address }} {{ this.homeTables[0].jibun }} <br />
        건축년도 : {{ this.homeTables[0].buildYear }} <br />
        <br />
        <h3>거래 내역</h3>
        <v-table fixed-header height="420px">
          <thead>
            <tr>
              <th class="text-left">거래일</th>
              <th class="text-left">면적</th>
              <th class="text-left">금액</th>
              <th class="text-left">층수</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="item in homeTables" :key="item.name">
              <td>2020년 10월 28일</td>
              <td>59.9m² (18평)</td>
              <td>1억 6,500만원</td>
              <td>16층</td>
            </tr>
          </tbody>
        </v-table>
        <v-card-actions>
          <v-btn color="primary" block @click="dialog = false">닫기</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </VCard>
</template>

<script>
import axios from 'axios'
import http from '@/api/http'

export default {
  name: 'KakaoMap',
  data() {
    return {
      dialog: false,
      markerPositions1: [
        [33.452278, 126.567803],
        [33.452671, 126.574792],
        [33.451744, 126.572441],
      ],
      markerPositions2: [
        [37.499590490909185, 127.0263723554437],
        [37.499427948430814, 127.02794423197847],
        [37.498553760499505, 127.02882598822454],
        [37.497625593121384, 127.02935713582038],
        [37.49629291770947, 127.02587362608637],
        [37.49754540521486, 127.02546694890695],
        [37.49646391248451, 127.02675574250912],
      ],
      markerPositions3: [],
      headers: [
        {
          text: '아파트 이름',
          value: 'aptName',
        },
        { text: '아파트 코드', value: 'aptCode' },
      ],
      homeTables: [
        {
          aptCode: 15,
          buildYear: 2004,
          dongCode: '1111018300',
          dongName: '평창동',
          img: null,
          jibun: '179-5',
          lat: '37.6069685',
          lng: '126.9675741',
          aptName: '에지앙빌',
        },
        {
          aptCode: 16,
          buildYear: 2001,
          dongCode: '1111018300',
          dongName: '평창동',
          img: null,
          jibun: '72',
          lat: '37.6091837',
          lng: '126.9756868',
          aptName: '롯데낙천대',
        },
        {
          aptCode: 17,
          buildYear: 1998,
          dongCode: '1111018300',
          dongName: '평창동',
          img: null,
          jibun: '596',
          lat: '37.6109493',
          lng: '126.9786561',
          aptName: '삼성',
        },
        {
          aptCode: 18,
          buildYear: 2004,
          dongCode: '1111018300',
          dongName: '평창동',
          img: null,
          jibun: '45',
          lat: '37.6080961',
          lng: '126.9737175',
          aptName: '벽산블루밍평창힐스',
        },
        {
          aptCode: 32,
          buildYear: 2009,
          dongCode: '1111018300',
          dongName: '평창동',
          img: null,
          jibun: '108',
          lat: '37.6098407',
          lng: '126.9774707',
          aptName: '롯데캐슬 로잔',
        },
        {
          aptCode: 33,
          buildYear: 1997,
          dongCode: '1111018300',
          dongName: '평창동',
          img: null,
          jibun: '595',
          lat: '37.6046031',
          lng: '126.9637494',
          aptName: '갑을',
        },
        {
          aptCode: 57,
          buildYear: 1996,
          dongCode: '1111018300',
          dongName: '평창동',
          img: null,
          jibun: '145-5',
          lat: '37.60901',
          lng: '126.9728101',
          aptName: '일성빌라트',
        },
        {
          aptCode: 81,
          buildYear: 2005,
          dongCode: '1111018300',
          dongName: '평창동',
          img: null,
          jibun: '33-1',
          lat: '37.6075659',
          lng: '126.9733623',
          aptName: '형우럭스빌(33-1)',
        },
        {
          aptCode: 88,
          buildYear: 2009,
          dongCode: '1111018300',
          dongName: '평창동',
          img: null,
          jibun: '66-1',
          lat: '37.60964300000001',
          lng: '126.9758708',
          aptName: '엘리시아',
        },
      ],
      homeInfos: [], // [아파트의 이름, 아파트 코드]
      eduInfos: [], //
      transportInfos: [],
      mediInfos: [],
      foodInfos: [],
      homePositions: [], //여기에 동코드로 검색한 주택정보 불러오자
      transportPositions: [], //주차장, 주유소, 지하철역
      eduPositions: [], //유치원, 학교, 학원
      mediPositions: [], //병원, 약국
      foodPositions: [], //음식점, 카페
      markers: [],
      transportMarkers: [],
      eduMarkers: [],
      mediMarkers: [],
      foodMarkers: [],
      transportSwitch: true,
      eduSwitch: true,
      mediSwitch: true,
      foodSwitch: true,
      infowindow: null,
      lat: 37.501055511276206,
      lng: 127.03937835966009,
      dongCode: 1168010100,
      address: '서울특별시 강남구 역삼동',
      homeInfoAptName: '안녕',
      homeInfoBuildYear: '하세',
      homeInfoJibun: '요오',
      imgSrc: 'https://i.ibb.co/m0VTfjT/image.jpg',
    }
  },
  mounted() {
    if (window.kakao && window.kakao.maps) {
      this.initMap()
    } else {
      const script = document.createElement('script')
      /* global kakao */
      script.onload = () => kakao.maps.load(this.initMap)
      script.src = '//dapi.kakao.com/v2/maps/sdk.js?autoload=false&appkey=915cffed372954b7b44804ed422b9cf0'
      document.head.appendChild(script)
    }
  },
  methods: {
    //아파트 리스트에서 아파트 이름을 클릭했을 때 마커 중심을 옮기는 기능
    setCenterInMap(lat, lng) {
      var moveLatLon = new kakao.maps.LatLng(lat, lng)
      // 지도 중심을 이동 시킵니다
      this.map.panTo(moveLatLon)
      //현재 위도 경도 갱신
      this.updateLatLng()
      this.dialog = true
    },

    //학군 버튼 눌렀을 때마다 마커 표시하고 끄는 기능
    foodToggle() {
      this.foodSwitch = !this.foodSwitch
      console.log(this.foodSwitch)
      if (this.foodSwitch) {
        this.displayFoodMarker([])
      } else {
        this.getFoodPositions()
      }
    },

    //동코드 기준으로 [학군] 리스트 불러오기
    getFoodPositions() {
      //37.501055511276206, 127.03937835966009

      axios
        .get(
          `https://dapi.kakao.com/v2/local/search/category.json?sort=distance&x=${this.lng}&y=${this.lat}&radius=1000&category_group_code=FD6`, //x=37.501055511276206&y=127.03937835966009&radius=2000&
          { headers: { Authorization: `KakaoAK c46f27eaebc3444220e50470b5d06e52` } },
        )
        .then(result => {
          console.log('교통 정보 출력')
          console.log(result)
          console.log(result.data)
          console.log(result.data.documents)
          console.log(result.data.documents[1])
          console.log(result.data.documents[1].x)
          console.log(result.data.documents[1].y)

          var list = []
          var infoList = []
          for (var i = 0; i < result.data.documents.length; i++) {
            list.push([result.data.documents[i].y, result.data.documents[i].x])
            // console.log(result.data.documents[i].x);
            // console.log(result.data.documents[i].y);
            infoList.push(result.data.documents[i].place_name)
          }
          console.log(list)
          this.displayFoodMarker(list)
          this.foodInfos = infoList
        })
        .catch(({ response }) => {
          alert(response.data)
          console.log('오류 떴다잉..')
          console.log(response)
        })
    },

    displayFoodMarker(markerPositions) {
      console.log('displayFoodMarker() 호출')
      console.log(markerPositions)
      if (this.foodMarkers.length > 0) {
        this.foodMarkers.forEach(marker => marker.setMap(null))
      }

      const positions = markerPositions.map(position => new kakao.maps.LatLng(...position))

      console.log('.......positions ')
      console.log(positions) ///////////////////////////////////////////////////////////////////////////////////////////////
      if (markerPositions.length > 0) {
        console.log(markerPositions[0])
        console.log(markerPositions[0][0])
        console.log('positions의 길이 : ', positions.length)
      }

      if (positions.length > 0) {
        console.log('마커 출력해보기 : ', this.foodMarkers)
        console.log(positions)
        console.log(positions[0])

        var imageSrc = 'src/views/user-interface/map/images/icon-food.png' // 마커이미지의 주소입니다

        var imageSize = new kakao.maps.Size(33, 37), // 마커이미지의 크기입니다
          imageOption = { offset: new kakao.maps.Point(0, 0) } // 마커이미지의 옵션입니다. 마커의 좌표와 일치시킬 이미지 안에서의 좌표를 설정합니다.

        //마커의 이미지정보를 가지고 있는 마커이미지를 생성합니다
        var markerImage = new kakao.maps.MarkerImage(imageSrc, imageSize, imageOption)

        for (var i = 0; i < positions.length; i++) {
          let marker = new kakao.maps.Marker({
            map: this.map,
            position: positions[i],
            image: markerImage, // 마커이미지 설정
          })
          this.foodMarkers.push(marker)

          let iwContent = `<div style="padding:5px;">${this.foodInfos[i]}</div>` // 인포윈도우에 표출될 내용으로 HTML 문자열이나 document element가 가능합니다

          let infowindow = new kakao.maps.InfoWindow({
            position: positions[i],
            content: iwContent,
          })

          // 마커에 마우스오버 이벤트를 등록합니다
          kakao.maps.event.addListener(marker, 'mouseover', () => {
            // 마커에 마우스오버 이벤트가 발생하면 인포윈도우를 마커위에 표시합니다
            infowindow.open(this.map, marker)
          })

          // 마커에 마우스아웃 이벤트를 등록합니다
          kakao.maps.event.addListener(marker, 'mouseout', function () {
            // 마커에 마우스아웃 이벤트가 발생하면 인포윈도우를 제거합니다
            infowindow.close()
          })
        }
      }
    },

    //학군 버튼 눌렀을 때마다 마커 표시하고 끄는 기능
    mediToggle() {
      this.mediSwitch = !this.mediSwitch
      console.log(this.mediSwitch)
      if (this.mediSwitch) {
        this.displayMediMarker([])
      } else {
        this.getMediPositions()
      }
    },

    //동코드 기준으로 [학군] 리스트 불러오기
    getMediPositions() {
      //37.501055511276206, 127.03937835966009
      axios
        .get(
          `https://dapi.kakao.com/v2/local/search/category.json?sort=distance&x=${this.lng}&y=${this.lat}&radius=1000&category_group_code=HP8`, //x=37.501055511276206&y=127.03937835966009&radius=2000&
          { headers: { Authorization: `KakaoAK c46f27eaebc3444220e50470b5d06e52` } },
        )
        .then(result => {
          console.log('교통 정보 출력')
          console.log(result)
          console.log(result.data)
          console.log(result.data.documents)
          console.log(result.data.documents[1])
          console.log(result.data.documents[1].x)
          console.log(result.data.documents[1].y)

          var list = []
          var infoList = []
          for (var i = 0; i < result.data.documents.length; i++) {
            list.push([result.data.documents[i].y, result.data.documents[i].x])
            // console.log(result.data.documents[i].x);
            // console.log(result.data.documents[i].y);
            infoList.push(result.data.documents[i].place_name)
          }
          console.log(list)
          this.displayMediMarker(list)
          this.mediInfos = infoList
        })
        .catch(({ response }) => {
          alert(response.data)
          console.log('오류 떴다잉..')
          console.log(response)
        })
    },

    displayMediMarker(markerPositions) {
      console.log('displayMediMarker() 호출')
      console.log(markerPositions)
      if (this.mediMarkers.length > 0) {
        this.mediMarkers.forEach(marker => marker.setMap(null))
      }

      const positions = markerPositions.map(position => new kakao.maps.LatLng(...position))

      console.log('.......positions ')
      console.log(positions) ///////////////////////////////////////////////////////////////////////////////////////////////
      if (markerPositions.length > 0) {
        console.log(markerPositions[0])
        console.log(markerPositions[0][0])
        console.log('positions의 길이 : ', positions.length)
      }

      if (positions.length > 0) {
        console.log('마커 출력해보기 : ', this.mediMarkers)
        console.log(positions)
        console.log(positions[0])

        var imageSrc = 'src/views/user-interface/map/images/icon-medi.png' // 마커이미지의 주소입니다

        var imageSize = new kakao.maps.Size(33, 37), // 마커이미지의 크기입니다
          imageOption = { offset: new kakao.maps.Point(0, 0) } // 마커이미지의 옵션입니다. 마커의 좌표와 일치시킬 이미지 안에서의 좌표를 설정합니다.

        //마커의 이미지정보를 가지고 있는 마커이미지를 생성합니다
        var markerImage = new kakao.maps.MarkerImage(imageSrc, imageSize, imageOption)

        for (var i = 0; i < positions.length; i++) {
          let marker = new kakao.maps.Marker({
            map: this.map,
            position: positions[i],
            image: markerImage, // 마커이미지 설정
          })
          this.mediMarkers.push(marker)

          let iwContent = `<div style="padding:5px;">${this.mediInfos[i]}</div>` // 인포윈도우에 표출될 내용으로 HTML 문자열이나 document element가 가능합니다

          let infowindow = new kakao.maps.InfoWindow({
            position: positions[i],
            content: iwContent,
          })

          // 마커에 마우스오버 이벤트를 등록합니다
          kakao.maps.event.addListener(marker, 'mouseover', () => {
            // 마커에 마우스오버 이벤트가 발생하면 인포윈도우를 마커위에 표시합니다
            infowindow.open(this.map, marker)
          })

          // 마커에 마우스아웃 이벤트를 등록합니다
          kakao.maps.event.addListener(marker, 'mouseout', function () {
            // 마커에 마우스아웃 이벤트가 발생하면 인포윈도우를 제거합니다
            infowindow.close()
          })
        }
      }
    },

    //학군 버튼 눌렀을 때마다 마커 표시하고 끄는 기능
    transportToggle() {
      this.transportSwitch = !this.transportSwitch
      console.log(this.transportSwitch)
      if (this.transportSwitch) {
        this.displayTransportMarker([])
      } else {
        this.getTransportPositions()
      }
    },

    //동코드 기준으로 [학군] 리스트 불러오기
    getTransportPositions() {
      //37.501055511276206, 127.03937835966009
      axios
        .get(
          `https://dapi.kakao.com/v2/local/search/category.json?sort=distance&x=${this.lng}&y=${this.lat}&radius=1000&category_group_code=SW8`, //x=37.501055511276206&y=127.03937835966009&radius=2000&
          { headers: { Authorization: `KakaoAK c46f27eaebc3444220e50470b5d06e52` } },
        )
        .then(result => {
          console.log('교통 정보 출력')
          console.log(result)
          console.log(result.data)
          console.log(result.data.documents)
          console.log(result.data.documents[1])
          console.log(result.data.documents[1].x)
          console.log(result.data.documents[1].y)

          var list = []
          var infoList = []
          for (var i = 0; i < result.data.documents.length; i++) {
            list.push([result.data.documents[i].y, result.data.documents[i].x])
            // console.log(result.data.documents[i].x);
            // console.log(result.data.documents[i].y);
            infoList.push(result.data.documents[i].place_name)
          }
          console.log(list)
          this.displayTransportMarker(list)
          this.transportInfos = infoList
        })
        .catch(({ response }) => {
          alert(response.data)
          console.log('오류 떴다잉..')
          console.log(response)
        })
    },

    displayTransportMarker(markerPositions) {
      console.log('displayTransportMarker() 호출')
      console.log(markerPositions)
      if (this.transportMarkers.length > 0) {
        this.transportMarkers.forEach(marker => marker.setMap(null))
      }

      const positions = markerPositions.map(position => new kakao.maps.LatLng(...position))

      console.log('.......positions ')
      console.log(positions) ///////////////////////////////////////////////////////////////////////////////////////////////
      if (markerPositions.length > 0) {
        console.log(markerPositions[0])
        console.log(markerPositions[0][0])
        console.log('positions의 길이 : ', positions.length)
      }

      if (positions.length > 0) {
        console.log('마커 출력해보기 : ', this.transportMarkers)
        console.log(positions)
        console.log(positions[0])

        var imageSrc = 'src/views/user-interface/map/images/icon-transport.png' // 마커이미지의 주소입니다

        var imageSize = new kakao.maps.Size(40, 37), // 마커이미지의 크기입니다
          imageOption = { offset: new kakao.maps.Point(0, 0) } // 마커이미지의 옵션입니다. 마커의 좌표와 일치시킬 이미지 안에서의 좌표를 설정합니다.

        //마커의 이미지정보를 가지고 있는 마커이미지를 생성합니다
        var markerImage = new kakao.maps.MarkerImage(imageSrc, imageSize, imageOption)

        for (var i = 0; i < positions.length; i++) {
          let marker = new kakao.maps.Marker({
            map: this.map,
            position: positions[i],
            image: markerImage, // 마커이미지 설정
          })
          this.transportMarkers.push(marker)

          let iwContent = `<div style="padding:5px;">${this.transportInfos[i]}</div>` // 인포윈도우에 표출될 내용으로 HTML 문자열이나 document element가 가능합니다

          let infowindow = new kakao.maps.InfoWindow({
            position: positions[i],
            content: iwContent,
          })

          // 마커에 마우스오버 이벤트를 등록합니다
          kakao.maps.event.addListener(marker, 'mouseover', () => {
            // 마커에 마우스오버 이벤트가 발생하면 인포윈도우를 마커위에 표시합니다
            infowindow.open(this.map, marker)
          })

          // 마커에 마우스아웃 이벤트를 등록합니다
          kakao.maps.event.addListener(marker, 'mouseout', function () {
            // 마커에 마우스아웃 이벤트가 발생하면 인포윈도우를 제거합니다
            infowindow.close()
          })
        }
      }
    },

    //학군 버튼 눌렀을 때마다 마커 표시하고 끄는 기능
    eduToggle() {
      this.eduSwitch = !this.eduSwitch
      console.log(this.eduSwitch)
      if (this.eduSwitch) {
        this.displayEduMarker([])
      } else {
        this.getEduPositions()
      }
    },

    //동코드 기준으로 [학군] 리스트 불러오기
    getEduPositions() {
      //37.501055511276206, 127.03937835966009
      axios
        .get(
          `https://dapi.kakao.com/v2/local/search/category.json?sort=distance&x=${this.lng}&y=${this.lat}&radius=1000&category_group_code=SC4`, //x=37.501055511276206&y=127.03937835966009&radius=2000&
          { headers: { Authorization: `KakaoAK c46f27eaebc3444220e50470b5d06e52` } },
        )
        .then(result => {
          console.log('학군 정보 출력')
          console.log(result)
          console.log(result.data)
          console.log(result.data.documents)
          console.log(result.data.documents[1])
          console.log(result.data.documents[1].x)
          console.log(result.data.documents[1].y)

          var list = []
          var infoList = []
          for (var i = 0; i < result.data.documents.length; i++) {
            list.push([result.data.documents[i].y, result.data.documents[i].x])
            // console.log(result.data.documents[i].x);
            // console.log(result.data.documents[i].y);
            infoList.push(result.data.documents[i].place_name)
          }
          console.log(list)
          this.eduInfos = infoList
          this.displayEduMarker(list)
        })
        .catch(({ response }) => {
          alert(response.data)
          console.log('오류 떴다잉..')
          console.log(response)
        })
    },

    displayEduMarker(markerPositions) {
      console.log('displayEduMarker() 호출')
      console.log(markerPositions)
      if (this.eduMarkers.length > 0) {
        this.eduMarkers.forEach(marker => marker.setMap(null))
      }

      const positions = markerPositions.map(position => new kakao.maps.LatLng(...position))

      console.log('.......positions ')
      console.log(positions) ///////////////////////////////////////////////////////////////////////////////////////////////
      if (markerPositions.length > 0) {
        console.log(markerPositions[0])
        console.log(markerPositions[0][0])
        console.log('positions의 길이 : ', positions.length)
      }

      if (positions.length > 0) {
        console.log('마커 출력해보기 : ', this.eduMarkers)
        console.log(positions)
        console.log(positions[0])

        var imageSrc = 'src/views/user-interface/map/images/icon-edu.png' // 마커이미지의 주소입니다

        var imageSize = new kakao.maps.Size(33, 37), // 마커이미지의 크기입니다
          imageOption = { offset: new kakao.maps.Point(0, 0) } // 마커이미지의 옵션입니다. 마커의 좌표와 일치시킬 이미지 안에서의 좌표를 설정합니다.

        //마커의 이미지정보를 가지고 있는 마커이미지를 생성합니다
        var markerImage = new kakao.maps.MarkerImage(imageSrc, imageSize, imageOption)

        for (var i = 0; i < positions.length; i++) {
          let marker = new kakao.maps.Marker({
            map: this.map,
            position: positions[i],
            image: markerImage, // 마커이미지 설정
          })
          this.eduMarkers.push(marker)

          let iwContent = `<div style="padding:5px;">${this.eduInfos[i]}</div>` // 인포윈도우에 표출될 내용으로 HTML 문자열이나 document element가 가능합니다

          let infowindow = new kakao.maps.InfoWindow({
            position: positions[i],
            content: iwContent,
          })

          // 마커에 마우스오버 이벤트를 등록합니다
          kakao.maps.event.addListener(marker, 'mouseover', () => {
            // 마커에 마우스오버 이벤트가 발생하면 인포윈도우를 마커위에 표시합니다
            infowindow.open(this.map, marker)
          })

          // 마커에 마우스아웃 이벤트를 등록합니다
          kakao.maps.event.addListener(marker, 'mouseout', function () {
            // 마커에 마우스아웃 이벤트가 발생하면 인포윈도우를 제거합니다
            infowindow.close()
          })
        }
      }
    },

    //동코드 기준으로 [집] 리스트 불러오기
    getHomePositions() {
      console.log('getHomePositions() 호출')
      // console.log('[actions]................................getResults 조회 조건:', payload);
      http
        .get(`home/${this.dongCode}`)
        .then(({ data }) => {
          console.log('[actions]................................getHomePositions:', data)
          this.homePositions = data
          if (data.length == 0) {
            alert('해당지역엔 매물이 없어유.. T^T;;')
          }
        })
        .catch(({ response }) => {
          alert(response.data)
        })
    },

    async currentLocationSearch() {
      // console.log('!검색한 현재 주소 : ',this.address);
      // console.log('!검색한 동코드 : ',this.dongCode);
      // console.log('!현재 좌표들 : ',this.homePositions);
      await this.getResults(this.dongCode)
      await this.displayMarker(this.homePositions)
      // console.log('!검색후 좌표들 : ',this.homePositions);
    },

    //현재 위,경도를 갱신하고, 현재 위치의 동코드, 주소를 갱신
    updateLatLng() {
      var latlng = this.map.getCenter()
      this.lat = latlng.getLat()
      this.lng = latlng.getLng()

      //좌표를 이용해서 법정동을 반환받는 요청
      axios
        .get(`https://dapi.kakao.com/v2/local/geo/coord2regioncode.json?x=${this.lng}&y=${this.lat}`, {
          headers: { Authorization: `KakaoAK c46f27eaebc3444220e50470b5d06e52` },
        })
        .then(result => {
          //동코드 갱신
          // console.log(result);
          // console.log(result.data);
          // console.log(result.data.documents[0].code);
          this.dongCode = result.data.documents[0].code

          //화면에 출력할 주소 갱신
          this.address = result.data.documents[0].address_name
          // console.log('1ㄴ현재 주소 : ',this.address);
          // console.log('1ㄴ동코드 : ',this.dongCode);
        })
        .catch(({ response }) => {
          alert(response.data)
          console.log('오류 떴다잉..')
          console.log(response)
        })

      // console.log('현재 주소 : ',this.address);
      // console.log('동코드 : ',this.dongCode);
    },

    initMap() {
      const container = document.getElementById('map')
      console.log('지도 불러온다잉~~')
      const options = {
        center: new kakao.maps.LatLng(37.50123567372594, 127.03949437304006),
        level: 5,
      }

      //지도 객체를 등록합니다.
      //지도 객체는 반응형 관리 대상이 아니므로 initMap에서 선언합니다.
      this.map = new kakao.maps.Map(container, options)
    },

    changeSize(size) {
      const container = document.getElementById('map')
      container.style.width = `${size}px`
      container.style.height = `${size}px`
      this.map.relayout()
    },

    getResults(dongCode) {
      console.log('getResults() 호출..........$%^&$%^&..............', dongCode)
      // console.log('[actions]................................getResults 조회 조건:', payload);
      http
        .get(`home/${dongCode}`)
        .then(({ data }) => {
          this.homeTables = data
          // console.log('[actions]................................getResults:', data);
          if (data.length > 0) {
            var arr2 = new Array() //2차원 배열 선언
            var element = null //2차원 배열 한칸마다 넣을 1차원 배열
            var arrInfos = new Array()
            var info = null

            for (var i = 0; i < data.length; i++) {
              // console.log(data[i].lat);
              // console.log(data[i].lng);
              element = new Array() //배열선언
              element.push(data[i].lat)
              element.push(data[i].lng)
              arr2.push(element) //2차원 배열에 넣기

              info = new Array()
              info.push(data[i].aptName)
              info.push(data[i].aptCode)
              arrInfos.push(info)

              // console.log(data[i]);
              // console.log(data[i].aptName);
              // console.log(data[i].aptCode);
            }
            // console.log(arr2);
            this.homePositions = arr2
            this.homeInfos = arrInfos
          } else {
            alert('해당지역엔 매물이 없어유.. T^T;;')
            this.homePositions = arr2
            this.homeInfos = arrInfos
          }
        })
        .catch(({ response }) => {
          alert(response.data)
        })
    },

    displayMarker(markerPositions) {
      if (this.markers.length > 0) {
        this.markers.forEach(marker => marker.setMap(null))
      }

      const positions = markerPositions.map(position => new kakao.maps.LatLng(...position))

      if (markerPositions.length > 0) {
        console.log(markerPositions[0])
        console.log(markerPositions[0][0])
        console.log('positions의 길이 : ', positions.length)
      }

      if (positions.length > 0) {
        console.log('마커 출력해보기 : ', this.markers)
        console.log(positions)
        console.log(positions[0])

        var imageSrc = 'src/views/user-interface/map/images/icon-home.png', // 마커이미지의 주소입니다
          imageSize = new kakao.maps.Size(30, 33), // 마커이미지의 크기입니다
          imageOption = { offset: new kakao.maps.Point(0, 0) } // 마커이미지의 옵션입니다. 마커의 좌표와 일치시킬 이미지 안에서의 좌표를 설정합니다.

        //마커의 이미지정보를 가지고 있는 마커이미지를 생성합니다
        var markerImage = new kakao.maps.MarkerImage(imageSrc, imageSize, imageOption)

        this.markers = []

        for (var i = 0; i < positions.length; i++) {
          const marker = new kakao.maps.Marker({
            map: this.map,
            position: positions[i],
            title: this.homeInfos[i][1],
            image: markerImage, // 마커이미지 설정
          })
          this.markers.push(marker)

          //윈도우 만들어주기
          //마커에 커서가 오버됐을 때 마커 위에 표시할 인포윈도우를 생성합니다
          // let iwContent = '<div style="padding:5px;">Hello World!</div>'; // 인포윈도우에 표출될 내용으로 HTML 문자열이나 document element가 가능합니다

          // // 인포윈도우를 생성합니다
          // let infowindow = new kakao.maps.InfoWindow({
          //     positions : positions[i],
          //     content : iwContent
          // });

          let iwContent = `<div style="padding:5px;">${this.homeInfos[i][0]}</div>` // ${this.homeInfos[i][0]}

          //var latlng = new kakao.maps.LatLng(positions[i][0], 127);

          // 인포윈도우를 생성합니다
          let infowindow = new kakao.maps.InfoWindow({
            position: positions[i],
            content: iwContent,
          })

          // 마커에 마우스오버 이벤트를 등록합니다
          kakao.maps.event.addListener(marker, 'mouseover', () => {
            // 마커에 마우스오버 이벤트가 발생하면 인포윈도우를 마커위에 표시합니다
            infowindow.open(this.map, marker)
          })

          // 마커에 마우스아웃 이벤트를 등록합니다
          kakao.maps.event.addListener(marker, 'mouseout', function () {
            // 마커에 마우스아웃 이벤트가 발생하면 인포윈도우를 제거합니다
            infowindow.close()
            //console.log('마우스 내렸다', marker.getPosition());
          })

          // 마커에 마우스클릭 이벤트를 등록합니다
          kakao.maps.event.addListener(marker, 'click', function () {
            var dongCode = marker.getTitle()
            console.log('해당 집의 집코드 : ', dongCode)
            //집 정보 불러와야함
            http
              .get(`home/apt/${dongCode}`)
              .then(({ data }) => {
                console.log(data)
                this.homeInfoAptName = data.aptName
                this.homeInfoJibun = data.jibun
                this.homeInfoBuildYear = data.buildYear
                console.log(this.homeInfoAptName)
                console.log(this.homeInfoJibun)
                console.log(this.homeInfoBuildYear)
                //아파트 이미지 불러오기
                http
                  .get(`home/aptimage/${data.aptName}`)
                  .then(({ data }) => {
                    console.log(data)
                    console.log(data.items[0])
                    console.log(data.items[0].thumbnail)
                    this.imgSrc = data.items[0].thumbnail
                  })
                  .catch(({ response }) => {
                    alert(response.data)
                  })
              })
              .catch(({ response }) => {
                alert(response.data)
              })
            console.log(this.homeInfo.aptName)

            //집 이미지 불러오기 CORS 떠서 안옴..;
            // axios.get(
            //           `https://openapi.naver.com/v1/search/image?query=${data.aptName}&display=10&start=1&sort=sim`, //x=37.501055511276206&y=127.03937835966009&radius=2000&
            //           { headers:{ 'X-Naver-Client-Id':'JodwiVXhYmXk3rD8cc5O',
            //                       'X-Naver-Client-Secret': 'aoJ7ssMour'
            //                     }
            //           }
            //       ).then((result) => {
            //         console.log('이미지 결과 출력');
            //         console.log(result);
            //       }).catch(({ response }) => {
            //           alert(response);
            //           console.log('오류 떴다잉..');
            //           console.log(response);
            //       });

            //집 거래내역 불러와야함
          })
        }
        // this.markers = positions.map(
        //   (position) =>
        //     new kakao.maps.Marker({
        //       map: this.map,
        //       position,
        //     })
        // );
        // positions.map(
        //   (position) =>
        //     console.log(position)
        // );

        console.log('마커 출력해보기2 : ', this.markers)

        // const bounds = positions.reduce(
        //   (bounds, latlng) => bounds.extend(latlng),
        //   new kakao.maps.LatLngBounds()
        // );

        // this.map.setBounds(bounds);

        // // 마커에 커서가 오버됐을 때 마커 위에 표시할 인포윈도우를 생성합니다
        // var mapContainer = document.getElementById('map'), // 지도를 표시할 div
        // mapOption = {
        //     center: new kakao.maps.LatLng(33.450701, 126.570667), // 지도의 중심좌표
        //     level: 3 // 지도의 확대 레벨
        // };

        // var map = new kakao.maps.Map(mapContainer, mapOption); // 지도를 생성합니다

        // //마커를 표시할 위치입니다
        // var position =  new kakao.maps.LatLng(33.450701, 126.570667);

        // // 마커를 생성합니다
        // var marker = new kakao.maps.Marker({
        //   position: position
        // });

        // // 마커를 지도에 표시합니다.
        // marker.setMap(this.map);

        // // 마커에 커서가 오버됐을 때 마커 위에 표시할 인포윈도우를 생성합니다
        // var iwContent = '<div style="padding:5px;">Hello World!</div>'; // 인포윈도우에 표출될 내용으로 HTML 문자열이나 document element가 가능합니다

        // // 인포윈도우를 생성합니다
        // var infowindow = new kakao.maps.InfoWindow({
        //     content : iwContent
        // });

        // // 마커에 마우스오버 이벤트를 등록합니다
        // kakao.maps.event.addListener(marker, 'mouseover', function() {
        //   // 마커에 마우스오버 이벤트가 발생하면 인포윈도우를 마커위에 표시합니다
        //     infowindow.open(this.map, marker);
        //     console.log('마우스 올라갔다');
        // });

        // // 마커에 마우스아웃 이벤트를 등록합니다
        // kakao.maps.event.addListener(marker, 'mouseout', function() {
        //     // 마커에 마우스아웃 이벤트가 발생하면 인포윈도우를 제거합니다
        //     infowindow.close();
        //     console.log('마우스 내렸다');
        // });
      }
    },
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#map {
  width: 100%;
  height: 550px;
}

.btn-group {
  margin-bottom: 10px;
}

button {
  margin: 0 3px;
}

/* div {
    width: 100%;
    height: 100px;
} */
div.left {
  width: 70%;
  float: left;
  box-sizing: border-box;
}
div.right {
  width: 30%;
  float: right;
  box-sizing: border-box;
  padding: 5px 15px;
}

.w-btn {
  position: relative;
  border: none;
  display: inline-block;
  padding: 5px 15px;
  border-radius: 5px;
  font-family: 'paybooc-Light', sans-serif;
  box-shadow: 0 15px 35px rgba(0, 0, 0, 0.2);
  text-decoration: none;
  font-weight: 600;
  transition: 0.25s;
  cursor: pointer;
}

.w-btn-search {
  background-color: #ffffff;
  color: #013825;
}

.w-btn-green {
  background-color: #41b991;
  color: #d7fff1;
}

.w-btn-selected {
  background-color: #30463f;
  color: #d7fff1;
  letter-spacing: 2px;
  transform: scale(0.8);
}

.w-btn-delete {
  background-color: #375249;
  color: #d7fff1;
}
/* .w-btn {
    letter-spacing: 2px;
    transform: scale(0.7);
    cursor: pointer;
} */

.modal {
  width: 50%;
  margin-left: auto;
  margin-right: auto;
  padding: 60px;
  padding-bottom: 0px;
}

.homeDetailIcon {
  width: 5%;
  height: 5%;
}
</style>
