<!DOCTYPE html>
<html lang="ko">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link href="./index.css" rel="stylesheet" />
    <title>Color Mind</title>
</head>

<body>
    <canvas id="gameContainer" width="700px" height="960px"></canvas>
    <!-- <div class="container">
        <div class="item">1</div>
        <div class="item">2</div>
        <div class="item">3</div>
        <div class="item">4</div>      
      </div> -->
</body>

</html>
<script src="https://cdn.jsdelivr.net/npm/lodash@4.17.11/lodash.min.js"></script>
</head>
<script>
    const canvas = document.getElementById("gameContainer");
    const ctx = canvas.getContext("2d");

    const startPt = (canvas.width - 540) / 2;
    const gameWidth = 540;
    const gameHeight = 960;
    const canvasLeftMargin = 90;
    const canvasTopMargin = 90;
    const sqSize = 170;
    var stage = 0;
    const upperBox = 320;
    const gameBox = 640;

    var rightColorName = '';
    var rightColorHex = '';

    var rectArr = [];
    var clickedColorName = '';

    //하늘, 분홍, 빨강, 주황, 보라, 노랑, 초록, 파랑, 회색
    // const colorArr = [{ key: '하늘', value: '#9bb7d6' },
    // { key: '분홍', value: '#da4f70' }, { key: '빨강', value: '#c02034' }, { key: '주황', value: '#f05442' }
    //     , { key: '보라', value: '#595ca1' }, { key: '노랑', value: '#f0bf59' },
    // { key: '초록', value: '#169c78' }, { key: '파랑', value: '#00498c' }, { key: '회색', value: '#808080' }];
    var colorArr = {
        '하늘': '#9bb7d6',
        '분홍': '#da4f70', '빨강': '#c02034', '주황': '#f05442'
        , '보라': '#595ca1', '노랑': '#f0bf59',
        '초록': '#169c78', '파랑': '#00498c', '회색': '#808080'
    };

    
    //타이머
    var timeLimit = 10000;
    var addBar = 0;

    //실제 게임 영역 캔버스 그리기
    ctx.strokeRect(startPt, 0, gameWidth, gameHeight);
    //선 시작 선언
    ctx.beginPath();
    //시작점
    ctx.moveTo(startPt, upperBox);
    //이어질 부분
    ctx.lineTo(gameWidth + startPt, upperBox);
    //선 그리기
    ctx.stroke();

    const width = canvas.width;
    const height = canvas.height;

    function clickedStart() {
        //start 이미지
        //300*300

        var startBtnX = startPt + (gameWidth - 300) / 2;
        var startBtnY = gameHeight - 300;
        var startBtnW = 300;
        var startBtnH = 300;

        drawImage(startBtnX, startBtnY, startBtnW, startBtnH, '/assets/img/start-button.png')


        canvas.addEventListener('click', addStartEvent.bind(this));
        
        const self = this;
        canvas.addEventListener('click', function(){
            //self//
        });

        const _bind = addStartEvent.bind(this);
        canvas.addEventListener('click', _bind);
        canvas.removeEventListener('click', _bind);

        canvas.addEventListener('click', addStartEvent, this);
        canvas.removeEventListener('click', addStartEvent, this);

        

        // let isDown = false;
        // let state = 0;

        // window.addEventListener('mouseup', function() {
        //     isDown = false;
        //     state = 3;
        // });

        // function loop() 
        // {
        //     lateUpdate();
        // }

        // function lateUpdate() {
        //     if( state === 1 ) {
        //         state = 2;
        //     }
        //     else if( state === 3 ) {
        //         state = 0;
        //     }
        // }
    }

    function hitRectToPoint( left, right, top, bottom, x, y ) {
        return  (x > left &&
            x < right &&
            y > top &&
            y < bottom)
    }

    function addStartEvent(e) {
        var startBtnX = 300;
        var startBtnY = 820;
        var startBtnW = 300;
        var startBtnH = 160;
        
        if( hitRectToPoint( startBtnX, 
        startBtnY, 
        startBtnX + startBtnW, 
        startBtnY +  startBtnH, 
        event.x, event.y ) ) {
            start();
        }

        return 1;
        // if (
        //     event.x > startBtnX &&
        //     event.x < 560 &&
        //     event.y > startBtnY &&
        //     event.y < 970

        // ) {
        //     start();
        // }
    }

    function drawImage(x, y, width, height, src) {
        this.width = width;
        this.height = height;
        this.x = x;
        this.y = y;

        var img = new Image();

        img.onload = () => {
            ctx.drawImage(img, this.x, this.y, this.width, this.height);
        };
        img.src = src;
    }


    function drawStage() {
        stage++;
        clearRect(230, upperBox / 3, 200, 130)
        inputText(100, 'serif', 300, upperBox / 3 * 2, stage, 'black')

    }
    function start() {
        canvas.removeEventListener('click', addRestartEvent, true)
        canvas.removeEventListener('click', addStartEvent, true)
        clearCanvas();

        inputText(36, 'serif', gameWidth / 2, upperBox / 3, 'STAGE', 'black')
        stage = 0;
        drawStage();
        timeStart();
        gameStart();

    }



    function clearCanvas() {
        ctx.clearRect(startPt, 0, gameWidth, gameHeight);
    }

    //직사각형 만들기
    function createRect(x, y, width, height, color) {
        this.width = width;
        this.height = height;
        this.x = x;
        this.y = y;
        this.color = color;
        ctx.fillStyle = color;
        ctx.fillRect(this.x, this.y, this.width, this.height);
    }

    function rectArrays(x, y, colorName, rectArr) {
        this.x = x;
        this.y = y;
        this.colorName = colorName;
        this.rectArr = rectArr;

        var rect = {
            x1: this.x,
            x2: this.x + sqSize,
            y1: this.y,
            y2: this.y + sqSize,
            width: sqSize,
            height: sqSize,
            colorName: this.colorName,
            colorHex: colorArr[this.colorName]
        }
        rectArr.push(rect);
        return rectArr;

    }

    function clearRect(x, y, width, height) {
        this.width = width;
        this.height = height;
        this.x = x;
        this.y = y;
        ctx.clearRect(this.x, this.y, this.width, this.height);
    }

    function msToSec(ms) {
        var tempMs = parseFloat(ms / 1000)
        var decimal = truncate(tempMs);
        var sec = parseInt(ms / 1000)
        var result = 0;

        if (sec === 10) {
            result = 10 + ':' + '00'

        } else {
            result = '0' + sec + ':' + decimal
        }

        return result;
    }
    function truncate(ms) {
        var str = ms.toString();
        var idx = str.indexOf('.');
        return idx != -1 ? str.slice(2, idx + 3) : str;
    }


    //타이머
    function timeStart() {
        //progress bar 그리기

        //타이머 사이즈
        var widthRect = 360.0

        var barSize = 0;
        if (timeLimit === 10000) {
            timeLimit = 9999;
            var width = widthRect;

            //10ms 마다 실행
            var id = setInterval(frame, 10);

            function frame() {
                //타이머 종료
                if (timeLimit <= 0) {
                    clearInterval(id);
                    timeLimit = 10000;

                    gameOver();
                } else {
                    ctx.clearRect(startPt, 0, startPt + 50, 292)

                    //10ms마다 줄어드는 bar 양
                    width -= widthRect / 10000 * 10;

                    inputText(36, 'serif', startPt + 15, 290, msToSec(timeLimit), '#F7958E')
                    clearRect(startPt + 115, 250, widthRect, 50, '#ffffff00')
                    createRect(startPt + 130, 250, widthRect, 50, '#ddd')

                    //10초 이상 제한
                    barSize = width + addBar
                    if (barSize > 360) {
                        createRect(startPt + 130, 250, width, 50, '#F7958E')

                    } else {
                        createRect(startPt + 130, 250, barSize, 50, '#F7958E')
                    }

                    timeLimit -= 10;


                }
            }
        }
    }

    function inputText(fontSize, fontName, x, y, text, color) {
        this.fontSize = fontSize;
        this.fontName = fontName;
        this.x = x;
        this.y = y;
        this.text = text;
        this.color = color;
        ctx.fillStyle = this.color
        ctx.font = `${this.fontSize}px ${this.fontName}`;
        ctx.fillText(this.text, this.x, this.y)
    }

    //game over
    function gameOver() {
        clearCanvas()
        inputText(36, 'serif', startPt, 290, 'game over', 'black')
        inputText(36, 'serif', startPt, 500, stage, 'black')

        var startBtnX = startPt + (gameWidth - 300) / 2;
        var startBtnY = gameHeight - 300;
        var startBtnW = 200;
        var startBtnH = 200;

        drawImage(startBtnX, startBtnY, startBtnW, startBtnH, '/assets/img/restart.png')
        canvas.addEventListener('click', addRestartEvent, true)
    }

    function addRestartEvent() {

        var startBtnX = startPt + (gameWidth - 300) / 2;;
        var startBtnY = gameHeight - 300;
        var startBtnW = 200;
        var startBtnH = 200;
        if (
            event.x > startBtnX &&
            event.x < 560 &&
            event.y > startBtnY &&
            event.y < 970

        ) {
            restart();
        }

    }


    const colorName = pickRandomProperty(colorArr);
    const colorHex = colorArr[colorName]

    //game start : stage 1
    function gameStart() {
        createRect(startPt + 170, 500, 170, 170, colorHex);
        inputText(36, 'serif', startPt, 400, colorName, colorHex)
        canvas.addEventListener('click', addColorClickedEvent, true)
    }

    function addColorClickedEvent(e) {
        const pos = {
            x: e.clientX,
            y: e.clientY
        };

        if (pos.x >= 340 && pos.x <= 510 && pos.y >= 590 && pos.y <= 760) {
            if (colorMatch(colorName, colorHex) === 1) {
                plusTime();
                stageTwo();
            }
        }
    }

    function plusTime() {
        if (0 < timeLimit && timeLimit < 10800) {
            //0.8초 증가
            timeLimit += 800;
            //타이머 바 증가 : 28.8
            addBar += 360 / 10000 * 800;
        }
    }

    function minusTime() {
        if (10000 > timeLimit && timeLimit > 1000) {
            //1초 빼기
            timeLimit -= 1000;
            //타이머 바 감소
            addBar -= 360 / 10000 * 1000;
        }
    }

    //정답 확인
    function colorMatch(colorName, colorHex) {
        this.colorName = colorName;
        this.colorHex = colorHex;

        if (colorArr[this.colorName] === this.colorHex) {
            return 1;
        } else {
            return 0;
        }
    }

    function stageTwo() {

        var rectArr = [];
        //클릭이벤트 제거
        canvas.removeEventListener('click', addColorClickedEvent, true)
        // ctx.strokeRect(startPt,350, 100, 100);
        clearRect(startPt, 350, 100, 100);
        clearRect(startPt + 170, 500, 170, 170);
        rightColorName = pickRandomProperty(colorArr);
        rightColorHex = colorArr[rightColorName]

        inputText(36, 'serif', startPt, 400, rightColorName, colorArr[pickRandomProperty(colorArr)])

        let copyArr = _.cloneDeep(colorArr);
        delete copyArr[rightColorName]

        var rectPosArr = [0, 1]

        //첫번째 배열이 1인경우 정답 먼저 출력
        if (shuffle(rectPosArr)[0] === 1) {
            createRect(startPt + 70, 500, sqSize, sqSize, rightColorHex);//correct rect
            rectArrays(startPt + 70 + canvasLeftMargin, 500 + canvasTopMargin, rightColorName, rectArr)

            const tempColor = pickRandomProperty(copyArr);
            createRect(startPt + 70 + 190, 500, sqSize, sqSize, copyArr[tempColor]);//incorrect rect
            rectArrays(startPt + 190 + 70 + canvasLeftMargin, 500 + canvasTopMargin, tempColor, rectArr)

        } else {
            const tempColor = pickRandomProperty(copyArr);
            createRect(startPt + 70, 500, sqSize, sqSize, copyArr[tempColor]);//incorrect rect
            rectArrays(startPt + 70 + canvasLeftMargin, 500 + canvasTopMargin, tempColor, rectArr)

            createRect(startPt + 70 + 190, 500, sqSize, sqSize, rightColorHex);//correct rect    
            rectArrays(startPt + 190 + 70 + canvasLeftMargin, 500 + canvasTopMargin, rightColorName, rectArr)

        }

        drawStage()

        canvas.addEventListener('click', addColorClickedEventTwo, true)
    }




    function addColorClickedEventTwo(e) {

        const pos = {
            x: e.clientX,
            y: e.clientY
        };


        for (let i = 0; i < rectArr.length; i++) {
            if ((rectArr[i].x1 === startPt + 70 + canvasLeftMargin) && (rectArr[i].y1 === canvasTopMargin + 500)) {
                //1행1열
                if (rectArr[i].x1 <= pos.x && rectArr[i].x2 >= pos.x && rectArr[i].y1 <= pos.y && rectArr[i].y2 >= pos.y) {
                    clickedColorName = rectArr[i].colorName
                    if (colorMatch(clickedColorName, rightColorHex) === 1) {
                        plusTime()
                        stageThree()
                    } else {
                        minusTime();
                    }

                }
            } if ((rectArr[i].x1 === startPt + 70 + canvasLeftMargin + 190) && (rectArr[i].y1 === canvasTopMargin + 500)) {
                //1행 2열
                if (rectArr[i].x1 <= pos.x && rectArr[i].x2 >= pos.x && rectArr[i].y1 <= pos.y && rectArr[i].y2 >= pos.y) {

                    clickedColorName = rectArr[i].colorName

                    if (colorMatch(clickedColorName, rightColorHex) === 1) {
                        plusTime()
                        stageThree()
                    } else {
                        minusTime();
                    }

                }
            }

        }
    }


    function stageThree() {
        drawStage()
        var rectArr = [];
        canvas.removeEventListener('click', addColorClickedEventTwo, true)
        clearRect(startPt, 350, 100, 100);
        clearRect(startPt, 500, 540, 170);

        var playColor = [];

        let copyArr = _.cloneDeep(colorArr);
    


        var tempColor = pickRandomProperty(copyArr);
        playColor.push(tempColor)
        delete copyArr[tempColor]
        console.log(playColor)
        rectArrays(startPt + 70, 500, tempColor, rectArr)


        delete copyArr[tempColor]
        tempColor = pickRandomProperty(copyArr);
        playColor.push(tempColor)
        console.log(playColor)
        rectArrays(startPt + 190 + 70, 500, tempColor, rectArr)

        delete copyArr[tempColor]
        tempColor = pickRandomProperty(copyArr);
        playColor.push(tempColor)
        rectArrays(startPt + 70, 500 + 190, tempColor, rectArr)

        delete copyArr[tempColor]
        tempColor = pickRandomProperty(copyArr);
        playColor.push(tempColor)

        rectArrays(startPt + 190 + 70, 500 + 190, tempColor, rectArr)

        rightColorName = shuffle(playColor)[0];
        rightColorHex = colorArr[rightColorName]

        inputText(36, 'serif', startPt, 400, rightColorName, colorArr[pickRandomProperty(colorArr)])

        const shuffledArr = shuffle(rectArr);

        for (let i = 0; i < shuffledArr.length; i++) {
            createRect(shuffledArr[i].x1, shuffledArr[i].y1, shuffledArr[i].width, shuffledArr[i].height, shuffledArr[i].colorHex);
        }

        canvas.addEventListener('click', addColorClickedEventThree, true)

    }

    function addColorClickedEventThree(e) {

        const pos = {
            x: e.clientX,
            y: e.clientY
        };


        for (let i = 0; i < rectArr.length; i++) {
            console.log(i, rectArr[i], pos)

            if ((rectArr[i].x1 === startPt + 70) && (rectArr[i].y1 === 500)) {
                //1행1열
                if (rectArr[i].x1 + 90 <= pos.x && rectArr[i].x2 + 90 >= pos.x && rectArr[i].y1 + 100 <= pos.y && rectArr[i].y2 + 100 >= pos.y) {
                    clickedColorName = rectArr[i].colorName

                    if (colorMatch(clickedColorName, rightColorHex) === 1) {
                        plusTime()
                        stageThree();
                    } else {
                        minusTime();
                    }

                }
            }
            // console.log(rectArr[i], pos,  canvasTopMargin + 600)
            if ((rectArr[i].x1 === startPt + 70 + 190) && (rectArr[i].y1 === 500)) {
                //1행 2열
                if (rectArr[i].x1 + 90 <= pos.x && rectArr[i].x2 + 90 >= pos.x && rectArr[i].y1 + 100 <= pos.y && rectArr[i].y2 + 100 >= pos.y) {

                    clickedColorName = rectArr[i].colorName
                    console.log('12', clickedColorName)

                    if (colorMatch(clickedColorName, rightColorHex) === 1) {
                        plusTime()
                        stageThree()
                    } else {
                        minusTime();
                    }

                }
            }

            if ((rectArr[i].x1 === startPt + 70) && (rectArr[i].y1 + 90 === canvasTopMargin + 500 + 190)) {
                //2행 1열
                if (rectArr[i].x1 + 90 <= pos.x && rectArr[i].x2 + 90 >= pos.x && rectArr[i].y1 + 90 <= pos.y && rectArr[i].y2 + 90 >= pos.y) {

                    clickedColorName = rectArr[i].colorName
                    console.log('21', clickedColorName)

                    if (colorMatch(clickedColorName, rightColorHex) === 1) {
                        plusTime()
                        stageThree()
                    } else {
                        minusTime();
                    }

                }
            }

            if ((rectArr[i].x1 === startPt + 70 + 190) && (rectArr[i].y1 + 90 === canvasTopMargin + 500 + 190)) {
                //2행 2열
                if (rectArr[i].x1 + 90 <= pos.x && rectArr[i].x2 + 90 >= pos.x && rectArr[i].y1 + 90 <= pos.y && rectArr[i].y2 + 90 >= pos.y) {

                    clickedColorName = rectArr[i].colorName

                    console.log('22', clickedColorName)
                    if (colorMatch(clickedColorName, rightColorHex) === 1) {
                        plusTime()
                        stageThree()
                    } else {
                        minusTime();
                    }

                }
            }

        }
    }

    function shuffle(array) {
        for (let i = 0; i < array.length; i++) {
            let j = Math.floor(Math.random() * (i + 1));
            [array[i], array[j]] = [array[j], array[i]];
        }
        return array;
    }

    //random값 가져오기
    function pickRandomProperty(obj) {
        var result;
        var count = 0;
        for (var prop in obj)
            if (Math.random() < (1 / ++count))
                result = prop;
        return result;
    }

    
    // function randomInt( startNum, endNum ) {
    //     return startNum + Math.floor( Math.random() * (endNum - startNum));
    // }

    // function randomPickObj( obj ) {
    //     let count = 0;
    //     for( let i in  obj ) {
    //         count++;
    //     }
    //     const r = randomInt( 0, count );
    //     count = 0;
    //     for( let i in obj ) {
    //         if(r === count) {
    //             return obj[i];
    //         } 
    //         count++;
    //     }
    //     return obj[0];
    // }

    function restart() {
        canvas.removeEventListener('click', addColorClickedEvent, true)
        canvas.removeEventListener('click', addColorClickedEventTwo, true)
        canvas.removeEventListener('click', addColorClickedEventThree, true)

        start();
    }


    clickedStart()

</script>
<style>
    #gameContainer {
        border: 1px solid black;
    }

    /* 타이머 */
    #myProgress {
        width: 60px;
        background-color: #ddd;
    }

    #myBar {
        width: 60px;
        height: 30px;
        background-color: #F7958E;
    }
</style>
