
<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">

<meta name="viewport"
content="width=device-width, initial-scale=1.0">

<title>대원 교차점 기반 낮·밤 시간 계산기</title>

<style>

body {
    font-family:
        Arial,
        "Noto Sans KR",
        sans-serif;

    margin: 0;
    padding: 20px;

    background: #f2f5f7;
}

.container {

    max-width: 850px;

    margin: auto;

    background: white;

    padding: 30px;

    border-radius: 15px;

    box-shadow:
        0 5px 20px
        rgba(0,0,0,0.12);

}

h1 {
    text-align: center;
}

.subtitle {
    text-align: center;

    color: #555;

    margin-bottom: 30px;
}

.input-group {

    margin-bottom: 20px;

}

label {

    display: block;

    font-weight: bold;

    margin-bottom: 8px;

}

input,
select {

    width: 100%;

    padding: 12px;

    box-sizing: border-box;

    border:
        1px solid #ccc;

    border-radius: 8px;

    font-size: 16px;

}

button {

    width: 100%;

    padding: 15px;

    border: none;

    border-radius: 8px;

    background: #1565c0;

    color: white;

    font-size: 18px;

    font-weight: bold;

    cursor: pointer;

}

button:hover {

    background: #0d47a1;

}

.result {

    margin-top: 30px;

    padding: 20px;

    background: #f8f9fa;

    border-radius: 10px;

    border-left:
        6px solid #1565c0;

}

.result-item {

    margin: 12px 0;

    font-size: 17px;

}

.big {

    font-size: 28px;

    font-weight: bold;

    color: #1565c0;

}

.formula {

    margin-top: 30px;

    padding: 20px;

    background: #eef5ff;

    border-radius: 10px;

}

.note {

    margin-top: 25px;

    padding: 15px;

    background: #fff8e1;

    border-radius: 8px;

}

table {

    width: 100%;

    border-collapse: collapse;

    margin-top: 30px;

}

th,
td {

    border:
        1px solid #ddd;

    padding: 10px;

    text-align: center;

}

th {

    background: #1565c0;

    color: white;

}

</style>

</head>


<body>

<div class="container">

<h1>
🌍 대원·소원 교차점
낮·밤 시간 계산기
</h1>

<p class="subtitle">

대원 최고점과 위도의 교차 경도 차를
시간각으로 변환하는 기하학적 계산 모델

</p>


<div class="input-group">

<label>
관측 위도
</label>

<input
type="number"
id="latitude"
value="60"
step="0.1"
min="-90"
max="90">

</div>


<div class="input-group">

<label>
대원 최고점 위도
</label>

<input
type="number"
id="vertexLatitude"
value="66.5"
step="0.1"
min="-90"
max="90">

</div>


<div class="input-group">

<label>
대원 최고점 기준 경도
</label>

<input
type="number"
id="vertexLongitude"
value="90"
step="0.1"
min="-180"
max="180">

</div>


<div class="input-group">

<label>
계절 계산 방식
</label>

<select id="seasonMode">

<option value="summer">

춘분 → 하지 → 추분
(계산값을 밤 시간으로 사용)

</option>

<option value="winter">

추분 → 동지 → 춘분
(계산값을 낮 시간으로 사용)

</option>

</select>

</div>


<button onclick="calculate()">

계산하기

</button>


<div
class="result"
id="result">

계산 결과가 여기에 표시됩니다.

</div>


<div class="formula">

<h3>
사용 공식
</h3>

<p>

tan(위도)
=
tan(대원 최고점 위도)
×
cos(경도 차)

</p>

<p>

경도 차
=
acos[
tan(위도)
/
tan(대원 최고점 위도)
]

</p>

<p>

시간
=
전체 경도 차 ÷ 15

</p>

<p>

보완 시간
=
24 − 계산 시간

</p>

</div>


<div class="note">

<strong>연구 모델 안내</strong>

<br><br>

이 계산기는 대원과 소원의 교차점을
시간각으로 변환하는 기하학적 모델입니다.

실제 천문학적 일출·일몰 시간은
태양 적위,
지구 자전축 경사,
대기 굴절 등의 요소에 따라
별도로 계산됩니다.

</div>


<h2>
대원 최고점 변화 표
</h2>


<table>

<thead>

<tr>

<th>
최고점 위도
</th>

<th>
관측 위도
</th>

<th>
교차 경도 차
</th>

<th>
시간
</th>

</tr>

</thead>

<tbody id="tableBody">

</tbody>

</table>


</div>


<script>


function formatTime(hours) {

    let h =
        Math.floor(hours);

    let minutesDecimal =
        (hours - h) * 60;

    let m =
        Math.floor(minutesDecimal);

    let s =
        Math.round(
            (minutesDecimal - m) * 60
        );

    if (s === 60) {

        s = 0;

        m++;

    }

    if (m === 60) {

        m = 0;

        h++;

    }

    return
        h + "시간 "
        + m + "분 "
        + s + "초";

}



function calculate() {

    const latitude =
        parseFloat(
            document.getElementById(
                "latitude"
            ).value
        );


    const vertexLatitude =
        parseFloat(
            document.getElementById(
                "vertexLatitude"
            ).value
        );


    const vertexLongitude =
        parseFloat(
            document.getElementById(
                "vertexLongitude"
            ).value
        );


    const seasonMode =
        document.getElementById(
            "seasonMode"
        ).value;



    if (
        isNaN(latitude)
        ||
        isNaN(vertexLatitude)
    ) {

        document.getElementById(
            "result"
        ).innerHTML =
        "위도 값을 입력하세요.";

        return;

    }



    const rad =
        Math.PI / 180;



    const phi =
        latitude * rad;



    const phiV =
        vertexLatitude * rad;



    const denominator =
        Math.tan(phiV);



    if (
        Math.abs(denominator)
        < 0.00000001
    ) {

        document.getElementById(
            "result"
        ).innerHTML =
        "대원 최고점 위도는 0°가 될 수 없습니다.";

        return;

    }



    let ratio =
        Math.tan(phi)
        /
        Math.tan(phiV);



    if (
        ratio > 1
        ||
        ratio < -1
    ) {

        document.getElementById(
            "result"
        ).innerHTML =

        `
        입력한 위도에서는
        현재 대원 최고점과
        교차점을 만들 수 없습니다.
        `;

        return;

    }



    let deltaLongitude =

        Math.acos(ratio)
        /
        rad;



    let totalLongitudeDifference =

        2
        *
        deltaLongitude;



    let timeHours =

        totalLongitudeDifference
        /
        15;



    let complementaryTime =

        24
        -
        timeHours;



    let calculatedLabel;

    let oppositeLabel;

    if (
        seasonMode
        ===
        "summer"
    ) {

        calculatedLabel =
            "기하학적으로 계산된 밤 시간";

        oppositeLabel =
            "24시간에서 뺀 낮 시간";

    }

    else {

        calculatedLabel =
            "기하학적으로 계산된 낮 시간";

        oppositeLabel =
            "24시간에서 뺀 밤 시간";

    }



    document.getElementById(
        "result"
    ).innerHTML =

    `

    <h2>
    계산 결과
    </h2>


    <div class="result-item">

    관측 위도:

    <strong>
    ${latitude.toFixed(2)}°
    </strong>

    </div>


    <div class="result-item">

    대원 최고점:

    <strong>
    ${vertexLatitude.toFixed(2)}°
    </strong>

    </div>


    <div class="result-item">

    최고점 기준 경도:

    <strong>
    ${vertexLongitude.toFixed(2)}°
    </strong>

    </div>


    <div class="result-item">

    한쪽 교차점 경도 차:

    <strong>
    ${deltaLongitude.toFixed(4)}°
    </strong>

    </div>


    <div class="result-item">

    양쪽 교차점 전체 경도 차:

    <strong>
    ${totalLongitudeDifference.toFixed(4)}°
    </strong>

    </div>


    <div class="result-item">

    ${calculatedLabel}:

    <div class="big">

    ${formatTime(timeHours)}

    </div>

    </div>


    <div class="result-item">

    ${oppositeLabel}:

    <div class="big">

    ${formatTime(complementaryTime)}

    </div>

    </div>

    `;



    createTable(
        latitude
    );

}



function createTable(
    latitude
) {

    const vertexList =

    [
        66.5,
        70.5,
        74.5,
        78.5,
        82.5,
        86.5,
        88.5
    ];



    let html = "";



    vertexList.forEach(

        function(vertex) {

            let ratio =

                Math.tan(
                    latitude
                    *
                    Math.PI
                    /
                    180
                )

                /

                Math.tan(
                    vertex
                    *
                    Math.PI
                    /
                    180
                );



            if (
                ratio >= -1
                &&
                ratio <= 1
            ) {

                let delta =

                    Math.acos(ratio)
                    *
                    180
                    /
                    Math.PI;



                let total =

                    delta
                    *
                    2;



                let hours =

                    total
                    /
                    15;



                html +=

                `

                <tr>

                <td>
                ${vertex}°
                </td>

                <td>
                ${latitude}°
                </td>

                <td>
                ${total.toFixed(2)}°
                </td>

                <td>
                ${formatTime(hours)}
                </td>

                </tr>

                `;

            }

            else {

                html +=

                `

                <tr>

                <td>
                ${vertex}°
                </td>

                <td>
                ${latitude}°
                </td>

                <td colspan="2">

                교차 없음

                </td>

                </tr>

                `;

            }

        }

    );



    document.getElementById(
        "tableBody"
    ).innerHTML =

    html;

}



calculate();


</script>


</body>

</html>
