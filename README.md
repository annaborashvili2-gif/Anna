<!DOCTYPE html>
<html>

<head>
<style>
body { text-align:center; font-family:Arial; }

#game {
  width:300px;
  height:150px;
  border:2px solid black;
  position:relative;
  overflow:hidden;
  margin:auto;
  background:#f2f2f2;
}

#player {
  width:30px;
  height:30px;
  background:blue;
  position:absolute;
  bottom:0;
  left:20px;
}

.obstacle {
  width:20px;
  height:30px;
  background:red;
  position:absolute;
  bottom:0;
  right:-20px;
}

button { margin:5px; padding:8px; }

#shop { margin-top:10px; }

</style>
</head>

<body>

<h2>ქულა: <span id="score">0</span></h2>

<div id="game">
  <div id="player"></div>
</div>

<button onclick="jump()">ხტომა</button>

<div id="shop">
  <h3>SHOP 🛒</h3>
  <button onclick="buy('blue',100)">ლურჯი - 100</button>
  <button onclick="buy('green',400)">მწვანე - 400</button>
  <button onclick="buy('purple',600)">იისფერი - 600</button>
  <button onclick="buy('orange',800)">ნარინჯისფერი - 800</button>
  <button onclick="buy('black',1000)">შავი - 1000</button>
</div>

<script>

let score = 0;
let speed = 3;
let isJumping = false;
let isAlive = true;
let obstacles = [];

// jump
function jump() {
  if (isJumping || !isAlive) return;
  isJumping = true;

  let player = document.getElementById("player");
  let h = 0;

  let up = setInterval(() => {
    if (h >= 70) {
      clearInterval(up);

      let down = setInterval(() => {
        if (h <= 0) {
          clearInterval(down);
          isJumping = false;
        }
        h -= 5;
        player.style.bottom = h + "px";
      }, 20);

    } else {
      h += 5;
      player.style.bottom = h + "px";
    }
  }, 20);
}

// SPACE
document.addEventListener("keydown", e => {
  if(e.code === "Space") jump();
});

// obstacle
function createObstacle() {
  let o = document.createElement("div");
  o.classList.add("obstacle");
  document.getElementById("game").appendChild(o);
  obstacles.push(o);
}

setInterval(() => {
  if(isAlive) createObstacle();
}, 2000);

// movement + collision
setInterval(() => {
  if(!isAlive) return;

  let player = document.getElementById("player");

  obstacles.forEach((o, i) => {
    let right = parseInt(o.style.right) || -20;
    right += speed;
    o.style.right = right + "px";

    let playerTop = parseInt(window.getComputedStyle(player).getPropertyValue("bottom"));

    if(right > 250 && right < 290 && playerTop < 25){
      gameOver();
    }

    if(right > 320){
      o.remove();
      obstacles.splice(i,1);
    }
  });
}, 20);

// score
setInterval(() => {
  if(isAlive){
    score++;
    document.getElementById("score").innerText = score;

    if(score % 30 === 0){
      speed += 0.3;
    }
  }
}, 500);

// GAME OVER
function gameOver(){
  isAlive = false;

  document.getElementById("game").innerHTML += `
    <div style="
      position:absolute;
      top:0; left:0;
      width:100%; height:100%;
      background:rgba(0,0,0,0.8);
      color:white;
      display:flex;
      flex-direction:column;
      justify-content:center;
      align-items:center;
    ">
      <h2>💀 Game Over</h2>
      <p>ქულა: ${score}</p>
      <button onclick="location.reload()">Play Again 🔄</button>
    </div>
  `;
}

// SHOP
function buy(color, price){
  if(score >= price){
    score -= price;
    document.getElementById("score").innerText = score;
    document.getElementById("player").style.background = color;
  } else {
    alert("არ გაქვს საკმარისი ქულა 😄");
  }
}

</script>

</body>
</html>
