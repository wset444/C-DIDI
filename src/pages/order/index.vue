<template>
    <div class="container">
        <div class="header">我的订单</div>
        <van-tabs v-model:active="active" @click-tab="onClickTab">
            <van-tab title="全部" name="" />
            <van-tab title="待支付" name="1" />
            <van-tab title="待服务" name="2" />
            <van-tab title="已完成" name="3" />
            <van-tab title="已取消" name="4" />
        </van-tabs>
        <van-row @click="goDetail(item)" v-for="item in orders" :key="item.out_trade_no">
            <van-col span="5">
                <van-image wdith="50" height="50" radius="5" :src="item.serviceImg"></van-image>
            </van-col>
            <van-col span="14">
                <div class="text1">
                    {{ item.service_name }}
                </div>
                <div class="text2">
                    {{ item.hospital_name }}
                </div>
                <div class="text2">
                    预约时间:{{ item.starttime }}
                </div>
            </van-col>
            <van-col class="text2" span="5" :style="{ color: colorMap[item.trade_state] }">
                {{ item.trade_state }}

                <counter :second="item.timer" v-if="item.trade_state === '待支付'"></counter>
            </van-col>
        </van-row>
    </div>
</template>

<script setup>
import { onMounted, ref } from 'vue';
import { useRouter, useRoute } from 'vue-router'
import { orderList } from '../../api/order/index'
import counter from '../../components/counter.vue'

const active = ref('0')
const router = useRouter();
const route = useRoute()
const colorMap = {
    '待支付': '#ffa200',
    '待服务': '#1da6fd',
    '已完成': '#21c521'
}



const onClickTab = (item) => {
    console.log(item.name);
    goorderList(item.name)

}

const goDetail = (item) => {
    router.push(`/detail?oid=${item.out_trade_no}`)
    console.log(item);
}


const goorderList = (item) => {
    orderList({
        state: item
    }).then(({ data }) => {
        data.data.forEach(item => {
            item.timer = item.order_start_time + 7200000 - Date.now()
        })
        orders.value = data.data
        console.log(data.data);
    })
}

const orders = ref([])
onMounted(() => {
    console.log();
    if (route.query.id) {
        active.value = route.query.id.toString()

        goorderList(route.query.id)
    } else {
        goorderList()
    }

})
</script>

<style scoped lang="less">
.container {
    background-color: #f0f0f0;
    height: 100vh;
}

.header {
    background-color: #fff;
    line-height: 40px;
    text-align: center;
}

.van-row {
    background-color: #fff;
    padding: 10px;
    margin: 5px;
    border-radius: 5px;

    .text1 {
        font-size: 16px;
        line-height: 25px;
        font-weight: bold;
    }

    .text2 {
        font-size: 14px;
        line-height: 20px;
        color: #999999;
    }
}

.bottom-text {
    line-height: 50px;
    text-align: center;
    color: #999999;
}
</style>