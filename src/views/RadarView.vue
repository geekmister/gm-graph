<template>
    <canvas ref="radar"></canvas>
</template>

<script setup>
    import {ref, onMounted} from "vue";

    let mW = 400;
    let mH = 400;
    let mData = [
        ["速度77", 21], ["力量72", 56], ["防守46", 46], ["射门50", 50], ["传球80", 80]
    ]
    let mCount = mData.length;
    let mCenter = mW / 2;
    let mRadius = mCenter - 50;
    let mAngle = Math.PI * 2 / mCount;
    let mCtx = null;
    let mColorPolygon = '#B8B8B8'; //多边形颜色
    let mColorLines = "#B8B8B8";
    let mColorText = "#000000";
    let radar = ref();

    const drawPolygon = (ctx => {
        ctx.save();
        ctx.strokeStyle = mColorPolygon;

        let r = mRadius/ mCount; //单位半径

        // var r = mRadius/ (mCount-2); //单位半径 画三个五边形

        //画5个圈

        for(let i = 0; i < mCount; i ++){
            // for(var i = 0; i < mCount-2; i ++){ // 画三个五边形
            ctx.beginPath();
            let currR = r * ( i + 1); //当前半径

            //画5条边
            for(let j = 0; j < mCount; j ++){
                let x = mCenter + currR * Math.cos(mAngle * j);
                let y = mCenter + currR * Math.sin(mAngle * j);
                ctx.lineTo(x, y);
            }

            ctx.closePath()
            ctx.stroke();
        }
        ctx.restore();
    })

    const drawLines = (ctx => {
        ctx.save();
        ctx.beginPath();
        ctx.strokeStyle = mColorLines;

        for(let i = 0; i < mCount; i ++){
            let x = mCenter + mRadius * Math.cos(mAngle * i);
            let y = mCenter + mRadius * Math.sin(mAngle * i);
            ctx.moveTo(mCenter, mCenter);
            ctx.lineTo(x, y);
        }

        ctx.stroke();
        ctx.restore();
    });

    const drawLines1 = (ctx => {
        ctx.save();
        ctx.beginPath();
        let count = 0;

        for(let i = 0; i < mCount; i ++){
            let x = mCenter + mRadius * Math.cos(mAngle * i) * mData[i][1] / 100;
            let y = mCenter + mRadius * Math.sin(mAngle * i) * mData[i][1] / 100;
            let x1 = 0;
            let y1 = 0;
            count = i + 1;

            if (count < mCount) {
                x1 = mCenter + mRadius * Math.cos(mAngle * (i+1)) * mData[i+1][1] / 100;
                y1 = mCenter + mRadius * Math.sin(mAngle * (i+1)) * mData[i+1][1] / 100;
            }else{
                x1 = mCenter + mRadius * Math.cos(mAngle * 0) * mData[0][1] / 100;
                y1 = mCenter + mRadius * Math.sin(mAngle * 0) * mData[0][1] / 100;
            }

            ctx.moveTo(x, y);
            ctx.lineTo(x1, y1);
            ctx.lineWidth = 2; //设置线宽状态
            ctx.strokeStyle = '#1478FA'; //设置线的颜色状态
        }

        ctx.stroke();
        ctx.restore();
    });

    const drawText = (ctx => {
        ctx.save();
        let fontSize = mCenter / 12;
        ctx.font = fontSize + 'px Microsoft Yahei';
        ctx.fillStyle = mColorText;

        for(let i = 0; i < mCount; i ++){
            let x = mCenter + mRadius * Math.cos(mAngle * i);
            let y = mCenter + mRadius * Math.sin(mAngle * i);
            if( mAngle * i >= 0 && mAngle * i <= Math.PI / 2 ){
                ctx.fillText(mData[i][0], x, y + fontSize);
            }else if(mAngle * i > Math.PI / 2 && mAngle * i <= Math.PI){
                ctx.fillText(mData[i][0], x - ctx.measureText(mData[i][0]).width, y + fontSize);
            }else if(mAngle * i > Math.PI && mAngle * i <= Math.PI * 3 / 2){
                ctx.fillText(mData[i][0], x - ctx.measureText(mData[i][0]).width, y);
            }else{
                ctx.fillText(mData[i][0], x, y);
            }
        }

        //中心绘制文字
        ctx.font="bold 36px Arial"
        ctx.fillStyle='#1478FA'
        ctx.fillText("75",mCenter-18,mCenter+16);
        ctx.restore();
    });

    const drawRegion = (ctx => {
        ctx.save();
        ctx.beginPath();
        for(var i = 0; i < mCount; i ++){
            let x = mCenter + mRadius * Math.cos(mAngle * i) * mData[i][1] / 100;
            let y = mCenter + mRadius * Math.sin(mAngle * i) * mData[i][1] / 100;
            ctx.lineTo(x, y);
        }

        ctx.closePath();
        ctx.fillStyle = 'rgba(255, 0, 0, 0.5)';
        ctx.fill();
        ctx.restore();
    });

    const drawCircle = (ctx => {
        ctx.save();

        let r = mCenter / 50;

        for(let i = 0; i < mCount; i ++){
            let x = mCenter + mRadius * Math.cos(mAngle * i) * mData[i][1] / 100;
            let y = mCenter + mRadius * Math.sin(mAngle * i) * mData[i][1] / 100;
            ctx.beginPath();
            ctx.arc(x, y, r, 0, Math.PI * 2);
            ctx.fillStyle = 'rgba(20, 120, 250, 0.8)';
            ctx.fill();
        }

        ctx.restore();
    });

    onMounted(() => {
        radar.value.height = mH;
        radar.value.width = mW;
        mCtx = radar.value.getContext('2d');
        drawPolygon(mCtx);
        drawLines(mCtx);
        drawRegion(mCtx);
        drawText(mCtx);
        drawCircle(mCtx);
        drawLines1(mCtx)
    });
</script>

<style>

</style>