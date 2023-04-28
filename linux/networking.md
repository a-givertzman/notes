# Linux Networking

## Config

- configuration file path:  
`/etc/network/interfaces`

- view all network interfaces:  
`ip a`

## netcat

- install netcat ubuntu/debian:  
`sudo apt install netcat`

### Port scanning

- in general use command:  
`netcat -z -v domain.com 1-1000`  
`netcat -z -n -v 198.51.100.0 1-1000`  
`-z` option to perform a scan instead of attempting to initiate a connection  
`-v` option to tell netcat to provide more verbose information  
`-n` option to specify that you do not need to resolve the IP address using DNS

- redirect standard error to standard output using the 2>&1 bash syntax, then filter the results with grep:  
`netcat -z -n -v 198.51.100.0 1-1000 2>&1 | grep succeeded`

### TCP Transfering

- listen on port 4444:  
`netcat -l 4444`  
`netcat domain.com 4444`

- view loggged in realtime:  
`tail -f ./esp32.log`

- send raw file:  
`netcat -l 4444 > received_file` - receaving a file  
`netcat domain.com 4444 < original_file` - sending a file

- sending zipped files/folders:  
`netcat -l 4444 | tar xzvf -` - unzip receaved files to the current dir  
`tar -czf - * | netcat domain.com 4444` - compress all files in current dir and send  

### UDP Transfering

- listening:  
`nc -l -u -k 2000`
`nc -l -u -k 2000 > ./esp32.log`

- sending general:  
`netcat -u host port`  
`netcat host startport-endport`  
`nc -u 127.0.0.1 2000`

### Simple Web Server

- listen once on port 8080  
`sudo printf 'HTTP/1.1 200 OK\n\n%s' "$(cat index.html)" | netcat -l 8080`

- listen infinit on port 8080:  
`while true; do printf 'HTTP/1.1 200 OK\n\n%s' "$(cat index.html)" | netcat -l 8888; done`

- try following html for fun:  

```JS
<!DOCTYPE html>
<html lang="en-US">
  <head>
    <meta charset="utf-8" />
    <title>Gamedev Canvas Workshop</title>
    <style>
      * {
        padding: 0;
        margin: 0;
      }
      canvas {
        background: #eee;
        display: block;
        margin: 0 auto;
      }
    </style>
  </head>
  <body>
    <canvas id="myCanvas" width="480" height="320"></canvas>

    <script>
        // JavaScript code goes here
        const canvas = document.getElementById("myCanvas");
        const ctx = canvas.getContext("2d");
        ctx.beginPath();
        ctx.rect(20, 40, 50, 50);
        ctx.fillStyle = "#FF0000";
        ctx.fill();
        ctx.closePath();

        ctx.beginPath();
        ctx.arc(240, 160, 20, 0, Math.PI * 2, false);
        ctx.fillStyle = "green";
        ctx.fill();
        ctx.closePath();

        ctx.beginPath();
        ctx.rect(160, 10, 100, 40);
        ctx.strokeStyle = "rgba(0, 0, 255, 0.5)";
        ctx.stroke();
        ctx.closePath();

        let score = 0;

        const ballRadius = 10;
        let x = canvas.width / 2;
        let y = canvas.height - 30;
        let dx = 2;
        let dy = -2;
        const paddleHeight = 10;
        const paddleWidth = 75;
        let paddleX = (canvas.width - paddleWidth) / 2;
        let rightPressed = false;
        let leftPressed = false;

        const brickRowCount = 3;
        const brickColumnCount = 5;
        const brickWidth = 75;
        const brickHeight = 20;
        const brickPadding = 10;
        const brickOffsetTop = 30;
        const brickOffsetLeft = 30;
        const bricks = [];
        for (let c = 0; c < brickColumnCount; c++) {
          bricks[c] = [];
          for (let r = 0; r < brickRowCount; r++) {
            bricks[c][r] = { x: 0, y: 0, status: 1 };
          }
        }

        document.addEventListener("keydown", keyDownHandler, false);
        document.addEventListener("keyup", keyUpHandler, false);
        function keyDownHandler(e) {
          if (e.key === "Right" || e.key === "ArrowRight") {
            rightPressed = true;
          } else if (e.key === "Left" || e.key === "ArrowLeft") {
            leftPressed = true;
          }
        }

        function keyUpHandler(e) {
          if (e.key === "Right" || e.key === "ArrowRight") {
            rightPressed = false;
          } else if (e.key === "Left" || e.key === "ArrowLeft") {
            leftPressed = false;
          }
        }

        function drawPaddle() {
            ctx.beginPath();
            ctx.rect(paddleX, canvas.height - paddleHeight, paddleWidth, paddleHeight);
            ctx.fillStyle = "#0095DD";
            ctx.fill();
            ctx.closePath();
        }

        function drawBall() {
            ctx.beginPath();
            ctx.arc(x, y, ballRadius, 0, Math.PI * 2);
            ctx.fillStyle = "#0095DD";
            ctx.fill();
            ctx.closePath();
        }
        function drawBricks() {
          for (let c = 0; c < brickColumnCount; c++) {
            for (let r = 0; r < brickRowCount; r++) {
              if (bricks[c][r].status === 1) {
                const brickX = c * (brickWidth + brickPadding) + brickOffsetLeft;
                const brickY = r * (brickHeight + brickPadding) + brickOffsetTop;
                bricks[c][r].x = brickX;
                bricks[c][r].y = brickY;
                ctx.beginPath();
                ctx.rect(brickX, brickY, brickWidth, brickHeight);
                ctx.fillStyle = "#0095DD";
                ctx.fill();
                ctx.closePath();
              }
            }
          }
        }
        function collisionDetection() {
          for (let c = 0; c < brickColumnCount; c++) {
            for (let r = 0; r < brickRowCount; r++) {
              const b = bricks[c][r];
              if (b.status === 1) {
                if (
                  x > b.x &&
                  x < b.x + brickWidth &&
                  y > b.y &&
                  y < b.y + brickHeight
                ) {
                  dy = -dy;
                  b.status = 0;
                  score++;
                  if (score === brickRowCount * brickColumnCount) {
                    alert("YOU WIN, CONGRATULATIONS!");
                    document.location.reload();
                    clearInterval(interval); // Needed for Chrome to end game
                  }                  
                }
              }
            }
          }
        }
        function drawScore() {
          ctx.font = "16px Arial";
          ctx.fillStyle = "#0095DD";
          ctx.fillText(`Score: ${score}`, 8, 20);
        }

        function draw() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            drawBall();
            drawBricks();
            
            x += dx;
            y += dy;

            if (x + dx > canvas.width - ballRadius || x + dx < ballRadius) {
              dx = -dx;
            }
            //if (y + dy > canvas.height - ballRadius || y + dy < ballRadius) {
            //  dy = -dy;
            //}
            if (y + dy < ballRadius) {
              dy = -dy;
            } else if (y + dy > canvas.height - ballRadius) {
              if (x > (paddleX - 1) && x < paddleX + paddleWidth + 1) {
                dy = -dy;
              } else {
                alert("GAME OVER");
                document.location.reload();
                clearInterval(interval);
              }
            }
                        
            if (rightPressed) {
              paddleX = Math.min(paddleX + 7, canvas.width - paddleWidth);
            } else if (leftPressed) {
              paddleX = Math.max(paddleX - 7, 0);
            }
            drawPaddle();
            drawScore();
            collisionDetection();
            
        }

        const interval = setInterval(draw, 10);

    </script>
  </body>
</html>

```
