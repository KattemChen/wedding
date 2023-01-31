<template>
    <div class="home-container">
        <MainHeadSection />
        <FixedRouteHeader />
        <CoupleProfile />
        <QuotaFrase />
        <Gallary />
        <Story />
        <CardCountDown />
    </div>
</template>

<script>
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';

import MainHeadSection from '../components/MainHeadSection.vue';
import FixedRouteHeader from '../components/FixedRouteHeader.vue';
import CoupleProfile from '../components/CoupleProfile.vue';
import QuotaFrase from '../components/QuotaFrase.vue';
import Story from '../components/Story.vue';
import CardCountDown from '../components/CardCountDown.vue';
import Gallary from '../components/Gallary.vue';
import { BaseTrack } from '../utils/track';
import { Performance } from '../utils/performance';
import { vScroll } from '../utils/scroll';

@Component({
    components: {
        MainHeadSection,
        FixedRouteHeader,
        CoupleProfile,
        QuotaFrase,
        Story,
        CardCountDown,
        Gallary
    }
})
export default class Home extends Vue {
    @Watch('showQuestionMask', { immediate: true })
    onQuestionMaskStatusChange() {
        if (this.showQuestionMask) {
            vScroll.inserted();
        } else {
            vScroll.unbind();
        }
    }

    created() {
        window.addEventListener('load', () => {
            BaseTrack.track(Performance.getTimings());
        });
    }
}
</script>
<style lang="less">
@import '../css/common.less';
body {
    color: #7b7571;
}

.text-center {
    text-align: center;
}

.img-responsive {
    display: block;
    max-width: 100%;
    height: auto;
}
</style>
<style lang="less" scoped>
.home-container {
    color: #7b7571;
}
</style>
