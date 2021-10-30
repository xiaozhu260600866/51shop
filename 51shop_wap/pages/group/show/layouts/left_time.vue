<!--  调用
 <star :position="5.0" v-model="ruleform['score3_' + v.id]" :disabled="v.quote.id >0 ? true:false"></star>
 -->
<template>
    <section>

        <span v-if="type == 0">{{ formatDate }}</span>
        <span v-if="type == 1">
            <input class="weui-input fc-6 fs-14 nowrap"  :value="currentValue"  hidden/>
            <p class="time fs-12 fc-white"><span class="Arial _span">{{ days }}</span>天<span class="Arial _span">{{ hours }}</span>时<span class="Arial _span">{{ minutes }}</span>分<span class="Arial _span">{{ seconds }}</span>秒</p>
        </span>
    </section>
</template>
<script>
export default {
    props: [ 'value','type'],
    data() {
        return {
           formatDate:'',
           timer:'',
           days:'',
           hours:'',
           minutes:'',
           seconds:''
        }
    },
    methods: {
        
    },
    computed: {
        currentValue: {
            // 动态计算currentValue的值
            get: function() {
                this.timer =setInterval(()=>{
                this.value=this.value.replace(new RegExp(/-/gm) ,"/");
                let expire_date = new Date(this.value).getTime();
                let now_date = new Date().getTime();
                let date3 = expire_date - now_date;
                //
                //计算出相差天数
                let days = Math.floor(date3 / (24 * 3600 * 1000))
               
                this.days = days;
                //计算出小时数
                let leave1 = date3 % (24 * 3600 * 1000) //计算天数后剩余的毫秒数
                let hours = Math.floor(leave1 / (3600 * 1000))
                this.hours = hours;
                //计算相差分钟数
                //计算相差秒数
                let leave2 = leave1 % (3600 * 1000);
                let minutes = Math.floor(leave2 / (60 * 1000))
                this.minutes = minutes;
                let leave3 = leave2 % (60 * 1000) //计算分钟数后剩余的毫秒数
                let seconds = Math.round(leave3 / 1000)
                this.seconds = seconds;
                this.formatDate =   days +"天"+ hours + ": " + minutes + ":" + seconds + ""
                },1000);
            	
                
            },
            set: function(val) {
                this.$emit('input', val);
            }
        }
    },
    onUnload(){
        clearInterval(this.timer);
    }

}
</script>
<style>
.star-image {
    position: absolute;

    width: 30rpx;
    height: 30rpx;
}


</style>