<template>
<!-- <script async src="https://cdnjs.cloudflare.com/ajax/libs/howler/2.1.3/howler.min.js"></script> -->
    <div class="video">
        <div class="player_audio" >
            <div @click="play(true)" v-show="!playing">播放</div>
            <div @click="pause(true)" v-show="playing">暂停</div>
            <div @click="stop">停止</div>
            <div @click="mute(true)" v-show="!muted">静音</div>
            <div @click="mute(false)" v-show="muted">解除静音</div>
            <div @click="rate(0.5)">播放速度-0.5</div>
            <div @click="rate(1)">播放速度-1</div>
            <div @click="rate(1.25)">播放速度-1.25</div>
            <div @click="rate(1.5)">播放速度-1.5</div>
            <div @click="rate(2)">播放速度-2</div>
            <div @click="pre">上一首</div>
            <div @click="next">下一首</div>
            <slider v-model="value" active-color="#ee0a24" @drag-start="pause" @drag-end="play"  @input="change" style="width:3rem;margin:0 auto;margin-top:0.3rem;padding:0"><div slot="button" class="custom-button" style="min-width: .6rem;">{{ timer }}</div></slider>
            
        </div>
    </div>
</template>

<script>
import {slider} from 'vant'
export default {
    name:'video',
    data() {
        return {
            audioSrc:['http://music.163.com/song/media/outer/url?id=1401924960.mp3','http://music.163.com/song/media/outer/url?id=1372796676.mp3','http://music.163.com/song/media/outer/url?id=1382596189.mp3'],
            value:0,
            timer:'',
            playing:false,
            currentIndex:0,
            muted:false
        }
    },
    components:{
        slider
    },
    created() {

    },
    mounted(){
        this.audioPlayer()
    },
    methods: {

        audioPlayer(index = 0){
            let _this = this
            this.sound = new Howl({
                src: this.audioSrc[index],
                autoplay: true,
                loop: true,
                volume: 0.5,
                html5: true,
                preload:true,
                rate:1,
                mute:_this.muted,
                onload() {
                    console.log('onload!',this);
                },
                onplay() {
                    _this.playing = true
                    //_this.duration = _this.formatTime(Math.round(this._duration));
                    _this.step()
                    console.log('onload!',this);
                },
                onmute() {
                    _this.muted = this._muted
                },
                onpause() {
                    _this.playing = false
                    _this.cancelAF()
                },
                onstop() {
                    _this.playing = false
                    _this.setTimerAndValue()
                    _this.cancelAF()
                },
                onend() {
                    console.log('end')
                    _this.cancelAF()
                    _this.next()
                },
            });
            
        },

        formatTime(secs) {
            let minutes = Math.floor(secs / 60) || 0;
            let seconds = (secs - minutes * 60) || 0;
            return minutes + ':' + (seconds < 10 ? '0': '') + seconds;
        },

        play(){
            this.sound.play();
        },

        pause(){
            this.sound.pause()
        },

        stop(){
            this.sound.stop()
        },

        mute(boolean){
            this.sound.mute(boolean)
        },

        rate(rate){
            this.sound.rate(rate)
        },

        pre(){
            this.stop()
            this.currentIndex = this.currentIndex >= 1 ? this.currentIndex - 1 : 0
            this.audioPlayer(this.currentIndex)
            this.play()
        },

        next(){
            this.stop()
            this.currentIndex = this.currentIndex + 1
            if(this.currentIndex <= this.audioSrc.length - 1){
                this.audioPlayer(this.currentIndex)
                this.play()
            }else{
                console.log('no next song!')
            }
            
        },

        setTimerAndValue(){
            let seek = this.sound.seek() || 0;
            this.timer = this.formatTime(Math.round(seek));
            this.value = Math.round((seek / this.sound._duration) * 100) || 0
        },

        step(){
            this.setTimerAndValue()
            this.s = requestAnimationFrame(this.step);
            console.log(1)
        },
        
        cancelAF(){
            cancelAnimationFrame(this.s)
        },

        change(value){
            this.sound.seek(this.sound._duration * value / 100);
            this.setTimerAndValue()
        },
        
    },
    beforeDestroy(){
        this.cancelAF()
    }
}
</script>

<style lang="less" scoped>
    .player_audio{
        padding-bottom: 0.3rem;
        div{
            padding: 0.1rem 0.15rem
        }
    }
</style>
