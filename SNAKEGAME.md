# SNAKE 게임 (컴퓨터 그래픽스 6조)

### 6조
#### 20171111 김종준
#### 20210817 신현진
#### 20210818 안현수

Preview
--

<img width="60%" src="https://github.com/GOSUofP5js/cgworksnake/assets/164286458/df4b8198-328d-4a45-a25d-76ec42e052e3.gif"/>

Code
--
```
let s; // 뱀 객체
let scl = 20; // 뱀과 음식의 크기
let food; // 음식 위치
let obstacles = []; // 장애물 배열
let obstacleCount = 5; // 초기 장애물 개수
let speedBoosts = []; // 속도 증가 아이템 배열
let playfield = 600; // 게임 화면 크기
let gameOverFlag = false; // 게임 종료 플래그
let startButton; // 시작 버튼
let gameStarted = false; // 게임 시작 여부 플래그
let img; // 배경 이미지
let img2; // 장애물 이미지
let img3; // 게임 오버 이미지

function preload() {
  // 이미지 로드
  img = loadImage("https://pds.joongang.co.kr/news/component/htmlphoto_mmdata/201408/14/htm_20140814134735a6003011.jpg");
  img2 = loadImage("https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTllFNw7lcVHq846FrVksA6xuMxZqe6Z01mjQ&usqp=CAU");
  img3 = loadImage("https://pds.joongang.co.kr/news/component/htmlphoto_mmdata/201408/14/htm_20140814134930a6003011.jpg");
}

function setup() {
  createCanvas(playfield, 640); // 캔버스 생성
  startScreen(); // 시작 화면 표시
}

function startScreen() {
  background(img); // 배경 이미지 설정
  textSize(80); // 텍스트 크기 설정
  textAlign(CENTER); // 텍스트 가운데 정렬
  fill(255); // 텍스트 색상 설정
  text("톰과 제리", width / 2, height / 3); // 제목 텍스트

  // 시작하기 버튼 스타일 및 이벤트 설정
  startButton = createButton('시작하기'); // 시작하기 버튼 생성
  // 버튼 스타일 설정
  startButton.position(width / 2 - 80, height / 2); // 버튼 위치
  startButton.style('background-color', 'rgba(255, 255, 255, 0.5)'); // 배경 색상 (흰색 50% 투명도)
  startButton.style('border', 'none'); // 테두리 없애기
  startButton.style('color', 'white'); // 텍스트 색상
  startButton.style('padding', '15px 32px'); // 내부 여백
  startButton.style('text-align', 'center'); // 텍스트 가운데 정렬
  startButton.style('text-decoration', 'none'); // 밑줄 없애기
  startButton.style('display', 'inline-block'); // 인라인 블록으로 표시
  startButton.style('font-size', '24px'); // 텍스트 크기
  startButton.style('margin', 'auto'); // 가운데 정렬
  startButton.style('cursor', 'pointer'); // 마우스 커서 설정
  startButton.style('border-radius', '12px'); // 테두리 모서리 반지름
  startButton.style('box-shadow', '0px 4px 8px rgba(0, 0, 0, 0.3)'); // 그림자 효과

  // 호버 효과 설정
  startButton.mouseOver(() => {
    startButton.style('background-color', 'rgba(255, 255, 255, 0.7)'); // 호버 시 밝은 색상
    startButton.style('transform', 'scale(1.1)'); // 호버 시 약간 확대
    startButton.style('color', 'rgb(255, 193, 7)'); // 호버 시 텍스트 색상 변경
  });

  // 호버 해제 시 설정 복귀
  startButton.mouseOut(() => {
    startButton.style('background-color', 'rgba(255, 255, 255, 0.5)'); // 원래 색상
    startButton.style('transform', 'scale(1)'); // 원래 크기
    startButton.style('color', 'white'); // 원래 색상
  });

  // 클릭 효과 설정
  startButton.mousePressed(() => {
    startButton.style('background-color', 'rgba(255, 255, 255, 0.3)'); // 클릭 시 색상 변경
  });
  startButton.mouseReleased(() => {
    startButton.style('background-color', 'rgba(255, 255, 255, 0.7)'); // 클릭 해제 시 호버 상태로 변경
  });

  startButton.mousePressed(startGame); // 시작 버튼 클릭 시 게임 시작
}

// 게임 시작 함수
function startGame() {
  s = new Snake(); // 뱀 객체 초기화
  frameRate(10); // 프레임 속도 설정
  pickLocation(); // 음식 위치 선택
  obstacleCount = 5; // 장애물 개수 설정
  obstacles = []; // 장애물 배열 초기화
  for (let i = 0; i < obstacleCount; i++) {
    obstacles.push(createVector(floor(random(playfield / scl)) * scl, floor(random(playfield / scl)) * scl));
  }
  startButton.remove(); // 시작 버튼 제거
  gameStarted = true; // 게임 시작 플래그 설정
}

function draw() {
  if (gameStarted) {
    drawGame(); // 게임 화면 그리기
  }
}

// 게임 화면 그리는 함수
function drawGame() {
  background(img3); // 게임 배경 이미지 설정
  scoreboard(); // 점수판 표시
  for (let i = 0; i < obstacles.length; i++) {
    image(img2, obstacles[i].x, obstacles[i].y, scl, scl); // 장애물 그리기
  }
  for (let i = 0; i < speedBoosts.length; i++) {
    fill("#0080FF");
    ellipse(speedBoosts[i].x + scl / 2, speedBoosts[i].y + scl / 2, scl, scl); // 속도 증가 아이템 그리기
  }
  if (s.eat(food)) {
    pickLocation();
    if (s.score % 5 === 0) {
      addSpeedBoost(); // 속도 증가 아이템 추가
    }
  }
  for (let i = 0; i < obstacles.length; i++) {
    let d = dist(s.x, s.y, obstacles[i].x, obstacles[i].y);
    if (d < 1) {
      gameOver(); // 게임 오버 처리
    }
  }
  for (let i = 0; i < speedBoosts.length; i++) {
    let d = dist(s.x, s.y, speedBoosts[i].x, speedBoosts[i].y);
    if (d < 1) {
      speedBoosts.splice(i, 1); // 속도 증가 아이템 먹으면 삭제
      applySpeedBoost(); // 속도 증가 효과 적용
    }
  }
  s.death();
  s.update();
  s.show();
  drawCheese(food.x + scl / 2, food.y + scl / 2, scl); // 음식 그리기
}

// 속도 증가 아이템 추가
function addSpeedBoost() {
  let cols = floor(playfield / scl);
  let rows = floor(playfield / scl);
  let newBoost;

  do {
    newBoost = createVector(floor(random(cols)) * scl, floor(random(rows)) * scl);
  } while (
    obstacles.some(obstacle => obstacle.equals(newBoost)) ||
    speedBoosts.some(boost => boost.equals(newBoost)) ||
    dist(s.x, s.y, newBoost.x, newBoost.y) < 1
  );

  speedBoosts.push(newBoost);
}

// 속도 증가 아이템 효과 적용
function applySpeedBoost() {
  s.speedBoost(); // 뱀의 속도 증가
  frameRate(15); // 5초간 속도 증가
  setTimeout(() => {
    frameRate(10); // 5초 후 속도 원래대로
  }, 5000); // 5000 밀리초 = 5초
}

// 음식 위치 선택
function pickLocation() {
  let cols = floor(playfield / scl);
  let rows = floor(playfield / scl);
  let isColliding = true;

  while (isColliding) {
    food = createVector(floor(random(cols)) * scl, floor(random(rows)) * scl);
    isColliding = false;

    for (let i = 0; i < s.tail.length; i++) {
      let pos = s.tail[i];
      let d = dist(food.x, food.y, pos.x, pos.y);
      if (d < 1) {
        isColliding = true;
        break;
      }
    }
    for (let i = 0; i < obstacles.length; i++) {
      let d = dist(food.x, food.y, obstacles[i].x, obstacles[i].y);
      if (d < 1) {
        isColliding = true;
        break;
      }
    }
    for (let i = 0; i < speedBoosts.length; i++) {
      let d = dist(food.x, food.y, speedBoosts[i].x, speedBoosts[i].y);
      if (d < 1) {
        isColliding = true;
        break;
      }
    }
  }

  animateItemAppearance(food.x + scl / 2, food.y + scl / 2, scl);
}

// 음식 등장 애니메이션
function animateItemAppearance(x, y, size) {
  let startSize = 1;
  let maxSize = size * 1.5;
  let animationDuration = 60;
  let scaleFactor = (maxSize - startSize) / animationDuration;
  let currentSize = startSize;

  for (let i = 0; i < animationDuration; i++) {
    ellipse(x, y, currentSize, currentSize);
    currentSize += scaleFactor;
  }

  drawCheese(x, y, maxSize); // 최종 크기로 그리기
}

// 음식 그리기
function drawCheese(x, y, radius) {
  fill("#F7FE2E"); // 치즈 색상
  let angle = TWO_PI / 3; // 삼각형의 각도
  let cheeseSize = radius * 0.7; // 치즈 크기
  let holeSize = cheeseSize * 0.2; // 구멍 크기
  beginShape();
  for (let a = 0; a < TWO_PI; a += angle) {
    let sx = x + cos(a) * cheeseSize;
    let sy = y + sin(a) * cheeseSize;
    vertex(sx, sy);
  }
  endShape(CLOSE);
  // 작은 구멍 추가
  for (let a = 0; a < TWO_PI; a += angle) {
    let holeX = x + cos(a) * (cheeseSize * 0.5);
    let holeY = y + sin(a) * (cheeseSize * 0.5);
    ellipse(holeX, holeY, holeSize, holeSize);
  }
}

// 게임 오버 처리
function gameOver() {
  textSize(64); // 텍스트 크기 설정
  fill(255, 0, 0); // 색상 설정
  textAlign(CENTER, CENTER); // 텍스트 정렬 설정
  text("Game Over", width / 2, height / 3); // 게임 오버 메시지
  text("re start push R", width / 2, height / 2); // 재시작 안내 메시지
  noLoop(); // 루프 중지
  gameOverFlag = true; // 게임 종료 플래그 설정
  // 재시작 시 장애물 배열 초기화 및 처음 시작할 때의 장애물 갯수로 채움
  obstacleCount = 5;
  obstacles = [];
  for (let i = 0; i < obstacleCount; i++) {
    obstacles.push(createVector(floor(random(playfield / scl)) * scl, floor(random(playfield / scl)) * scl));
  }
}

// 점수판 표시
function scoreboard() {
  fill(0); // 색상 설정
  rect(0, 600, 600, 40); // 점수판 영역
  fill(255); // 텍스트 색상 설정
  textFont("Georgia"); // 폰트 설정
  textSize(24); // 텍스트 크기 설정
  text("Score: " + s.score, 70, 625); // 현재 점수 표시
  text("Highscore: " + s.highscore, 520, 625); // 최고 점수 표시
}

// 키 입력 처리
function keyPressed() {
  if (keyCode === UP_ARROW) {
    s.dir(0, -1); // 위쪽 방향키
  } else if (keyCode === DOWN_ARROW) {
    s.dir(0, 1); // 아래쪽 방향키
  } else if (keyCode === RIGHT_ARROW) {
    s.dir(1, 0); // 오른쪽 방향키
  } else if (keyCode === LEFT_ARROW) {
    s.dir(-1, 0); // 왼쪽 방향키
  } else if (keyCode === 82 && gameOverFlag) { // R 키를 누르면 게임 다시 시작
    restartGame();
  }
}

// 게임 재시작
function restartGame() {
  s = new Snake(); // 새로운 뱀 생성
  gameOverFlag = false; // 게임 종료 플래그 초기화
  // 장애물 배열 초기화 및 처음 장애물 개수로 채움
  obstacleCount = 5;
  obstacles = [];
  for (let i = 0; i < obstacleCount; i++) {
    obstacles.push(createVector(floor(random(playfield / scl)) * scl, floor(random(playfield / scl)) * scl));
  }
  loop(); // 루프 재개
}

// 뱀 객체 생성
function Snake() {
  this.x = 0; // x 좌표 초기화
  this.y = 0; // y 좌표 초기화
  this.xspeed = 1; // x 속도 초기화
  this.yspeed = 0; // y 속도 초기화
  this.total = 0; // 뱀 길이 초기화
  this.tail = []; // 뱀 꼬리 초기화
  this.score = 1; // 현재 점수 초기화
  this.highscore = 1; // 최고 점수 초기화

  // 방향 설정 메서드
  this.dir = function (x, y) {
    this.xspeed = x;
    this.yspeed = y;
  }

  // 음식 먹기 처리
  this.eat = function (pos) {
    let d = dist(this.x, this.y, pos.x, pos.y);
    if (d < 1) {
      this.total++;
      this.score++;
      text(this.score, 70, 625);
      if (this.score > this.highscore) {
        this.highscore = this.score;
      }
      text(this.highscore, 540, 625);
      // 치즈 5개 먹을 때마다 장애물 추가
      if (this.score % 5 === 0) {
        obstacleCount++;
        obstacles.push(createVector(floor(random(playfield / scl)) * scl, floor(random(playfield / scl)) * scl));
      }
      return true;
    } else {
      return false;
    }
  }

  // 속도 증가 효과
  this.speedBoost = function () {
    // 뱀 길이 증가
    this.total++;
    this.score++;
    text(this.score, 70, 625);
    if (this.score > this.highscore) {
      this.highscore = this.score;
    }
    text(this.highscore, 540, 625);
  }

  // 죽음 처리
  this.death = function () {
    for (let i = 0; i < this.tail.length; i++) {
      let pos = this.tail[i];
      let d = dist(this.x, this.y, pos.x, pos.y);
      if (d < 1) {
        this.total = 0;
        this.score = 0;
        this.tail = [];
      }
    }
  }

  // 제리 업데이트
  this.update = function () {
    if (this.total === this.tail.length) {
      for (let i = 0; i < this.tail.length - 1; i++) {
        this.tail[i] = this.tail[i + 1];
      }
    }
    this.tail[this.total - 1] = createVector(this.x, this.y);
    this.x = this.x + this.xspeed * scl;
    this.y = this.y + this.yspeed * scl;
    this.x = constrain(this.x, 0, playfield - scl);
    this.y = constrain(this.y, 0, playfield - scl);
  }

  // 제리 그리기
  this.show = function () {
    drawjeri(this.x, this.y); // 제리 그리기
    fill("#8A4B08"); // 얼굴색
    for (let i = 0; i < this.tail.length; i++) {
      circle(this.tail[i].x + 10, this.tail[i].y + 10, scl, scl); 
    }
  }
}

// 제리 그리기
function drawjeri(x, y) {
  // 제리 머리
  fill("#8A4B08");
  ellipse(x + 10, y + 10, 20, 20);

  // 왼쪽 귀
  push();
  translate(x, y + 5);
  rotate(radians(-30));
  fill("#FF8000");
  triangle(0, 0, 10, 0, 5, -15);
  pop();

  // 오른쪽 귀
  push();
  translate(x + 12, y - 0);
  rotate(radians(30));
  fill("#FF8000");
  triangle(0, 0, 10, 0, 5, -15);
  pop();

  // 눈
  fill(0);
  ellipse(x + 5, y + 5, 5, 5);
  ellipse(x + 15, y + 5, 5, 5);

  // 코
  fill(255, 0, 0);
  triangle(x + 10, y + 10, x + 8, y + 13, x + 12, y + 13);

  // 수염
  stroke(0);
  line(x - 0, y + 5, x - 10, y + 0);
  line(x - 0, y + 10, x - 10, y + 10);
  line(x - 0, y + 15, x - 10, y + 20);
  line(x + 20, y + 5, x + 30, y + 0);
  line(x + 20, y + 10, x + 30, y + 10);
  line(x + 20, y + 15, x + 30, y + 20);
}
