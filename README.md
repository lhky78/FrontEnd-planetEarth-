<<<<<<< HEAD
# 프론트 엔드
참고) 웹 사이트의 백엔드 코드는 [여기를 클릭하면](https://github.com/planetEarth1886/backend) 확인 가능합니다.
##실행 화면
![](src/img/main.png)

Korea marker click 시 화면 
![](src/img/9s.gif)


## 프로그램 개발 시 사용한 프레임워크 버전 설명
1) node.js 버전 : v13.12.0
2) react.js 버전 : 16.13.1
3) npm 버전  : 6.14.4

### 프론트엔드 패키지 다운로드 시 주의사항
* master 브랜치에서 npm i로 노드 모듈을 새로 다운받습니다
	* package-lock.json 파일 다운로드 시 참고 자료: [package-lock.josn에 관하여](https://medium.com/@han7096/package-lock-json-%EC%97%90-%EA%B4%80%ED%95%98%EC%97%AC-5652f90b734c)

* dependencies 설치 (ex:three.js,emoji-flag,etc...)
    * package-lock.json 의 역할: 백엔드 작동을 위해 필요한 모듈 리스트로 `npm install (모듈이름)` 를 터미널해서 작동하면 원하는 모듈들을 로컬 컴퓨터에 설치 가능합니다. 

##local 환경에서 script run
* `npm start` (localhost will run at port 3000)  

#server build
* `yarn build` 
* `npx serve -s build` (localhost will run at port 5000)  

## Development goal
A website that crawls the world's hottest news in real time, translates it into Korean, and provides it.
: 세계 실시간 뉴스를 한국어로 번역하여 제공해주는 목적입니다 :)

### 

## 프론트엔드 코드 설명 

1. <b>index.js</b> : 여러 react component들을 main 페이지에 모아서 실행시켜주 가장 핵심적인 javascript 파일

2. <b>markers.js</b> : 각 나라별 id,name,국가 코드,marker 색상,각 나라의 수도 위치 등 나라별로 표시되는 marker의 정보를 담고 있는 형식으로 저장하는 javascript 파일

3. <b>Posts.js</b> : 각 나라별 국기에 대한 정보와 백엔드로부터 top 10 keyword 정보를 받아오는 코드를 포함하고 있다.실시간으로 (사용자가 click한 국가) post 해주는 javascript 파일 

## 1. index.js 
### Globe(3차원 지구)의 모습을 표현한 ReactGlobe 입니다.
ReactGlobe는 Three.js 등의 프레임워크를 활용하여 구의 형태를 구현한 api이고, 이에 평면 세계지도를 입혀서 3D 지구의 형태를 구현하였습니다.
```js
<ReactGlobe
    markers={markers}
    markerOptions={{
      getTooltipContent,
      activeScale: 1.1,
      enableGlow: false,
      enableTooltip: true,
      enterAnimationDuration: 3000,
      enterEasingFunction: ['Bounce', 'InOut'],
      exitAnimationDuration: 3000,
      exitEasingFunction: ['Cubic', 'Out'],
      radiusScaleRange: [0.01, 0.01],
    }}
    onClickMarker={onClickMarker}
    onDefocus={onDefocus}
/>
``` 
사용자가 각 나라별 marker에 마우스를 가져가면 해당 나라의 정보가 뜨는 코드입니다.
```js
function getTooltipContent(marker) {
  clicked = `${marker.code}`
  return `${marker.city}`;
}
```  
사용자가 원하는 나라의 marker를 클릭했을때 나타나는 event 입니다. 사용자가 원하는 나라(click한 나라)의 Top 10 keyword를 불러오는 event를 보여줍니다.
```js
function onClickMarker(marker, markerObject, event) {
setEvent({
  type: "CLICK",
  marker,
  markerObjectID: markerObject.uuid,
  pointerEventPosition: { x: event.clientX, y: event.clientY }
});
setDetails(getTooltipContent(marker));
  }
```
Posts(국가별 실시간 10개의 keyword를 보여주는 역할),HelloCountry(국가 코드 가져오는 역할)component들을 불러오는 코드입니다.
```js
{details && (
<div
  style={{
    position: "absolute",
    fontSize: 18,
    top: 20,
    right: 50,
    padding: 10,
  }}
>
  {/* 사용자가 클릭한 국가 코드 가져오는 함수들 */}
  <HelloCountry name={clicked} color="red" />  

  {/* Posts.js 내용 표시 */}
  <Posts />
</div>
)
}
```

## 2. markers.js 
### 각 나라별의 정보를 표시해주는 marker 입니다.

Example) Korea
```js
  {
    id: 28, //국가 id
    city: 'Korea', //국가 이름 
    code: 'KR', //국가 코드 
    color: '#31A567', //marker 색상 
    coordinates: [37.34, 126.5841], //국가의 수도 위도&경도 
    value: 10,
  },
```    
## 3. Posts.js 
### 사용자가 click한 국가의 Top 10 keyword를 post해주는 역할을 합니다.

keyword의 title과 content를 보여줍니다.
```js
const keywordstitleList = keywords.map((keyword, index) => (
    <div key={keyword.title}>
        {/* {keyword.title} */}
        <a href={googleURL + keyword.title} target="_blank">{keyword.title}</a>
    </div>
));
const keywordscontentList = keywords.map((keyword, index) => (
    <div key={keyword.content}>
        {keyword.content}
    </div>
));
```     
keyword를 보여주는 디자인에 대한 코드입니다.
```js
<CardStack
    height={650}
    width={400}
    //background='#f8f8f8'
    hoverOffset={100}
    borderRadius={15}>
    <Card background='#540b0e' borderRadius={15}>
        <center><h3><font color="white">
            {datesList}</font></h3></center>
    </Card>
    <Card background='#6F2B2C' borderRadius={15}>
        <h3><ul>{keywordstitleList[0]}</ul></h3>
        <h6>{keywordscontentList[0]}</h6>
    </Card>
    ...
    </Card>
</CardStack>
```
사용자가 click한, 각 국가별 국기와 날짜를 보여주는 코드입니다. 
```js
/* 사용자가 클릭한 국가 코드 가져오는 함수들 */
var country;
function HelloCountry(props) {
    country = `${props.name}`;
    return <div style={{ country: props.name }}></div>
}
...
/* 각 나라별 국기 보여주는 함수 */
var emojiFlags = require('emoji-flags');
function showflag() {
    return " " + emojiFlags.countryCode(country).emoji + " ";
}
const { sizes, dates, keywords } = this.state;
const datesList = dates.map((date, index) => (
    <div key={date}>
        <center>
            <span>{showflag()}
                {date.date}
                {showflag()}
            </span>
        </center>
    </div>

));
```
=======
##### We belong to DSC EWHA(2019-2020)

# ReactGlobe 🌎
![](src/img/main.png)

#### 
  if click the marker of KOREA (2020.08.22 15:00) 
  ![](src/img/KR.png)
  <p align="center"><img src="src/img/play.gif"></p>


## Used
* React.js + Node.js + Google Translate API

## Development goal
A website that crawls the world's hottest news in real time,  
translates it into Korean, and provides it.


## Run script in local
* install dependencies (ex:three.js,emoji-flag,etc...)
1) npm install
2) npm start
(localhost will run at port 3000)  
    - if you want to build on server  
    2-1) yarn build  
    2-2) npx serve -s build  
    (localhost will run at port 5000)  
    
If you want contact us, email to planetearth.dsc@gmail.com or lhky0708@gmail.com 😊😊
>>>>>>> e2e9148e6f8f8ef7ee21e40f269925f249eb81b3
