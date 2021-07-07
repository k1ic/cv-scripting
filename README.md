# CryptoVoxels Scripting Demo
## Demo 地址
[https://www.cryptovoxels.com/play?coords=NW@661W,144S,23.5U](https://www.cryptovoxels.com/play?coords=NW@661W,144S,23.5U)

## Demo 清单 
* 盯着你的狗子
* 可抱走的狗子
* 猜数字
* Big Big Tree
* 一扇门
* 井字棋
* 陷阱

## Demo 详情

### 盯着你的狗子
#### 1. 代码
```
setInterval(() => {
	let p = parcel.getPlayers()[0]
	
	if (!p) {
		return
	}
	
	let v1 = feature.position
	let v2 = p.position
	let angle = Math.atan2(v1.z - v2.z, v1.x - v2.x)
	
	feature.rotation.y = Math.PI / -2 - angle
}, 50)
```
#### 2. 截图
#### 3. 其他配置

---

### 可抱走的狗子

#### 1. 代码
```
//Makes an object grabbable v1.1 - Fayelure

let Distance_to_player=0.5 // How far the object is relative to the player (negative will be behind)
let up_down_constant= -0.65  // Positive object will go higher, negative will go lower. eg: -0.65 places the object where your hands are, approximately. 0 will place the object right above your eyes.
let refresh_rate = 50       // Number of milliseconds. Please avoid a refresh rate < 30 as it could crash the grid.

let clone = false; // change this to false if you want to not clone the object.

//-------------- Do not touch ---------------------------

function moveObject(object,player){ // calculates new position of object

  let spherePos = object.position;
  let playerRotation = player.rotation;

  let xDelta = Distance_to_player*Math.cos(-playerRotation.y+Math.PI/2 + Math.PI);
  let yDelta = Distance_to_player*Math.tan(playerRotation.x);
  let zDelta = Distance_to_player*Math.sin(-playerRotation.y+Math.PI/2 + Math.PI);

      var positionRell = [
    player.position.x - xDelta,
    player.position.y - yDelta +(up_down_constant),
    player.position.z - zDelta
  ];

return positionRell
}

function setPosition(newobject,e){ // refreshes the positions
    newobject.set({position:moveObject(newobject,e.player)})
  if(!newobject.position || !newobject.rotation){
    parcel.removeFeature(newobject)
  }else{
  setTimeout(()=>{
    setPosition(newobject,e)
  },refresh_rate)
  }
}

feature.on('click',e=>{ // On click, create new object and start refreshing the position
let newobject = feature
if(clone){
newobject = parcel.createFeature('megavox')
newobject.set({scale:[feature.scale.x,feature.scale.y,feature.scale.z]})

newobject.set({url:feature._content.url})
newobject.position=feature.position
newobject.rotation=feature.rotation
}

setPosition(newobject,e)

})

```
#### 2. 截图

#### 3. 其他配置
---

### 猜数字

#### 1. 代码
```
var correct = [6,6,6,6] // your code

let txt = parcel.getFeatureById('correcttxt') //Sign that displays the result

/* ---------------- */

var code = [0,0,0,0]

let b1 =parcel.getFeatureById('b1')
let b2 =parcel.getFeatureById('b2')
let b3 =parcel.getFeatureById('b3')
let b4 =parcel.getFeatureById('b4')
let b5 =parcel.getFeatureById('b5')
let b6 =parcel.getFeatureById('b6')


function hasStarted(element){
  return element==0
}

b1.on('click', e=>{
  console.log('1')
  position = code.findIndex(hasStarted)
  code[position]=1

})

b2.on('click', e=>{

  position = code.findIndex(hasStarted)
  code[position]=2

})

b3.on('click', e=>{

  position = code.findIndex(hasStarted)
  code[position]=3

})

b4.on('click', e=>{

  position = code.findIndex(hasStarted)
  code[position]=4

})

b5.on('click', e=>{

  position = code.findIndex(hasStarted)
  code[position]=5

})

b6.on('click', e=>{

  position = code.findIndex(hasStarted)
  code[position]=6

})

function arraysEqual(arr1,arr2){
  i=arr1.length
  for(i;i--;){
    if(arr1[i] != arr2[i]){
      return false;
    }
  }
  return true;
}

function reset(){
  code=[0,0,0,0]
}

feature.on('click', e=>{
  if(arraysEqual(correct,code)){
    txt.set({text:"Great! Your are right!"})
  }else{
    txt.set({text:"Please try again"})
    reset()
  }

})
```
### 2. 截图
### 3. 其他配置
---

### Big Big Tree

#### 1. 代码
```
let is_grow = true

feature.on('click',e=>{
  if (is_grow) {
    if (feature.scale.x >= 3.8) {
      is_grow = false
    } else {
      feature.scale.x += 0.2;
      feature.scale.y += 0.2;
      feature.scale.z += 0.2;    
    }    
  } else {
    if (feature.scale.x <= 0.2) {
      is_grow = true
    } else {
       feature.scale.x -= 0.2;
       feature.scale.y -= 0.2;
       feature.scale.z -= 0.2;   
    }
  }
})
```
#### 2. 截图
#### 3. 其他配置
---

### 一扇门

#### 1. 代码
```
let closed = true

feature.on('click',e=>{
  if(closed){
      feature.rotation.y=0
  }else{
      feature.rotation.y=1.2
  }
  
  closed=!closed
})
```
#### 2. 截图
#### 3. 其他配置
---

### 井字棋

#### 1. 代码
```
let o = parcel.getFeatureById('nought')
let x = parcel.getFeatureById('cross')

let clones = []
let nought = true

feature.on('click', e => {
  if (clones.length === 9) {
    clones.forEach(c => c.remove())
    clones = []
  }

  let c = (nought ? o : x).clone()
  c.position.copyFrom(e.point)
  clones.push(c)

  nought = !nought
})
```
#### 2. 截图
#### 3. 其他配置
---

### 陷阱

#### 1. 代码
```
let b = parcel.getFeatureById('Trapdoor')

b.set({collidable:true})
let timeNonCollidable = 0.001 // time in seconds

feature.on('click',e=>{
    b.set({collidable:false})
    setTimeout(()=>{b.set({collidable:true})},timeNonCollidable*1000)
})
```
#### 2. 截图
#### 3. 其他配置
---