# Country-Flag-Unit-1-Vocabulary
Grade 6 U1
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
    radial-gradient(circle at top,#4f46e5,#111827);
    display:flex;
    justify-content:center;
    align-items:center;
    color:white;
    overflow:hidden;
}

.container{
    text-align:center;
}

h1{
    font-size:42px;
    margin-bottom:10px;
}

.status{
    font-size:22px;
    margin:15px;
}

.board-wrapper{
    width:90vw;
    height:70vh;
    overflow:auto;
    background:#ffffff15;
    backdrop-filter:blur(20px);
    border-radius:25px;
    padding:20px;
}

.board{
    display:grid;
    grid-template-columns:repeat(30,70px);
    grid-template-rows:repeat(30,70px);
}

.cell{
    width:70px;
    height:70px;
    border:1px solid #ffffff30;
    display:flex;
    justify-content:center;
    align-items:center;
    font-size:40px;
    cursor:pointer;
    transition:.2s;
}

.cell:hover{
    background:#ffffff20;
    transform:scale(1.08);
}

.x{
    color:#38bdf8;
    animation:pop .3s;
}

.o{
    color:#f472b6;
    animation:pop .3s;
}

.win{
    background:#22c55e;
    animation:pulse .5s infinite alternate;
}


button{
    margin-top:20px;
    padding:12px 30px;
    border:none;
    border-radius:50px;
    background:white;
    color:#111;
    font-size:18px;
    cursor:pointer;
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
        box-shadow:0 0 10px white;
    }
    to{
        box-shadow:0 0 30px white;
    }
}

</style>
</head>


<body>

<div class="container">

<h1>⚡ Infinite XOX Arena</h1>

<div class="status" id="status">
Player X Turn
</div>


<div class="board-wrapper">
<div class="board" id="board"></div>
</div>


<button onclick="restart()">Restart Game</button>

</div>



<script>


const SIZE = 30;

let board=[];
let current="X";

const boardEl=document.getElementById("board");
const statusEl=document.getElementById("status");


// sound generator
function sound(freq,duration){

let ctx=new AudioContext();

let osc=ctx.createOscillator();
let gain=ctx.createGain();

osc.frequency.value=freq;
osc.type="sine";

osc.connect(gain);
gain.connect(ctx.destination);

osc.start();

gain.gain.exponentialRampToValueAtTime(
0.0001,
ctx.currentTime+duration
);

osc.stop(
ctx.currentTime+duration
);

}



// create board

function createBoard(){

board=[];

boardEl.innerHTML="";


for(let y=0;y<SIZE;y++){

board[y]=[];

for(let x=0;x<SIZE;x++){

board[y][x]="";

let cell=document.createElement("div");

cell.className="cell";

cell.dataset.x=x;
cell.dataset.y=y;


cell.onclick=()=>move(x,y,cell);


boardEl.appendChild(cell);

}

}

}



function move(x,y,cell){

if(board[y][x]!=="")
return;


board[y][x]=current;

cell.innerHTML=current;

cell.classList.add(
current==="X"?"x":"o"
);


sound(
current==="X"?500:700,
0.15
);



let result=checkWin(x,y);


if(result){

result.forEach(p=>{

let c=document.querySelector(
`[data-x="${p.x}"][data-y="${p.y}"]`
);

c.classList.add("win");

});


sound(900,0.5);

statusEl.innerHTML=
"🎉 "+current+" WINS!";

return;

}


current=current==="X"?"O":"X";

statusEl.innerHTML=
"Player "+current+" Turn";


}



// infinite style pattern check

function checkWin(x,y){


let dirs=[
[1,0],
[0,1],
[1,1],
[1,-1]
];


for(let d of dirs){

let line=[
{x,y}
];


for(let sign of [1,-1]){

let nx=x;
let ny=y;


while(true){

nx+=d[0]*sign;
ny+=d[1]*sign;


if(
nx<0||
ny<0||
nx>=SIZE||
ny>=SIZE||
board[ny][nx]!==current
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

current="X";

statusEl.innerHTML=
"Player X Turn";

createBoard();

}



createBoard();


</script>


</body>
</html>
