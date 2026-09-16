<!DOCTYPE html>
<html>
<head>
<title>Infinite XOX Arena</title>

<style>

*{
box-sizing:border-box;
font-family:Inter,Arial,sans-serif;
}


body{

margin:0;
height:100vh;

background:
radial-gradient(circle at top,#6366f1,#111827);

display:flex;
justify-content:center;
align-items:center;

overflow:hidden;

color:white;

}


.container{

width:100%;
height:100%;

display:flex;
flex-direction:column;

justify-content:center;
align-items:center;

}



h1{

font-size:38px;
margin:5px;

}



.controls{

display:flex;
gap:15px;
margin:10px;

}


.controls button{

padding:12px 25px;

border:none;

border-radius:30px;

font-size:18px;

cursor:pointer;

background:white;

}


.active{

background:#22c55e!important;

color:white;

}



.status{

font-size:22px;

margin:10px;

}



.board-wrapper{

width:80vw;

height:65vh;

display:flex;

justify-content:center;

align-items:center;


overflow:auto;


background:#ffffff15;

backdrop-filter:blur(20px);

border-radius:25px;

padding:20px;

}



.board{

display:grid;

grid-template-columns:repeat(20,60px);

grid-template-rows:repeat(20,60px);


}



.cell{

width:60px;

height:60px;

border:1px solid #ffffff30;


display:flex;

justify-content:center;

align-items:center;


font-size:36px;

cursor:pointer;


transition:.2s;

}



.cell:hover{

background:#ffffff25;

transform:scale(1.1);

}



.x{

color:#38bdf8;

animation:pop .25s;

}



.o{

color:#fb7185;

animation:pop .25s;

}



.win{

background:#22c55e;

animation:pulse .5s infinite alternate;

}




@keyframes pop{

from{

transform:scale(0);

}

to{

transform:scale(1);

}

}


@keyframes pulse{

from{

box-shadow:0 0 5px white;

}

to{

box-shadow:0 0 30px white;

}

}


.reset{

margin-top:15px;

}


</style>

</head>


<body>


<div class="container">


<h1>⚡ Infinite XOX Arena</h1>


<div class="controls">

<button id="chooseX"
onclick="choose('X')">
Choose X
</button>


<button id="chooseO"
onclick="choose('O')">
Choose O
</button>

</div>



<div class="status" id="status">
Player X Turn
</div>



<div class="board-wrapper">

<div class="board" id="board"></div>

</div>



<button class="reset" onclick="restart()">
Restart
</button>


</div>






<script>


const SIZE=20;


let board=[];

let player="X";

let current="X";



const boardEl=
document.getElementById("board");


const statusEl=
document.getElementById("status");




function sound(freq){

let ctx=
new AudioContext();

let osc=
ctx.createOscillator();

let gain=
ctx.createGain();


osc.frequency.value=freq;

osc.connect(gain);

gain.connect(ctx.destination);


osc.start();


gain.gain.exponentialRampToValueAtTime(
0.001,
ctx.currentTime+.2
);


osc.stop(
ctx.currentTime+.2
);


}





function choose(value){

player=value;

current=value;


document
.querySelectorAll(".controls button")
.forEach(b=>b.classList.remove("active"));


document
.getElementById(
"choose"+value
)
.classList.add("active");


statusEl.innerHTML=
"Playing as "+value;

}





function createBoard(){


board=[];

boardEl.innerHTML="";


for(let y=0;y<SIZE;y++){


board[y]=[];


for(let x=0;x<SIZE;x++){


board[y][x]="";


let cell=
document.createElement("div");


cell.className="cell";


cell.dataset.x=x;

cell.dataset.y=y;



cell.onclick=()=>move(x,y,cell);



boardEl.appendChild(cell);


}

}


}





function move(x,y,cell){


/*

Click empty box:
place symbol

Click existing box:
change symbol

*/


if(board[y][x]===""){

board[y][x]=player;

}

else{

board[y][x]=
board[y][x]==="X"?
"O":"X";

}



cell.innerHTML=
board[y][x];



cell.className="cell";


cell.classList.add(
board[y][x]=="X"?"x":"o"
);



sound(
board[y][x]=="X"?500:700
);



let result=
checkWin(x,y);



if(result){

result.forEach(p=>{

let c=document.querySelector(
`[data-x="${p.x}"][data-y="${p.y}"]`
);


c.classList.add("win");


});


statusEl.innerHTML=
"🎉 "+board[y][x]+" Wins!";


sound(1000);

return;

}


statusEl.innerHTML=
"Your turn: "+player;



}





function checkWin(x,y){


let symbol=
board[y][x];


let directions=[

[1,0],

[0,1],

[1,1],

[1,-1]

];



for(let d of directions){


let line=[
{x,y}
];


for(let s of [1,-1]){


let nx=x;

let ny=y;


while(true){


nx+=d[0]*s;

ny+=d[1]*s;



if(

nx<0||

ny<0||

nx>=SIZE||

ny>=SIZE||

board[ny][nx]!=symbol

)

break;



line.push({
x:nx,
y:ny
});


}



}


if(line.length>=5)
return line;



}


return null;


}





function restart(){

createBoard();

statusEl.innerHTML=
"Player "+player+" Turn";


}



choose("X");

createBoard();



</script>


</body>

</html>
