# CryptoVoxels Scripting
## 零、目的：使地块变得可交互
### 交互方式：
* 单人交互（默认）
* 多人交互（地块Owner配置后，通过Grid服务器实现）

## 一、基础知识
### 1.1 Feature object
#### Feature List
![image](https://user-images.githubusercontent.com/5465932/124850593-80fba100-dfd3-11eb-9c65-56a66037199e.png)

#### Feature Location
* Position
* Scale
* Rotation

### 1.2 Player object（当前玩家）
#### 关键属性
* player.name => 'captainbenis.eth'
* player.wallet => '0x2D891ED45C4C3EAB978513DF4B92a35Cf131d2e2'
* player.uuid => avatar uuid for this instance of the player (player may have multiple tabs open with seperate avatars)

### 1.3 Scripting Wiki
[https://wiki.cryptovoxels.com/en/Scripting/Examples](https://wiki.cryptovoxels.com/en/Scripting/Examples)

### 1.4 vox模型库(100+ vox现成可用)
[https://wiki.cryptovoxels.com/en/voxel-library](https://wiki.cryptovoxels.com/en/voxel-library)

### 1.5 CV interactive map
[https://benjythebee.github.io/CV_interactive_map/index.html](https://benjythebee.github.io/CV_interactive_map/index.html)

## 二、Demo 地址
[https://www.cryptovoxels.com/play?coords=NW@661W,144S,23.5U](https://www.cryptovoxels.com/play?coords=NW@661W,144S,23.5U)


## 三、示例清单 
1. 围着你转的狗子
1. 一直跟着你的狗子
1. 猜数字
1. Big Big Tree
1. 一扇门
1. 井字棋
1. 陷阱

## 四、示例详情

### 4.1 围着你转的狗子
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
![image](https://user-images.githubusercontent.com/5465932/124737890-fa9b7c80-df4a-11eb-857f-5c02e6a77290.png)
![image](https://user-images.githubusercontent.com/5465932/124741044-e86f0d80-df4d-11eb-8a6f-ea51e23598dc.png)


#### 3. 其他配置
![image](https://user-images.githubusercontent.com/5465932/124737954-0b4bf280-df4b-11eb-8c16-62a71bedc7d6.png)

---

### 4.2 一直跟着你的狗子

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
![image](https://user-images.githubusercontent.com/5465932/124738020-1a32a500-df4b-11eb-8361-80cddfee940c.png)
![image](https://user-images.githubusercontent.com/5465932/124745841-d17eea00-df52-11eb-8dc4-7ad6c08ceb82.png)


#### 3. 其他配置
![image](https://user-images.githubusercontent.com/5465932/124738079-2a4a8480-df4b-11eb-9350-0c524763bb8f.png)

---

### 4.3 猜数字

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
![image](https://user-images.githubusercontent.com/5465932/124738290-5a922300-df4b-11eb-8613-d0673f37f444.png)
![image](https://user-images.githubusercontent.com/5465932/124746180-29b5ec00-df53-11eb-800a-e13e36661b3c.png)


### 3. 其他配置
![image](https://user-images.githubusercontent.com/5465932/124738336-654cb800-df4b-11eb-9373-96a197021bc8.png)

---

### 4.4 Big Big Tree

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
![image](https://user-images.githubusercontent.com/5465932/124738402-77c6f180-df4b-11eb-83ae-82c48118f4fd.png)
![image](https://user-images.githubusercontent.com/5465932/124738768-d8562e80-df4b-11eb-9b89-b143fb49a391.png)


#### 3. 其他配置
![image](https://user-images.githubusercontent.com/5465932/124738444-82818680-df4b-11eb-92dd-90f7d87eb4b4.png)

---

### 4.5 一扇门

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
![image](https://user-images.githubusercontent.com/5465932/124738922-f754c080-df4b-11eb-83c0-293c913f1cc6.png)
![image](https://user-images.githubusercontent.com/5465932/124738949-00459200-df4c-11eb-93a4-193889f6bd28.png)

#### 3. 其他配置
![image](https://user-images.githubusercontent.com/5465932/124739012-105d7180-df4c-11eb-9666-26e61603a1e5.png)

---

### 4.6 井字棋

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
![image](https://user-images.githubusercontent.com/5465932/124739106-24a16e80-df4c-11eb-8d32-baeaa89fadaf.png)

#### 3. 其他配置
![image](https://user-images.githubusercontent.com/5465932/124739171-3551e480-df4c-11eb-9378-42e0617b686a.png)

---

### 4.7 陷阱

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
![image](https://user-images.githubusercontent.com/5465932/124739570-8feb4080-df4c-11eb-9d68-2ce0114b391e.png)

#### 3. 其他配置
![image](https://user-images.githubusercontent.com/5465932/124739611-9bd70280-df4c-11eb-9cb9-d7172e0ed22a.png)

---
```
