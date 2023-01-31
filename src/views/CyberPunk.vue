<template>
    <div class="number-select-mask" @touchmove.prevent @click="onClickRoot">
        <div v-for="(text, index) in CYBERPUNK_TEXT_LIST" :key="text + index">
            <CyberpunkTextContainer :text="text" v-if="index === currentDisplayTextIdx" />
        </div>

        <div class="btn-wrap" v-if="showBtn">
            <div class="btn">愿意</div>
            <div class="btn">非常愿意</div>
        </div>
    </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import CommonConfig from '../const/commonConfig';
import CyberpunkTextContainer from '../components/CyberpunkTextContainer.vue';

@Component({
    components: { CyberpunkTextContainer }
})
export default class NumberSelectMask extends Vue {
    readonly CYBERPUNK_TEXT_LIST = ['回答我的问题', '准备好了么', '3', '2', '1', '你愿意嫁给我吗?'];
    readonly DEFALUT_MASK_X = -500;
    readonly DEFALUT_MASK_Y = -500;
    digitalMatrix: string[] = [];
    selectedDigitalIdx: number[] = [];
    questions = CommonConfig.maskQuestions;
    maskX: number = this.DEFALUT_MASK_X;
    maskY: number = this.DEFALUT_MASK_Y;
    timer = 0;
    currentDisplayTextIdx: number = 0;
    showBtn = false;

    $refs!: {
        gridBorderContainer: HTMLUListElement;
    };

    get maskStyle() {
        return {
            WebkitMaskPosition: `${this.maskX}px ${this.maskY}px`, // 此处设置 mask 样式
            maskPosition: `${this.maskX}px ${this.maskY}px`
        };
    }

    created() {
        this.initDigitalMatrix();
    }

    onClickRoot() {
        switch (this.currentDisplayTextIdx) {
            case 0: {
                this.currentDisplayTextIdx = 1;
                break;
            }
            case 1: {
                this.timer = setInterval(() => {
                    if (this.currentDisplayTextIdx === this.CYBERPUNK_TEXT_LIST.length - 1) {
                        clearInterval(this.timer);
                        this.showBtn = true;
                        return;
                    }
                    this.currentDisplayTextIdx += 1;
                }, 1300);
                break;
            }
            default: {
                return;
            }
        }
    }

    // 将遮罩移动到鼠标位置
    handleMouseMove(e: MouseEvent) {
        e.stopPropagation();
        const rect = this.$refs.gridBorderContainer.getBoundingClientRect() || null;

        this.maskX = e.pageX - (rect ? rect.x : this.DEFALUT_MASK_X) - (150 / 1500) * document.body.clientWidth;
        this.maskY = e.pageY - (rect ? rect.y : this.DEFALUT_MASK_Y) - (150 / 1500) * document.body.clientWidth;
    }

    handleMouseLeave() {
        this.maskX = this.DEFALUT_MASK_X;
        this.maskY = this.DEFALUT_MASK_Y;
    }

    onSelect(idx: number) {
        const existedIndex = this.selectedDigitalIdx.findIndex(item => item === idx);
        if (existedIndex !== -1) {
            // 已点选
            if (existedIndex === this.selectedDigitalIdx.length - 1) {
                // 允许从后开始逐个取消点选
                this.selectedDigitalIdx.pop();
            }
            return;
        }
        this.selectedDigitalIdx.push(idx);
    }

    initDigitalMatrix() {
        // 初始化一个10*10的, 填充0-9随机整数的二维数组
        this.digitalMatrix = Array.from({ length: 120 }, () => String(Math.floor(Math.random() * 10)));
    }
}
</script>
<style lang="less">
body {
    background-color: #333;
}
</style>
<style scoped lang="less">
.number-select-mask {
    position: fixed;
    left: 0;
    top: 0;
    right: 0;
    bottom: 0;
    width: 100vw;
    height: 100vh;
    background: rgba(0, 0, 0, 1);
    z-index: 1000;
}
.grid {
    margin: (10/375) * 100vw (5/375) * 100vw;
    position: relative;
}

.grid-list {
    top: 0;
    left: 0;
    right: 0;
    margin: 0 auto;
    color: #666;
    display: flex;
    flex-wrap: wrap;
    position: absolute;
}

.grid-item {
    width: (20/375) * 100vw;
    height: (20/375) * 100vw;
    border: (1/375) * 100vw solid transparent;
    margin-top: (3/375) * 100vw;
    margin-left: (3/375) * 100vw;
    display: flex;
    font-size: (10/375) * 100vw;
    justify-content: center;
    align-items: center;
    outline: none;
    transition: 300ms;
}

.grid-border {
    position: absolute;
    -webkit-mask-size: (75/375) * 100vw (75/375) * 100vw;
    -webkit-mask-repeat: no-repeat;
    -webkit-mask-image: radial-gradient(circle, #fff, transparent (25/375) * 100vw);
    -webkit-position: 0 0;

    .grid-item {
        border-color: #2bf;
    }
}

.grid-num {
    .grid-item {
        cursor: pointer;

        &:hover {
            color: #fff;
        }

        &.active {
            color: #666;
            background-color: #fe2;
        }
    }
}

.btn-wrap {
    position: absolute;
    left: 0;
    right: 0;
    bottom: 300px;
    display: flex;
    justify-content: space-around;
    align-items: center;
    .btn {
        font-size: 3vw;
        color: #fff;
    }
}
.fade-enter-active {
    animation: fade-in 0.5s;
}
.fade-leave-active {
    animation: fade-in 0.5s reverse;
}
@keyframes fade-in {
    0% {
        transform: scale(0);
        opacity: 0;
    }
    50% {
        transform: scale(1.5);
        opacity: 0.5;
    }
    100% {
        transform: scale(1);
        opacity: 1;
    }
}
</style>
